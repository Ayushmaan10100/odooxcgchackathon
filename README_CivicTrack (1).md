
# 🚨 CivicTrack – Hackathon Project

### Problem Statement 2

Empower citizens to easily report and track local issues (e.g., road damage, water leaks, garbage overflow) with location-based filtering and status tracking.

---

## 🔍 Problem Summary
The civic infrastructure in urban areas faces frequent challenges—potholes, water supply issues, broken lights, etc.—yet most of these go unreported or unresolved due to lack of an effective platform. **CivicTrack** provides a user-friendly system to report, track, and manage such issues efficiently, encouraging citizen participation and improved municipal response.

---

## 🌟 Key Features

### ➤ Features Visibility
- Issues visible only within a **3–5 km radius** using GPS/manual location.
- User can’t interact with reports outside their zone.

### ➤ Quick Issue Reporting
- Report with title, description, photos (up to 3), and category.
- Supports **anonymous** or **verified** reporting.

### ➤ Supported Categories
- Roads (potholes, obstructions)  
- Lighting (broken/flickering lights)  
- Water Supply (leaks, low pressure)  
- Cleanliness (overflowing bins, garbage)  
- Public Safety (open manholes, exposed wiring)  
- Obstructions (fallen trees, debris)

---

## 🗺️ Map Mode & Filtering

- Issues displayed as pins on map (Leaflet.js or Google Maps).
- Filter by:
  - Status: `Reported`, `In Progress`, `Resolved`
  - Category
  - Distance: 1 km, 3 km, 5 km

---

## 📈 Status Tracking
- Issues have status logs and timestamps.
- Reporters get notified on status updates.

---

## 🧹 Moderation & Safety
- Spam/irrelevant reports can be flagged.
- Multiple flags auto-hide issues pending review.

---

## 👮 Admin Role
- Manage and review reports.
- Access issue analytics.
- Ban abusive users.

---

## 💻 Technical Overview

| Feature       | Technology Used       |
|---------------|------------------------|
| Frontend      | React.js or Flutter    |
| Backend       | Node.js + Express      |
| Database      | PostgreSQL + PostGIS   |
| Map/Location  | Leaflet.js, GPS API    |
| Auth          | JWT, Firebase optional |
| Media Upload  | Firebase/Cloudinary    |
| Hosting       | Render/Netlify/Heroku  |

---

## 🧩 Evaluation Criteria Matrix

| Criterion         | Implementation Summary |
|------------------|-------------------------|
| Coding Standards | Modular folders, ESLint, comments |
| Logic            | Verified reports, status flow, radius filter |
| Modularity       | Separated controllers, routes, DB models |
| Database         | Normalized schema with geo-indexing |
| Design           | User stories, clear UI for report, map, status |
| Content Design   | Icon-based categories, status colors |
| Performance      | Pagination, index usage, async queries |
| Scalability      | Modular APIs, map clustering support |
| Security         | Input sanitization, auth middleware, role check |
| Usability        | Location filter, category icons, feedback notifications |

---

## ✅ Extras
- Map clustering for high-density areas.
- Notifications with EmailJS or Twilio.
- Admin dashboard with filters and charts.

---

## 📂 Folder Structure

```
/civictrack
├── client/                # Frontend (React)
├── server/                # Backend (Express)
│   ├── routes/            # API endpoints
│   ├── controllers/       # Logic handlers
│   ├── models/            # DB schema/models
│   └── middleware/        # JWT/auth
├── database/              # PostgreSQL schema
├── .env                   # Secrets/configs
└── README.md              # Project doc
```

---

## 🧠 Key Code Snippets

### 📍 1. Location-Based Filtering (PostgreSQL + PostGIS)
```sql
-- Find all issues within a 3km radius from a given point
SELECT * FROM issues 
WHERE ST_DWithin(location, ST_MakePoint($1, $2)::geography, 3000);
```

---

### 📝 2. Issue Reporting (Express + Sequelize)
```js
// controllers/issueController.js
exports.createIssue = async (req, res) => {
  const { title, description, category, location, photoURL, isAnonymous } = req.body;
  if (!title || !category || !location) return res.status(400).json({ error: "Missing fields" });

  const newIssue = await Issue.create({ title, description, category, location, photo_url: photoURL, is_anonymous: isAnonymous });
  res.status(201).json(newIssue);
};
```

---

### 🗺️ 3. Displaying Map Pins (React + Leaflet.js)
```js
<Map center={[latitude, longitude]} zoom={13}>
  {issues.map((issue) => (
    <Marker key={issue.id} position={[issue.location.lat, issue.location.lng]}>
      <Popup>{issue.title}</Popup>
    </Marker>
  ))}
</Map>
```

---

### 🔐 4. JWT Authentication Middleware
```js
// middleware/auth.js
const jwt = require("jsonwebtoken");

module.exports = function(req, res, next) {
  const token = req.header("Authorization");
  if (!token) return res.status(401).send("Access Denied");

  try {
    const verified = jwt.verify(token, process.env.JWT_SECRET);
    req.user = verified;
    next();
  } catch (err) {
    res.status(400).send("Invalid Token");
  }
};
```

---

### 🛡️ 5. Admin: Ban a User
```js
// controllers/adminController.js
exports.banUser = async (req, res) => {
  const { userId } = req.params;
  await User.update({ banned: true }, { where: { id: userId }});
  res.send("User banned");
};
```

---

### ⚠️ 6. Flagging a Report
```js
// routes/flagRoute.js
router.post("/flag/:issueId", async (req, res) => {
  const issue = await Issue.findByPk(req.params.issueId);
  issue.flags += 1;

  if (issue.flags >= 3) {
    issue.hidden = true; // auto-hide after 3 flags
  }

  await issue.save();
  res.send("Report flagged.");
});
```

---

### 📊 7. Admin Analytics Query
```sql
-- Count issues by category
SELECT category, COUNT(*) as total FROM issues GROUP BY category;

-- Most active areas (heatmap prep)
SELECT ST_AsText(location), COUNT(*) FROM issues GROUP BY location;
```


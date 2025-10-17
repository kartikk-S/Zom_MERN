Zom MERN — Food Reels (MERN) App

Zom MERN is a full-stack MERN (MongoDB, Express, React, Node.js) application that implements a food-reels style experience. Food partners can register and upload short food videos; users can browse, like, and save items. The project demonstrates end-to-end web engineering topics: authentication, file upload, third-party media hosting, RESTful API design, and a React SPA.

Key technologies:

React · Vite · Axios · react-router-dom · Node.js · Express · MongoDB · Mongoose · JWT · Multer · ImageKit · CORS · dotenv

Features:

Role-based flows: Food Partner and User.

User and Food Partner registration / login (JWT).

Upload short food videos (Multer memoryStorage → ImageKit).

Browse feed (GET /api/food).

Like and save food items (likes and saves persisted).

Protected routes via authentication middleware.

Modular code: controllers, models, routes, services, middleware.

Repo structure (important files)
/backend
  ├─ server.js                # app entry — connects DB, starts server
  └─ src/
     ├─ app.js                # express app, CORS, route mounts
     ├─ db/db.js              # mongoose connection
     ├─ routes/               # auth.routes.js, food.routes.js, ...
     ├─ controllers/          # auth.controller.js, food.controller.js, ...
     ├─ models/               # user.model.js, food.model.js, ...
     ├─ middlewares/          # auth.middleware.js
     └─ services/
        └─ storage.service.js # ImageKit wrapper
/frontend
  ├─ package.json
  └─ src/
     ├─ pages/
     ├─ components/
     └─ App.jsx
README.md

Getting started (development)

Prerequisites

Node.js (v18+ recommended), npm

MongoDB (Atlas or local)

ImageKit account (public/private keys and endpoint) or replace with another CDN/storage

1. Clone
git clone <repo-url>
cd <repo-folder>

2. Backend — install & run
cd backend
npm install
# Start dev server (nodemon recommended)
node server.js
# or
# npm install --save-dev nodemon
# npx nodemon server.js


The backend will listen on http://localhost:3000
 by default.

3. Frontend — install & run
cd frontend
npm install
npm run dev


The frontend Vite dev server typically runs on http://localhost:5173
.

Environment variables

Create backend/.env (do not commit secrets). Example .env.example:

# backend/.env.example
PORT=3000
MONGODB_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/<dbname>?retryWrites=true&w=majority
JWT_SECRET=your_jwt_secret_here
IMAGEKIT_PUBLIC_KEY=your_imagekit_public_key
IMAGEKIT_PRIVATE_KEY=your_imagekit_private_key
IMAGEKIT_URL_ENDPOINT=https://ik.imagekit.io/your_endpoint


Security note: Do not commit .env. Use your deployment platform's secret manager in production, and rotate keys if leaked.

API (important endpoints)

Base path: http://localhost:3000/api

Auth

POST /api/auth/user/register — register user

POST /api/auth/user/login — login user (sets cookie token or returns token)

GET /api/auth/user/logout — logout

POST /api/auth/food-partner/register — register partner

POST /api/auth/food-partner/login — login partner

GET /api/auth/food-partner/logout — logout

Food

POST /api/food/ — create food (protected; multipart form-data; file field mama)

Use FormData client-side: formData.append('mama', file) and formData.append('name', ...)

GET /api/food/ — list food items

POST /api/food/like — like a food item (protected)

POST /api/food/save — save a food item (protected)

GET /api/food/save — get saved items (protected)

Food partner

GET /api/food-partner/:id — get partner data (protected where required)

Example client request snippets

Login with axios (cookies):

axios.post("http://localhost:3000/api/auth/user/login",
  { email, password },
  { withCredentials: true }
);


Upload (create food) with FormData:

const formData = new FormData();
formData.append('mama', fileInput.files[0]);    // field name 'mama' expected by backend
formData.append('name', 'Pizza');
formData.append('description', 'Tasty');

axios.post('http://localhost:3000/api/food', formData, {
  withCredentials: true // include cookie for auth
  // Do not set Content-Type header; browser sets boundary
});


curl login test (inspect Set-Cookie):

curl -i -X POST http://localhost:3000/api/auth/user/login \
  -H "Content-Type: application/json" \
  -d '{"email":"user@example.com","password":"password"}'

Models (overview)

User — { fullName, email, password, timestamps }

FoodPartner — { name, email, password, timestamps }

Food — { name, video (URL), description, foodPartner: ObjectId, timestamps }

Likes — { user: ObjectId, food: ObjectId }

Save — { user: ObjectId, food: ObjectId }

Design decisions & rationale

Multer memoryStorage + ImageKit: avoids local disk I/O, allows direct upload of file buffers to a CDN-backed host.

JWT in httpOnly cookies: provides protection from XSS (cookie cannot be read by JS). For cross-site cookies in production, configure sameSite:'none' and secure:true.

Separation of concerns: controllers for business logic, models for DB schemas, services for external integrations (ImageKit), middleware for auth.

Known issues & TODOs

.env was present in earlier commits — rotate secrets and add .env to .gitignore. (Security)

Add validations (e.g., express-validator) to reject invalid payloads.

Add comprehensive unit/integration tests (Jest + supertest, mongodb-memory-server).

Improve UX: error messages, progress on uploads, client-side validation.

Add pagination and indexing for large feeds; denormalize counts for performance.

Testing & QA

Use Postman to create and exercise auth, food, like, and save flows.

Manual testing steps in demo checklist below.

Demo checklist (what to show in an interview)

Start backend and frontend servers.

Open the browser, log in as a test user (or register).

Show network tab: login request and Set-Cookie header / response body token.

If food partner: upload a small video via the Create Food page (field name mama) and show the item appearing in feed and DB.

Demonstrate like/save and show the DB record update.

Deployment recommendations

Host frontend on Vercel/Netlify; host backend on Heroku/GCP/AWS/Fargate.

Use MongoDB Atlas for DB hosting.

Store ImageKit keys in env/secrets; rotate keys periodically.

Serve backend behind HTTPS; ensure cookies use secure:true and sameSite:'none' for cross-site contexts.

Contributing

Fork the repo.

Create a new branch: git checkout -b feat/your-feature.

Implement tests and code changes.

Open a PR with a clear description of changes.

create a one-page demo script you can read during interviews (3 steps, 90 seconds).
Which would you like next?

# EduNest

EduNest is a full-stack Learning Management System (LMS) built with React, Vite, Tailwind CSS, Express.js, MongoDB, and Clerk. It provides separate student and educator experiences, course discovery, course details, enrollment/player views, educator dashboards, and authentication with Clerk.

> **Implementation note:** The current repository combines a working frontend LMS experience with a backend foundation. Many course, enrollment, dashboard, rating, and lecture records are currently represented by local frontend data in `client/src/assets/assets.js`, while the backend provides MongoDB user persistence and Clerk webhook synchronization.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [Technology Stack](#technology-stack)
- [System Architecture](#system-architecture)
- [Repository Structure](#repository-structure)
- [Frontend Architecture](#frontend-architecture)
- [Backend Architecture](#backend-architecture)
- [Authentication Architecture](#authentication-architecture)
- [Database Architecture](#database-architecture)
- [Course Data Architecture](#course-data-architecture)
- [Routing](#routing)
- [Complete Application Workflow](#complete-application-workflow)
- [Student Workflow](#student-workflow)
- [Educator Workflow](#educator-workflow)
- [Course Player Workflow](#course-player-workflow)
- [State Management](#state-management)
- [API Architecture](#api-architecture)
- [Deployment Architecture](#deployment-architecture)
- [Environment Variables](#environment-variables)
- [Local Development Setup](#local-development-setup)
- [Available Scripts](#available-scripts)
- [Security](#security)
- [Current Implementation Status](#current-implementation-status)
- [Future Enhancements](#future-enhancements)
- [Project Highlights](#project-highlights)
- [Author](#author)

---

## Overview

EduNest is designed as a modern online learning platform with two primary user experiences:

1. **Student Experience**
   - Browse available courses
   - Search/filter courses
   - Open detailed course pages
   - View course content
   - Access enrolled courses
   - Watch lectures through the course player
   - View ratings and course information

2. **Educator Experience**
   - Access an educator dashboard
   - View course-related statistics
   - Add/create course content
   - Manage existing courses
   - View enrolled students

The application follows a layered architecture:

```
Presentation Layer
       ↓
React Pages + Components
       ↓
Global Application Context
       ↓
Authentication / API Layer
       ↓
Express Backend
       ↓
MongoDB
```

Clerk handles authentication and identity management, while the backend synchronizes authenticated users into MongoDB using Clerk webhooks.

---

## Key Features

### Student Features

- Responsive LMS homepage
- Course listing and search
- Course detail pages
- Course curriculum display
- Course enrollment view
- My Enrollments page
- Lecture/video player
- YouTube-based lecture playback
- Course ratings
- Course duration and lecture calculations
- Authentication using Clerk

### Educator Features

- Dedicated educator dashboard
- Educator navigation/sidebar
- Add Course interface
- My Courses interface
- Enrolled Students interface
- Educator role management through backend API
- Dashboard statistics and course-management UI

### Platform Features

- React-based single-page application
- Client-side routing with React Router
- Global state through React Context
- Tailwind CSS responsive styling
- Clerk authentication
- Clerk webhook synchronization
- MongoDB user persistence
- Express REST API foundation
- Vercel deployment configuration
- Modular frontend component structure

---

# Technology Stack

| Layer | Technology |
|---|---|
| Frontend | React 19 |
| Build Tool | Vite |
| Styling | Tailwind CSS |
| Routing | React Router |
| Authentication | Clerk |
| Backend | Node.js + Express |
| Database | MongoDB |
| Webhook Verification | Svix |
| Video | YouTube + react-youtube |
| Rich Text | Quill |
| Progress UI | rc-progress |
| Duration Formatting | humanize-duration |
| Ratings | react-simple-star-rating |
| IDs | uniqid |
| Deployment | Vercel |
| Language | JavaScript |

---

# System Architecture

## High-Level Architecture

```
                         ┌──────────────────────────┐
                         │        User/Browser      │
                         └────────────┬─────────────┘
                                      │
                                      ▼
                         ┌──────────────────────────┐
                         │       React Frontend     │
                         │      Vite + Tailwind     │
                         └────────────┬─────────────┘
                                      │
                 ┌────────────────────┴────────────────────┐
                 │                                         │
                 ▼                                         ▼
       ┌───────────────────┐                    ┌───────────────────┐
       │ Clerk Frontend SDK │                    │ AppContext / UI   │
       │ Authentication     │                    │ Application State │
       └─────────┬─────────┘                    └─────────┬─────────┘
                 │                                        │
                 └────────────────┬───────────────────────┘
                                  │
                                  ▼
                         ┌──────────────────────┐
                         │   Express Backend    │
                         │       Node.js        │
                         └──────────┬───────────┘
                                    │
                  ┌─────────────────┴─────────────────┐
                  │                                   │
                  ▼                                   ▼
        ┌───────────────────┐               ┌────────────────────┐
        │      Clerk        │               │      MongoDB       │
        │ Auth + Webhooks   │               │  User Persistence  │
        └───────────────────┘               └────────────────────┘
```

---

# Repository Structure

```
EduNest/
│
├── client/
│   ├── public/
│   │
│   ├── src/
│   │   ├── assets/
│   │   │   └── assets.js
│   │   │
│   │   ├── components/
│   │   │   ├── educator/
│   │   │   │   ├── Footer.jsx
│   │   │   │   ├── Navbar.jsx
│   │   │   │   └── Sidebar.jsx
│   │   │   │
│   │   │   └── student/
│   │   │       ├── CallToAction.jsx
│   │   │       ├── Companies.jsx
│   │   │       ├── CourseCard.jsx
│   │   │       ├── CoursesSection.jsx
│   │   │       ├── Footer.jsx
│   │   │       ├── Hero.jsx
│   │   │       ├── Loading.jsx
│   │   │       ├── Navbar.jsx
│   │   │       ├── Rating.jsx
│   │   │       ├── SearchBar.jsx
│   │   │       └── TestimonialsSection.jsx
│   │   │
│   │   ├── context/
│   │   │   └── AppContext.jsx
│   │   │
│   │   ├── pages/
│   │   │   ├── educator/
│   │   │   │   ├── AddCourse.jsx
│   │   │   │   ├── Dashboard.jsx
│   │   │   │   ├── Educator.jsx
│   │   │   │   ├── MyCourses.jsx
│   │   │   │   └── StudentsEnrolled.jsx
│   │   │   │
│   │   │   └── student/
│   │   │       ├── CourseDetails.jsx
│   │   │       ├── CoursesList.jsx
│   │   │       ├── Home.jsx
│   │   │       ├── MyEnrollments.jsx
│   │   │       └── Player.jsx
│   │   │
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── index.css
│   │
│   ├── .env
│   ├── package.json
│   ├── vite.config.js
│   └── vercel.json
│
├── server/
│   ├── configs/
│   │   └── mongodb.js
│   │
│   ├── controllers/
│   │   ├── educatorController.js
│   │   └── webhooks.js
│   │
│   ├── models/
│   │   └── User.js
│   │
│   ├── routes/
│   │   └── educatorRoutes.js
│   │
│   ├── .env
│   ├── package.json
│   ├── server.js
│   └── vercel.json
│
└── README.md
```

---

# Frontend Architecture

The frontend is a Vite-powered React single-page application.

The main application layers are:

```
main.jsx
   │
   ▼
App.jsx
   │
   ├── Student Routes
   │     ├── Home
   │     ├── Courses List
   │     ├── Course Details
   │     ├── My Enrollments
   │     └── Player
   │
   └── Educator Routes
         ├── Dashboard
         ├── Add Course
         ├── My Courses
         └── Students Enrolled

             ↓

        AppContext.jsx

             ↓

        Application Data
```

## Entry Point

`client/src/main.jsx` initializes the React application and mounts it to the DOM.

## Application Router

`client/src/App.jsx` defines the main application routes and separates student and educator experiences.

---

# Frontend Component Architecture

## Student Components

Student-facing reusable components include:

- `Navbar`
- `Hero`
- `Companies`
- `CoursesSection`
- `CourseCard`
- `SearchBar`
- `Rating`
- `CallToAction`
- `TestimonialsSection`
- `Footer`
- `Loading`

These components keep the UI modular and allow course information to be displayed consistently across different pages.

## Educator Components

Educator pages use:

- `Navbar`
- `Sidebar`
- `Footer`

The educator layout provides a separate interface for course management and student-related operations.

---

# Global State Management

EduNest uses React Context through:

`client/src/context/AppContext.jsx`

The context provides shared application-level information such as:

- `allCourses`
- `isEducator`
- `enrolledCourses`
- `currency`

It also provides helper functions including:

- `calculateRating()`
- `calculateChapterTime()`
- `calculateCourseDuration()`
- `calculateNoOfLectures()`
- `fetchAllCourses()`
- `fetchUserEnrolledCourses()`

This avoids passing course and enrollment information through many levels of component props.

---

# Authentication Architecture

EduNest uses Clerk for authentication.

## Authentication Flow

```
User
 │
 ▼
Clerk Sign In / Sign Up
 │
 ▼
Authenticated Clerk Session
 │
 ▼
React Application
 │
 ├── Student Experience
 │
 └── Educator Experience
```

Clerk provides:

- User identity
- Authentication state
- Session management
- Frontend authentication components
- User lifecycle events

The frontend uses the Clerk React integration, while the backend uses Clerk middleware to identify authenticated requests.

---

# Clerk Webhook Architecture

The backend includes a webhook endpoint that synchronizes Clerk users with MongoDB.

## User Creation Flow

```
User creates Clerk account
          │
          ▼
       Clerk
          │
          │ user.created
          ▼
     Webhook Endpoint
          │
          ▼
   Svix Signature Check
          │
          ▼
   Webhook Controller
          │
          ▼
      MongoDB User
```

## Supported User Events

The webhook controller handles the main lifecycle events:

- `user.created`
- `user.updated`
- `user.deleted`

This keeps the application's local user records synchronized with Clerk.

---

# Database Architecture

MongoDB is used as the backend database.

The MongoDB connection is configured in:

`server/configs/mongodb.js`

The application uses the database:

`lms`

The backend obtains the MongoDB connection string from:

`MONGODB_URI`

---

# User Data Model

The current User model contains the core information required to associate application users with LMS data.

Conceptually:

```
User
│
├── _id
├── name
├── email
├── imageUrl
├── enrolledCourses[]
├── createdAt
└── updatedAt
```

The `enrolledCourses` field provides the foundation for connecting users with course enrollment records.

---

# Course Data Architecture

The current course catalogue and several LMS demonstration records are represented in:

`client/src/assets/assets.js`

The course structure is hierarchical.

```
Course
│
├── Course Information
│   ├── title
│   ├── description
│   ├── price
│   ├── thumbnail
│   ├── discount
│   ├── published
│   └── educator
│
├── Students
│   └── enrolled students
│
├── Ratings
│   └── course ratings
│
└── Course Content
    │
    ├── Chapter 1
    │   ├── title
    │   ├── order
    │   └── lectures
    │       ├── title
    │       ├── duration
    │       ├── url
    │       ├── order
    │       └── isPreviewFree
    │
    ├── Chapter 2
    │   └── lectures
    │
    └── ...
```

This hierarchy allows the UI to calculate:

- Total course duration
- Number of lectures
- Chapter duration
- Course rating
- Course curriculum structure

---

# Routing

## Student Routes

| Route | Purpose |
|---|---|
| `/` | Homepage |
| `/course-list` | Course catalogue |
| `/course-list/:input` | Course search/filter route |
| `/course/:id` | Course details |
| `/my-enrollments` | Student enrollments |
| `/player/:courseId` | Course player |
| `/loading/:path` | Loading state |

## Educator Routes

| Route | Purpose |
|---|---|
| `/educator` | Educator dashboard |
| `/educator/add-course` | Add course |
| `/educator/my-courses` | Manage courses |
| `/educator/student-enrolled` | View enrolled students |

---

# Complete Application Workflow

## 1. Application Startup

```
Browser opens application
        ↓
Vite serves React application
        ↓
main.jsx initializes React
        ↓
App.jsx loads routes
        ↓
AppContext initializes global state
        ↓
Clerk checks authentication state
        ↓
Application becomes interactive
```

## 2. Course Discovery

```
Student
  ↓
Home Page
  ↓
Course Section
  ↓
Course Card
  ↓
Course Details
```

The course card provides a summary, while the course details page provides deeper information about the selected course and curriculum.

## 3. Enrollment

The student selects a course and accesses the enrollment-related experience. Current course/enrollment records are primarily supplied by the frontend data layer, while the backend contains the user persistence foundation required for a more complete production enrollment system.

## 4. Learning

```
My Enrollments
      ↓
Select Course
      ↓
Player
      ↓
Select Chapter
      ↓
Select Lecture
      ↓
YouTube Video
```

---

# Student Workflow

## Step 1 — Open Platform

The user opens the EduNest homepage.

## Step 2 — Authentication

The user can authenticate through Clerk.

## Step 3 — Browse Courses

The student navigates to:

`/course-list`

The course catalogue displays available courses using reusable `CourseCard` components.

## Step 4 — Search

A search input can route the user to:

`/course-list/:input`

## Step 5 — Course Details

The student opens:

`/course/:id`

The course detail view displays course metadata and curriculum information.

## Step 6 — Enrolled Courses

The student accesses:

`/my-enrollments`

This page presents courses associated with the current learner.

## Step 7 — Start Learning

The student opens:

`/player/:courseId`

The player presents chapters and lectures.

---

# Educator Workflow

Educators have a separate application area.

```
Educator
   ↓
/educator
   ↓
Dashboard
   ├── Statistics
   ├── Course Information
   └── Enrollment Information
        │
        ├── Add Course
        ├── My Courses
        └── Students Enrolled
```

## Dashboard

Route:

`/educator`

The dashboard acts as the main educator landing page.

## Add Course

Route:

`/educator/add-course`

This provides the course creation interface.

The frontend includes Quill for rich-text editing.

## My Courses

Route:

`/educator/my-courses`

This area is intended for course management.

## Students Enrolled

Route:

`/educator/student-enrolled`

This area provides enrolled-student information.

---

# Course Player Workflow

The course player receives a course identifier:

`/player/:courseId`

The application identifies the corresponding course and renders its content hierarchy.

```
Course ID
   ↓
Find Course
   ↓
Load Chapters
   ↓
Load Lectures
   ↓
Select Lecture
   ↓
Lecture URL
   ↓
react-youtube
   ↓
Video Playback
```

Lectures use YouTube URLs and the `react-youtube` package to embed video playback.

---

# Course Calculation Utilities

The frontend includes utility logic for deriving useful course statistics.

## Course Rating

Ratings can be aggregated to calculate the overall course rating displayed by the interface.

## Chapter Duration

Lecture durations are combined to determine the total time associated with a chapter.

```
Chapter Duration
=
Σ Lecture Duration
```

## Course Duration

Course duration is calculated from the durations of its chapters/lectures.

```
Course Duration
=
Σ Chapter Durations
```

## Number of Lectures

The application traverses the course content hierarchy and counts the available lectures.

```
Total Lectures
=
Σ Lectures in Every Chapter
```

These calculated values are used throughout course cards, course details, dashboards, and player-related UI.

---

# Backend Architecture

The backend is an Express.js application.

Main entry point:

`server/server.js`

Architecture:

```
HTTP Request
     ↓
Express
     ↓
CORS / Clerk Middleware
     ↓
Routes
     ↓
Controllers
     ↓
MongoDB
```

---

# Backend Components

## server.js

Responsibilities include:

- Creating the Express application
- Enabling JSON request handling
- Configuring CORS
- Initializing Clerk middleware
- Connecting to MongoDB
- Registering API routes
- Registering webhook routes
- Starting the server

The default development port is:

`5000`

## MongoDB Configuration

File:

`server/configs/mongodb.js`

This module establishes the MongoDB connection.

## User Model

File:

`server/models/User.js`

This defines the application's local user representation.

## Educator Controller

File:

`server/controllers/educatorController.js`

Contains educator-related backend controller logic, including educator role updates.

## Webhook Controller

File:

`server/controllers/webhooks.js`

Processes Clerk webhook events and synchronizes users.

---

# API Architecture

The current backend exposes a small but important API foundation.

## Health Check

```
GET /
```

Response:

```
API Working
```

This provides a simple way to verify that the backend is running.

## Clerk Webhook

```
POST /clerk
```

Purpose:

- Receive Clerk events
- Verify/process webhook events
- Synchronize users with MongoDB

## Educator Role Update

```
POST /api/educator/update-role
```

Purpose:

- Update a user's educator role through the backend controller flow.

The exact request authorization and role logic should be extended as the application moves toward a fully productionized LMS backend.

---

# Data Flow

## User Synchronization

```
Clerk
  │
  │ user.created / updated / deleted
  ▼
POST /clerk
  │
  ▼
Webhook Controller
  │
  ▼
MongoDB
  │
  ▼
Local User Record
```

## Frontend Course Flow

```
assets.js
   ↓
AppContext
   ↓
React Pages
   ↓
Reusable Components
   ↓
Rendered Course UI
```

## Backend User Flow

```
Authenticated User
      ↓
Clerk
      ↓
Clerk Webhook
      ↓
Express
      ↓
MongoDB
      ↓
User Record
```

---

# Deployment Architecture

EduNest includes Vercel configuration for both frontend and backend deployment.

## Frontend

The Vite frontend can be built using:

```bash
npm run build
```

The production output can then be deployed to Vercel or another static/frontend hosting platform.

Client-side routing requires the configured rewrite so routes such as:

```
/course/123
/educator
/player/course-id
```

continue to resolve correctly when directly opened.

## Backend

The backend contains:

`server/vercel.json`

which provides deployment configuration for the Express/Node backend on Vercel.

---

# Environment Variables

## Frontend

Create:

`client/.env`

with:

```env
VITE_CLERK_PUBLISHABLE_KEY=your_clerk_publishable_key
VITE_CURRENCY=your_currency_symbol
```

Example:

```env
VITE_CLERK_PUBLISHABLE_KEY=pk_test_xxxxxxxxx
VITE_CURRENCY=$
```

## Backend

Create:

`server/.env`

with:

```env
MONGODB_URI=your_mongodb_connection_string
CLERK_WEBHOOK_SECRET=your_clerk_webhook_signing_secret
PORT=5000
```

### Important

Never commit real:

- API keys
- Database credentials
- Clerk secrets
- Webhook secrets
- Payment secrets

to GitHub.

---

# Local Development Setup

## 1. Clone the Repository

```bash
git clone https://github.com/anurag3401/EduNest.git
cd EduNest
```

## 2. Install Frontend Dependencies

```bash
cd client
npm install
```

## 3. Configure Frontend Environment Variables

Create:

```
client/.env
```

and add the required frontend variables.

## 4. Start Frontend

```bash
npm run dev
```

Vite will provide the local development URL.

## 5. Install Backend Dependencies

Open another terminal:

```bash
cd server
npm install
```

## 6. Configure Backend Environment Variables

Create:

```
server/.env
```

and add:

```env
MONGODB_URI=...
CLERK_WEBHOOK_SECRET=...
PORT=5000
```

## 7. Start Backend

```bash
npm run server
```

The backend runs on the configured port, normally:

```
http://localhost:5000
```

---

# Available Scripts

## Client

### Development

```bash
npm run dev
```

Starts the Vite development server.

### Production Build

```bash
npm run build
```

Creates an optimized production build.

### Preview

```bash
npm run preview
```

Serves the production build locally for testing.

### Lint

```bash
npm run lint
```

Runs ESLint checks.

## Server

```bash
npm run server
```

Starts the backend development server according to the server package configuration.

---

# Security

EduNest uses several security-oriented mechanisms.

## Clerk Authentication

Authentication is delegated to Clerk instead of implementing password handling manually.

## Webhook Verification

Clerk webhook events use a signing secret so the backend can verify that incoming events originate from the configured webhook source.

## Environment Variables

Sensitive configuration is kept outside source code using environment variables.

## CORS

The Express backend configures CORS so frontend requests can communicate with the API while allowing the deployment configuration to control permitted origins.

## Recommended Production Practices

For production expansion, add:

- Strict origin allowlists
- Role-based authorization middleware
- Server-side course ownership validation
- Server-side enrollment authorization
- Input validation
- Rate limiting
- Secure HTTP headers
- Audit logging
- Stronger API error handling
- Database indexes
- Secrets management

---

# Current Implementation Status

EduNest currently has a hybrid architecture.

## Implemented / Present

- React + Vite frontend
- Tailwind CSS UI
- Student pages
- Educator pages
- React Router navigation
- Global React Context
- Clerk authentication integration
- Clerk webhook handling
- MongoDB user model
- Express backend
- Educator role endpoint
- Course/player UI
- YouTube lecture integration
- Vercel deployment configuration

## Currently Data-Driven from Frontend Assets

The repository currently contains substantial LMS demonstration data in:

`client/src/assets/assets.js`

including:

- Course catalogue data
- Course content
- Enrollment-related data
- Dashboard data
- Student data
- Ratings
- Progress-related information

Therefore, the current architecture should be understood as a frontend-rich LMS prototype/application with a backend foundation rather than a completely database-driven production LMS.

---

# Future Enhancements

The architecture can be extended into a complete production LMS by moving the remaining application data and business logic to the backend.

## 1. Course Database

Create MongoDB collections/models for:

```
Course
Chapter
Lecture
Category
Rating
```

## 2. Enrollment System

Move enrollment creation and retrieval to secure backend endpoints.

```
Student
   ↓
Enroll
   ↓
Backend API
   ↓
MongoDB
   ↓
Enrollment Record
```

## 3. Payment Integration

The backend package includes Stripe-related dependencies, providing a foundation for payment integration.

A production flow could be:

```
Student
  ↓
Checkout
  ↓
Stripe
  ↓
Payment Success
  ↓
Backend Verification
  ↓
Enrollment Created
  ↓
Course Access
```

Payment verification should always happen server-side.

## 4. Cloud Media Storage

Cloudinary-related dependencies are present in the backend package, providing a foundation for future image/file upload integration.

A production media flow could be:

```
Educator
   ↓
Upload Thumbnail / Media
   ↓
Backend
   ↓
Cloud Storage
   ↓
Secure Media URL
   ↓
Course
```

## 5. Role-Based Access Control

Implement server-side roles such as:

```
Student
Educator
Admin
```

and enforce authorization on every protected backend operation.

## 6. Progress Tracking

Store:

- Completed lectures
- Course progress
- Last watched lecture
- Completion percentage
- Course completion status

## 7. Production Course Management

Move course creation from frontend/local data into:

```
Educator UI
   ↓
REST API
   ↓
Validation
   ↓
MongoDB
   ↓
Published Course
   ↓
Student Catalogue
```

## 8. Search and Filtering

Implement backend search with:

- Course title
- Category
- Educator
- Price
- Rating
- Difficulty
- Tags

## 9. Reviews and Ratings

Persist student ratings and reviews in MongoDB and calculate aggregate course ratings server-side.

---

# Suggested Production Architecture

After implementing the planned backend features, the architecture could evolve into:

```
                         ┌──────────────────┐
                         │      Client      │
                         │ React + Vite     │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │  Authentication  │
                         │      Clerk       │
                         └────────┬─────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │   Express API    │
                         │ Node.js Backend  │
                         └────────┬─────────┘
                                  │
          ┌───────────────────────┼───────────────────────┐
          │                       │                       │
          ▼                       ▼                       ▼
   ┌──────────────┐       ┌──────────────┐       ┌──────────────┐
   │   MongoDB    │       │    Stripe    │       │  Cloudinary  │
   │ Application  │       │   Payments   │       │    Media     │
   │     Data     │       │              │       │   Storage    │
   └──────────────┘       └──────────────┘       └──────────────┘
                                  │
                                  ▼
                         ┌──────────────────┐
                         │     Students     │
                         │   Enrollment +   │
                         │ Learning Data    │
                         └──────────────────┘
```

---

# Why This Architecture?

## Separation of Concerns

The frontend is responsible for:

- UI
- Navigation
- User interaction
- Presentation
- Client-side state

The backend is responsible for:

- Business logic
- Authentication verification
- Authorization
- Data persistence
- Webhook processing
- Secure operations

MongoDB is responsible for persistent application data.

Clerk is responsible for identity and authentication.

This separation makes the application easier to maintain and scale.

---

# Component-to-Feature Mapping

| Feature | Main Files |
|---|---|
| Homepage | `Home.jsx`, student components |
| Course Catalogue | `CoursesList.jsx`, `CourseCard.jsx` |
| Course Search | `SearchBar.jsx`, routing |
| Course Details | `CourseDetails.jsx` |
| Enrollments | `MyEnrollments.jsx` |
| Course Player | `Player.jsx` |
| Educator Dashboard | `Dashboard.jsx` |
| Course Creation | `AddCourse.jsx` |
| Course Management | `MyCourses.jsx` |
| Student Management | `StudentsEnrolled.jsx` |
| Global State | `AppContext.jsx` |
| Authentication | Clerk integration |
| User Sync | `webhooks.js` |
| User Database | `User.js` |
| API Routes | `educatorRoutes.js` |

---

# Project Workflow at a Glance

```
                    ┌─────────────┐
                    │    USER     │
                    └──────┬──────┘
                           │
                    ┌──────▼──────┐
                    │    CLERK    │
                    │     AUTH     │
                    └──────┬──────┘
                           │
              ┌────────────┴────────────┐
              │                         │
       ┌──────▼──────┐           ┌──────▼──────┐
       │   STUDENT   │           │  EDUCATOR   │
       └──────┬──────┘           └──────┬──────┘
              │                         │
       Browse Courses             Dashboard
              │                         │
       Course Details             Add Course
              │                         │
       My Enrollments             My Courses
              │                         │
           Player                Students
              │
       Watch Lectures
              │
              ▼
       Learning Experience

                    BACKEND
                       │
              ┌────────▼────────┐
              │ Express + Clerk │
              └────────┬────────┘
                       │
              ┌────────▼────────┐
              │    MongoDB      │
              │ User Persistence│
              └─────────────────┘
```

---

# Project Highlights

- Full-stack LMS architecture
- Separate student and educator experiences
- Modern React component architecture
- Responsive Tailwind CSS interface
- Clerk-based authentication
- MongoDB persistence foundation
- Clerk-to-MongoDB webhook synchronization
- Course hierarchy with chapters and lectures
- YouTube-based course player
- Global state management using React Context
- Vercel-ready deployment configuration
- Clear path toward a production-ready LMS backend

---

# Author

**Anurag Prasad**

GitHub: [anurag3401](https://github.com/anurag3401)

---

# Conclusion

EduNest is structured as a modern full-stack Learning Management System with a React/Vite frontend, Express backend, Clerk authentication, and MongoDB persistence.

The current implementation provides the complete LMS user-interface flow for students and educators while establishing the backend foundation for authentication, user synchronization, and role management. The architecture is intentionally modular, making it straightforward to evolve the current local course-data layer into a fully database-driven system with persistent courses, enrollments, payments, media storage, progress tracking, reviews, and production-grade authorization.

The overall design follows a clear separation:

```
React UI
   ↓
Application State
   ↓
Express API
   ↓
Business Logic
   ↓
MongoDB / External Services
```

This makes EduNest suitable as a foundation for a scalable online learning platform.

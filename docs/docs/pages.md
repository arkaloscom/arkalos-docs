---
title: React Router Pages for Python FastAPI
description: How to use React Router 7 and Python FastAPI backend integration with uv and Arkalos.
author: Mev-Rael
---

# React Router Pages for Python FastAPI

Arkalos comes with a ready-to-use frontend built with React and React Router 7, designed to work seamlessly with your FastAPI backend. This guide explains how to set up, structure, and extend your frontend pages.



## Requirements

To use the default frontend:

Make sure you have the latest version of **Node.js** installed.

Change into the `frontend` directory and install the dependencies:

```bash
cd frontend
npm install
```

To run the development server:

```bash
npm run dev
```

To build static files for production:

```bash
npm run build
```



## Frontend Routes

Frontend routes are registered in `frontend/app/app_routes.jsx` using React Router:

```jsx
<Route path="*" element={<_404NotFoundPage />} />
<Route path="/" element={<IndexPage />} />
<Route path="/dashboard" element={<DashboardPage />} />
<Route path="/chat" element={<AIChatPage />} />
<Route path="/logs" element={<LogsPage />} />
```

Each route maps a URL path to a specific **Page Component**.



## Pages

Pages are React components stored in the `frontend/app/pages/` directory. Each page typically:

* Uses a layout (e.g., `MainLayout`)
* Sets the document `<title>`
* Renders main content using standard React components
* Optionally fetches data from the backend using `fetch` and `FormData` APIs



## Layouts

Layouts are wrapper components that provide shared structure (e.g., navbar, footer). They live in `frontend/app/layouts/`.

Arkalos includes a default layout:

```jsx
import MainLayout from '@/layouts/MainLayout'
```

You can create additional layouts here for different sections or contexts of your app.




## Creating a New Page

Let’s walk through adding a simple "About" page.

### 1. Create the Page Component

Create a new `frontend/app/pages/AboutPage.jsx` file:

```jsx title="frontend/app/pages/AboutPage.jsx"
import MainLayout from '@/layouts/MainLayout'

export default function AboutPage() {
  return (
    <MainLayout title="About">
      <h1>About Page</h1>
      <section>
        <p>Hi from my About page 👋</p>
      </section>
    </MainLayout>
  )
}
```

### 2. Register the Route

In `frontend/app/app_routes.jsx`, import and add the new route:

```jsx title="frontend/app/app_routes.jsx"
import AboutPage from '@/pages/AboutPage'

<Route path="/about" element={<AboutPage />} />
```

### 3. Add to the Navigation

In `frontend/app/components/Nav.jsx`:

```jsx title="frontend/app/components/Nav.jsx"
import { NavLink } from 'react-router-dom'

<NavLink to="/about">About</NavLink>
```

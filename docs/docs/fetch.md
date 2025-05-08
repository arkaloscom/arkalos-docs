---
title: Fetching API Data in React Router Pages From Python FastAPI Endpoints
description: How to use fetch() with backend Python FastAPI into your React Router 7 pages.
author: Mev-Rael
---

# Fetching API Data in React Router Pages From Python FastAPI Endpoints

Once your FastAPI backend endpoints are set up, you can fetch and display that data inside your frontend pages using Arkalos components and hooks.



## Using the `<DataLoader>` Component

The `<DataLoader>` component simplifies data fetching during initial page rendering. It automatically handles loading states, error handling, and animations.

### Steps to Use `<DataLoader>`:

1. Create a local function like `render(data)` that will receive and render the fetched data.
2. Specify the backend API endpoint URL (no need to prefix it with `/api`—it's added automatically).
3. Pass the `render` function to the `fn` prop of `<DataLoader>`.
4. Optionally, pass query parameters or payload data via the `data` prop.

### Example: Basic Data Fetch

Assume your backend provides data from `/api/reports`. Here's how to fetch and render that in the About page:

```jsx title="frontend/app/pages/AboutPage.jsx"
import MainLayout from '@/layouts/MainLayout'
import DataLoader from '@/components/DataLoader'

export default function AboutPage() {

  function render(data) {
    console.log('Data fetched: ', data)
    return (
      <ul>
        {data.map((item, idx) => (
          <li key={idx}>{item.title}</li>
        ))}
      </ul>
    )
  }

  return (
    <MainLayout title="About">
      <h1>About Page</h1>
      <section>
        <DataLoader url="/reports" fn={render} />
      </section>
    </MainLayout>
  )
}
```

### Example: Fetching Filtered Data

To pass query parameters (e.g. `/api/reports?origin=japan`):

```jsx
<DataLoader url="/reports" data={{ origin: 'japan' }} fn={render} />
```

## Using the "useFetchApi" Hook

`<DataLoader>` is powered by a custom `useFetchApi` hook that you can use directly for more control.

```jsx
import { useFetchApi } from '@/hooks/useFetchApi'

// Inside your component:
const [data, error, isLoading] = useFetchApi.get('/reports', { origin: 'japan' })
```

This gives you fine-grained access to the loading and error states.



## Using the "Api" Service for Manual Calls & POST FormData

If you prefer fully manual control (e.g. inside event handlers or `useEffect`), you can use the `Api` service directly.

```jsx
import Api from '@/services/Api'

// Example: GET request
const [data, error] = Api.get('/reports')

// Example: POST request with FormData
async function handleFormSubmit(form) {
  const formData = new FormData(form)
  const [data, error] = await Api.post('/something/create', formData)

  if (error) {
    console.error('API Error:', error)
  } else {
    console.log('Response:', data)
  }
}
```

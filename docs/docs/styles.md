---
title: Styling React Router Frontend for Python FastAPI Backend with CSS
description: How to organize and style React frontend UI using vanilla CSS in Python FastAPI projects.
author: Mev-Rael
---

# Styling React Router Frontend for Python FastAPI Backend with CSS

Arkalos uses **vanilla CSS** for full control over styling while keeping the setup simple and lightweight. All styles live in the `frontend/app/css/` directory and are bundled together via a central `_app.css` file.



## Main Entry Point: "_app.css"

The file `frontend/app/css/_app.css` acts as the main entry point. It does not contain direct styles but instead assembles all your CSS modules:

```css title="frontend/app/css/_app.css"
@import "_vars";
@import "_core";

@import "link";
@import "form";
@import "button";
@import "label";
@import "loader";
@import "nav";
@import "table";
@import "pagination";
@import "card";
@import "grid";
@import "chart";
@import "metric";

@import "index";

@import "chat/chat";
@import "chat/message";
@import "chat/markdown";
```

> [!NOTE] 
> Always import `_vars.css` and `_core.css` first, followed by component styles and custom page styles last.



## Defining CSS Variables

Use `frontend/app/css/_vars.css` to define or override global CSS variables:

```css title="frontend/app/css/_vars.css"
:root {
  --base-font-size: 16px;
  --base-font-color: #f1f1f1;
}
```

These variables are then used across all stylesheets:

```css
.my-button {
  color: var(--base-font-color);
}
```



## Global Layout and Typography

The `frontend/app/css/_core.css` file handles:

* Reset styles (normalize)
* Global typography
* HTML and body layout

Example:

```css title="frontend/app/css/_core.css"
html,
body {
  background-color: var(--base-bg-color);
}
```


## Component-Specific CSS

Each UI component or page section typically has its own CSS file. For example:

* `form.css` — styles for form elements
* `nav.css` — styles for the navigation bar
* `card.css` — styles for content cards
* `chat/message.css` — styles specific to chat messages

This separation helps you keep styles clean and modular.


## Adding New Styles

When creating a new component or page:

1. **Create a new CSS file** inside `frontend/app/css/`, e.g. `my-widget.css`
2. Define your styles:

```css title="my-widget.css"
.my-widget {
  padding: 1rem;
  border: 1px solid #ccc;
  background: #fff;
}
```

3. **Import the new file** at the end of `_app.css`:

```css title="_app.css"
@import "my-widget";
```

4. Then use it in your component:

```jsx
export default function MyWidget() {
  return <div className="my-widget">Hello!</div>
}
```

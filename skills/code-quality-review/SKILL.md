---
name: code-quality-review
description:
  This is not a bug report or code review. Rather a code quality review, targeted towards
  readability, maintainability, and best practices. This is a highly opiniated skill
disable-model-invocation: true
---

# Code Quality Review

Make the requested code easier to maintain and read. Preserve the input, output behavior,
side effects, ordering, performance constraints, and error behavior.

## File structure

1. File structure should follow the below structure

```
/root
    /src
        /app
            /index.tsx
        /util
            /index.ts
            /utitity1.ts
            /utility2.ts
        /components
            /component1.tsx
            /component2.tsx
            /ui
                /ui-component1.tsx
                /ui-component2.tsx
        /hooks
            /hook1.ts
            /hook2.ts
        /stores
        /styles
    agent.md
    formatter.config.js
    framework.config.js
    readme.md
```

You can directly place index.ts/tsx inside of src if its not a nextjs app router. For
example

```
    /src
        /index.ts
```

2. In a nextjs or tanstack app, follow the nextjs app router + src convention. In
   nextjs/react app, do not put all your components in `/components` folder. Instead of
   first build the page.

   - `page.tsx` should contain the most amount of content and markup, do data fetching (if
     multiple comps consume the same data)
   - `layout.tsx` should contain the skeleton of the page. Such as sidebar layout, header,
     nav bar, footer etc. It should not contain any business logic or data fetching. It
     may perform some auth to redirect users ie redirect unauthorized users who try to
     access pages under this layout eg dashboards, admin pages etc
   - Then take out large, complex components out and place them in their own component.tsx
     file. If the component is reused across multiple pages, then place it in the
     /components folder otherwise put it in the same folder as the page

```
/src
    /app
        /page1
            /component1.tsx
            /page.tsx
            /layout.tsx
        /admin-profile
            /admin-sidebar.tsx
            /component2.tsx
            /page.tsx
            /layout.tsx
        /user-profile
            /page.tsx
        page.tsx
        layout.tsx
        app-sidebar.tsx
    /components
        /profile
            /profile-component.tsx
        /ui/(strictly ui/primitive components)
        /shared-component.tsx

```

3. File names should be in kebab-case. For example `user-profile.tsx`,
   `admin-sidebar.tsx`. If it's a nextjs app, add `.server` and `.client` suffix at the
   end. Doesnt apply to comps under `/components` or `/ui`

## Naming conventions

1. Use camelCase for variables and functions. For example `userProfile`, `getUserData()`.
2. Use PascalCase for component names and class names. For example `UserProfile`,
   `UserCard`.
3. Use `is` or `has` prefix for boolean variables. For example `isLoggedIn`,
   `hasPermission`.
4. Use `on` prefix for event handler functions. For example `onClick`, `onSubmit`.
5. Use `get` prefix for functions that return a value or fetches data. For example
   `getUserData()`, `getProfile()`.
6. Names should reduce the reader's mental work


# Deploying a Vite + React App to `Github Pages`

This **README.md** describes the **entire** process of building and publishing a **Vite + React** application to **GitHub Pages** as a **user site**. You’ll also find details on **common mistakes** made during the process and how to solve them.

---

## 1. Introduction

A **user site** on GitHub Pages is served from a repository named **`<username>.github.io`**. In this guide, we’re using `yourName.github.io` as the target repository, which will be accessible at:
```
https://yourName.github.io/
```

For **user sites**, GitHub only supports publishing via:
1. The **`main`** (or `master`) branch **root**, or
2. A **`/docs`** folder on the **`main`** (or `master`) branch.

> **Note**: The typical `gh-pages` branch workflow is for **project sites** (where you’d have a URL like `https://yourName.github.io/my-project`), not user sites.

---

## 2. Repository Setup

1. **Create/Rename the repo** to `yourName.github.io`.
   - If you already have code elsewhere, you can rename that repo or create a fresh repo.  
2. Clone it locally:
   ```bash
   git clone https://github.com/yourName/yourName.github.io.git
   ```
3. Place your **Vite + React** project files in that cloned folder (if they aren’t already).

Your final directory might look like this:

```
yourName.github.io/
├─ index.html
├─ src/
│  ├─ main.jsx
│  └─ App.jsx
├─ vite.config.js
├─ package.json
└─ README.md  (this file)
```

---

## 3. Vite + React Basics

A **standard** Vite + React setup:

1. **`index.html`** (root level) should have:
   ```html
   <!DOCTYPE html>
   <html lang="en">
     <head>
       <meta charset="UTF-8" />
       <title>My React App</title>
     </head>
     <body>
       <div id="root"></div>
       <!-- The critical part: Vite needs a module script referencing your React entry -->
       <script type="module" src="/src/main.jsx"></script>
     </body>
   </html>
   ```
2. **`src/main.jsx`** (entry point):
   ```jsx
   import React from 'react'
   import ReactDOM from 'react-dom/client'
   import App from './App'

   ReactDOM.createRoot(document.getElementById('root')).render(
     <React.StrictMode>
       <App />
     </React.StrictMode>
   )
   ```
3. **`src/App.jsx`** (example):
   ```jsx
   export default function App() {
     return <h1>Hello from Vite + React!</h1>
   }
   ```
4. **`package.json`** with scripts:
   ```json
   {
     "scripts": {
       "dev": "vite",
       "build": "vite build",
       "preview": "vite preview"
     },
     "dependencies": {
       "react": "^18.x",
       "react-dom": "^18.x"
     },
     "devDependencies": {
       "vite": "^4.x",
       "@vitejs/plugin-react": "^4.x"
     }
   }
   ```

Install everything:

```bash
npm install
```

---

## 4. Configure `vite.config.js`

Create or edit **`vite.config.js`** to ensure we have:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  base: '/',             // Since this is a user site at the root URL
  build: {
    outDir: 'docs'       // Place final build output into a 'docs' folder
  }
})
```

- **`base: '/'`** is crucial so your app’s JavaScript assets load correctly from `https://yourName.github.io/`.
- **`outDir: 'docs'`** ensures the production build is placed in a folder named `docs` at the repo root, making it easy for GitHub Pages to serve.

---

## 5. Building the Project

Run:

```bash
npm run build
```

Upon success, you should see a **`docs`** folder appear:

```
docs/
├─ index.html
└─ assets/
    ├─ index-xxxx.js
    └─ index-xxxx.css (if needed)
```

### **If you only see `index.html` (no assets)**

- Check your **terminal** for errors.
- Ensure **`<script type="module" src="/src/main.jsx">`** is present in `index.html`.
- Verify you have **installed** `@vitejs/plugin-react` and added it to your Vite config.

---

## 6. Publishing to GitHub Pages

Since this is a **user site**:

1. **Commit and push** to the **`main`** branch:
   ```bash
   git add .
   git commit -m "Build for GitHub Pages"
   git push origin main
   ```
2. On GitHub, go to **Settings** → **Pages**:
   - **Source**: Select **“Deploy from a branch”**.
   - **Branch**: `main`.
   - **Folder**: `/docs`.
   - Click **Save**.

> GitHub may take up to a minute to deploy. Afterwards, a green message appears stating your site is published at **`https://yourName.github.io/`**.

---

## 7. Verifying Your Live Site

Open your browser to:

```
https://yourName.github.io/
```

You should see your React application in action.

### If You Only See Raw HTML or a Blank Page

- Check the **browser console** for 404 errors on JS files. This usually indicates a wrong `base` path or missing `assets` folder.
- Ensure you actually **pushed** the `docs` folder to GitHub. Look in your repo’s “Code” tab to confirm `docs/index.html` and `docs/assets/*` are present.
- Double-check your **Settings > Pages** config is set to `main` + `/docs`.

---

## 8. Common Mistakes & Their Solutions

1. **Incorrect Repo Name**  
   - **Mistake**: Named the repo something else, like `my-project` or `gh-pages`.  
   - **Solution**: For a user site, rename or create it as `yourName.github.io`.

2. **Missing React Plugin**  
   - **Mistake**: Not installing or not importing `@vitejs/plugin-react`.  
   - **Solution**: `npm install -D @vitejs/plugin-react` and add `react()` in `plugins`.

3. **No `<script>` in `index.html`**  
   - **Mistake**: Vite has no entry point to bundle.  
   - **Solution**: Add `<script type="module" src="/src/main.jsx"></script>` inside `index.html`.

4. **Forgetting to Commit the `docs/` Folder**  
   - **Mistake**: Built locally but never pushed it.  
   - **Solution**: Ensure `docs/` is **not** ignored, then `git add docs/` and commit.

5. **Wrong GitHub Pages Settings**  
   - **Mistake**: Setting the branch to `main` but forgetting to select `/docs`, or vice versa.  
   - **Solution**: Go to Settings > Pages, pick the correct branch + folder.

6. **Cache/Delay**  
   - **Mistake**: Immediately checking the site and finding it blank.  
   - **Solution**: Wait ~1 minute or do a hard refresh (Ctrl+F5) after you push.

---

## 9. Example Final Structure

Below is a typical final layout for your `yourName.github.io` repository:

```
yourName.github.io/
├─ index.html
├─ src/
│  ├─ main.jsx
│  └─ App.jsx
├─ docs/             <-- Generated after running "npm run build"
│  ├─ index.html
│  └─ assets/
│      ├─ index-xxxx.js
│      └─ index-xxxx.css
├─ vite.config.js
├─ package.json
└─ README.md         <-- This file
```

Once this is all pushed, GitHub Pages is set to `main` + `/docs`, and you can access your React app at:
```
https://yourName.github.io/
```

---

## 10. Final Checklist

1. **Repo Name**: `yourName.github.io` (user site requirement).  
2. **Vite Config**: `base: '/'`, `build.outDir = 'docs'`, plugin: `[react()]`.  
3. **index.html**: Has `<script type="module" src="/src/main.jsx">`.  
4. **Build**: `npm run build` → Confirm `docs` folder with `index.html + assets/`.  
5. **Git Push**: `git commit && git push origin main`.  
6. **GitHub Settings**: Pages → `main` → `/docs`.  
7. **Verify**: `https://yourName.github.io/` should show your React app.

---

## 11. Conclusion

By following these steps:

- You’ll have a **fully functional** Vite + React user site at `https://yourName.github.io/`.
- Common mistakes (missing React plugin, wrong base path, not pushing `docs`) will be avoided.
- Your site should automatically update whenever you rebuild (`npm run build`) and push to `main`.
- Also You need to link your <script> in `index.html` before running `npm run build`. If your file is not linked then you won't be able to see `/assets` folder in `/docs` folder.

**Enjoy your live site!**

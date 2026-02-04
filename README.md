# GitHub Pages Deployment Guide

Deploy your Digital Menu to GitHub Pages with Firebase authentication and cloud storage.

## What Works on GitHub Pages

| Feature | Status | Notes |
|---------|--------|-------|
| Responsive carousel | ✅ | Touch-enabled |
| Category navigation | ✅ | Food, Drink, Promo |
| Admin panel | ✅ | Full editing |
| Image URLs | ✅ | All external |
| User authentication | ✅ | Firebase Auth |
| Data storage | ✅ | Firestore |
| Password change | ✅ | Built-in |
| Offline support | ✅ | Local storage sync |
| Auto-deploy | ✅ | GitHub Actions |

## Prerequisites

1. **GitHub Account** - Free at [github.com](https://github.com)
2. **Firebase Project** - Free at [console.firebase.google.com](https://console.firebase.google.com)

---

## Step 1: Set Up Firebase

### 1.1 Create Firebase Project
1. Go to [Firebase Console](https://console.firebase.google.com)
2. Click "Add project" → Enter name (e.g., "digital-menu")
3. Disable Google Analytics → Click "Create project"

### 1.2 Enable Authentication
1. **Authentication** → **Sign-in method**
2. Click "Email/Password"
3. Enable "Email/Password"
4. Click "Save"

### 1.3 Create Admin User
1. **Authentication** → **Users**
2. Click "Add user"
3. Email: `admin@yourdomain.com`
4. Password: `your-password`
5. Click "Add user"

### 1.4 Enable Firestore Database
1. **Firestore Database** → **Create database**
2. Start in "Production mode"
3. Select location → Click "Enable"

### 1.5 Get Configuration
1. **Project Settings** (gear icon)
2. Scroll to "Your apps" → Click "</>" (Web)
3. App nickname: "digital-menu"
4. **Copy the `firebaseConfig` object**

---

## Step 2: Configure Your Site

### 2.1 Edit index.html
1. Open `deploy/github-pages/index.html`
2. Find the line with `const firebaseConfig = {`
3. Replace with your Firebase config:
   ```javascript
   const firebaseConfig = {
       apiKey: "AIzaSy...",
       authDomain: "my-menu.firebaseapp.com",
       projectId: "my-menu",
       storageBucket: "my-menu.appspot.com",
       messagingSenderId: "123456789",
       appId: "1:123456789:web:abc123"
   };
   ```

---

## Step 3: Create GitHub Repository

### 3.1 Create New Repository
1. Go to [GitHub](https://github.com) → Click "+" → "New repository"
2. Repository name: `digital-menu` (or your choice)
3. Description: "Digital Menu for my restaurant"
4. Public/Private: **Public** (required for free GitHub Pages)
5. Click "Create repository"

### 3.2 Upload Files
```bash
# Clone the repository (or create new folder)
git clone https://github.com/YOUR_USERNAME/digital-menu.git
cd digital-menu

# Copy files from deploy/github-pages
cp -r deploy/github-pages/* .
cp deploy/github-pages/.gitignore .

# Commit and push
git add .
git commit -m "Initial commit: Digital Menu"
git push origin main
```

### 3.3 Enable GitHub Pages
1. Go to your repository on GitHub
2. Click **Settings** → **Pages** (left sidebar)
3. Under "Source":
   - Branch: `main` (or `master`)
   - Folder: `/(root)`
4. Click "Save"
5. Wait 1-2 minutes for deployment

### 3.4 Get Your URL
- Your site will be at: `https://YOUR_USERNAME.github.io/digital-menu/`
- Or custom domain if configured

---

## Step 4: Test Your Deployment

### 4.1 Check Public View
1. Open your GitHub Pages URL
2. Verify:
   - [ ] Carousel images load
   - [ ] Category tabs work
   - [ ] Menu items display
   - [ ] Mobile responsive

### 4.2 Test Admin Login
1. Click the dots (⋮) in top-right
2. Enter your Firebase admin credentials
3. Verify:
   - [ ] Login succeeds
   - [ ] Admin panel opens
   - [ ] Can edit items
   - [ ] Save works

---

## Managing Your Menu

### Editing Items
1. Login with admin credentials
2. Click dots (⋮) → Admin Panel
3. Switch Food/Drink/Promo tabs
4. Add slides, edit items, change prices
5. Click "Save All Changes"

### Changing Password
1. Login as admin
2. Click "Change Password" button
3. Enter current and new password
4. Password updates in Firebase

### Offline Support
- Menu loads from localStorage when offline
- Changes sync to Firebase when back online
- Offline badge appears when disconnected

---

## Auto-Deploy (Optional)

With GitHub Actions, changes push automatically:

### Enable GitHub Actions
1. Repository → **Settings** → **Pages**
2. Under "Build and deployment":
   - Source: **GitHub Actions**
3. Commit the workflow file below

### Create Workflow
Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      
      - name: Setup Pages
        uses: actions/configure-pages@v4
      
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: '.'
      
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

---

## File Structure

```
digital-menu/
├── index.html          # Main application
├── 404.html           # Custom 404 page
├── .gitignore         # Git ignore rules
└── .github/
    └── workflows/
        └── deploy.yml # Auto-deploy workflow
```

---

## Custom Domain (Optional)

1. **Settings** → **Pages**
2. Enter your domain under "Custom domain"
3. Create DNS records:
   - Type: `A` → Points to: `185.199.108.153`
   - Type: `A` → Points to: `185.199.109.153`
   - Type: `A` → Points to: `185.199.110.153`
   - Type: `A` → Points to: `185.199.111.153`
   - Type: `CNAME` → Name: `www` → Value: `YOUR_USERNAME.github.io`

4. Enable "Enforce HTTPS" after DNS propagates

---

## Troubleshooting

### "Firebase not initialized"
- Check firebaseConfig is correct in index.html
- Ensure all values have quotes
- No trailing commas

### "Login not working"
- Verify user exists in Firebase Authentication
- Check email/password match
- Ensure Email/Password provider is enabled

### "Data not saving"
- Firestore must be in "Production mode"
- Check Firestore rules allow authenticated writes

### Images not loading
- Use public HTTPS image URLs
- Avoid local/relative paths
- Check URL accessibility

### GitHub Pages not updating
- Wait 2-5 minutes for rebuild
- Check Actions tab for errors
- Force refresh: Ctrl+F5

---

## Advantages of GitHub Pages

1. **100% Free** - No hosting costs
2. **Custom Domain** - Use your own domain
3. **SSL Included** - HTTPS automatic
4. **Version Control** - Full git history
5. **Auto-Deploy** - GitHub Actions
6. **Fast CDN** - Global edge network
7. **Reliable** - 99.99% uptime

---

## Limitations

1. **Static Only** - No server-side code
2. **Bandwidth** - 100GB/month soft limit
3. **Storage** - 500MB soft limit
4. **Build Time** - 10 minutes max per run

---

## Quick Reference

| Task | Location |
|------|----------|
| Edit menu | Admin panel (login required) |
| Add admin user | Firebase Console > Authentication |
| Change password | Admin panel > Change Password |
| View site | https://YOUR_USERNAME.github.io/REPO/ |
| Custom domain | Settings > Pages > Custom domain |
| Deployment logs | GitHub > Actions tab |

---

## Support

For issues:
1. Check browser console (F12)
2. Verify Firebase config
3. Check Firestore rules
4. Review GitHub Actions logs

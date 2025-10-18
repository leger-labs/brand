# Leger Labs Brand Kit

Design system with automated builds via GitHub Actions.

## Usage in app.leger.run

### 1. Add as submodule
```bash
git submodule add https://github.com/leger-labs/brand.git brand
git submodule update --init --recursive
```

### 2. GitHub Actions auto-updates submodule

Add to `.github/workflows/update-brand.yml`:
```yaml
name: Update Brand Submodule
on:
  schedule:
    - cron: '0 0 * * *'  # Daily
  workflow_dispatch:

jobs:
  update:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true
          token: ${{ secrets.GITHUB_TOKEN }}
      
      - name: Update submodule
        run: |
          git submodule update --remote --merge brand
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add brand
          git diff --staged --quiet || git commit -m "chore: update brand submodule"
          git push
```

### 3. Update your files

**tailwind.config.js:**
```javascript
const brandPreset = require('./brand/dist/tailwind.preset');

module.exports = {
  presets: [brandPreset],
  content: ['./src/**/*.{ts,tsx}'],
  plugins: [require('tailwindcss-animate')],
};
```

**src/index.css:**
```css
@import '../brand/dist/fonts.css';
@import '../brand/dist/tokens.css';

@tailwind base;
@tailwind components;
@tailwind utilities;

/* Your app-specific styles */
@layer components {
  .shadow-sm {
    box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.3);
  }
  .shadow {
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.3);
  }
  .shadow-md {
    box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.3);
  }
  .shadow-lg {
    box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.3);
  }
  
  .focus-visible\:ring-2:focus-visible {
    --tw-ring-color: hsl(var(--primary));
  }
}
```

### 4. Remove duplicates from app

Delete from your app:
- ❌ Color definitions in `tailwind.config.js` (comes from preset)
- ❌ Font definitions in `tailwind.config.js` (comes from preset)
- ❌ Typography scale in `tailwind.config.js` (comes from preset)
- ❌ CSS variables in `src/index.css` (comes from tokens.css)

Keep only:
- ✅ `tailwindcss-animate` plugin
- ✅ App-specific component styles
- ✅ Content paths

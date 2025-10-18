# Quick Command Reference

## 🚀 Deploy Now (Recommended)

```bash
# Install dependencies
npm install

# Build for production
npm run build

# Test locally (optional)
npm run preview

# Deploy the 'dist' folder to your hosting service
```

## 🗄️ Apply Database Migration (Optional - Can do later)

```bash
# Link to your Supabase project
supabase link --project-ref YOUR_PROJECT_REF

# Push the migration
supabase db push

# Verify it worked
supabase db diff
```

## 🧪 Testing Commands

```bash
# Run development server
npm run dev

# Build and check for errors
npm run build

# Preview production build
npm run preview
```

## 🔍 Debugging

```bash
# Check for TypeScript errors
npm run type-check

# Check build output
npm run build -- --debug

# View build stats
npm run build -- --stats
```

## 📊 Database Verification (After Migration)

Run these in Supabase SQL Editor:

```sql
-- Check if coins column exists
SELECT coins FROM profiles LIMIT 1;

-- Check constraints
SELECT conname, pg_get_constraintdef(oid) 
FROM pg_constraint 
WHERE conrelid = 'public.profiles'::regclass;

-- Check functions exist
SELECT routine_name 
FROM information_schema.routines 
WHERE routine_schema = 'public' 
AND routine_name LIKE '%coin%';

-- Check coin_transactions table
SELECT * FROM coin_transactions LIMIT 5;

-- Check RLS policies
SELECT * FROM pg_policies WHERE tablename = 'profiles';
```

## 🔄 Rollback Commands

```bash
# Revert last commit
git revert HEAD

# Revert specific files
git checkout HEAD~1 src/components/ui/BlingCurrency.tsx
git checkout HEAD~1 src/hooks/useProfile.ts

# Rebuild
npm run build
```

## 🧹 Clean Commands

```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install

# Clear build cache
rm -rf dist
npm run build

# Clear browser cache
# Press Ctrl+Shift+R (or Cmd+Shift+R on Mac)
```

## 📦 Environment Setup

Create `.env` file:
```env
VITE_SUPABASE_URL=your_supabase_url
VITE_SUPABASE_PUBLISHABLE_KEY=your_anon_key
```

## 🎯 Quick Deploy Checklist

```bash
# 1. Install
npm install

# 2. Build
npm run build

# 3. Test (optional)
npm run preview

# 4. Deploy
# Upload 'dist' folder to hosting

# 5. Apply migration (optional, can do later)
supabase db push
```

## 🆘 Emergency Commands

If something breaks:

```bash
# Clear everything and start fresh
rm -rf node_modules dist .next
npm install
npm run build

# Reset git to last working state
git reset --hard HEAD~1

# Check what changed
git diff HEAD~1
```

## 📱 Platform-Specific Deploy

### Netlify
```bash
npm run build
netlify deploy --prod --dir=dist
```

### Vercel
```bash
npm run build
vercel --prod
```

### GitHub Pages
```bash
npm run build
# Push dist folder to gh-pages branch
```

### Custom Server
```bash
npm run build
scp -r dist/* user@server:/var/www/html/
```

---

**That's it!** Just run `npm install && npm run build` and deploy the `dist` folder. Everything else is optional and can be done later. 🚀

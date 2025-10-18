# Deployment Checklist ✅

## Pre-Deployment

- [ ] All security vulnerabilities have been fixed
- [ ] Error boundaries are in place
- [ ] Fallback mechanisms are implemented
- [ ] Environment variables are set correctly

## Build & Deploy

### 1. Build the Application
```bash
npm install
npm run build
```

### 2. Test Build Locally (Optional)
```bash
npm run preview
```

### 3. Deploy to Your Hosting Service
- Upload the `dist` folder to your hosting service
- Or use your CI/CD pipeline

## Post-Deployment Testing

### Essential Tests:
- [ ] **App loads without blank page**
  - Visit your deployed URL
  - Should see the main menu

- [ ] **Authentication works**
  - Click "Sign In" button
  - Create account or log in
  - Should redirect to main menu

- [ ] **Coin display shows**
  - Top right corner should show "BLING" with coin count
  - Should show 100 coins for new users

- [ ] **Game plays correctly**
  - Click "Play" → "VS AI" → "Battle AI"
  - Play a game to completion
  - Should see winner modal

- [ ] **Cheat codes are removed**
  - Click on coin display
  - Should NOT show any input field or popover
  - Should be read-only display

### Optional Tests (After Migration):
- [ ] **AI victory rewards**
  - Win against AI
  - Should receive coins (10-50 based on difficulty)
  - Check browser console for success message

- [ ] **Crate purchases**
  - Click "Boards & Cones"
  - Try to purchase a crate
  - Should deduct coins and unlock item

## Database Migration (Optional - Can be done later)

### Apply Security Migration:
```bash
# Using Supabase CLI
supabase link --project-ref YOUR_PROJECT_REF
supabase db push

# OR manually in Supabase Dashboard
# 1. Go to SQL Editor
# 2. Copy contents of supabase/migrations/20251018000000_security_fixes.sql
# 3. Run the SQL
```

### Verify Migration:
```sql
-- Check coins column exists
SELECT coins FROM profiles LIMIT 1;

-- Check constraints exist
SELECT conname FROM pg_constraint 
WHERE conrelid = 'public.profiles'::regclass 
AND conname LIKE '%coins%';

-- Check new functions exist
SELECT routine_name FROM information_schema.routines 
WHERE routine_schema = 'public' 
AND routine_name IN ('add_coins_reward', 'purchase_with_coins');
```

## Troubleshooting

### Blank Page Issues:
1. **Check browser console** (F12) for errors
2. **Clear browser cache** (Ctrl+Shift+Delete)
3. **Check environment variables**:
   - `VITE_SUPABASE_URL` should be set
   - `VITE_SUPABASE_PUBLISHABLE_KEY` should be set
4. **Check network tab** for failed requests

### Migration Issues:
- App will work WITHOUT migration using fallback methods
- Apply migration when ready for full security
- Check console for "Migration Status" logs in development

### Common Errors:
- **"Function not found"** → Migration not applied (app will use fallback)
- **"Insufficient permissions"** → RLS policies need adjustment
- **"Column does not exist"** → Old database schema (migration needed)

## Success Indicators

✅ **App loads and displays main menu**  
✅ **No blank pages or crashes**  
✅ **Cheat codes are completely removed**  
✅ **Coin display is read-only**  
✅ **Games can be played to completion**  
✅ **Authentication works**  
✅ **No console errors (except type warnings)**  

## Rollback Plan

If something goes wrong:

1. **Revert frontend changes:**
   ```bash
   git revert HEAD
   git push origin main
   ```

2. **Revert database migration:**
   ```sql
   -- See DEPLOYMENT_STEPS.md for rollback SQL
   ```

## Support

- Check `QUICK_DEPLOY.md` for quick fixes
- Check `SECURITY_FIXES.md` for detailed security info
- Check browser console for debugging info
- All fallback mechanisms are in place

---

**Note:** The app is designed to work gracefully with or without the security migration. Deploy the frontend first, test it, then apply the migration when ready.
ncounter issues:
- Check `QUICK_DEPLOY.md` for quick fixes
- Check `SECURITY_FIXES.md` for detailed documentation
- Review browser console logs
- Check Supabase logs in dashboard

---

**Note:** The app is designed to work with graceful degradation. Even without the database migration, basic functionality will work using fallback methods. Apply the security migration when you're ready for full security features.

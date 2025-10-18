# Security Fixes Deployment Guide

## Prerequisites
- Supabase CLI installed
- Access to your Supabase project
- Database connection configured

## Deployment Steps

### 1. Review Changes
Review the security migration file:
```bash
cat supabase/migrations/20251018000000_security_fixes.sql
```

### 2. Test Locally (Recommended)
If you have a local Supabase instance:
```bash
supabase db reset
supabase start
```

### 3. Deploy to Production

#### Option A: Using Supabase CLI
```bash
# Link to your project (if not already linked)
supabase link --project-ref YOUR_PROJECT_REF

# Push the migration
supabase db push
```

#### Option B: Using Supabase Dashboard
1. Go to your Supabase Dashboard
2. Navigate to SQL Editor
3. Copy the contents of `supabase/migrations/20251018000000_security_fixes.sql`
4. Paste and run the SQL

### 4. Verify Deployment

Run these queries in the SQL Editor to verify:

```sql
-- Check coin constraints exist
SELECT conname, contype, pg_get_constraintdef(oid) 
FROM pg_constraint 
WHERE conrelid = 'public.profiles'::regclass 
AND conname LIKE '%coins%';

-- Check new functions exist
SELECT routine_name 
FROM information_schema.routines 
WHERE routine_schema = 'public' 
AND routine_name IN ('add_coins_reward', 'purchase_with_coins', 'update_profile_safe');

-- Check coin_transactions table exists
SELECT table_name 
FROM information_schema.tables 
WHERE table_schema = 'public' 
AND table_name = 'coin_transactions';

-- Check RLS policies
SELECT tablename, policyname, permissive, roles, cmd, qual 
FROM pg_policies 
WHERE schemaname = 'public' 
AND tablename = 'profiles';
```

### 5. Deploy Frontend Changes

The frontend changes are already in the codebase. Deploy using your normal process:

```bash
# If using Netlify/Vercel
npm run build
# Then deploy the build folder

# Or if using automated deployment
git add .
git commit -m "Security fixes: Remove cheat codes, add coin validation, restrict profile access"
git push origin main
```

### 6. Post-Deployment Testing

Test these scenarios:

1. **Cheat Code Removal**
   - Verify the coin display no longer has a popover/input
   - Verify clicking on coins does nothing

2. **Coin Balance Validation**
   - Try to purchase an item without enough coins
   - Verify error message appears
   - Check database to ensure balance didn't go negative

3. **Profile Privacy**
   - Log in as User A
   - Try to view User B's profile (who you haven't played)
   - Should not be able to see their stats

4. **Game Completion**
   - Play a game to completion
   - Verify coins are awarded/deducted correctly
   - Check `coin_transactions` table for audit log

5. **AI Victory Rewards**
   - Win against AI on different difficulties
   - Verify correct coin amounts are awarded:
     - Easy: 10 coins
     - Normal: 20 coins
     - Hard: 30 coins
     - Master: 50 coins

6. **Crate Purchases**
   - Purchase a crate
   - Verify coins are deducted
   - Verify item is unlocked
   - Check transaction log

### 7. Monitor for Issues

Check Supabase logs for:
- Failed function calls
- RLS policy violations
- Constraint violations

```sql
-- View recent coin transactions
SELECT * FROM public.coin_transactions 
ORDER BY created_at DESC 
LIMIT 50;

-- Check for any negative balances (should be none)
SELECT id, username, coins 
FROM public.profiles 
WHERE coins < 0;

-- Check for excessive balances (should be none > 999999)
SELECT id, username, coins 
FROM public.profiles 
WHERE coins > 999999;
```

## Rollback Plan

If issues occur, you can rollback by:

1. **Restore Previous Migration State**
```bash
supabase db reset --version <previous_version>
```

2. **Manual Rollback SQL**
```sql
-- Remove new constraints
ALTER TABLE public.profiles DROP CONSTRAINT IF EXISTS profiles_coins_check;
ALTER TABLE public.profiles DROP CONSTRAINT IF EXISTS profiles_coins_max_check;

-- Restore old policies
DROP POLICY IF EXISTS "Users can view own profile" ON public.profiles;
DROP POLICY IF EXISTS "Users can view opponents from game history" ON public.profiles;

CREATE POLICY "Profiles are viewable by everyone"
ON public.profiles FOR SELECT
USING (true);

-- Remove new functions
DROP FUNCTION IF EXISTS public.add_coins_reward(INTEGER, TEXT);
DROP FUNCTION IF EXISTS public.purchase_with_coins(INTEGER, TEXT, TEXT);
DROP FUNCTION IF EXISTS public.update_profile_safe(TEXT, TEXT);

-- Remove audit table
DROP TABLE IF EXISTS public.coin_transactions;
```

3. **Revert Frontend Changes**
```bash
git revert HEAD
git push origin main
```

## Support

If you encounter issues:
1. Check Supabase logs in the Dashboard
2. Review the SECURITY_FIXES.md document
3. Test each fix individually
4. Contact your database administrator

## Success Criteria

✅ All migrations applied successfully  
✅ No constraint violations in database  
✅ Cheat codes removed from UI  
✅ Coin operations use secure functions  
✅ Profile access restricted  
✅ Game completion validated  
✅ Audit trail working  
✅ No TypeScript errors  
✅ All tests passing  

## Notes

- The migration is designed to be non-breaking
- Existing data is preserved
- No downtime required
- Can be applied during normal operation

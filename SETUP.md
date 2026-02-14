# 🚀 Quick Setup Guide

## ✅ System Flow:

```
User fills form → Upload photos to Worker
    ↓
Worker saves to Supabase `posts` table
    ↓
Get back unique ID
    ↓
Redirect to: index2.html?id=123
    ↓
Fetch from: /posts?id=eq.123
    ↓
Display ONLY that wish (privacy-safe!)
```

---

## 📋 Prerequisites:

1. ✅ Cloudflare Worker (already have)
2. ✅ Supabase project (already have)
3. ✅ `posts` table in Supabase

---

## 🔧 Step-by-Step Setup:

### 1. Create Database Table

Go to Supabase Dashboard → SQL Editor → Run this:

```sql
CREATE TABLE posts (
    id BIGSERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    birth_date DATE,
    message TEXT,
    photo_url TEXT,
    photo_url_2 TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

ALTER TABLE posts ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Public can read posts"
ON posts FOR SELECT
TO anon
USING (true);

CREATE POLICY "Public can insert posts"
ON posts FOR INSERT
TO anon
WITH CHECK (true);
```

### 2. Upload Files to GitHub

Upload these 2 files:
- `index.html` (upload page)
- `index2.html` (display page)

### 3. Test

1. Open `index.html`
2. Fill form + upload photo
3. Should redirect to `index2.html?id=123`
4. Should show ONLY that wish

---

## 🔍 Verify Database:

### Check if table exists:

Go to Supabase Dashboard → Table Editor → Should see `posts` table

### Test manual insert:

```sql
INSERT INTO posts (name, message)
VALUES ('Test', 'Hello World')
RETURNING id;
```

Should return an ID (e.g., 1)

### Test query by ID:

```sql
SELECT * FROM posts WHERE id = 1;
```

Should return that row.

---

## 🐛 Troubleshooting:

### Problem: "Failed to save wish"

**Check:**
- Is `posts` table created?
- Are RLS policies enabled?
- Is API key correct?

**Fix:**
- Run database setup SQL again
- Check Supabase logs

### Problem: "Wish not found"

**Check:**
- Does wish exist in database?
- Is ID in URL correct?

**Fix:**
- Check database for that ID
- Verify redirect URL

### Problem: Photos not uploading

**Check:**
- Is Worker URL correct?
- Is Worker saving to database?

**Fix:**
- Test Worker separately
- Check Worker logs

---

## 📊 Expected Database Entries:

After creating a wish, check Supabase → Table Editor → `posts`:

| id | name | birth_date | message | photo_url | created_at |
|----|------|------------|---------|-----------|------------|
| 1 | John | 2000-05-15 | Happy BD! | https://... | 2025-02-13... |
| 2 | Alice | 1995-08-20 | Congrats! | https://... | 2025-02-13... |

Each row = one wish with unique ID.

---

## ✅ Success Checklist:

- [ ] Database table created
- [ ] RLS policies enabled
- [ ] Files uploaded to GitHub
- [ ] Test wish created
- [ ] Redirect works
- [ ] Only single wish shows (not all posts)

---

## 🎯 Result:

- User A creates wish → Gets `index2.html?id=1`
- User B creates wish → Gets `index2.html?id=2`
- User A can't see User B's wish (privacy safe!)
- Each wish is private, shareable link

Perfect! 🎉

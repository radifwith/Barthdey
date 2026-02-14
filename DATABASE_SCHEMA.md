# 📊 Database Schema

## Supabase Table: `posts`

### Required Columns:

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
```

### Column Details:

| Column | Type | Required | Description |
|--------|------|----------|-------------|
| `id` | BIGSERIAL | Yes | Auto-generated unique ID |
| `name` | TEXT | Yes | Birthday person's name |
| `birth_date` | DATE | No | Birth date (YYYY-MM-DD) |
| `message` | TEXT | No | Birthday message |
| `photo_url` | TEXT | No | First photo URL from Supabase Storage |
| `photo_url_2` | TEXT | No | Second photo URL (optional) |
| `created_at` | TIMESTAMP | Yes | Auto-generated creation time |

---

## Setup in Supabase:

### 1. Create Table

Go to Supabase Dashboard → SQL Editor → Run:

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
```

### 2. Enable Row Level Security (RLS)

```sql
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
```

### 3. Create Policy (Public Read)

```sql
CREATE POLICY "Public can read posts"
ON posts FOR SELECT
TO anon
USING (true);
```

### 4. Create Policy (Public Insert)

```sql
CREATE POLICY "Public can insert posts"
ON posts FOR INSERT
TO anon
WITH CHECK (true);
```

---

## API Usage:

### Insert New Wish:

```javascript
POST https://YOUR_PROJECT.supabase.co/rest/v1/posts
Headers:
  apikey: YOUR_ANON_KEY
  Authorization: Bearer YOUR_ANON_KEY
  Content-Type: application/json
  Prefer: return=representation

Body:
{
  "name": "John",
  "birth_date": "2000-05-15",
  "message": "Happy Birthday!",
  "photo_url": "https://...",
  "photo_url_2": "https://..."
}

Response:
[
  {
    "id": 123,
    "name": "John",
    ...
  }
]
```

### Get Single Wish:

```javascript
GET https://YOUR_PROJECT.supabase.co/rest/v1/posts?id=eq.123&select=*
Headers:
  apikey: YOUR_ANON_KEY
  Authorization: Bearer YOUR_ANON_KEY

Response:
[
  {
    "id": 123,
    "name": "John",
    "birth_date": "2000-05-15",
    "message": "Happy Birthday!",
    "photo_url": "https://...",
    "photo_url_2": "https://...",
    "created_at": "2025-02-13T12:00:00Z"
  }
]
```

---

## Privacy & Security:

✅ **Privacy-Safe:**
- Each wish has unique ID
- Users can only access wishes via direct link
- No public list of all wishes

✅ **Secure:**
- RLS enabled
- Public can only read/insert (no update/delete)
- Anon key is safe to expose (publishable)

---

## Testing:

### Test Insert:

```bash
curl -X POST 'https://urneinbrxacnwojhuvut.supabase.co/rest/v1/posts' \
  -H 'apikey: sb_publishable_xLnFSAUTtuwpcdBjL2mu7w_k-zekj1K' \
  -H 'Authorization: Bearer sb_publishable_xLnFSAUTtuwpcdBjL2mu7w_k-zekj1K' \
  -H 'Content-Type: application/json' \
  -H 'Prefer: return=representation' \
  -d '{
    "name": "Test User",
    "message": "Test message"
  }'
```

### Test Query:

```bash
curl 'https://urneinbrxacnwojhuvut.supabase.co/rest/v1/posts?id=eq.1' \
  -H 'apikey: sb_publishable_xLnFSAUTtuwpcdBjL2mu7w_k-zekj1K' \
  -H 'Authorization: Bearer sb_publishable_xLnFSAUTtuwpcdBjL2mu7w_k-zekj1K'
```

---

## Notes:

- Worker should save to this table after uploading photos
- `id` is returned in response for redirect
- `birth_date` is optional (can be NULL)
- Photos are optional (can be NULL)

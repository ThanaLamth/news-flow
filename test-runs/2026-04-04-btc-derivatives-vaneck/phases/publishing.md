# Phase 5 — WordPress Publishing

Use the WordPress MCP tools for the target site. Execute these steps in order:

**Efficiency rules:**
- Do not use `ToolSearch` during publishing.
- Do not use `wp_get_post_snapshot`, `list_media`, `wp_get_media`, or `wp_db_query` for normal publishing flow.
- Do not retry a tool after a permission-denied response.
- Do not loop on verification. Trust the direct tool responses from `upload-image.py`, `wp_create_post`, `wp_set_featured_image`, and `wp_update_post_meta`.

### Step 1: Upload Thumbnail
Use `upload-image.py` to compress and upload the thumbnail:
```bash
python3 "/home/claudeuser1/pipeline-v2/pipeline-v2/upload-image.py" \
  --file "<thumbnail_path>" --filename "<slug>-thumbnail.jpg" --site coincu \
  --max-width 1200 --max-height 900
```
Parse the returned JSON for `media_id`.

### Step 2: Create Post
Use `wp_create_post` with:
- `title`: The SEO title
- `content`: Full HTML article + disclaimer
- `status`: "publish"
- `categories`: [selected_category_id]
- `slug`: generated URL slug

### Step 3: Set Featured Image
Use `wp_set_featured_image` with the uploaded media_id.

### Step 4: Update SEO Meta
Use `wp_update_post_meta` to set:
- `rank_math_title`: SEO title
- `rank_math_description`: Meta description
- `rank_math_focus_keyword`: Primary keyword

Or use `wp_update_post` to set the excerpt to the meta description.

### Step 5: Output Summary
Print a JSON summary:
```json
{
  "post_id": 12345,
  "post_url": "https://coincu.com/slug/",
  "title": "SEO Title",
  "seo_title": "SEO Title",
  "meta_description": "Meta description text",
  "category": "Bitcoin",
  "category_id": 415,
  "thumbnail_id": 67890,
  "word_count": 1500,
  "internal_links": 4,
  "external_links": 5
}
```

If any step fails after one attempt, output the best available summary and stop. Do not perform post-publication verification loops.

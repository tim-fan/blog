# Project Specification: Headless Static Blog

## Architecture Overview

A static site workflow leveraging a local editor (Obsidian) for authoring, an object storage bucket for media hosting, and Jekyll (via standard GitHub Pages) for compilation. The frontend utilizes a client-side rendering approach for the index feed to support instantaneous filtering without a backend database. Media uploads are handled via a custom CLI tool to ensure robust handling of both images and large video files.

### Tech Stack

- **Authoring Environment:** Obsidian (Local Markdown)
    
- **Media Storage:** Cloudflare R2 (S3-Compatible Object Storage)
    
- **Media Uploader:** Custom local CLI script (Bash or Python wrapper around AWS CLI)
    
- **Static Site Generator:** Jekyll (GitHub Pages native build)
    
- **Frontend UI:** Vanilla JavaScript & HTML (No heavy frameworks required)
    
- **Version Control & CI/CD:** Git / GitHub Pages
    

## Authoring Workflow

1. **Draft:** Create a new Markdown file in the appropriate topic subdirectory within the local Obsidian vault (e.g., `_posts/project-a/2026-04-05-update.md`).
    
2. **Attach Media:** Use the custom CLI script to upload the file and append the embed code to the post.
    
    - _Example:_ `embed_media ~/Downloads/rover_test.mp4 >> vault/_posts/project-a/2026-04-05-update.md`
        
    - _Alternative:_ Run `embed_media ~/Downloads/rover_test.mp4 | pbcopy` and paste the generated markup directly into the Obsidian editor. Obsidian will instantly render the live HTML/Markdown.
        
3. **Publish:** Commit the changes and push to the `main` branch. GitHub Pages automatically runs the Jekyll build and deploys the updated site.
    

## Implementation To-Do List

### Phase 1: Repository & Basic Setup

- [ ] Initialize a new GitHub repository and enable GitHub Pages (deploy from `main` branch or standard Jekyll configuration).
    
- [ ] Scaffold a basic Jekyll directory structure (`_posts`, `_layouts`, `_site`, `_config.yml`).
    
- [ ] Verify standard Markdown rendering works by creating a dummy post (`_posts/2026-04-05-test.md`) and checking the live URL.
    

### Phase 2: Media Storage & Custom CLI Uploader

- [ ] **Provision Storage:** Set up a Cloudflare R2 bucket. Enable "Public Access" using the provided `r2.dev` subdomain. This is sufficient for low-traffic personal use.
    
- [ ] **Configure Local AWS CLI:** Install `aws-cli` and configure a dedicated profile with your R2 API tokens and the R2 S3-compatible endpoint URL.
    
- [ ] **Write the `embed_media` Script:** Create an executable script (Bash or Python) that performs the following sequence:
    
    1. **Accept Input:** Take the local file path as an argument `$1`.
        
    2. **Determine MIME Type:** Inspect the file (e.g., using the `file --mime-type` command) to categorize it as an `image` or `video`.
        
    3. **Generate Safe Filename:** Optionally sanitize the filename or append a timestamp/UUID to prevent namespace collisions in the bucket.
        
    4. **Execute Upload:** Run the `aws s3 cp` command (or use `boto3`) to push the file to the R2 bucket.
        
    5. **Construct URL:** Concatenate your public `r2.dev` domain with the uploaded filename.
        
    6. **Format Output to Stdout:**
        
        - If `image/*`: Print `![alt_text](https://pub-abcdef123...r2.dev/filename.jpg)`
            
        - If `video/*`: Print `<video controls width="100%"><source src="https://pub-abcdef123...r2.dev/filename.mp4" type="video/mp4"></video>`
            
- [ ] **Test Script:** Ensure the script correctly uploads files, accurately outputs the formatted string to `stdout`, and that the resulting markup renders correctly in Obsidian's live preview.
    

### Phase 3: The Dynamic Feed (Frontend)

- [ ] **Generate JSON API:** Create `posts.json` in the Jekyll root directory using Liquid templating to iterate over `site.posts` and output an array of post metadata (title, URL, date, category, excerpt).
    
- [ ] **Verify JSON Build:** Push to GitHub and verify `yoursite.com/posts.json` outputs valid, well-formed JSON.
    
- [ ] **Build Index UI (`index.html`):**
    
    - [ ] Create an empty container for the feed.
        
    - [ ] Add category/topic filter buttons at the top.
        
    - [ ] Write a Vanilla JS script to `fetch('./posts.json')`.
        
    - [ ] Implement a render function to map the JSON array into DOM elements.
        
    - [ ] Add event listeners to the filter buttons to update the DOM based on the post category.
        
    - [ ] _(Optional)_ Implement an `IntersectionObserver` on the last rendered item to build infinite scroll/pagination if the post volume requires it.
        

### Phase 4: Future Enhancements (TBD)

- [ ] **Custom Asset Domain:** If the blog traffic grows and `r2.dev` rate limits become an issue, purchase a cheap TLD, onboard it to a free Cloudflare zone, and route it to the R2 bucket to leverage edge caching. Update the CLI script to output the new domain.
    
- [ ] **Tagging System:** Expand the JSON metadata to include an array of tags. Add secondary filter controls to the frontend script to handle multi-parameter filtering (Topic + Tags).
    
- [ ] **RSS/Atom Feed:** Generate a standard `feed.xml` using Jekyll for syndication.

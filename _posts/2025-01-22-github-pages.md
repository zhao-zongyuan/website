---
title: "Github Pages"
date: 2025-01-21
categories: github
---

# Info

[Github Pages](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)

When submitting in main branch, Github action will start a workflow to build Jekyll automatically. 


## Post

[Posts](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll#adding-a-new-post-to-your-site)
1. Navigate to the _posts directory.

2. Create a new file called ``YYYY-MM-DD-NAME-OF-POST.md``, replacing ``YYYY-MM-DD`` with the date of your post and ``NAME-OF-POST`` with the name of your post. Must use ``-`` and no space allowed.

3. Add the following YAML frontmatter to the top of the file, including the post's title enclosed in quotation marks, the date and time for the post in YYYY-MM-DD hh:mm:ss -0000 format, and as many categories as you want for your post.
```
title: "POST-TITLE"
date: YYYY-MM-DD hh:mm:ss -0000
categories: CATEGORY-1 CATEGORY-2
```

4. Below the frontmatter, add content for your post.

5. Commit changes to main branch.

## Page

[Page](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/adding-content-to-your-github-pages-site-using-jekyll#adding-a-new-page-to-your-site)

Adding any .md or .html file in root directory will add a page and the title will be added onto the front banner. 

## Category

Jekyll supports front matter key Category and Categories. 

The file ``categories.html`` is a page for grouping posts with same categories. Do not change that.

# Markdown Syntax

[Markdown Syntax](https://www.markdownguide.org/basic-syntax/)



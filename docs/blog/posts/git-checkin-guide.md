---
slug: github
title: How to Check In a Local Project Folder to GitHub
date: 2025-06-01
tags: [github]
---

# How to Check In a Local Project Folder to GitHub

## Summary

This document shows how to take a local folder that contains a project and check it into GitHub using Git. It works for any project — code, config, data, etc.
<!-- more -->

## Use Case

“I have a folder on my machine and I want to upload it to GitHub so I can version it, share it, or back it up.”

## Step-by-Step: Check In Local Code to GitHub

### 1. Navigate to Your Local Project

Open a terminal and move into your project folder:

```bash
cd ~/path/to/my-project
```

### 2. Initialize a Git Repository

```bash
git init
```

This sets up Git to track changes in your folder.

### 3. Add Files and Make Your First Commit

```bash
git add .
git commit -m "Initial commit"
```

- `git add .` stages everything
- `git commit` saves the snapshot

### 4. Create a Repository on GitHub

Go to: https://github.com/new

- Enter a repo name (e.g., my-project)
- Choose public or private
- Do not initialize with README
- Click Create repository

### 5. Link the Local Repo to GitHub

GitHub will show you a command like:

```bash
git remote add origin https://github.com/YOUR_USERNAME/my-project.git
```

Run it in your terminal.

### 6. Push Code to GitHub

```bash
git branch -M main
git push -u origin main
```

This uploads your code to GitHub under the main branch.

### 7. You're Done

Go to:

```
https://github.com/YOUR_USERNAME/my-project
```

You’ll see your files uploaded and versioned.

## Best Practices

- Add a `.gitignore` to skip logs, binaries, and secrets.
- Use meaningful commit messages.
- Push often to keep your remote updated.

## Sample .gitignore

```gitignore
# System files
.DS_Store
Thumbs.db

# Editor folders
.vscode/
.idea/

# Output/build files
build/
dist/
*.log
*.tmp

# Secrets and configs
.env
*.pem
*.key
```

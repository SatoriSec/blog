---
slug: docusaurus
title: Docusaurus Setup
date: 2024-05-04
tags: [docusaurus]
---

## Creating a site using Docusaurus

Creating a personal documentation site is a great way to organize your knowledge, share insights, and build an online presence. Using Docusaurus, a modern static site generator, you can quickly set up a professional-looking site with ease. In this guide, we'll walk you through the steps to create and deploy your own documentation site using Docusaurus on GitLab, making your content accessible to the world.
<!-- more -->
### Step 1: Create a Repository on GitLab 
 
1. **Login to GitLab:**  
  - Visit [GitLab](https://gitlab.com/)  and log in to your account.
 
2. **Create a New Project:**  
  - Click on the **"New project"**  button from the dashboard or the **"+"**  icon at the top right.
 
  - Select **"Create blank project"** .
 
  - Name your project (e.g., `my-docusaurus-site`).

  - Choose the visibility level (Public, Private, or Internal).

  - Optionally, add a description.
 
  - Click **"Create project"** .
 
3. **Clone the Repository:** 
  - After creating the project, you’ll see the project page.

  - Copy the repository URL (SSH or HTTPS).
 
  - Open your terminal and run the following command:

```bash
git clone <repository-url>
cd my-docusaurus-site
```

### Step 2: Set Up a Docusaurus Site 
 
1. **Install Node.js:**  
  - Ensure you have Node.js installed. You can check by running:

```bash
node -v
npm -v
```
 
  - If not installed, download and install Node.js from [here](https://nodejs.org/) .
 
2. **Install Docusaurus:**  
  - Run the following command in your project directory to install Docusaurus:

```bash
npx create-docusaurus@latest my-website classic
cd my-website
```
 
3. **Run the Docusaurus Site Locally:**  
  - To see the site locally, run:

```bash
npm start
```
 
  - Open a browser and go to `http://localhost:3000` to view your site.

### Step 3: Push the Docusaurus Site to GitLab 
 
1. **Initialize Git and Push Code:**  
  - Ensure you're in the root directory of your Docusaurus site (`my-website`).
 
  - Initialize git and push the code to GitLab:

```bash
git init
git remote add origin <repository-url>
git add .
git commit -m "Initial commit with Docusaurus site"
git push -u origin master
```
:::note

Change the default branch to 'Master'.
https://docs.gitlab.com/ee/user/project/repository/branches/default.html

:::

### Step 4: Deploy the Docusaurus Site Using GitLab Pages 
 
1. **Create a `.gitlab-ci.yml` File:**  
  - In the root of your project, create a file named `.gitlab-ci.yml` and add the following content:

```yaml
image: node:18

cache:
  paths:
    - node_modules/

before_script:
  - npm install

pages:
  script:
    - npm run build
    - mv build public
  artifacts:
    paths:
      - public
```
 
  - This configuration installs dependencies, builds the Docusaurus site, and then moves the build output to the `public` directory, which is required for GitLab Pages.
 
2. **Commit and Push the Changes:**  
  - Commit the `.gitlab-ci.yml` file and push it to GitLab:

```bash
git add .gitlab-ci.yml
git commit -m "Add GitLab CI configuration for Docusaurus"
git push
```

### Step 5: Access Your Site 
 
1. **Enable GitLab Pages:** 
  - GitLab will automatically run the pipeline. Once completed, your site will be deployed to GitLab Pages.
 
 :::note 

 Make sure to change the 'baseUrl' in docusaurus.config.ts. Typically, it is your project name in Gitlab. 
:::

  - Go to your project’s **Settings > Pages**  to find the URL where your site is hosted. It will typically be something like `https://<your-username>.gitlab.io/<your-project-name>`.

### Summary 

You've now created a Docusaurus site, pushed it to GitLab, and deployed it using GitLab Pages! You can continue editing your Docusaurus site locally and push changes to update your live site.
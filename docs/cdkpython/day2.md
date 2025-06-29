### Background

Congratulations on setting up your environment!

Today, you’ll take your first real step into AWS infrastructure as code by creating your very own CDK project.

You’ll learn how to initialize a project, understand its structure, and synthesize your first CloudFormation template—all using Python.

Get ready to see how quickly and easily you can define cloud resources with code!

### What You’ll Do

- **Initialize a new AWS CDK project**
- **Explore the project structure**
- **Synthesize your first stack**


## Step-by-Step Guide (Ubuntu)

### 1. Open Your Terminal

Navigate to a directory where you want to create your project. For example, you can use your home directory or a dedicated `projects` folder:

```bash
mkdir -p ~/projects
cd ~/projects
```


### 2. Initialize a New CDK Project

Run the following command to create a new CDK project using Python:

```bash
cdk init app --language python
```

This creates a new folder with your project name (by default, it uses the current directory name unless you specify otherwise).

**Optional:** If you want a specific project name, create a folder and enter it before running the command:

```bash
mkdir my-first-cdk-app
cd my-first-cdk-app
cdk init app --language python
```


### 3. Activate the Python Virtual Environment

CDK projects use a Python virtual environment to manage dependencies.
Activate it by running:

```bash
source .venv/bin/activate
```

You should see `(.venv)` at the beginning of your terminal prompt.

### 4. Install Project Dependencies

Install the required Python packages:

```bash
pip install -e .
```

This installs the CDK core and other dependencies listed in your project.

### 5. Explore the Project Structure

Take a look at the files and folders created by the CDK init command.
Here’s a quick overview of the important files:

- **app.py**: The main entry point for your CDK app.
- **my_first_cdk_app_stack.py**: The stack definition (replace `my_first_cdk_app` with your project name if you changed it).
- **requirements.txt**: Lists the Python dependencies.
- **cdk.json**: Configuration file for the CDK tool.
- **.venv/**: The Python virtual environment directory.


### 6. Synthesize Your First Stack

The CDK can generate a CloudFormation template from your code.
Run the following command to synthesize your stack:

```bash
cdk synth
```

This outputs a CloudFormation template to your terminal.
You don’t need to understand the template yet—just know that this is what AWS will use to create your infrastructure.

## Summary

You’ve created your first AWS CDK project, explored its structure, and synthesized your first stack!
You’re now ready to start defining your own infrastructure and, soon, deploying it to AWS.

**Tomorrow, you’ll prepare your AWS account for CDK deployments by running the bootstrap command.**
Keep up the great work—your cloud automation journey is just getting started!


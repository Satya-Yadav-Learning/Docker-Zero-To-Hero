Creating a GitHub Repository Using Git Bash and GitHub CLI (GH CLI)
1. Task Overview

The objective is to create a new GitHub repository from a local project using Git Bash and GitHub CLI, configure a remote origin, and push the project to GitHub.

This guide covers:

Initializing a Git repository

Authenticating with GitHub using GitHub CLI

Creating a GitHub repository using the CLI

Configuring a remote origin

Pushing the local project to GitHub

2. Resolution Steps
Step 1 — Prepare Local Project Directory
cd ~
mkdir <REPO_NAME>
cd <REPO_NAME>


Verify directory:

pwd
ls -ltr

Step 2 — Initialize Local Git Repository
git init


Output:

Initialized empty Git repository in .../.git/


Add files:

git add .


Commit them:

git commit -m "Initial commit"

Step 3 — Login to GitHub Using GitHub CLI

Run:

gh auth login


Provide the following answers:

Prompt	Answer
Where do you use GitHub?	GitHub.com
Preferred protocol?	HTTPS
Authenticate Git?	Yes
How to authenticate?	Paste authentication token

Generate a token with required scopes:

GitHub → Settings → Developer Settings → Personal Access Tokens → Tokens (Classic)
Select scopes:

repo

read:org

workflow

Paste token when prompted.

Output should confirm:

✓ Logged in as <GITHUB_USERNAME>

Step 4 — Create GitHub Repository Using GH CLI

Run:

gh repo create <REPO_NAME> --public --source=. --remote=origin --push


Expected Output:

✓ Created repository <GITHUB_USERNAME>/<REPO_NAME> on github.com
https://github.com/<GITHUB_USERNAME>/<REPO_NAME>
✓ Pushed commits to origin/main


If remote already exists, use:

git remote set-url origin https://github.com/<GITHUB_USERNAME>/<REPO_NAME>.git

Step 5 — Verify Remote URL
git remote -v


Expected:

origin  https://github.com/<GITHUB_USERNAME>/<REPO_NAME>.git (fetch)
origin  https://github.com/<GITHUB_USERNAME>/<REPO_NAME>.git (push)

Step 6 — Push Local Code to GitHub

If the repository was not pushed in Step 4, run:

git push -u origin main


Output:

Enumerating objects...
Writing objects...
To https://github.com/<GITHUB_USERNAME>/<REPO_NAME>.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'

Step 7 — Confirm on GitHub UI

Open:

https://github.com/<GITHUB_USERNAME>/<REPO_NAME>


You should now see all your files uploaded successfully.

Final Status
Step	Status
Git initialization	Completed
GitHub CLI authentication	Completed
Repository creation on GitHub	Completed
Remote configuration	Completed
Initial push to GitHub	Completed

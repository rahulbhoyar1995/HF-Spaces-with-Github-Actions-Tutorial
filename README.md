# HuggingSpace Repos with Github Actions Tutorial
#### Author : Rahul Bhoyar

In this tutorial, you'll learn how to use GitHub Actions to keep your application synchronized with your GitHub repository. When using Spaces, remember that files larger than 10MB require Git-LFS. If you choose not to use Git-LFS, you'll need to review your files and history. You can use tools like BFG Repo-Cleaner to remove large files from your history, ensuring smooth synchronization.

#### Step 1: Create a repo on Github
To initiate the process, navigate to Github and create a new repository for your codebase. Ensure you provide an informative name and description for your repository. Store your code to this repository.

#### Step 2: Set up Repository and Spaces App

Now, we will link the GitHub repository with your Spaces app. Add your Spaces app as an additional remote to your existing Git repository using the command:

```
git remote add space https://huggingface.co/spaces/HF_USERNAME/SPACE_NAME
```
Verify Remote Addition: To confirm that the "space" remote repository has been successfully added, run the command:
```
git remote
```
This command will display a list of remote repositories, including "space" if it has been added correctly.

Then, force push to sync everything for the first time:
```
git push --force space main
```
#### Step 2: Create GitHub Action for Synchronization
(filename : **.github/workflows/sync_to_hub.yml** )
Next, create a GitHub Action to automatically push your main branch to Spaces. Replace HF_USERNAME with your username and SPACE_NAME with your Space name. Also, [create a GitHub secret](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-encrypted-secrets-for-an-environment) with your Hugging Face API token. You can find your token under API Tokens on your Hugging Face profile.

```
name: Sync to Hugging Face hub
on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  sync-to-hub:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          lfs: true
      - name: Push to hub
        env:
          HF_TOKEN: ${{ secrets.HF_TOKEN }}
        run: git push https://HF_USERNAME:$HF_TOKEN@huggingface.co/spaces/HF_USERNAME/SPACE_NAME main
```

### Step 3: Create Action for File Size Checking
(filename : **.github/workflows/check_file_size.yml**)
Finally, set up an Action that automatically checks the file size of any new pull request. This ensures that you stay within the file size limit of 10MB required for synchronization with HF Spaces.

```
name: Check file size
on:
  pull_request:
    branches: [main]
  workflow_dispatch:

jobs:
  sync-to-hub:
    runs-on: ubuntu-latest
    steps:
      - name: Check large files
        uses: ActionsDesk/lfs-warning@v2.0
        with:
          filesizelimit: 10485760 # 10MB limit for HF Spaces
```

By following these steps, you can effectively manage your Spaces with GitHub Actions, ensuring seamless synchronization and efficient development workflows.

For more information, click here : [Managing Spaces with Github Actions](https://huggingface.co/docs/hub/en/spaces-github-actions)

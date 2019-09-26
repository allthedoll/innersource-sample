# Migrating to GitHub
This guide walks you through how to do a `clean cutover` to GitHub, which means migrating your latest production data without any code revision history.

1. Create a repository on GitHub.com  
  a. Pay attention to naming conventions  
  b. Don't initialize with a `README.md`  
1. SSH into the appropriate r7 box
1. On your local machine, create a `GitHub` directory if one does not exist
1. Copy the project directory from the r7 box to your `GitHub` folder
1. Change the folder name, if needed, to match the repository name on GitHub.com
1. Make any necessary adjustments to the contents and directories of the project to ensure they're exactly how you want them to appear on GitHub  
  a. Delete files you don't need to bring  
  b. Create any additional directories to group items  
  c. Rearrange any files and their structures  
1. Add templates from the innersource repository and change their names as appropriate  
  a. `.gitignore-template` > `.gitignore`  
  b. `.gitconfig-template` > `.gitconfig`  
  c. `codeowners-template` > `CODEOWNERS`  
  d. `contributing-template.md` > `CONTRIBUTING.md`  
  e. `readme-template.md` > `README.md`  
1. In the command line with git, cd to your repository 
1. `git init`
1. `git status`  
  a. Verify everything in this list should be tracked by git  
  b. If anything should not be tracked by git, add it to your `.gitignore`  
1. `git add .`
1. `git commit -m "Initial commit"`
1. `git remote add origin [repository url with .git extension]`
1. `git push -u origin master`

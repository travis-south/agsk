---
name: create-private-repo
description: Create a private repository in the user's GitHub account from a two-word abbreviation of its description, then clone it to a specified folder.
---

# Create private repo

1. Get the repository description and the exact local destination folder. If either is missing, ask for it. Take the first two letters of each of the first two words in the description, lowercase them, and concatenate them (`movie downloader` → `modo`). If either word has fewer than two letters, ask for a different description.
2. Check `gh auth status`, identify the authenticated user with `gh api user --jq .login`, and check the destination folder and whether `<login>/<name>` already exists. Stop before creating anything if authentication fails, the folder contains files, or the name is taken. Ask for a different description when the name is taken; keep the naming rule.
3. Run `gh repo create <login>/<name> --private --description <description>`, then `gh repo clone <login>/<name> <destination-folder>`. If creation succeeds but cloning fails, report the repository URL and the clone error so the user can retry without creating another repository.
4. Verify the repository is private and the destination has its `origin` remote. Return the repository URL and absolute local path.

# Git Interview Questions and Answers (10+ Years Experience)

## Basic Questions

**1. What is Git?**
Git is a distributed version control system used to track changes in source code during software development. It allows multiple developers to work on a project simultaneously.

**2. How do you initialize a new Git repository?**
```bash
git init
```

**3. How do you clone a repository?**
```bash
git clone https://github.com/user/repo.git
```

**4. How do you check the status of your working directory?**
```bash
git status
```

**5. How do you stage and commit changes?**
```bash
git add <file>
git commit -m "Commit message"
```

**6. How do you view commit history?**
```bash
git log
```

**7. How do you create and switch branches?**
```bash
git branch feature-branch
git checkout feature-branch
```

**8. How do you merge branches?**
```bash
git checkout main
git merge feature-branch
```

## Intermediate Questions

**9. How do you resolve merge conflicts?**
- Open conflicting files, edit to resolve, then:
```bash
git add <conflicted-file>
git commit
```

**10. How do you stash changes?**
```bash
git stash
```
- To apply stashed changes:
```bash
git stash apply
```

**11. How do you view remote repositories?**
```bash
git remote -v
```

**12. How do you push and pull changes?**
```bash
git push origin main
git pull origin main
```

**13. How do you delete a branch?**
```bash
git branch -d feature-branch
```

**14. How do you revert a commit?**
```bash
git revert <commit-hash>
```

**15. How do you reset your working directory?**
```bash
git reset --hard <commit-hash>
```

**16. How do you tag releases?**
```bash
git tag v1.0.0
git push origin v1.0.0
```

## Advanced Questions

**17. How do you rebase a branch?**
```bash
git checkout feature-branch
git rebase main
```

**18. How do you cherry-pick a commit?**
```bash
git cherry-pick <commit-hash>
```

**19. How do you squash commits?**
```bash
git rebase -i HEAD~3
```
- Mark commits as "squash" or "fixup" in the editor.

**20. How do you work with submodules?**
```bash
git submodule add https://github.com/user/repo.git path/to/submodule
git submodule update --init --recursive
```

**21. How do you sign commits and tags?**
```bash
git commit -S -m "Signed commit"
git tag -s v1.0.0 -m "Signed tag"
```

**22. How do you configure global settings?**
```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

**23. How do you clean untracked files?**
```bash
git clean -fd
```

**24. How do you bisect to find a bug?**
```bash
git bisect start
git bisect bad <bad-commit>
git bisect good <good-commit>
```

**25. How do you view a file from a previous commit?**
```bash
git show <commit-hash>:<path-to-file>
```

## Most Common Git Interview Questions

**26. What is the difference between Git and GitHub?**
Git is a version control system; GitHub is a hosting service for Git repositories with collaboration features.

**27. What is the difference between merge and rebase?**
Merge combines histories, preserving all commits. Rebase moves or combines commits to create a linear history.

**28. What is a detached HEAD?**
A detached HEAD occurs when you checkout a commit that is not a branch tip. Changes made here are not associated with any branch.

**29. How do you undo the last commit but keep the changes?**
```bash
git reset --soft HEAD~1
```

**30. How do you undo the last commit and discard the changes?**
```bash
git reset --hard HEAD~1
```

**31. How do you list all branches (local and remote)?**
```bash
git branch -a
```

**32. How do you fetch changes from remote without merging?**
```bash
git fetch origin
```

**33. How do you view differences between commits or branches?**
```bash
git diff <commit1> <commit2>
git diff branch1 branch2
```

**34. How do you amend a commit message?**
```bash
git commit --amend -m "New message"
```

**35. How do you set up a .gitignore file?**
Create a .gitignore file and list patterns for files/folders to ignore.

**36. How do you view who changed a line in a file?**
```bash
git blame <file>
```

---
This file covers Git interview questions and answers from basic to advanced, including the most commonly asked questions, with code examples.

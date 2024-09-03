
# Jenkins Multi branch pipeline for PR only build

- our target is to build only PR’s not branches
- so in jenkins multi-branch pipeline, we need to add regular expression
- Filter by name (with regular expression): PR-[0-9]+
- the above Regualr expression will only build PR's, we can also add branches to that expression to build
- but the issue is, when we add multiple commits immediately to the PR, then our jenkins machine will be fully loaded to build them evenough they will be in queue, there are some minor situations where we need to add commits to Active PR, which doesn’t require jenkins build, we need to skip that commits to build but changes will be saved.
- we can do trigger at PR head(initial pr request) and trigger at merge. -> this option can be enabled
- pr-head -> i will go with this option only
- when we add build to jenkins initially, then all active PR’s in the repo will be triggered for the first time, then from next on wards, only new PR which is raised will be triggered. and commits to that PR will also trigger build again.
- multi-branch pipeline deciding factor is presence of jenkinsfile in each branch 


## Create a multi-branch pipeline job

<img width="1669" alt="Screenshot 2024-09-03 at 3 34 36 PM" src="https://github.com/user-attachments/assets/b0896b66-a5d9-4d55-b6c9-0f2690ed4586">

- select branch sources as github, and add username and password to github credential manager.
- 


# Jenkins Multi branch pipeline for PR only build

- our target is to build only PR’s not branches
- so in jenkins multi-branch pipeline, we need to add regular expression
- Filter by name (with regular expression): PR-[0-9]+
- the above Regualr expression will only build PR's, we can also add branches to that expression to build, orelse all branches will be built.
- but the issue is, when we add multiple commits immediately to the PR, then our jenkins machine will be fully loaded to build them evenough they will be in queue, there are some minor situations where we need to add commits to Active PR, which doesn’t require jenkins build, we need to skip that commits to build but changes will be saved.
- we can do trigger at PR head(initial pr request) and trigger at merge. -> this option can be enabled
- pr-head -> i will go with this option only
- when we add build to jenkins initially, then all active PR’s in the repo will be triggered for the first time, then from next on wards, only new PR which is raised will be triggered. and commits to that PR will also trigger build again.
- multi-branch pipeline deciding factor is presence of jenkinsfile in each branch
- each PR is also a branch with respect to multi-branch pipeline
- build executors means, if we have multiple jobs, then they can be built parallely, if job build multiple times at one job, then only one first build will happen and other will be in queue
- if we want to build same job two times and it needs to run then we need to enable concurrent build option
- if build is inprogress, then you built same job, then it will be queue...if you build again, jenkins may drop the 3rd build of same job
- Jenkins might have limitations on the number of builds it can queue, depending on the configuration. If the queue is full or the build system is overwhelmed, additional builds may not be scheduled.
- the disableConcurrentBuilds() option ensures that concurrent builds of the same branch are not allowed. This means that if a build is running for a particular branch (or PR) and another build is triggered for the same branch, the second build will wait for the first to complete before starting.
- However, builds for different branches (or PRs) can still run in parallel. For example, if you have two PRs, PR-1 and PR-2, builds for PR-1 and PR-2 can run simultaneously. But if PR-1 is already being built and another build for PR-1 is triggered, the second build will be queued until the first build finishes.

## create a webhook

<img width="1511" alt="Screenshot 2024-09-03 at 3 49 04 PM" src="https://github.com/user-attachments/assets/5ec8355c-fc77-4f3f-b9d9-6d07fa0be1de">

- select individual events, we will only use hook for PRs

<img width="832" alt="Screenshot 2024-09-03 at 3 50 06 PM" src="https://github.com/user-attachments/assets/d89fed4f-8445-49b7-840f-f64c6e997f61">

<img width="835" alt="Screenshot 2024-09-03 at 3 51 11 PM" src="https://github.com/user-attachments/assets/4fa287fa-f1b1-4c71-92bf-15eb1d4943ed">


## Create a multi-branch pipeline job

<img width="1669" alt="Screenshot 2024-09-03 at 3 34 36 PM" src="https://github.com/user-attachments/assets/b0896b66-a5d9-4d55-b6c9-0f2690ed4586">

- select branch sources as github, and add username and password to github credential manager.

<img width="1105" alt="Screenshot 2024-09-03 at 3 40 21 PM" src="https://github.com/user-attachments/assets/542c1ce4-634d-47ad-964c-2cd1e132bf9c">

- in behaviours section, you can add according to your usecase, my use case is to only build PR
- iam going with pr-head not with pr-head and pr-merge

<img width="1178" alt="Screenshot 2024-09-03 at 3 41 32 PM" src="https://github.com/user-attachments/assets/d0970e20-baea-417f-8411-ad9ddde11209">

<img width="1157" alt="Screenshot 2024-09-03 at 3 44 05 PM" src="https://github.com/user-attachments/assets/735d8c65-cfb0-489e-8cc3-275b8d02e3ee">


<img width="1228" alt="Screenshot 2024-09-03 at 3 44 36 PM" src="https://github.com/user-attachments/assets/2b72ce0a-2a74-4cad-9171-73e0c2991444">


<img width="1232" alt="Screenshot 2024-09-03 at 3 45 12 PM" src="https://github.com/user-attachments/assets/07039eca-c342-4e55-90c3-c36c4cc084fa">

- rest, keep them as it is.

- apply and save

- now jenkins will scan the repo and checks all PRs, it will add a seperate PR section and all PRs will be built for the first time


```
- we have a main branch in repo https://github.com/vijay2181/jenkins-pipeline-build-on-PR.git
- from main branch we will create a test branch, now main branch and test branch has Jenkinsfile
- commit something and push to test branch
- now raise PR on test branch
- when we raise PR, then build will happen inside jenkins server
- there is seperate PR section build, you can check
```

<img width="954" alt="Screenshot 2024-09-03 at 3 54 55 PM" src="https://github.com/user-attachments/assets/a42b0817-98a6-47cb-aa49-001e3f3135af">

<img width="1272" alt="Screenshot 2024-09-03 at 4 00 43 PM" src="https://github.com/user-attachments/assets/d0d9a69d-7b2d-4f68-b149-1768fde39adf">

<img width="186" alt="Screenshot 2024-09-03 at 4 03 13 PM" src="https://github.com/user-attachments/assets/86c85ded-66bf-4220-b1aa-8761d03cbb32">

- you PR will be built inside jenkins and you will get response back to PR

<img width="964" alt="Screenshot 2024-09-03 at 4 43 01 PM" src="https://github.com/user-attachments/assets/d2a39e2e-f737-40ee-8505-f67667fd4543">


- when you add commits to that PR branch test, then again build will trigger

- the issue, is when we add multiple commits immediately to the PR, then our jenkins machine will be fully loaded, there are some minor situations where we need to add commits to Active PR, which doesn’t require jenkins build.

- below Jenkinsfile can be used to control that behaviour

```
pipeline {
    agent any

    stages {
        stage('Filter PR Commits') {
            steps {
                script {
                    def commitMessage = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
                    if (commitMessage.contains('skip-ci')) {
                        echo "Skipping build as per commit message."
                        currentBuild.result = 'NOT_BUILT'
                        return
                    }
                }
            }
        }
        stage('Build') {
            when {
                expression { !currentBuild.result.equals('NOT_BUILT') }
            }
            steps {
                echo 'Building...'
                // adding build steps here
            }
        }
    }
}
```

## How It Works:

- The commitMessage.contains('[skip-ci]') checks if the commit message includes the [skip-ci] keyword.
- If found, currentBuild.result = 'NOT_BUILT' is set to mark the build as not built, and the script returns, skipping further stages.
- The when condition in the Build stage ensures it runs only if the build result is not NOT_BUILT.
- This setup will help avoid unnecessary builds based on the commit message, thereby reducing the load on your Jenkins server.

```
when you add below commit message to the test branch, then your build will be skipped and changes will be saved to that test branch

git commit -m "skip-ci"
```

<img width="340" alt="Screenshot 2024-09-03 at 4 49 13 PM" src="https://github.com/user-attachments/assets/560c453f-4a23-4fac-acf4-aa9236780cbb">

```
- so for initial PR request, job will be built
- for next commits, you can control whether you need to build or not through this commit message
- by this setup, you will have control and load on jenkins would be less.
```












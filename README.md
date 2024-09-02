# jenkins-pipeline-build-on-PR

we wanted Jenkins to build all of our Pull Requests and GitHub has to check for those enabled builds.
Whenever a build fails, no one should be able to merge, thus making a successful build a basic requirement, next to the approval of at least one team member.

Requirements:

```
GitHub Pull Request Builder -> plugin
Webhook to be configured in github repo with Jenkins url

```

- configure a webook

<img width="1370" alt="Screenshot 2024-09-02 at 3 29 40 PM" src="https://github.com/user-attachments/assets/7016a6b7-4389-47ee-93b3-afa6bac00c7f">

- select individual events for only pull request, your webhook should be activated


<img width="694" alt="Screenshot 2024-09-02 at 3 30 19 PM" src="https://github.com/user-attachments/assets/da867e35-a10d-4702-ba6a-d7f4dcdfb209">


<img width="818" alt="Screenshot 2024-09-02 at 3 31 01 PM" src="https://github.com/user-attachments/assets/9181bcfe-da83-42cd-b589-e798eaa134c6">





- create a new pipeline job

<img width="1653" alt="Screenshot 2024-09-02 at 3 38 05 PM" src="https://github.com/user-attachments/assets/16a5611b-3400-4f3c-82bd-f359fc8ed526">


- install GitHub Pull Request Builder Plugin

<img width="1605" alt="Screenshot 2024-09-02 at 3 40 51 PM" src="https://github.com/user-attachments/assets/c92749e4-1922-416f-ba1f-83fb661cecf4">  


<img width="1645" alt="Screenshot 2024-09-02 at 3 42 04 PM" src="https://github.com/user-attachments/assets/361b4b88-51c6-4c70-bcb8-b916267d9260">

- rest of the options, leave it as it is


```
The goal is to build individual Pull Requests before they are merged into your main branch.
As they originate from another branch as your main one, Jenkins somehow needs to get told which specific branch to build.
By adding a refspec and a branch specifier, including variables that are set by the plugin,
the job will always build what is specified by the webhook trigger, that comes from GitHub itself.
```

<img width="1646" alt="Screenshot 2024-09-02 at 3 45 11 PM" src="https://github.com/user-attachments/assets/193e4d64-0ff5-4ff7-be30-214d376a83d1">

<img width="1599" alt="Screenshot 2024-09-02 at 3 47 05 PM" src="https://github.com/user-attachments/assets/f2455551-eafe-4d47-ad89-29c1da8b6987">

<img width="1108" alt="Screenshot 2024-09-02 at 3 47 58 PM" src="https://github.com/user-attachments/assets/5e2e53d3-70e2-4ef8-b549-3130113549a0">


```
refs:
freestyle job: https://dev.to/oliverjumpertz/setting-up-jenkins-to-handle-github-pull-requests-5bjc
pipeline job: https://cwiki.apache.org/confluence/display/OPENWHISK/How+to+make+Jenkins+pipeline+jobs+triggered+by+pull+requests+for+Apache+projects
```











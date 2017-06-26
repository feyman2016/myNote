## Jenkins笔记

- 如何在不重启jenkins的情况下杀死僵尸job？

  Go to "Manage Jenkins" > "Script Console" and run a script:

  ```
  Jenkins.instance.getItemByFullName("JobName").getBuildByNumber(JobNumber).delete();
  ```

  for example:

  ```
  Jenkins.instance.getItemByFullName("vmseDashboardDockerize").getBuildByNumber(10).delete();
  ```

  是删除job: vmseDashboardDockerize的第10次构建

  [参考链接](https://stackoverflow.com/questions/14456592/how-to-stop-an-unstoppable-zombie-job-on-jenkins-without-restarting-the-server)




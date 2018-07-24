## gitlab-ci与Heroku集成部署

### gitlab-ci Runner
#### 安装runner
#### 注册与配置runner

**GitLab Runner　相关命令**

gitlab-runner --help

使用环境变量：
`gitlab-runner run --help`
使用Debug模式启动
`gitlab-runner --debug <command>`
启动gitlab-runner
`gitlab-runner run`
配置文件
`/etc/gitlab-runner/config.toml on *nix systems when gitlab-runner is executed as super-user (root)
~/.gitlab-runner/config.toml on *nix systems when gitlab-runner is executed as non-root
./config.toml on other systems`



注册runner
`gitlab-runner register`
验证状态
`gitlab-runner verify`
启动runner
`gitlab-runner start`
关闭runner
`gitlab-runner stop` 
重启runner
`gitlab-runner restart`
查看runner状态
`gitlab-runner status` 

２.在项目根目录下添加文件.gitlab-ci.yml
```shell
before_script:
  - echo "install dpl deploy tool" 

stages:
  - deploy

deploy to production:
  stage: deploy
  environment: production
  tags:
    - heroku
  only:
    - master
  script: 
    - dpl --provider=heroku --app=gitlabtestci --api-key=$HEROKU_API_KEY
```

以上为使用ssh runner，使用了本地服务器，所以在服务器安装了heroku-cli和dpl，如果使用docker或其他shared runner，可能需要在before-script处添加安装heroku或者dpl的过程
示例如下
```
before_script:
  - echo "install heroku" 
  - sudo curl https://cli-assets.heroku.com/install-ubuntu.sh | sh
  - echo "install dpl deploy tool" 
  - sudo gem install dpl

stages:
  - deploy

deploy to production:
  stage: deploy
  environment: production
  tags:
    - heroku
  only:
    - master
  script: 
    - dpl --provider=heroku --app=gitlabtestci --api-key=$HEROKU_API_KEY
    ```

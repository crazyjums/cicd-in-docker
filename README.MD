# GITLAB
## GITLAB
gitlab的启动需要修改基础配置，然后重启一下，才能正常工作。
修改的配置如下：
/etc/gitlab/gitlab.rb：
```
// 在gitlab创建项目时指定的容器的host(不用添加端口)
external_url 'http://gitlab.zhu.com'

gitlab_rails['gitlab_ssh_host'] = 'gitlab.zhu.com' //和external_url一样
gitlab_rails['gitlab_shell_ssh_port'] = 8022 // 此端口是run时22端口映射的8022端口
```
/opt/gitlab/embedded/service/gitlab-rails/config/gitlab.yml:
```
gitlab:
  host: gitlab.zhu.com
  port: 80
  https: false
```

修改完配置之后，然后执行以下命令，重启gitlab：
```
gitlab-ctl restart
gitlab-ctl status  # 查看gitlab的启动状态，出现以下的输出表示启动成功
run: alertmanager: (pid 646) 7506s; run: log: (pid 552) 7530s
run: gitaly: (pid 267) 7597s; run: log: (pid 265) 7597s
run: gitlab-exporter: (pid 590) 7523s; run: log: (pid 473) 7548s
run: gitlab-kas: (pid 339) 7586s; run: log: (pid 358) 7582s
run: gitlab-workhorse: (pid 577) 7523s; run: log: (pid 416) 7565s
run: logrotate: (pid 4923) 29s; run: log: (pid 269) 7597s
run: nginx: (pid 436) 7555s; run: log: (pid 459) 7554s
run: postgres-exporter: (pid 664) 7506s; run: log: (pid 575) 7524s
run: postgresql: (pid 340) 7587s; run: log: (pid 337) 7587s
run: prometheus: (pid 633) 7507s; run: log: (pid 533) 7535s
run: puma: (pid 879) 7391s; run: log: (pid 370) 7576s
run: redis: (pid 268) 7598s; run: log: (pid 266) 7598s
run: redis-exporter: (pid 592) 7524s; run: log: (pid 499) 7543s
run: sidekiq: (pid 896) 7387s; run: log: (pid 382) 7572s
run: sshd: (pid 31) 7619s; run: log: (pid 30) 7619s
```

## GITLAB-RUNNER
启动完gitlab-runner镜像之后，需要进入gitlab-runner镜像，执行以下命令，注册一个runner出来，其中token是从gitlab的配置中获取的。
```
gitlab-runner register -n \
    --url https://gitlab.zhu.com/ \
    --registration-token $YOUR_TOKEN \
    --executor docker \
    --description "docker runner" \
    --docker-image "docker.lab.com/docker-with-certs:stable" \
    --docker-privileged --tls-ca-file=/lab.com.crt
```

# SONAR
sonar启动时，因为在容器里面内置了一个es，所以所需的内存偏大，正常情况下，第一次启动sonar容器时会失败，因为docker的内存不足导致，修改最大允许的内存即可。
```
elasticsearch exited with code 78
```
以下要求和建议适用于在生产中的Docker中运行Elasticearch。
将vm.max_map_count至少设置为262144要用于生产，必须将vm.max_map_count内核设置至少设置为262144。
如何设置vm.max_map_count取决于您的平台。
windows系统使用下面的命令解决：
```
wsl -d docker-desktop
sysctl -w vm.max_map_count=262144
```
unix/linux系统使用下面的命令解决：
```
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
sysctl -p
```
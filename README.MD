# GITLAB
## GITLAB-RUNNER
```
gitlab-runner register -n \
    --url https://gitlab.zhu.com/ \
    --registration-token $YOUR_TOKEN \
    --executor docker \
    --description "docker runner" \
    --docker-image "docker.lab.com/docker-with-certs:stable" \
    --docker-privileged --tls-ca-file=/lab.com.crt
```
```shell
version: '3'
networks:
  auto-constr-network:
volumes:
  gitlab-config-volume:
  gitlab-log-volume:
  gitlab-data-volume:
  gitlab-runner-config:
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    restart: unless-stopped
    networks:
      - auto-constr-network
    ports:
      - "80:80"
      - "2224:22"
      - "443"
    volumes:
      - gitlab-config-volume:/etc/gitlab
      - gitlab-log-volume:/var/log/gitlab
      - gitlab-data-volume:/var/opt/gitlab
      - /etc/localtime:/etc/localtime:ro
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        external_url 'http://172.19.192.167/gitlab'
        gitlab_rails['gitlab_shell_ssh_port'] = 2224
  gitlab-runner:
    image: gitlab/gitlab-runner:latest
    restart: unless-stopped
    networks:
      - auto-constr-network
    volumes:
      - gitlab-runner-config:/etc/gitlab-runner
      - /var/run/docker.sock:/var/run/docker.sock
```



```shell
#!/bin/bash


URL="http://172.19.192.167/gitlab/"
PROJECT_REGISTRATION_TOKEN="vZhxZSHxyazdqkxE5ry9"

docker-compose exec gitlab-runner gitlab-runner register \
--non-interactive \
--executor "docker" \
--docker-privileged=true \
--docker-image alpine:latest \
--url ${URL} \
--registration-token ${PROJECT_REGISTRATION_TOKEN} \
--description "docker-runner" \
--run-untagged="true" \
--locked="false" \
--access-level="not_protected" \
--docker-volumes "/reports" \
--docker-pull-policy "if-not-present"
```


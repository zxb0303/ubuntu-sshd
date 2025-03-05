# ubuntu-sshd
Ubuntu Docker image with SSH service enabled
```yaml
version: '3'
services:
  ubuntu-sshd:
    restart: always
    image: zxb0303/ubuntu-ssh:24.04
    container_name: ubuntu-sshd
    ports:
      - "22:22"
    environment:
      ROOT_PASSWORD: 123456
    network_mode: "bridge"
```
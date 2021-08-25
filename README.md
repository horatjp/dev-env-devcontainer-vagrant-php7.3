#### 開発環境 1

- Docker Desktop
- Visual Studio Code [Remote - Containers]

#### 開発環境 2

- Virtual Box
- Vagrant
- Docker CLI
- Docker Compose
- Visual Studio Code [Remote - Containers]

### 開発環境 1

`vi .devcontainer/.env`

```env
## Docker Desktop
DOCKER_DESKTOP_IP_ADDRESS_SETTING=127.0.1.11:

## VAGRANT
#VAGRANT_DEVCONTAINER_PATH=/vagrant/.devcontainer/
```

hosts

```
127.0.1.11 devcontainer.test
```

下記コマンドで`Remote - Containers`を起動

```
Remote-Containers: Reopen in composer Container
```

### 開発環境 2

- Vagrant Plugin

```
vagrant plugin install vagrant-vbguest
vagrant plugin install dotenv
vagrant plugin install vagrant-disksize
vagrant plugin install vagrant-hostmanager
vagrant plugin install vagrant-docker-compose
```

`vi .env`

```env
## VAGRANT
VAGRANT_HOST="devcontainer.test"
```

`vi .devcontainer/.env`

```env
## Docker Desktop
#DOCKER_DESKTOP_IP_ADDRESS_SETTING=127.0.1.11:

## VAGRANT
VAGRANT_DEVCONTAINER_PATH=/vagrant/.devcontainer/
```

`vi .vscode\settings.json`

```json
    "docker.host": "ssh://vagrant@devcontainer.test",
```

Vagrant 起動。

```
vagrant up
```

`.vscode/settings.json` に`docker.host`が設定されているので、それを参考に、SSH でログインできるように公開鍵を登録する。
ログインできるか確認、かつ`known_hosts `に登録。

```
ssh-keygen -t ed25519 -C ""
cp ~/.ssh/id_ed25519.pub ./
vagrant ssh
cat /vagrant/id_ed25519.pub >> ~/.ssh/authorized_keys
rm /vagrant/id_ed25519.pub
exit

ssh-keygen -R devcontainer.test
ssh-keyscan -H devcontainer.test >> ~/.ssh/known_hosts
ssh vagrant@devcontainer.test
```

`ssh-agent`に鍵を登録する。

```
ssh-add ~/.ssh/id_ed25519
```

拡張機能`Docker`で接続できるか確認。

下記コマンドで`Remote - Containers`を起動

```
Remote-Containers: Reopen in composer Container
```

###### **Tips**

公開鍵が RSA だと Docker ホストに接続できない。

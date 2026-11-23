# Конфигурация стенда DevOps

## 10. Ansible

Управляющий узел — macOS. Управляемый узел — изолированное окружение Ubuntu 24.04 в Docker Desktop. С Mac SSH доступен через 127.0.0.1:12222, Nginx — через 127.0.0.1:18080. Перед bootstrap для вложенного Docker Engine на локальном стенде задан драйвер хранения `vfs` и отключён `containerd-snapshotter`. Это позволяет запустить контейнер приложения внутри контейнера Ubuntu.

В `ansible/inventory/bootstrap.ini` указан пользователь `student`. В `ansible/inventory/prod.ini` указан пользователь `devops`, который подключается по ключу. Путь к ключу можно задать через `DEVOPS_VM_KEY`. Ключ хоста проверяется через SSH known_hosts.

Роль `base` устанавливает Docker Engine из официального репозитория. Роль `ssh` настраивает ключ, sudo, sshd и UFW. Роль `nginx` включает обратный прокси. Роль `app` загружает `ghcr.io/mnwa610/devops-ci-app:d25e8c6382f89d44a02cb4341c8e11666839e233`, публикует контейнер на 127.0.0.1:5000 внутри узла и проверяет `/health`.

Из каталога `ansible`:

```sh
ansible-galaxy collection install -r requirements.yml
ansible-playbook -i inventory/bootstrap.ini site.yml --ask-pass --ask-become-pass
ansible-playbook site.yml
ansible-playbook verify.yml
curl --fail http://127.0.0.1:18080/health
```

# Управление сервером через Ansible

Стенд состоит из двух VirtualBox VM с Ubuntu Server 24.04. Управляющий узел devops-control подключается к devops-vm по SSH через шлюз NAT 10.0.2.2 и локальный проброс порта 2222. Стенд доступен из Windows по http://127.0.0.1:8089 и из контроллера по http://10.0.2.2:8089.

1. На контроллере используйте ключ ~/.ssh/devops_vm. Ключ хоста VM должен быть проверен и записан в ~/.ssh/devops_lab_known_hosts. Пароли и закрытые ключи хранятся вне Git.
2. Установите коллекции из каталога ansible: ansible-galaxy collection install -r requirements.yml.
3. Первый запуск: ansible-playbook -i inventory/bootstrap.ini site.yml --ask-become-pass. В импортированном образе bootstrap-пользователь называется sa. Перед запуском установите ему открытый ключ.
4. Повторное применение: ansible-playbook site.yml. В рабочем inventory используется devops.
5. Аудит: ansible-playbook verify.yml. Это плейбук чтения состояния.

Роли применяются в порядке base, ssh, nginx, app. Nginx принимает трафик на порту 80 гостевой машины. Контейнер доступен внутри VM по 127.0.0.1:5000. Образ ghcr.io/parad1ze/devops-ci-app загружается по полному SHA из group_vars/all.yml. Для проверки вызовите /health через Nginx.

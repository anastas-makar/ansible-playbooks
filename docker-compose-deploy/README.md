# Playbook для подготовки к деплою docker-контейнеров на сервер

При помощи ansible осуществляется:

- Установка docker и docker-compose
- Установка openssh
- Создание пользователя gitlab-runner и добавление его в группу docker. 

Пользователю gitlab-runner добавляется в authorized keys публичный ключ, который можно использовать для деплоя на виртуалку с помощью gitlab. Он добавлен в переменные роли gitlab-runner. При настройке проекта с нуля нужно сгенерировать ключи и переопределить значение переменной. 

Для того, чтобы ansible смог добавить ключ в authorized keys, должен быть установлен ansible.posix. Установить можно так:

```
ansible-galaxy collection install ansible.posix
```

Приватный ключ должен быть известен и не быть скомпрометирован. Для того, чтобы осуществлять деплой при помощи gitlab, нужно:

1. Выполнить на своём сервере команду:

```
ssh-keyscan <ip вируталки для деплоя>
```

2. Полученный вывод записать в переменную gitlab.
3. Добавить приватный ключ в перменную gitlab.

Пример использования в before_script:

```yaml
  before_script:
    - eval $(ssh-agent -s)
    - echo "$SSH_PRIVATE_KEY" | tr -d '\r' | ssh-add -
    - mkdir -p ~/.ssh
    - chmod 700 ~/.ssh
    - echo "$SSH_KNOWN_HOSTS" >> ~/.ssh/known_hosts
    - chmod 644 ~/.ssh/known_hosts
```
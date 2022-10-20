# Домашнее задание к занятию "08.01 Введение в Ansible"

## Подготовка к выполнению
1. Установите ansible версии 2.10 или выше.

            root@yulka98356:/ansible_netology# ansible --version
            ansible [core 2.13.5]

3. Создайте свой собственный публичный репозиторий на github с произвольным именем.


5. Скачайте [playbook](./playbook/) из репозитория с домашним заданием и перенесите его в свой репозиторий.

## Основная часть
1. Попробуйте запустить playbook на окружении из `test.yml`, зафиксируйте какое значение имеет факт `some_fact` для указанного хоста при выполнении playbook'a.

                                  "msg": 12

2. Найдите файл с переменными (group_vars) в котором задаётся найденное в первом пункте значение и поменяйте его на 'all default fact'.

                                    ansible@yulka98356:/devops-netology/playbook$ cat group_vars/all/examp.yml
                                    ---
                                      some_fact: 'all default fact'
                                      
                                    TASK [Print fact] ***********************************************************************************************************************************************************************************************
                                    ok: [localhost] => {
                                        "msg": "all default fact"

3. Воспользуйтесь подготовленным (используется `docker`) или создайте собственное окружение для проведения дальнейших испытаний.

                                    ansible@yulka98356:/root$ docker ps -a
                                    CONTAINER ID   IMAGE          COMMAND       CREATED          STATUS          PORTS     NAMES
                                    cd96effc1dd7   centos         "/bin/bash"   15 seconds ago   Up 13 seconds             centos
                                    a345398f06d1   ubuntu:20.04   "bash"        32 seconds ago   Up 30 seconds             ubuntu

4. Проведите запуск playbook на окружении из `prod.yml`. Зафиксируйте полученные значения `some_fact` для каждого из `managed host`.

                                    TASK [Print fact] ***********************************************************************************************************************************************************************************************
                                    ok: [centos7] => {
                                        "msg": "el"
                                    }
                                    ok: [ubuntu] => {
                                        "msg": "deb"
                                    }


5. Добавьте факты в `group_vars` каждой из групп хостов так, чтобы для `some_fact` получились следующие значения: для `deb` - 'deb default fact', для `el` - 'el default fact'.

                                    root@yulka98356:/devops-netology/playbook# cat group_vars/deb/examp.yml
                                    ---
                                      some_fact: 'deb default fact'

                                    root@yulka98356:/devops-netology/playbook# cat group_vars/el/examp.yml
                                    ---
                                      some_fact: 'el default fact'

6.  Повторите запуск playbook на окружении `prod.yml`. Убедитесь, что выдаются корректные значения для всех хостов.

                                    TASK [Print fact] ***********************************************************************************************************************************************************************************************
                                    ok: [centos7] => {
                                        "msg": "el default fact"
                                    }
                                    ok: [ubuntu] => {
                                        "msg": "deb default fact"
                                    }

7. При помощи `ansible-vault` зашифруйте факты в `group_vars/deb` и `group_vars/el` с паролем `netology`.

                                    root@yulka98356:/devops-netology/playbook# ansible-vault encrypt group_vars/deb/examp.yml
                                    New Vault password:
                                    Confirm New Vault password:
                                    Encryption successful
                                    root@yulka98356:/devops-netology/playbook# ansible-vault encrypt group_vars/el/examp.yml
                                    New Vault password:
                                    Confirm New Vault password:
                                    Encryption successful

8. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь в работоспособности.

                                    root@yulka98356:/devops-netology/playbook# ansible-playbook -i inventory/prod.yml site.yml --ask-vault-pass
                                    Vault password:

9. Посмотрите при помощи `ansible-doc` список плагинов для подключения. Выберите подходящий для работы на `control node`.

                                    local                          execute on controller

10. В `prod.yml` добавьте новую группу хостов с именем  `local`, в ней разместите localhost с необходимым типом подключения.


                                    root@yulka98356:/devops-netology/playbook# cat inventory/prod.yml
                                    ---
                                      el:
                                        hosts:
                                          centos7:
                                            ansible_connection: docker
                                      deb:
                                        hosts:
                                          ubuntu:
                                            ansible_connection: docker
                                      local:
                                        hosts:
                                          localhost:
                                            ansible_connection: local
        
11. Запустите playbook на окружении `prod.yml`. При запуске `ansible` должен запросить у вас пароль. Убедитесь что факты `some_fact` для каждого из хостов определены из верных `group_vars`.

                                    TASK [Print fact] ***********************************************************************************************************************************************************************************************
                                    ok: [centos7] => {
                                        "msg": "el default fact"
                                    }
                                    ok: [ubuntu] => {
                                        "msg": "deb default fact"
                                    }
                                    ok: [localhost] => {
                                        "msg": "all default fact"
                                    }

12. Заполните `README.md` ответами на вопросы. Сделайте `git push` в ветку `master`. В ответе отправьте ссылку на ваш открытый репозиторий с изменённым `playbook` и заполненным `README.md`.

https://github.com/yulkann/devops-netology/tree/main/playbook

---

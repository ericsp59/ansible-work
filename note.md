#+++++++++++++++++++++++++++++++++++++++++++
+++++++++++++++++ Ansible ++++++++++++++++++
+++++++++++++++++++++++++++++++++++++++++++

_______hosts.ini______
[demo] # группа серверов
127.0.0.1 ansible_user=av ansible_port=22
_________________

############# adhoc ###############
#ansible -i hosts.ini(Инвентарь) -m ping(Модуль) demo --ask-pass(запрос SSH пароля)  ## пинг 
ansible -i hosts.ini -m ping demo

#ansible -i hosts.ini(Инвентарь) -m user(Модуль) -a "name=av state=present"(аргументы) demo ## Создание пользователя av
ansible -i hosts.ini -m user -a "name=av state=present" demo
ansible -i hosts.ini --ask-pass(запрос SSH пароля) -m user -a "name=av2 state=present" demo -b(от root) -K(запрос пароля root) 
ansible -i hosts.ini --ask-pass -m user -a "name=av2 state=present" -e(переменные) "ansible_become=true ansible_become_password=1234qwer" demo


################ Playbook ####################

_____________ user.yml__________________
---
- name: user
  hosts: demo
  vars_files:
    - ./my_vars.yml 						# файлы с переменными
  tasks:
    - name: Create user
			vars:											# блок с переменными
        user: av2
      user:
        name: "{{ user }}"
        state: present
      become: true  
__________________________________________

ansible-playbook -i hosts.ini --ask-pass  user.yml # Запустить playbook
ansible-playbook -i hosts.ini --ask-pass user.yml -K
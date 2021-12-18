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
  # vars in group_vars/demo/vars.yml
  # vars_files:
  #   - ./my_vars.yml
  # vars_prompt: ## Ввод переменной
  #   - name: user
  #     prompt: "Введите пользователя"
  #     private: no
  tasks:
    - name: Preconfig block
      become: true
      block:
        - name: Create user
          vars:
            user: av3
          user:
            name: "{{ user }}"
            state: absent
          
          register: out # регистрируем переменную для дебага
        - debug:
            var: out # вкл. дебаг и выводим в переменную 

        - name: Install links
          apt:
            name: links
            update-cache: yes
          register: out # регистрируем переменную для дебага
        - debug:
            var: out # вкл. дебаг и выводим в переменную 
          #debugger: always
          ## команды в дебагере:
            ## p task (Вывод task name)
            ## p task.args (Вывод аргументов задачи, котоые передавались в модуль)
            ## p task.vars (Вывод переменных в task)
__________________________________________

ansible-playbook -i hosts.ini --ask-pass  user.yml # Запустить playbook
ansible-playbook -i hosts.ini --ask-pass user.yml -K
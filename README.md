# Домашнее задание к занятию 6 «Создание собственных модулей»

## Ссылка на репозиторий коллекции и архива:

[Коллекция ditry86.my_collection](https://github.com/Ditry86/my_collection.git)

## Выполнение заданий:

Модуль выполняет примитивные действия по созданию файла с содержимым по следующим атрибутам: имя фала - `name`, путь в файловой системе - `path`, содержимое - `content`. 

> Модуль не создает новые каталоги, если таки нет в пути файла. Вместо этого возвращает ошибку 'Bad path of file'.

1. Локальное тестирование модуля (пункт 4)

    В качестве вводных значений для атрибутов модуля используется файл `payload.json` с содержимым:

    ```
    {
    "ANSIBLE_MODULE_ARGS": {
        "path": ".",
        "name": "test",
        "content": "texttexttext"
    }
    }   
    ```
    
    Локальный тест работы модуля:

    1) Создание нового фала по соществующему пути

    ```
    (venv) ditry@study:~/ansible_study_6/ansible$ python3 -m ansible.modules.my_module payload.json 

    {"state": "File test is created", "error": "", "invocation": {"module_args": {"path": ".", "name": "test", "content": "texttexttext"}}}
    ```
    2) Поведение модуля, если файл уже существует

    ```
    (venv) ditry@study:~/ansible_study_6/ansible$ python3 -m ansible.modules.my_module payload.json 

    {"state": "File test exists", "error": "", "invocation": {"module_args": {"path": ".", "name": "test", "content": "texttexttext"}}}
    ```
    3) Работа модуля при ошибке в пути (не существует директории):

    ```
    (venv) ditry@study:~/ansible_study_6/ansible$ python3 -m ansible.modules.my_module payload.json 

    {"state": "File test is not created", "error": "Bad path of file", "invocation": {"module_args": {"path": "test", "name": "test", "content": "texttexttext"}}}
    ```
2. Проверка на иденпотентность через playbook (пункт 6):

```
(venv) ditry@study:~/ansible_study_6/ansible$ ansible-playbook site.yml 

PLAY [Test my module] ****************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************
ok: [localhost]

TASK [Run module] ********************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] *************************************************************************************************************************************************************************
ok: [localhost] => {
    "module_out": {
        "changed": false,
        "error": "",
        "failed": false,
        "state": "File test2 is created"
    }
}

PLAY RECAP ***************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) ditry@study:~/ansible_study_6/ansible$ ^C
(venv) ditry@study:~/ansible_study_6/ansible$ ansible-playbook site.yml 

PLAY [Test my module] ****************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************
ok: [localhost]

TASK [Run module] ********************************************************************************************************************************************************************
ok: [localhost]

TASK [debug] *************************************************************************************************************************************************************************
ok: [localhost] => {
    "module_out": {
        "changed": false,
        "error": "",
        "failed": false,
        "state": "File test2 exists"
    }
}

PLAY RECAP ***************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) ditry@study:~/ansible_study_6/ansible$ 
```

3. Установка коллекции локально из архива (пункт 15):

```
ditry@study:~/test$ ansible-galaxy collection install ditry86-my_collection-1.0.0.tar.gz 
Starting galaxy collection install process
Process install dependency map
Starting collection install process
Installing 'ditry86.my_collection:1.0.0' to '/home/ditry/.ansible/collections/ansible_collections/ditry86/my_collection'
ditry86.my_collection:1.0.0 was installed successfully
```

4. Запуск playbook:

```
ditry@study:~/test$ ansible-playbook site.yml 

PLAY [Test] **************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************
ok: [localhost]

TASK [ditry86.my_collection.my_role : Run module] ************************************************************************************************************************************
ok: [localhost]

TASK [ditry86.my_collection.my_role : debug] *****************************************************************************************************************************************
ok: [localhost] => {
    "module_out": {
        "changed": false,
        "error": "",
        "failed": false,
        "state": "File test2 is created"
    }
}

PLAY RECAP ***************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

ditry@study:~/test$ 
```
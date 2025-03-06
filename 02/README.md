# Домашнее задание к занятию «Базовые объекты K8S»


### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.
2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

------

### Ответ
1. создадим фаил pod_echo.yaml

              apiVersion: v1
              kind: Pod
              metadata:
                name: hello-world
              spec:
                containers:
                  - name: hello-world
                    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
                    ports:
                    - containerPort: 8080

2. Запустим создание пода 
![image](https://github.com/user-attachments/assets/c7d1f90e-5c03-40d1-b86f-061293d2d3c1)

3. Выполним port-forward

![image](https://github.com/user-attachments/assets/acbfbc8b-7ec4-4501-8196-bfbfd0aeea58)

4. Подключимся к поду
![image](https://github.com/user-attachments/assets/24b23c6a-0b5a-45ef-b85c-b8c140454a7b)

![image](https://github.com/user-attachments/assets/0e020556-fab7-4555-ac1e-660a9e10c522)


------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

------

### Ответ

------

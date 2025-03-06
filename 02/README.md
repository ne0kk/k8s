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
1. Создадим Pod с именем netology-web с помощью файла pod-netology-web.yaml
   Добавим label
   
          apiVersion: v1
          kind: Pod
          metadata:
            name: netology-web
            labels:
              app: netology
          spec:
            containers:
            - name: netology-web
              image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
   
![image](https://github.com/user-attachments/assets/7d47d5c1-39d7-4f4f-8eba-0d49272631dc). 

2. Создаlим Service с именем netology-svc и подключим его к поду netology-web с помощью файла pod-netology-svc.yam
Добавим Selector

      apiVersion: v1
      kind: Service
      metadata:
        name: netology-svc
      spec:
        ports:
          - name: web
            port: 8080
        selector:
          app: netology

![image](https://github.com/user-attachments/assets/5480a4e3-8e6d-4c69-8883-dc4c414a4d68)
![image](https://github.com/user-attachments/assets/8e334596-c810-40ff-9150-4c7100add787)


3. Пробросим порт и подключимся с сервису
![image](https://github.com/user-attachments/assets/340aa5d0-6d31-4432-8567-908fcc31c445)
![image](https://github.com/user-attachments/assets/fe5b72e3-a104-41dd-b142-1f1ea6e35853)

   
------

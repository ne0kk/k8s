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

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

------

### Ответ

------

# Домашнее задание к занятию «Запуск приложений в K8S»

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Deployment с приложением, состоящим из нескольких контейнеров, и масштабировать его.

------

### Задание 1. Создать Deployment и обеспечить доступ к репликам приложения из другого Pod

1. Создать Deployment приложения, состоящего из двух контейнеров — nginx и multitool. Решить возникшую ошибку.
2. После запуска увеличить количество реплик работающего приложения до 2.
3. Продемонстрировать количество подов до и после масштабирования.
4. Создать Service, который обеспечит доступ до реплик приложений из п.1.
5. Создать отдельный Pod с приложением multitool и убедиться с помощью `curl`, что из пода есть доступ до приложений из п.1.

------
### Ответ
1. Создадим deployment.yaml

      apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: netology-home
        labels:
         app: netology
      spec:
       selector:
        matchLabels:
         app: netology
       replicas: 1
       template :
        metadata:
         labels:
          app: netology
        spec:
         containers:
          - name: nginx
            image: nginx:latest
            ports:
            - containerPort: 80
          - name: multitool
            image: wbitt/network-multitool:latest
            ports:

Запустим deployment

![image](https://github.com/user-attachments/assets/bc55a3e3-f580-4f1f-8521-2ea0f6255e30)

Один контейнер падает в ошибку. Проверим какой. 

![image](https://github.com/user-attachments/assets/999a1354-552e-4837-b9ff-c610e2d611e4)

По всей видимости в контейнер multitool стартует сервис (кстати тоже nginx) на 80 порту, который уже занят контейнером nginx.
по ответам на ДЗ другим студентам, понял что надо сменить порт nginx в контейнере 

![image](https://github.com/user-attachments/assets/5d628b48-4cb6-400a-9da1-38fb905bcce0)

![image](https://github.com/user-attachments/assets/81b773d4-ae93-42b5-aee4-1c6c0d60d5c7)


2. Добавим еще однй реплику.
 
![image](https://github.com/user-attachments/assets/6ab9230b-9277-451d-8b97-eef83588c929)

4. Создадим сервис для подов

![image](https://github.com/user-attachments/assets/e041b347-e3fb-43d8-b461-b2dd6bdc83f7)

6. Создадим отдельный POD для проверки из поднего доступа к приложениям созданым ранее
   
 ![image](https://github.com/user-attachments/assets/14de24ca-76dd-4574-97e5-2415a0478014)
  
Провери ответ curl на оба сервиса  
узнаем внутренние IP контейнеров 

![image](https://github.com/user-attachments/assets/7666e227-afcb-4d06-8c5a-c4f160efb6c8)

Ответ curl 
![image](https://github.com/user-attachments/assets/6ec5ced8-9165-4107-816f-e0c12c5c0c49)

![image](https://github.com/user-attachments/assets/24634dee-9cd4-4373-b465-7ed061acb840)


------

### Задание 2. Создать Deployment и обеспечить старт основного контейнера при выполнении условий

1. Создать Deployment приложения nginx и обеспечить старт контейнера только после того, как будет запущен сервис этого приложения.
2. Убедиться, что nginx не стартует. В качестве Init-контейнера взять busybox.
3. Создать и запустить Service. Убедиться, что Init запустился.
4. Продемонстрировать состояние пода до и после запуска сервиса.

------
### Ответ

------

# Домашнее задание к занятию «Kubernetes. Причины появления. Команда kubectl»

### Задание 1. Установка MicroK8S

1. Установить MicroK8S на локальную машину или на удалённую виртуальную машину.
2. Установить dashboard.
3. Сгенерировать сертификат для подключения к внешнему ip-адресу.

### Ответ

1. Развернем инстанс в облаке с помощью терраформ

   ![image](https://github.com/user-attachments/assets/6bf720fc-eb9e-4bdd-b4c1-666b49ca3354)

2. Выполним sudo apt update
   
   ![image](https://github.com/user-attachments/assets/96d08a93-4829-4b28-bb63-0d4b1cea8d42)

4. Выполним sudo apt install snapd,
   
   ![image](https://github.com/user-attachments/assets/a2a56adc-2034-41c8-8e8d-8e106132f05f)

6. Выполним sudo snap install microk8s --classic,
   
  ![image](https://github.com/user-attachments/assets/fd0200c7-7f37-401d-ba97-f48a4c94bd2b)

  
8.  добавить локального пользователя в группу sudo usermod -a -G microk8s $USER
9.  изменить права на папку с конфигурацией sudo chown -f -R $USER ~/.kube.

10. ![image](https://github.com/user-attachments/assets/37e31ce7-40c8-4f71-9811-bae9242f5217)
11. Установим дашборд
    ![image](https://github.com/user-attachments/assets/088d80dc-6b28-4449-a64c-d2f0b7705c1e)
Обновим сертификты
`sudo microk8s refresh-certs --cert front-proxy-client.crt`. 

------

### Задание 2. Установка и настройка локального kubectl
1. Установить на локальную машину kubectl.
2. Настроить локально подключение к кластеру.
3. Подключиться к дашборду с помощью port-forward.

### Ответ

1. На локалном компьютере устновим  kubectl
 
  - curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl;
    - chmod +x ./kubectl;
    - sudo mv ./kubectl /usr/local/bin/kubectl;
      
2. Настроим локальные подключения
![image](https://github.com/user-attachments/assets/372abdb4-b4ed-44a4-a5ad-f4f8efe34589)

3. Подключимся локально с даашборду
![image](https://github.com/user-attachments/assets/ffbb4213-4903-424e-8af4-6edbf5596968)

4. Сгенерируем токен
![image](https://github.com/user-attachments/assets/36ea108d-b89e-4db4-a86a-92386bf8706d)

5. Зайдем на дашборд
   ![image](https://github.com/user-attachments/assets/be29d280-71ae-49f7-97e2-5c9b629a2b29)
   ![image](https://github.com/user-attachments/assets/5090ada1-b0b6-4ca9-8fb9-9623c4c62d77)




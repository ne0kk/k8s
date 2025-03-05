1. Развернем инстанс в облаке с помощью терраформ

   ![image](https://github.com/user-attachments/assets/6bf720fc-eb9e-4bdd-b4c1-666b49ca3354)

2. Выполним sudo apt update
   ![image](https://github.com/user-attachments/assets/96d08a93-4829-4b28-bb63-0d4b1cea8d42)

4. Выполним sudo apt install snapd,
   ![image](https://github.com/user-attachments/assets/a2a56adc-2034-41c8-8e8d-8e106132f05f)

6. Выполним sudo snap install microk8s --classic,
  ![image](https://github.com/user-attachments/assets/fd0200c7-7f37-401d-ba97-f48a4c94bd2b)

  
7.  добавить локального пользователя в группу sudo usermod -a -G microk8s $USER
8.  изменить права на папку с конфигурацией sudo chown -f -R $USER ~/.kube.

9. ![image](https://github.com/user-attachments/assets/37e31ce7-40c8-4f71-9811-bae9242f5217)

# Домашнее задание к занятию «Управление доступом»

### Цель задания

В тестовой среде Kubernetes нужно предоставить ограниченный доступ пользователю.


------

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создайте и подпишите SSL-сертификат для подключения к кластеру.
2. Настройте конфигурационный файл kubectl для clear
3. подключения.
4. Создайте роли и все необходимые настройки для пользователя.
5. Предусмотрите права пользователя. Пользователь может просматривать логи подов и их конфигурацию (kubectl logs pod <pod_id>, kubectl describe pod <pod_id>).
6. Предоставьте манифесты и скриншоты и/или вывод необходимых команд.

------
### Ответ

Создадим serviceaccount 

![image](https://github.com/user-attachments/assets/9446f63c-2ccb-465b-bf84-f39e03df7ea0)

Создадим токен для serviceaccount
                    apiVersion: v1
                    kind: Secret
                    metadata:
                      name: netology-token
                      annotations:
                        kubernetes.io/service-account.name: netology-sa
                    type: kubernetes.io/service-account-token

![image](https://github.com/user-attachments/assets/388b3661-d7cb-4288-8b86-2792bf3430fe)

Создадим RSA сертификат 

![image](https://github.com/user-attachments/assets/fb570ea5-a784-4600-a4b5-b07ff06e23cd)

Создадим csr для пользователя netology

![image](https://github.com/user-attachments/assets/f8bbf265-cc97-4e4b-a445-3bebdc1ee588)

Для выполнения request вытащим серты с кластера 

![image](https://github.com/user-attachments/assets/1945c4b6-aef2-45c3-a9ee-d51e9e2147c3)

И выпустим сертифика пользователя подписанный кластером (пользователь выпускает csr и передает его админу и тот подписывает и выпускает серт пользователя). 

![image](https://github.com/user-attachments/assets/634896cc-c8e0-448a-9c67-efb2c023f76c)

Создаем юзера netology 

![image](https://github.com/user-attachments/assets/87dbf5c7-5084-45e5-9c01-e75de52ecbd0)

проверим конфиг  vi ~/.kube/config 

![image](https://github.com/user-attachments/assets/d98d76bf-bb2a-4676-8de3-c96af283089b)

проверим права, создадим контекст netology 

![image](https://github.com/user-attachments/assets/2d15257b-58e1-4e61-9fe0-e3cd442dc1df)

Видим что наш пользователь все права получает без ограничений, необходимо включить RBAC

![image](https://github.com/user-attachments/assets/7775cc91-df3c-4144-a596-90155b931468)

Проверим еще раз наличие прав, прав недостаточно 

![image](https://github.com/user-attachments/assets/7e627892-7bb1-4fad-b313-3d9b8f71c6a3)

Создадим новую role

                apiVersion: rbac.authorization.k8s.io/v1
                kind: Role
                metadata:
                  name: user-role
                rules:
                - apiGroups: [""]
                  resources: ['pods',"pods/log"]
                  verbs: ["get","watch"]


![image](https://github.com/user-attachments/assets/bba296b2-153e-48be-95de-3448ab45bd2b)

Создадим новый RoleBinding - role-user-binding.yaml


                apiVersion: rbac.authorization.k8s.io/v1
                kind: RoleBinding
                metadata:
                  name: netology-binding
                subjects:
                  - kind: User
                    name: netology
                    apiGroup: rbac.authorization.k8s.io
                roleRef:
                  kind: Role
                  name: user-role
                  apiGroup: rbac.authorization.k8s.io

![image](https://github.com/user-attachments/assets/988efe89-dc5d-4355-beaf-47f836bbfbdd)

Проверим появились ли права у пользователя

![image](https://github.com/user-attachments/assets/3f4055c8-7a97-40b2-88ca-dc5fea35b011)



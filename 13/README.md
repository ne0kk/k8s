# Домашнее задание к занятию «Как работает сеть в K8s»

### Цель задания

Настроить сетевую политику доступа к подам.

### Чеклист готовности к домашнему заданию

1. Кластер K8s с установленным сетевым плагином Calico.

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Документация Calico](https://www.tigera.io/project-calico/).
2. [Network Policy](https://kubernetes.io/docs/concepts/services-networking/network-policies/).
3. [About Network Policy](https://docs.projectcalico.org/about/about-network-policy).

-----

### Задание 1. Создать сетевую политику или несколько политик для обеспечения доступа

1. Создать deployment'ы приложений frontend, backend и cache и соответсвующие сервисы.
2. В качестве образа использовать network-multitool.
3. Разместить поды в namespace App.
4. Создать политики, чтобы обеспечить доступ frontend -> backend -> cache. Другие виды подключений должны быть запрещены.
5. Продемонстрировать, что трафик разрешён и запрещён.
-----
### Ответ
1. Создадим сервера согласно задания
![image](https://github.com/user-attachments/assets/674e2c32-b7de-4f6d-8336-47da7b94d51e)
Установим k8s с помошью rke2
![image](https://github.com/user-attachments/assets/4c75c76c-2238-49e2-b773-9f7dbde146b8)

                              cat <<EOF >>config.server.yaml
                              
                              write-kubeconfig-mode: "0644"
                              tls-san:
                                - "89.169.131.127"
                              node-ip::
                                - "10.0.2.33"
                              node-external-ip:
                                - "89.169.131.127"
                              advertise-address:
                                - "10.0.2.33"
                              cni:
                                - calico
                              disable:
                                - rke2-canal
                              
                              EOF
                              
                              cat <<EOF >>config.agent.yaml 
                              
                              server: https://10.0.2.33:9345
                              token: 
                              node-ip:
                                - "10.0.2.7"
                              node-external-ip:
                                - "89.169.132.39"
                              cni:
                                - calico
                              disable:
                                - rke2-canal
                              
                              EOF

  создадим необходимые поды 

                                  apiVersion: networking.k8s.io/v1
                                kind: NetworkPolicy
                                metadata:
                                  name: all-deny
                                  namespace: app
                                spec:
                                  podSelector: { }
                                  policyTypes:
                                    - Ingress
                                ---
                                
                                apiVersion: apps/v1
                                kind: Deployment
                                metadata:
                                  name: frontend
                                  namespace: app
                                spec: 
                                 selector:
                                  matchLabels:
                                   app: frontend
                                 replicas: 1
                                 template :
                                  metadata:
                                   labels:
                                    app: frontend
                                  spec:
                                   containers:
                                    - name: front
                                      image: wbitt/network-multitool
                                      ports:
                                      - containerPort: 80
                                        name: front-port
                                
                                ---
                                apiVersion: v1
                                kind: Service
                                metadata:
                                  name: svc-front
                                  namespace: app
                                spec:
                                  ports:
                                    - name: http-multitool
                                      port: 80
                                      targetPort: front-port
                                  selector:
                                    app: frontend
                                
                                
                                ---
                                
                                apiVersion: apps/v1
                                kind: Deployment
                                metadata:
                                  name: backend
                                
                                  namespace: app
                                spec: 
                                 selector:
                                  matchLabels:
                                   app: backend
                                 replicas: 1
                                 template :
                                  metadata:
                                   labels:
                                    app: backend
                                  spec:
                                   containers:
                                    - name: back
                                      image: wbitt/network-multitool
                                      ports:
                                      - containerPort: 80
                                        name: back-port
                                
                                ---
                                
                                
                                apiVersion: v1
                                kind: Service
                                metadata:
                                  name: svc-back
                                  namespace: app
                                spec:
                                  ports:
                                    - name: http-multitool
                                      port: 80
                                      targetPort: back-port
                                  selector:
                                    app: backend
                                
                                
                                ---
                                
                                apiVersion: networking.k8s.io/v1
                                kind: NetworkPolicy
                                metadata:
                                  name: allow-back
                                  namespace: app
                                spec:
                                  podSelector:
                                    matchLabels:
                                      app: backend
                                  ingress:
                                    - from:
                                        - podSelector:
                                            matchLabels:
                                              app: frontend
                                      ports:
                                        - protocol: TCP
                                          port: 80
                                  policyTypes:
                                    - Ingress
                                ---
                                
                                apiVersion: apps/v1
                                kind: Deployment
                                metadata:
                                  name: cache
                                  namespace: app
                                spec: 
                                 selector:
                                  matchLabels:
                                   app: cache
                                 replicas: 1
                                 template :
                                  metadata:
                                   labels:
                                    app: cache
                                  spec:
                                   containers:
                                    - name: cache
                                      image: wbitt/network-multitool
                                      ports:
                                      - containerPort: 80
                                        name: cache-port
                                
                                ---
                                
                                apiVersion: v1
                                kind: Service
                                metadata:
                                  name: svc-cache
                                  namespace: app
                                spec:
                                  ports:
                                    - name: http-multitool
                                      port: 80
                                      targetPort: cache-port
                                  selector:
                                    app: cache
                                
                                
                                
                                
                                ---
                                apiVersion: networking.k8s.io/v1
                                kind: NetworkPolicy
                                metadata:
                                  name: allow-cache
                                  namespace: app
                                spec:
                                  podSelector:
                                    matchLabels:
                                      app: cache
                                  ingress:
                                    - from:
                                        - podSelector:
                                            matchLabels:
                                              app: backend
                                      ports:
                                        - protocol: TCP
                                          port: 80
                                  policyTypes:
                                    - Ingress

Проверим работу наших NetworkPolicy
![image](https://github.com/user-attachments/assets/7f31679b-a5ca-4c80-bd83-1196fd0ddf0d)
Дотсупы предоставлены подам согласно задания. 
-----








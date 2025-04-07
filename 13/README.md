

k8smaster = tomap({
  "k8smaster-01" = {
    "fqdn" = "k8smaster-01.ru-central1.internal"
    "id" = "fhmjp2mbsel2g3d19pj4"
    "ipexternal" = "89.169.131.127"
    "iplocal" = "10.0.2.33"
    "name" = "k8smaster-01"
  }
})
k8snodes = tomap({
  "k8snodes-01" = {
    "fqdn" = "k8snodes-01.ru-central1.internal"
    "id" = "fhmgphmrm727e84e3ika"
    "ipexternal" = "89.169.132.39"
    "iplocal" = "10.0.2.7"
    "name" = "k8snodes-01"
  }
})


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
token: K10f5fc5aad6af65920b8f4c8408d0de24fb3635d997870fa7b19daf9a30bdc892c::server:0e3e9a956b560d8b6feb3caf994247f1
node-ip:
  - "10.0.2.7"
node-external-ip:
  - "89.169.132.39"
cni:
  - calico
disable:
  - rke2-canal

EOF
![image](https://github.com/user-attachments/assets/4c75c76c-2238-49e2-b773-9f7dbde146b8)

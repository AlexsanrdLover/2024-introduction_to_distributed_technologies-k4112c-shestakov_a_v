University: [ITMO University](https://itmo.ru/ru/)
Faculty: [FICT](https://fict.itmo.ru)
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)
Year: 2024
Group: K4112c
Author: Shestakov Albert Victorovich
Lab: Lab4
Date of create: 24.12.2024
Date of finished: ...

**1. Установка плагина и режима работы Multi-Node Clusters**
```
$ minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode-demo
```
**2. Проверка работы Calico**

```
kubectl get pods -l k8s-app=calico-node -A
kubectl get nodes
```

**3. Пометка нод** 

--overwrite=true - если уже помечали nods до этого и хотите rename

```
$ kubectl label nodes multinode-m02 rack=1 --overwrite=true
$ kubectl label nodes multinode rack=0 --overwrite=true 
```

**4. Манифест для Calico**

```
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: rack-0
spec:
  cidr: 192.168.10.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "0"
---
apiVersion: crd.projectcalico.org/v1
kind: IPPool
metadata:
  name: rack-1
spec:
  cidr: 192.168.20.0/24
  ipipMode: Always
  natOutgoing: true
  nodeSelector: rack == "1"
```

**5. Скачивание конфигурационного файла**

**(перед командой необходимо [скачать конфигурационный файл)](https://github.com/projectcalico/calico/blob/master/manifests/calicoctl.yaml)**
```
$ kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

**6. Создание deployment**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment
  labels:
    app: lab4
spec:
  replicas: 2
  selector: 
    matchLabels:
      app: lab4
  template:
    metadata:
      labels:
        app: lab4
    spec:
      containers:
      - name: lab4
        image: ifilyaninitmo/itdt-contained-frontend:master
        env:
        - name: REACT_APP_USERNAME
          value: Shestakov
        - name: REACT_APP_COMPANY_NAME
          value: itmo
        ports:
        - containerPort:  3000

```

**7. Создание service**
```
apiVersion: v1
kind: Service
metadata:
  name: service-lab4
spec:
  type: NodePort
  selector:
    app: lab4
  ports:
    - port: 3000
      protocol: TCP
      name: http
```

**8. Обновление deployment и service**
```
$ kubectl apply -f deployment.yaml
$ kubectl apply -f service.yaml
```

**9. Вход в веб браузер**
```
localhost:3000
```
**10. Узнать информацию о FQDN имена подов**
```
kubectl get pods -o wide
NAME                          READY   STATUS    RESTARTS   AGE     IP               NODE                 NOMINATED NODE   READINESS GATES
deployment-54768b7798-4dpdd   1/1     Running   0          6m44s   10.244.113.194   multinode-demo       <none>           <none>
deployment-54768b7798-xf4h6   1/1     Running   0          6m44s   10.244.239.2     multinode-demo-m02   <none>           <none>
kubectl exec deployment-54768b7798-4dpdd nslookup 10.244.113.194
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
Server:  10.96.0.10
Address: 10.96.0.10:53

194.113.244.10.in-addr.arpa name = 10-244-113-194.service-lab4.default.svc.cluster.local


kubectl exec deployment-54768b7798-xf4h6 nslookup 10.244.239.2
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
Server:  10.96.0.10
Address: 10.96.0.10:53

2.239.244.10.in-addr.arpa name = 10-244-239-2.service-lab4.default.svc.cluster.local
```
![photo_2024-12-25_16-00-22](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab4/lab4_ход_работы/image.jpg)
![photo_2024-12-25_16-00-22](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab4/lab4_ход_работы/image5.jpg)
**11. Пинг подов**
```
kubectl exec -it deployment-54768b7798-4dpdd sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
/frontend # ping 10-244-239-2.service-lab4.default.svc.cluster.local
PING 10-244-239-2.service-lab4.default.svc.cluster.local (10.244.239.2): 56 data bytes
64 bytes from 10.244.239.2: seq=0 ttl=62 time=0.573 ms
64 bytes from 10.244.239.2: seq=1 ttl=62 time=0.399 ms
64 bytes from 10.244.239.2: seq=2 ttl=62 time=0.516 ms
64 bytes from 10.244.239.2: seq=3 ttl=62 time=0.569 ms
64 bytes from 10.244.239.2: seq=4 ttl=62 time=0.634 ms
![image](https://github.com/user-attachments/assets/dcef09b4-8e60-47e0-90e9-46ad98cb7a6d)

kubectl exec -it deployment-54768b7798-xf4h6 sh
kubectl exec [POD] [COMMAND] is DEPRECATED and will be removed in a future version. Use kubectl exec [POD] -- [COMMAND] instead.
/frontend # ping 10-244-113-194.service-lab4.default.svc.cluster.local
PING 10-244-113-194.service-lab4.default.svc.cluster.local (10.244.113.194): 56 data bytes
64 bytes from 10.244.113.194: seq=0 ttl=62 time=0.364 ms
64 bytes from 10.244.113.194: seq=1 ttl=62 time=0.319 ms
64 bytes from 10.244.113.194: seq=2 ttl=62 time=0.267 ms
64 bytes from 10.244.113.194: seq=3 ttl=62 time=0.420 ms
64 bytes from 10.244.113.194: seq=4 ttl=62 time=0.271 ms
```
![photo_2024-12-25_16-00-22](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab4/lab4_ход_работы/image3.jpg)
![photo_2024-12-25_16-00-22](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab4/lab4_ход_работы/image4.jpg)
![photo_2024-12-25_16-00-22](https://github.com/AlexsanrdLover/2024-introduction_to_distributed_technologies-k4112c-shestakov_a_v/blob/main/lab4/lab4_ход_работы/схем.png)

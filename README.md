# Домашнее задание к занятию «Конфигурация приложений»

### Цель задания

В тестовой среде Kubernetes необходимо создать конфигурацию и продемонстрировать работу приложения.

------

### Чеклист готовности к домашнему заданию

1. Установленное K8s-решение (например, MicroK8s).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключённым GitHub-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. [Описание](https://kubernetes.io/docs/concepts/configuration/secret/) Secret.
2. [Описание](https://kubernetes.io/docs/concepts/configuration/configmap/) ConfigMap.
3. [Описание](https://github.com/wbitt/Network-MultiTool) Multitool.

------

### Задание 1. Создать Deployment приложения и решить возникшую проблему с помощью ConfigMap. Добавить веб-страницу

1. Создать Deployment приложения, состоящего из контейнеров nginx и multitool.

<img width="431" alt="task1" src="https://github.com/user-attachments/assets/4c0446f8-9f87-47f1-b387-082445667e94">

Deployment не поднялся, в одной из д/з это решилось через добавление env

<img width="461" alt="change task1" src="https://github.com/user-attachments/assets/6dbd4ad8-801d-451c-b197-510e9df8ebd3">

2. Решить возникшую проблему с помощью ConfigMap.
ConfigMap

```bash
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-configmap1
data:
  multitool_port: "8080"
```

Deployment

```bash
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: multitool-dep
  name: multitool-dep
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: multitool
  template:
    metadata:
      labels:
        app: multitool
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80
      - name: multitool
        image: wbitt/network-multitool:latest
        ports:
        - containerPort: 8080
        env:
        - name: HTTP_PORT
          valueFrom:
            configMapKeyRef:
              name: my-configmap1
              key: multitool_port
```

3. Продемонстрировать, что pod стартовал и оба конейнера работают.

<img width="413" alt="deployment1 with configmap1" src="https://github.com/user-attachments/assets/093f11ed-21b4-454c-81f3-1ea3aad8e541">

4. Сделать простую веб-страницу и подключить её к Nginx с помощью ConfigMap. Подключить Service и показать вывод curl или в браузере.

<img width="467" alt="started deployment, configmap, svc" src="https://github.com/user-attachments/assets/bf24461b-803c-4880-b98a-0f5e0b0179a4">

```bash
Name:             myapp-pod-59cc4c74d5-mgb66
Namespace:        default
Priority:         0
Service Account:  default
Node:             kuber/192.168.43.233
Start Time:       Sat, 23 Nov 2024 00:14:37 +0300
Labels:           app=myapp
                  pod-template-hash=59cc4c74d5
Annotations:      cni.projectcalico.org/containerID: d067e9fa569f10ebd3ab5327c9724a4e3a27ef49b3329d1d3add9dfb737b9796
                  cni.projectcalico.org/podIP: 10.1.106.138/32
                  cni.projectcalico.org/podIPs: 10.1.106.138/32
Status:           Running
IP:               10.1.106.138
IPs:
  IP:           10.1.106.138
Controlled By:  ReplicaSet/myapp-pod-59cc4c74d5
Init Containers:
  init-myservice:
    Container ID:  containerd://ca9fed67b7213c5f8a08c0eb79eed03291cb54a9bab85384e6928c9f3a2e6552
    Image:         busybox:1.28
    Image ID:      docker.io/library/busybox@sha256:141c253bc4c3fd0a201d32dc1f493bcf3fff003b6df416dea4f41046e0f37d47
    Port:          <none>
    Host Port:     <none>
    Command:
      sh
      -c
      until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done
    State:          Terminated
      Reason:       Completed
      Exit Code:    0
      Started:      Sat, 23 Nov 2024 00:14:44 +0300
      Finished:     Sat, 23 Nov 2024 00:14:44 +0300
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-npwln (ro)
Containers:
  network-multitool:
    Container ID:   containerd://d8f5c4b4e58ed451d6c67f534aef97d5ab94d0a23323be0dba7eb359009cfc90
    Image:          wbitt/network-multitool
    Image ID:       docker.io/wbitt/network-multitool@sha256:d1137e87af76ee15cd0b3d4c7e2fcd111ffbd510ccd0af076fc98dddfc50a735
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Sat, 23 Nov 2024 00:14:44 +0300
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     200m
      memory:  512Mi
    Requests:
      cpu:        100m
      memory:     256Mi
    Environment:  <none>
    Mounts:
      /usr/share/nginx/html/ from nginx-index-file (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-npwln (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       True 
  ContainersReady             True 
  PodScheduled                True 
Volumes:
  nginx-index-file:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      index-html-configmap
    Optional:  false
  kube-api-access-npwln:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  82s   default-scheduler  Successfully assigned default/myapp-pod-59cc4c74d5-mgb66 to kuber
  Normal  Pulling    82s   kubelet            Pulling image "busybox:1.28"
  Normal  Pulled     76s   kubelet            Successfully pulled image "busybox:1.28" in 5.969s (5.969s including waiting). Image size: 727869 bytes.
  Normal  Created    76s   kubelet            Created container init-myservice
  Normal  Started    76s   kubelet            Started container init-myservice
  Normal  Pulled     76s   kubelet            Container image "wbitt/network-multitool" already present on machine
  Normal  Created    76s   kubelet            Created container network-multitool
  Normal  Started    76s   kubelet            Started container network-multitool
```

<img width="354" alt="curl nginx our page" src="https://github.com/user-attachments/assets/d9469d45-bf01-4669-acb3-1eb6b364b662">

<img width="648" alt="browser check before change" src="https://github.com/user-attachments/assets/dfc4985f-a2ae-4152-b0aa-b0442a152e71">

[configmap1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap1-4.yaml)

5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

[configmap1](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap1.yaml)
[configmap1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap1-4.yaml)
[deployment1](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment1.yaml)
[deployment1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment1-4.yaml)
[deployment-svc1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment-svc1-4.yaml)

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.
2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.
3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.
4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 
4. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

------

### Правила приёма работы

1. Домашняя работа оформляется в своём GitHub-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------

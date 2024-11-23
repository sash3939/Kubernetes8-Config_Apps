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
[deployment1](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment1.yaml)

---
[configmap1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap1-4.yaml)
[deployment1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment1-4.yaml)
[deployment-svc1-4](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment-svc1-4.yaml)

------

### Задание 2. Создать приложение с вашей веб-страницей, доступной по HTTPS 

1. Создать Deployment приложения, состоящего из Nginx.

[deployment2](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment2.yaml)

2. Создать собственную веб-страницу и подключить её как ConfigMap к приложению.

[configmap2](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap2.yaml)

3. Выпустить самоподписной сертификат SSL. Создать Secret для использования сертификата.

<img width="766" alt="generated certs" src="https://github.com/user-attachments/assets/f8c89d87-2ea0-4b04-bb36-4884cb272381">

[tls.crt](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/cert/tls.crt)
[tls.key](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/cert/tls.key)

<img width="682" alt="created secret" src="https://github.com/user-attachments/assets/d4ee7986-1c31-4a47-99af-bd35c8358802">

```bash
root@kuber:~/Kubernetes8-Config_Apps# kubectl get secret -o yaml
apiVersion: v1
items:
- apiVersion: v1
  data:
    tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUZxVENDQTVHZ0F3SUJBZ0lVTXdObURiYlc4NXdPR1gxMXp5enhDSWowVTlrd0RRWUpLb1pJaHZjTkFRRUwKQlFBd1pERUxNQWtHQTFVRUJoTUNRMVV4RHpBTkJnTlZCQWdNQmtkaGRtRnVZVEVNTUFvR0ExVUVCd3dEUTJsMApNUkV3RHdZRFZRUUtEQWhPWlhSdmJHOW5lVEVPTUF3R0ExVUVDd3dGVTNSMVpIa3hFekFSQmdOVkJBTU1DblJsCmMzUXViRzlqWVd3d0hoY05NalF4TVRJek1EZ3lOVE0wV2hjTk1qVXhNVEl6TURneU5UTTBXakJrTVFzd0NRWUQKVlFRR0V3SkRWVEVQTUEwR0ExVUVDQXdHUjJGMllXNWhNUXd3Q2dZRFZRUUhEQU5EYVhReEVUQVBCZ05WQkFvTQpDRTVsZEc5c2IyZDVNUTR3REFZRFZRUUxEQVZUZEhWa2VURVRNQkVHQTFVRUF3d0tkR1Z6ZEM1c2IyTmhiRENDCkFpSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnSVBBRENDQWdvQ2dnSUJBS0lOSEhVczd5RStWOVJnelVtWXpScmUKRDJra3RNRVZpeDZwU3FBbTF1MWFQQ1dlM24rT3RqZEFuS01NbDZhditJLzBYR245a0VkYzNTVDJ1VEtWNnJJRgp0Mzg3N3Fvd3E0cFhTSm1WcUx2YThvRkN1RTExaDNyNjIyMFg5SDFLNnR5dWcrUFp2eVhSTHVVaUJuKzBEejIvCncvUFErZ2xmTkNpN2orQnlVaitzNGhzMWtBODFFMS9qRXdBQnA4alMvRmgwZDdpVWN3UlJHZkVPanNCdS9DRmMKc3B3OFo3emlsTUJ1ZW1BU1pIM1ljYjE1cFJLOEFNN0tLMUYrRGgwZ1ZIV0ZVeDZOTGNUMmNVbTA0ZjQyVEUvagpmQkwxUHhxaFQxYUJONHJ1cld0bU83QUsrTU1laXJjMW9udldRZzBYOW9BVGRES3ZQd2FrampOblVaTkd3QzgvCi90eFFOd1pJMHFNSVlUYUVMRGorT1ViSkJMTGh3ZituWVNsQkIzYzA4b2lnQm1ZVkY1ZENLaFVkY1FHcHV6OWwKU2dyM25OU2IwQUJyQTZ0MldycFNYWVhwQlVjaHExckgwSW1UWWxrZ1RXb3J2NGMyUUtWNjRBY3k1VHUxMldJRgpnaEpKUGZxSHFnY3pwRzdCNW4vZlNwUEtYOWd2VDdYRmZyTVU2RGxVU01qTnZYcmlXSVJDcEpwcHZWWHByRWxtClM0bllNV0NUM3lqNVdVOVZRd0JDOUNaWDFwQnQwUWJLQU5YK2hUeU5IdmJyaERoRzYxS2Q2dDVkZ01KMk9VckkKT25FbFB0RS9GUEpQQlBrL0dvd1NmaVYyYUhEdlpjOVV2UHZSZndHZFdDb21GWGQ1Y1NFdnpZdzd0ektTL1FjTwpXRlZsdUNLZnIyeXRXL1QxdGphOUFnTUJBQUdqVXpCUk1CMEdBMVVkRGdRV0JCUzZ3c0hEN1krd2Vzbm0wMXQvCkUwMTYvRjk3dlRBZkJnTlZIU01FR0RBV2dCUzZ3c0hEN1krd2Vzbm0wMXQvRTAxNi9GOTd2VEFQQmdOVkhSTUIKQWY4RUJUQURBUUgvTUEwR0NTcUdTSWIzRFFFQkN3VUFBNElDQVFCcjBlYUNwbXFjNmY3M1oyYzRtd01vdEc2MQpmLzlGWnY2SmszbGVWV3dsaGUxdHZ4dnYyZGh4a1NzUWx3VThNelMwU2ZEMWdyOHdyRXAwNlYvdmk3cXMvTEUyCnZlT2JBVmZYMXFyUUhjY3E3U2Z6L3duMFJ2bFN3L3l5aFBLNkxncmlLMFBHU2FicFZXSFFCcGxXTm9tZjdqWFYKL0VCRnQzTStCUFZoQk5wb1M1SnZoQitzck04aHVOc2FTbWtGdndZL1FxeGEvM1hlMzVSMmFCR0xkVVlHdElGZwovYzRYV1l1VVpCTHRKZEFoZjE3OUtiQ3ZkQkhTY0pWY0R1Sm1FUktSYmJ2UEhlOW5BSDNSU3JlU0tkazRZME1xCm0zdDAwSGtqSTV3TWxZZDdOVUlWNEh1dnVldmYvNUt3UmhhQnEvNE1mMTBvY0dRenIwWUFhalcrZ3Vlc002UmwKTitEYjcrK1dGdUN3YUZXalE4UmNCNWRvWHhES2NOdU9la3ZvUnFpOGJ1c0haemI0ZUlVQVRyZmxJMWNSKy95VgpXbGdKTFc5S1QzOXZrSDRtVk5zYzAwbUx1SklzV1ZQTk5zTHVhaFBDVWVQTWZYSmdIWkZTZkJQMnJFcDhoK0V0CkNjSnhWNFNUMTRHZEl0bjJGUVpsNzQ0V1BBUlFwNkpLZHBsRFN2QktSU1puakljMjliRlowZWtyQWNSUWZsZFMKZU1qeFRUS0tOY3VmTWJXckhNUUZjUzJUMjFwMWlPU25iVm1wQ3JQeVZNZkNndnA2MEpjRXZEeWlxT240U3lWeApxRVdxajRieEROamtGSFB3TExESHhQYmkyR0l0OG9JNmkvTCtGZ2s3SHlhZW1SQXN3WnZodkpicXNXSlRNZTFpClJNOCtuZkpvYWF5Z0xsZVJxZz09Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    tls.key: LS0tLS1CRUdJTiBQUklWQVRFIEtFWS0tLS0tCk1JSUpRd0lCQURBTkJna3Foa2lHOXcwQkFRRUZBQVNDQ1Mwd2dna3BBZ0VBQW9JQ0FRQ2lEUngxTE84aFBsZlUKWU0xSm1NMGEzZzlwSkxUQkZZc2VxVXFnSnRidFdqd2xudDUvanJZM1FKeWpESmVtci9pUDlGeHAvWkJIWE4wawo5cmt5bGVxeUJiZC9PKzZxTUt1S1YwaVpsYWk3MnZLQlFyaE5kWWQ2K3R0dEYvUjlTdXJjcm9QajJiOGwwUzdsCklnWi90QTg5djhQejBQb0pYelFvdTQvZ2NsSS9yT0liTlpBUE5STmY0eE1BQWFmSTB2eFlkSGU0bEhNRVVSbngKRG83QWJ2d2hYTEtjUEdlODRwVEFibnBnRW1SOTJIRzllYVVTdkFET3lpdFJmZzRkSUZSMWhWTWVqUzNFOW5GSgp0T0grTmt4UDQzd1M5VDhhb1U5V2dUZUs3cTFyWmp1d0N2akRIb3EzTmFKNzFrSU5GL2FBRTNReXJ6OEdwSTR6CloxR1RSc0F2UC83Y1VEY0dTTktqQ0dFMmhDdzQvamxHeVFTeTRjSC9wMkVwUVFkM05QS0lvQVptRlJlWFFpb1YKSFhFQnFicy9aVW9LOTV6VW05QUFhd09yZGxxNlVsMkY2UVZISWF0YXg5Q0prMkpaSUUxcUs3K0hOa0NsZXVBSApNdVU3dGRsaUJZSVNTVDM2aDZvSE02UnV3ZVovMzBxVHlsL1lMMCsxeFg2ekZPZzVWRWpJemIxNjRsaUVRcVNhCmFiMVY2YXhKWmt1SjJERmdrOThvK1ZsUFZVTUFRdlFtVjlhUWJkRUd5Z0RWL29VOGpSNzI2NFE0UnV0U25lcmUKWFlEQ2RqbEt5RHB4SlQ3UlB4VHlUd1Q1UHhxTUVuNGxkbWh3NzJYUFZMejcwWDhCblZncUpoVjNlWEVoTDgyTQpPN2N5a3YwSERsaFZaYmdpbjY5c3JWdjA5YlkydlFJREFRQUJBb0lDQUFmN3p1RGIrWjBBNmdsUnlwTmJacEZ5CkNyN1NtdUFmWEZjQ25xVlV2SVo5aVZTT0p1YVFaSnhFditMTmlrUWhTOFRsMUU2NWVndjJBSVFqYnB6V0kzV3AKVC9EQ0JtQlduUURvQzNEYm5YQkIyV3d5V2d1ZzVHK1QzODhZWE1oMmNpczBvdFZOSXp4cytaczZ5YWl0ZVluQQpncG9tdThiTjdLOHNER3JSbndrNWpuc3FNS0o3S2l6eGJqeDBHR3pOcmpaNmFIS014N3pZOVFiNkFXRHNKdHBLCjY1YkVhQlQzY2I0b1F6bXI5a3ozNXFTZjAxamdyOGFBVWRHR1BSSkV4enh4K05ZYXZQeEU2dkJuYTdIZlFYTzMKSUtQN2RxUXNlZERvZFZEUU1QV0hXQjMwY0d5R2V1MkxSQ25ITk1KWjRhQk9OUTJSTWpEMDF2ZWoyYkpBakZQRAplUVdwMGVObmhrSGpOOU9HYUMzVkdPZGxlMnZpV3RIc2R5SWhXYlFEelo4M1RoYVBVdlI2Vm9ZZDJ2c25pYXRBCjBvSHlWRjZtbFpXdGovM2ZQVGdEMjE4WG1VMitMVFhtckNETjdoeG1oa1k3RmR3YTBDMW4rUHMwa1FMMFJvTncKcHJkWjNOS0tRMHNNK0Y2eWtKa0NKOXFwbkpMVG5hazh0TnB5d3l6OWhwTFVINW4xMTZnamlxQlNkNU54NEp2aAp3dHlNZE1odEpZRVFWY3EwaEluTlhtWmpCaFBwaVlPREE2T0wrNnBWc0JWWkRPK1IyUjdFOEtHSzcwbW0wZWN2Cm9VTmFzczNZVS9QVWdIUmtYclplajVDMkU0Q1czMjRVMVR5VWpOaHZ3UkZaMFNqR0VMMW9Xc1ZJbW01NUo1d28KRmNmYzFzSG5ZRUFxVUdwVFIrVHBBb0lCQVFEY1ZPTzd0N2dBYk5DdTNtOWN0M2QzS0R4VU9mS3ZXMm1wTHhOUwpzckxtRHBwRmx1WDRqN3ZZZ0pvZlRjeDdITjZHZTcvY3ZsOGxYVDZFUmR2ZE5LUW16TEd2dDZUbGtyUkxVbTBuClllODVheWxRTHg0WlVjbEpxNEdDVnRxZXoyZkpDZWFsd2o1VitNY2xVVGl5VzhLYzQva0Z3NDc0dldOZ1IvbjUKWmRpWlEyMUJJTW00QVNmbUZGQmtKeEdac2U5em0yUWtBWEk1aGFLOWo0ZEcxMDZzZWcxcjlOMzV0VUlNRG9JNApDQndqNmhGd1JwclB2K0VNazViVHkza3p5aHBiemZTeGxvaWl2L1NnSDByb01Ga1RRVk9XVTBMNkU4WHRmckZYClNSbUF3RzBUZXVpUHZvWTE2MEFXK3BKbWF3bkpRYXBXbUlsQy9JVzIramdyNGkyNUFvSUJBUUM4U082VklLTnMKd2hSd0YwT3lUKzE5a3EwNUZVQ0txOXg2RS8wSk0vamxLMVA4YW5uMExBN0VsY0xnWlp4Qktva3dWUjdiQkhCdQpleVFnaDFoelF3dk1ITWFXdGwyTnFzcUlKVVZqbjZnU2pyUWRCMWpKTHRQdk5RWURVTFl1dW8zSENESkF2SlNRCk4ydHg3N3RFNWlIZ0NSbGtva2J3UHBEc2x4clIvL01QVEhOVytzS2M5a3RieFFHbEJjL05pNlV1dFpmenRjSVQKalNUTlFMckZXNDJaUEdrKzhERWxac3BqbGh4aUhSV0ZRdGtrUFZrT3NYTkVoT1FiSXBqZ0p5Zmx3ZnF6V2ppegorY3d4Qm5tUlVsMDhWWG4rV29zSU5ZNW9STWVTRCszUUFDSGJQWWJlQUcybjNkaXJ5VWtyUEZCdFdoRWMxS2NxCjJyT0tESFBaRi9NbEFvSUJBUUN1TVRyK3ZRZnU1aGl5Tjg5cFNPOWRPR1ZCM2JKOWF0TUZXOTkyQVN1bzhLQ04KSmZqWTQ2SUtUOW9KcDdOakhmYmI4ZGhGQ0FrbS9Db2gzeTB0SEtJdXZxUTRIUU4wTU9EenI4MzJWZG9RMWlVSwpiTVhxRkp3RDcyRHJrQWsxaHhveGVlOXMyejMzTUVFWndyWUZaTUJlMDJtY1lmaVZ0UDF0TUZwMEQwNElGYU81CjJ0Yy83MElCQjh4cThleGJTNjdaQW1CUWl3Z29hL3UyekZPUjhVVVUzVVhoTk8yTnJ2enhsVUxrVTUwVDA4bEgKcjdwVFJ3c0FxMnFTTllxMEpETmtvMWF5VkNYZ0xjeVVEMGxrZWx6aCtVTEJWVUJkZitaNmxqQlVwc2xQM2xJZQpGWXpwb0NKeFhIVUY5Y0pxMEhNak54UVpkRzBJbGFhTmZCT090am1aQW9JQkFIVUxVV1RhMlR5dW1VM2s2R3hjClMySVlZQjV3RzZNWW13STRrcis1MHl1Qzk3NmQ0aG5ybVhLVE5vV1FKTVpOenVLQXg2R1c3TjJCSjBBaFl0YWsKQXgxcmRmZ1NmYTJuVWllNEk1NStqVmliNVZOMlViY2VxUmkyZVhwdUhoS0dYY1F3VUN6MkRkUm8zeDRBelFWVAptaE5QRkwzK011TDl3ZEdSVFZibUtRNkZrOTJxSEhpK2tySUZrYlFvSExuRjZYVlQ1WlhXazBMY0p0aUJPSm1mCkJETVIzc3NGUFVmbTBrRjkrejd5bllJdHdCWkxIS1dKb2dJaUtqckVFd3lreXFTRkpYZUF5bWIzRGZ3YjdrNXMKU1JGTWdYMmdnM1VpOWRmVGljdytvckwrb2cxTC9oN2JYVTlSRlhRVXVLdHIzd05iVGZBQ01ianVJMVhaejlwUQpqUkVDZ2dFQkFJVSsrSERranI5OWxHRTh6SjVPTXRtOEdQcitaNTk2NVNnUU0zR2lFVGFxSVUvL0E0YUU4SFBrCmZSUDU2QzRLdThCd0FIcEF5cFpoSFRjQVdnRTlaaDRXb2lndFdVWWJESCt5UlQ0TlEyMmN6UE93eDVmMk1hT0kKd1BXU0ZnQlNqTEQrRzI3UmpCZFhzODVoTXU2RWdPT0ZPMGNMUG94Ui9zQU5aWGVZZ1BVbkpKQXdvdHpuaFNXSQp4SThDaUZHZEovb1FaUkVCSjhWNUd3SUVmU2xYa0hpb1IranVKK0R4Q0gwZE12RkRnNlBZM2NmWEVMVURWMTdsCk5Jcm9OL2d3bDQ0azRURHdXWllWaS9mTXViR3YvNzcydVpveUdEbGJCK0dwTk54bHcwZjRSMjNDSExJSnU0VFIKNGZmSTY3eERyTFlSRzVFTG9xa2QxcUFKZlM1Sk9tRT0KLS0tLS1FTkQgUFJJVkFURSBLRVktLS0tLQo=
  kind: Secret
  metadata:
    creationTimestamp: "2024-11-23T08:32:53Z"
    name: my-app-secret-tls
    namespace: default
    resourceVersion: "224908"
    uid: 821c0986-eb90-4c3e-b2cb-7f6d3b26cecd
  type: kubernetes.io/tls
kind: List
metadata:
  resourceVersion: ""
```


4. Создать Ingress и необходимый Service, подключить к нему SSL в вид. Продемонстировать доступ к приложению по HTTPS. 

<img width="428" alt="hosts" src="https://github.com/user-attachments/assets/c35b96a6-b498-40cb-8cfc-a9fa565dfbd3">

<img width="414" alt="svc" src="https://github.com/user-attachments/assets/370d39c2-bac9-4a68-9dc5-bd4b0467391f">

<img width="412" alt="ingress" src="https://github.com/user-attachments/assets/7fb8193d-a54b-40f6-b795-f20c3f4a8e18">

<img width="413" alt="status objects" src="https://github.com/user-attachments/assets/074021ac-a3d1-44b8-9468-9e1628887bc4">

<img width="449" alt="curl https" src="https://github.com/user-attachments/assets/d783c491-f453-402b-b8a7-d323081c337f">

<img width="737" alt="browser check" src="https://github.com/user-attachments/assets/6e1f3e93-e962-4558-852c-e724117c29e6">

5. Предоставить манифесты, а также скриншоты или вывод необходимых команд.

[deployment2](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment2.yaml)

[configmap2](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/configmap2.yaml)

[deployment-svc2](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/deployment-svc2.yaml)

[ingress](https://github.com/sash3939/Kubernetes8-Config_Apps/blob/main/ingress.yaml)


------

### Правила приёма работы

1. Домашняя работа оформляется в своём GitHub-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода необходимых команд `kubectl`, а также скриншоты результатов.
3. Репозиторий должен содержать тексты манифестов или ссылки на них в файле README.md.

------

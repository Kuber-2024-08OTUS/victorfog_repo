# Выполнено ДЗ №4

 - [X] Основное ДЗ
 - [x] Задание со *

## В процессе сделано:
 - создание пользователей в кластере для работы в namespase "homework"
 - назначение привелегий
 - написание файла kubeconf

## Как запустить проект:
 - запускается minikube командой ``` minikube start --nodes 3 ```
 - включаем ingress контроллер на minikube ``` minikube addons enable ingress ```
 - из каталога проекта, с помощью утилиты kubectl создается namespace ``` kubectl create -f namespace.yaml ```
 - создание storageClass ``` kubectl create -f storageClass.yaml ```
 - создание запроса на использование храненища ``` kubectl create -f pvc.yaml ```
 - создания конфигурации для NGINX ``` kubectl create -f cm.yaml ```
 - создание пользователей monitoring и admin ``` kubectl create -f security.yaml ```
 - выставить срок действия учетной записи monitoring 24 сутки ``` kubectl create token cd --duration=24h > token``` 
 - после успешного создания namrspace, configMap, PersistentVolumeClaim 
 разворачивается deployment подами с окружением ``` kubectl create -f deployment.yaml ```
 - создаем сервис LoadBaalnser ```kubectl create -f service.yaml```
 - настраиваем service для ingress controller ``` kubectl create -f ingress-controller-lb.yaml ```
 - настройка сомого ингресс для перенаправления запросов основываясь на http URL ``` kubectl create -f ingress.yaml ```
 - включить тунелирование ``` minikube tunnel ```

## Как проверить работоспособность:
 - проверить созданное пространство имен можно командой ``` kubectl get namespace | grep homework ```
    ответом будет сообщение
    ```console
    homework               Active   45h
    ```
  - проверка созданного storageClass ``` kubectl get sc | grep stogarec-class-homework ```
    stogarec-class-homework   kubernetes.io/no-provisioner   Retain          WaitForFirstConsumer   false                  37m
  - проверка созданного PV, PVC ``` kubectl get pv && kubectl get pvc -n homework ```
    ```
    NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS              VOLUMEATTRIBUTESCLASS   REASON   AGE
    pv-homework   2Gi        RWO            Retain           Available           stogarec-class-homework   <unset>                          79s
    NAME           STATUS    VOLUME   CAPACITY   ACCESS MODES   STORAGECLASS              VOLUMEATTRIBUTESCLASS   AGE
    pvc-homework   Pending                                      stogarec-class-homework   <unset>                 79s
    ```
  - проверка созданнных конфигураций ```kubectl get cm -n homework```
    ```
    NAME               DATA   AGE
    cm-homework        1      21s
    ```
  - проверить созданные поды ``` kubectl get all --namespace homework | grep pod ```
    ``` console
    pod/dpl-webserver-667f89bd9b-2kfbd   1/1     Running   0          2d17h
    pod/dpl-webserver-667f89bd9b-cbxtk   1/1     Running   0          2d17h
    pod/dpl-webserver-667f89bd9b-q252d   1/1     Running   0          2d17h
    ```
    статус должен измениться на запущенные.
  - проверим что запустился service  ``` minikube service svc-webserver --url -n homework ```
    получим ссылку для проверки сервиса ``
    ``` console 
    😿  service homework/svc-webserver has no node port
    ❗  Services [homework/svc-webserver] have type "ClusterIP" not meant to be exposed, however for local development minikube allows you to access this !
    http://127.0.0.1:53268
    ❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
    ```
    проверим доступность ``` curl http://127.0.0.1:54587 ```    
    ```console
    My index html from HW ...........
    ```
  - проверим как работает ingress controller ``` kubectl get svc ingress-nginx-controller-lb -n ingress-nginx ```
    ```
    NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
    ingress-nginx-controller-lb   LoadBalancer   10.107.57.45   10.107.57.45   80:31116/TCP,443:31061/TCP   29m
    ```
  - проверка нормально созданного ingress ``` kubectl get ingress -n homework ```
    ```
    NAME            CLASS   HOSTS           ADDRESS        PORTS   AGE
    ing-webserver   nginx   homework.otus   192.168.49.2   80      24m
    ```
  - проверка надо прописать в hosts файл строку вида ip адресс из вывода ``` kubectl get svc ingress-nginx-controller-lb -n ingress-nginx ```
      поля    external-it и имени сервиса из конфигурации  ``` kubectl get ingress -n homework -o yaml | grep host ```
  - host: homework.otus
    ``` file: ./hosts
    10.107.57.45 homework.otus
    ```
  - проверяем как рабоатет ``` curl http://homework.otus ```
      ``` console
      My index html from HW 4.  POD name - dpl-webserver.........
      ```
      ``` curl  http://homework.otus/homepage ```
      ``` console
      My index html from HW 4.  POD name - dpl-webserver.........
      ```
      ``` curl  http://homework.otus/conf/file ```
      ``` console
        randomkey: value
        secondrandomkey: secondvalue
      ```
# проверка учетных записей 
  - проверим какие записи созданы кроме default ``` kubectl get sa -n homework | grep -v default ```
      ```
      NAME         SECRETS   AGE
      cd           1         10h
      monitoring   1         10h
      ```
  - прооверим что создались роль для пользователя monitoring ``` kubectl get clusterroles -A | grep homework ```
      ```
      metrics-read-homework 2024-09-25T18:34:24Z
      ```
  - проверка присвоение роли привелегий ``` kubectl get rolebinding,clusterrolebinding | grep homework ```
     ```
     clusterrolebinding.rbac.authorization.k8s.io/monitiring_user_homework 
     ```
  - проверка на запуск deployment от имени пользователя metrics ``` kubectl get deployment dpl-webserver -n homework -o jsonpath="{.spec.template.spec.serviceAccount}" ```
     ```
     monitoring
     ```
# создание файла ``` kubeconf ```
  - для подключения к кластеру у пользователя должен быть файл ```kubeconfig```
  в котором содержаться параметры для подключения к кластеру имя пользователя сертификат и токен
  - получить токен для пользователя CD и потом вставить в файл kubeconf в поле token:
    ``` kubectl get secret $(kubectl get sa cd -n homework -o jsonpath='{.secrets[0].name}') -n homework -o jsonpath='{.data.token}' | base64 -Dd ```
  - получить сертификат 
    ``` kubectl get secret cd-secrets-homework -n homework -o jsonpath="{.data.ca\.crt}" | base64 -Dd | tr -d '\r\n' ```
    и также вставить его в соответствуюзее поле "certificate-authority-data:" файла kubeconf
  - получить адрес кластера ``` kubectl config view -o jsonpath='{.clusters[0].cluster.server}' ```
    полученные данные вписать в поле "server:" файла kubeconf

## PR checklist:
  - [kubernetes-volumes] Выставлен label с темой домашнего задания

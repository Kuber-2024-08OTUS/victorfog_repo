# Выполнено ДЗ №2

 - [X] Основное ДЗ
 - [x] Задание со *

## В процессе сделано:
 - создание persistenvolumeclaim "создание запроса на использование хранилища"
 - создние сужности configMap с конфигурацией nginx и рандомными полями ключ-значение
 - 

## Как запустить проект:
 - запускается minikube командой ``` minikube start --nodes 3 ```
 - включаем ingress контроллер на minikube ``` minikube addons enable ingress ```
 - из каталога проекта, с помощью утилиты kubectl создается namespace ``` kubectl create -f namespace.yaml ```
 - создание запроса на использование храненища ``` kubectl create -f pvc.yaml ```
 - создания конфигурации для NGINX ``` kubectl create -f cm.yaml ```
 - после успешного создания namrspace, configMap, PersistentVolumeClaim 
 разворачивается deployment подами с окружением ``` kubectl creale -f deployment.yaml ```
 - создаем сервис LoadBaalnser ```kubectl creale -f service.yaml```
 - настраиваем service для ingress controller ``` kubectl create -f ingress-controller-lb.yaml ```
 - настройка сомого ингресс для перенаправления запросов основываясь на http URL
 - включить тунелирование ``` minikube tunnel ```


## Как проверить работоспособность:
 - проверить созданное пространство имен можно командой ``` kubectl get namespace | grep homework ```
    ответом будет сообщение 
    ```console
    homework               Active   45h
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
    My index html from HW 2
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

  - проверка надо прописать в hosts файл строку вида ip адресс из вывода ``` kubectl get svc ingress-nginx-controller-lb -n ingress-nginx ``` поля    external-it и имени сервиса из конфигурации  ``` kubectl get ingress -n homework -o yaml | grep host ```
  ```
  - host: homework.otus
  ```
  ``` file: ./hosts
  10.107.57.45 homework.otus
  ```
  - проверяем как рабоатет 
  curl http://homework.otus
  curl http://homework.otus/homepage
в обоих случаях мы увидим 
```
My index html from HW 2
```
## PR checklist:
  - [kubernetes-networks] Выставлен label с темой домашнего задания

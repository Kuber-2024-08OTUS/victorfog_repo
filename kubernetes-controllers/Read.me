# Выполнено ДЗ №2

 - [X] Основное ДЗ
 - [x] Задание со *

## В процессе сделано:
 - был создан deployment разворачивающий 3 контейнера и следащий за их состоянием
 - научились обновлять приложение
 - настроили probes для проверти работоспособности приложения

## Как запустить проект:
 - запускается minikube командой ``` minikube start --nodes 3 ```
 - из каталога проекта, с помощью утилиты kubectl создается namespace ``` kubectl create -f namespace.yaml ```
 - после успешного создания namrspace, разворачивается deployment подами с окружением ``` kubectl creale -f deployment.yaml ```

## Как проверить работоспособность:
 - проверить созданное пространство имен можно командой ``` kubectl get namespace | grep homework ```
    ответом будет сообщение 
    ```console
    homework               Active   45h
    ```

  - проверить созданные поды ``` kubectl get all --namespace homework | grep pod ```
    ```
    pod/dpl-webserver-667f89bd9b-9ljmt   0/1     Pending   0          10s
    pod/dpl-webserver-667f89bd9b-jmkwv   0/1     Pending   0          10s
    pod/dpl-webserver-667f89bd9b-pq8c5   0/1     Pending   0          10s
    ```
    как видно поды создались, но не запустились. - Это связанно с тем, что у нас выставленно требование наличие у нод метки homework с значением true
    добавим LABEL homework=true на все ноды ```kubectl label node $(kubectl get nodes | awk /mini/'{print $1}') homework=true```

  - проверить созданные поды ``` kubectl get all --namespace homework | grep pod ```

    в ответ вы получите вывод 
    ``` console
    pod/dpl-webserver-667f89bd9b-2kfbd   1/1     Running   0          2d17h
    pod/dpl-webserver-667f89bd9b-cbxtk   1/1     Running   0          2d17h
    pod/dpl-webserver-667f89bd9b-q252d   1/1     Running   0          2d17h
    ```
    статус должен измениться на запущенные.

  - проверим что все запустилось 
    получим ссылку для проверки сервиса
    ``` console 
    😿  service homework/svc-webserver has no node port
    ❗  Services [homework/svc-webserver] have type "ClusterIP" not meant to be exposed, however for local development minikube allows you to access this !
    http://127.0.0.1:53268
    ❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
    ```
    проверим доступность ``` curl http://127.0.0.1:54587 ```    
    ```console
    My index html from HW 1
    ```
  - проверка rollingupdate
    Изменим параметры в deployment.yaml и запустим процесс обновления.
    ```Deployment.yaml
    .... echo "My index html from HW 1" > /init/index.html....
    ````
    заменим цифру 1 на 2 и выполним команду ``` kubectl apply -f deployment.yaml ```
    ```
    если в процессе обновления просмотреть что происходит с подами можно увиидеть как происходит обновление 
    при этом выключенным будет только 1 под из 3х согласно политики обновления указанной в манифесте
    ```
    NAMESPACE     NAME                               READY   STATUS            RESTARTS       AGE
    homework      dpl-webserver-5d7985877f-57ppc     0/1     PodInitializing   0              2s
    homework      dpl-webserver-5d7985877f-69kcx     1/1     Running           0              17s
    homework      dpl-webserver-5d7985877f-bnwr2     1/1     Running           0              17s
    ```

## PR checklist:
  - [kubernetes-controllers] Выставлен label с темой домашнего задания

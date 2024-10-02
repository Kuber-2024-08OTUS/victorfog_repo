# Выполнено ДЗ №2

 - [X] Основное ДЗ


## В процессе сделано:
 - установлен шаблонезатор helm 
 - перенесен проект в шаблонизатор
 - установка mysql (был изменен под arm архитектуру)
 - снаписан helmfile для запуска нескольких чартов

## Как запустить проект:
 - запускается minikube командой ``` minikube start --nodes 3 ```
 - включаем ingress контроллер на minikube ``` minikube addons enable ingress ```
 - из каталога ./homework выполнить команду ``` helm install helmapp . --namespace=homework ```
 - настройка сомого ингресс для перенаправления запросов основываясь на http URL
   включить тунелирование ``` minikube tunnel ```

 # homework2
 - добаыить bitnami в repo ```helm repo add bitnami	https://charts.bitnami.com/bitnami```
 - произвести инициализацию и установить требуемые плагины ```helmfile init```
 - запустить развертывание kafka из созданного helfile
 ``` helmfile apply ```

## Как проверить работоспособность:
 - проверить созданное пространство имен можно командой ``` kubectl get namespace | grep homework ```
    ответом будет сообщение 
    ```console
    homework               Active   45h
    ```
  - проверить созданные поды ``` kubectl get all --namespace homework | grep pod ```
    ``` console
    pod/dpl-webserver-6c75675c4f-2hfkt    1/1     Running   0          18m
    pod/dpl-webserver-6c75675c4f-7t6md    1/1     Running   0          18m
    pod/dpl-webserver-6c75675c4f-c5n29    1/1     Running   0          18m
    pod/helmapp-1-mysql-f4784788b-52d9b   1/1     Running   0          18m
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
  ну или просто
  ```
  curl --header "Host: homework.otus" "http://127.0.0.1"
  curl --header "Host: homework.otus" "http://127.0.0.1/homepage"
  curl --header "Host: homework.otus" "http://127.0.0.1//conf/file"
  ```
# homework2
- посмотреть количество запущенных контроллеров в namespace dev ```kubectl get pod -n dev | grep kafka-dev-cont | wc -l```
- посмотреть количество запущенных брокеров в dev ```kubectl get pod -n dev | grep kafka-dev-bro | wc -l```
- посмотреть количество запущенных контроллеров в namespace prod ```kubectl get pod -n prod | grep kafka-prod-cont | wc -l```
- посмотреть количество запущенных брокеров в prod ```kubectl get pod -n dev | grep kafka-dev-prod | wc -l```

## PR checklist:
  - [kubernetes-templating] Выставлен label с темой домашнего задания

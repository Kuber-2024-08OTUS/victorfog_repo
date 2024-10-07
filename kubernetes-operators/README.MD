# Выполнено ДЗ №2

 - [X] Основное ДЗ
 - [x] Задание со *

## В процессе сделано:
 - рассматривается сосдание custom resource defenition
 - создание объекта собственного CRD

## Как запустить проект:
 - запускается minikube командой ``` minikube start --nodes 3 ```
 - включаем ingress контроллер на minikube ``` minikube addons enable ingress ```
 - из каталога проекта, с помощью утилиты kubectl создается namespace ``` kubectl create -f namespace.yaml ```
 - создвние аккаунта ```kubectl create -f serviceaccaunt.yaml```
 - создание кластерной роли ```kubectl create -f clusterRole.yaml```
 - связываем кластерную роль с аккаунтом ```kubectl create -f binding.yaml```
 - создание deployment ```kubectl create -f deployment.yaml```
 - создание CRD ```kubectl create -f crd.yaml```
 - создание объекта созданого ранее CRD ```kubectl create -f mysql-custom-obgects.yaml```


## Как проверить работоспособность:
 - проверить созданное пространство имен можно командой ``` kubectl get namespace | grep homework ```
    ответом будет сообщение 
    ```console
    homework               Active   45h
    ```
  - проверить созданные поды ``` kubectl get all --namespace homework | grep pod ```
    ``` console
    mysql-operator-ff8886c4b-5fwkv  1/1     Running   0          2d17h
    ```
    статус должен измениться на запущенные.
  - проверка что создался CRD ```kubectl describe crd mysqls.otus.homework```
  - проверка созданного лбъекта по из нашего CRD ```kubectl describe db -n homework | grep Kind```
  ``` console
  Kind:         MySQL
  ```

## PR checklist:
  - [kubernetes-operators] Выставлен label с темой домашнего задания

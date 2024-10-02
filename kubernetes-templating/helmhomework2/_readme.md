# File read.me

для развертывания dev выполнить ```helmfile -e dev apply```
для развертывания prod выполнить ```helmfile -e prod apply```

можно изменить имя namespace добавив параметр ```-n <namespace>``` пример для prod ```helmfile -n production -e dev apply```
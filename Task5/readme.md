### Коментарии к файлу non-admin-api-allow.yaml:
Политика non-admin-api-allow разрешает обмен трафиком между pod-ами, у которых role=back-end-api и role=front-end.
Политика admin-api-allow разрешает обмен трафиком между pod-ами, у которых role=admin-back-end-api и role=admin-front-end.

### Cоздадим 4 сервиса (пода) с соответствующими метками:
kubectl run front-end-app --image=nginx --labels role=front-end --expose --port 80
kubectl run back-end-api-app --image=nginx --labels role=back-end-api --expose --port 80
kubectl run admin-front-end-app --image=nginx --labels role=admin-front-end --expose --port 80
kubectl run admin-back-end-api-app --image=nginx --labels role=admin-back-end-api --expose --port 80

### Применяем созданные политики:
kubectl apply -f non-admin-api-allow.yaml

### Тест:
Проверяем, что трафик есть между сервисами, для которых он разрешён, но его нет между сервисами, для которых он запрещён. Для этого используйте команду:

kubectl run test-$RANDOM --rm -i -t --image=alpine -- sh
/ # wget -qO- --timeout=2 http://apiserver

# 1. Запуск Minikube
minikube start

# 2. Создание namespace 'dev'
kubectl create namespace dev


# 3. Применение всех конфигураций (из директории Task4)
kubectl apply -f users-service-accounts.yaml
kubectl apply -f cluster-admin-role.yaml
kubectl apply -f developer-role.yaml
kubectl apply -f security-auditor-role.yaml
kubectl apply -f admin-role-binding.yaml
kubectl apply -f dev-role-binding.yaml
kubectl apply -f auditor-role-binding.yaml

# 4. Проверка прав (должны возвращать 'yes' где указано)
echo "Проверка admin-user (должен иметь полный доступ):"
kubectl auth can-i '*' '*' --as=system:serviceaccount:default:admin-user

echo "Проверка dev-user (права в namespace dev, должно вернуть: yes yes):"
kubectl auth can-i create pods -n dev --as=system:serviceaccount:default:dev-user
kubectl auth can-i delete deployments -n dev --as=system:serviceaccount:default:dev-user

echo "Проверка auditor-user (только чтение, должно вернуть: yes no):"
kubectl auth can-i get secrets -n dev --as=system:serviceaccount:default:auditor-user
kubectl auth can-i create pods -n dev --as=system:serviceaccount:default:auditor-user  

# 5. Очистка (если нужно начать заново)
kubectl delete -f . --ignore-not-found
kubectl delete namespace dev
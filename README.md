# com.stouffcapital.openfaas


1. `kubectl apply -f https://raw.githubusercontent.com/openfaas/faas-netes/master/namespaces.yml`
1. `helm repo add openfaas https://openfaas.github.io/faas-netes/`
1. `helm repo update`
1. `PASSWORD=$(head -c 12 /dev/urandom | shasum| cut -d' ' -f1)`
1. `kubectl -n openfaas create secret generic basic-auth --from-literal=basic-auth-user=admin --from-literal=basic-auth-password="$PASSWORD"`
1. `helm upgrade openfaas --install openfaas/openfaas --namespace openfaas --set basic_auth=true --set functionNamespace=openfaas-fn --set ingress.enabled=true`
1. `kubectl --n openfaas edit ing openfaas-ingress`
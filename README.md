# Kubernetes

# KUBERNETES 

1º Instalar o docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
Usar docker sem ser root
```
sudo usermod -aG docker ${USER}
```

2º Instalar o Kubectl

[Tutorial Oficial:](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

Autocomplete Kubectl: 
```
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null
```
3º Instalar o MiniKube
[Tutorial Oficial:](https://minikube.sigs.k8s.io/docs/start/)


## MiniKube Comandos básicos
```
minikube start

minikube stop

minikube delete

minikube delete --all
```

### Atividade 01 - Criando meu primeiro cluster

```
kubectl cluster-info

kubectl get nodes

kubectl get pods

kubectl get pods -n <namespaces>

kubectl get pods -A -o wide

kubectl get namespaces

kubectl describe pod etcd-minikube -n kube-system
```

### Criando meu primeiro Pod
* Abordagem Imperativa: 
```
kubectl run meunginx --image=nginx 
```

* Deletar Pod
```
?
```

* Abordagem Declarativa, utilizando manifesto yaml
Criar um arquivo com a extensão .yaml, exemplo: meu-pod.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx-container
    image: nginx
```

### Exportando manifesto de um Pod

* Salva manifesto de um Pod
```
kubectl get pod my-pod -o yaml > my-pod.yaml
```

* Salva manifesto sem informações específicas do cluster
```
kubectl get pod my-pod -o yaml --export  > my-pod.yaml
```

### Criando Namespaces

K8s usa namespaces para organizar objetos no cluster através de uma divisão lógica (como se fosse uma pasta).

Por padrão, kubectl interage com o namespace padrão (default).
Para usar um namespace específico (diferente do padrão), pode-se usar a flag --namespace=<nome>, ou ainda -n <nome>. 
Para interagir com todos os namespaces, pode-se passar a flag --all-namespaces ou -A para o comando.


* Criar um novo namespaces
```
kubectl create namespace dev

kubectl create namespace teste
```

1. Listar os namespaces?
```

```
2. Remover os namespaces?
```

```
* Filtrar Pods por namespace 
```
kubectl get pods --namespace=teste
kubectl get pods -n teste
```

3. Listar pods de todos os namespaces?
```

```


### Labels

[Referências](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)

Um Label é um par chave-valor do tipo string.
Todos os recursos/objetos K8s podem ser rotulados.

* Equality-based requirement
	environment = production
	tier != frontend
* Set-based requirement
	environment in (production, qa)
	tier notin (frontend, backend)

* Mostrar labels dos recursos:
```
kubectl get pods --show-labels
```
* Deletar Pods que têm label run=myapp
```
kubectl delete pods -l environment=production,tier=frontend
kubectl get pods -l 'environment in (production),tier in (frontend)'
```
*Atribuir label
```
kubectl label deployment nginx-deployment tier=dev
```

### ReplicaSet

[Referências](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/)

A finalidade de um ReplicaSet é manter um conjunto estável de réplica de pods em execução a todo momento. 
Na teoria: É frequentemente usado para garantir a disponibilidade de um número especificado de Pods idênticos.
* Na prática: É raramente utilizado diretamente.

* Exemplo:
```
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```
	
*Criar ReplicaSet
```
kubectl create -f rs-frontend.yaml
```

*Listar ReplicaSets e Pods
```
kubectl get rs
kubectl get pods -o wide
```

*Mostrar detalhes do ReplicaSet
```
kubectl describe rs frontend
```

*Escalar um ReplicaSet
```
kubectl scale --replicas 3 rs frontend
kubectl scale --replicas 1 rs frontend
```

*Deletar um ReplicaSet
```
kubectl delete rs frontend
```

*Deletar todos os Pods e ReplicaSets
```
kubectl delete pod,rs --all
```

### Deployment

Um Deployment fornece atualizações declarativas para Pods e ReplicaSet.

Você descreve um estado desejado em um Deployment e a Deployment Controlador altera o estado real para o estado desejado a uma taxa controlada. 
Você pode definir implantações para criar novos ReplicaSets ou remover implantações existentes e adotar todos os seus recursos com novas implantações.

* Vantagens:
Escalabilidade: com um Deployment, pode-se especificar o número de réplicas desejado e o Deployment vai criar ou remover Pods até alcançar o número desejado.
Atualizações: é possível alterar a imagem de um container para uma nova versão e o Deployment vai gradualmente substituir os containers para a nova versão (evita downtime).
Self-healing: se um dos Pods for acidentalmente destruído, o Deployment vai imediatamente iniciar um novo Pod para substituí-lo.

	
*Exemplo
	
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```
	

* Criar Deployment
kubectl create deployment http-deployment --image=nginx

* Listar Pods e Deployments
kubectl get pods
kubectl get deployments

* Aumentar a quantidade de réplicas 
kubectl scale --replicas 3 deployment http-deployment

* Diminuir a quantidade de réplicas 
kubectl scale --replicas 2 deployment http-deployment

* Listar os deployments
kubectl get deployments

* Mostrar detalhes de um deployment
kubectl describe deployment http-deployment

* Nova versão da aplicação
	

Criar Deployment
kubectl create deployment nginx-deployment --image=nginx:1.7.9

Listar Pods, Deployments e ReplicaSets
kubectl get pods
kubectl get deployments
kubectl get rs

* Escalar o Deployment
kubectl scale --replicas 3 deployment nginx-deployment

* Atualizar o Deployment com uma nova versão da aplicação
kubectl set image deployment/nginx-deployment nginx=nginx:1.91 --record

* Mostrar detalhes do Deployment
kubectl describe deployment nginx-deployment

* Verificar status da atualização
kubectl rollout status deployment.v1.apps/nginx-deployment

* Listar Deployments
kubectl get deployments

* Em caso de erro, desfazer a atualização
kubectl rollout undo deployment.v1.apps/nginx-deployment

* Mostrar detalhes do Deployment
kubectl describe deployment nginx-deployment

* Atualizar o Deployment com a versão correta da aplicação
kubectl set image deployment/nginx-deployment nginx=nginx:1.9.1 --record

* Verificar status da atualização
kubectl rollout status deployment.v1.apps/nginx-deployment

	
### Service
	



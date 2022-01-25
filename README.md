# Kubernetes

# KUBERNETES 

1º Instalar o Kubectl

Tutorial: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

Autocomplete Kubectl: kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null

2º Instalar o MiniKube
https://minikube.sigs.k8s.io/docs/start/


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

kubectl create namespace dev
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

*Criar ReplicaSet
```
kubectl create -f busybox-rs.yaml
```

*Listar ReplicaSets e Pods
```
kubectl get rs
kubectl get pods -o wide
```

*Mostrar detalhes do ReplicaSet
```
kubectl describe rs/busybox
```

*Escalar um ReplicaSet
```
kubectl scale --replicas 3 rs busybox
kubectl scale --replicas 1 rs busybox
```

*Deletar um ReplicaSet
```
kubectl delete rs busybox
```

*Deletar todos os Pods e ReplicaSets
```
kubectl delete pod,rs --all
```

### Deployment

Um Deployment fornece atualizações declarativas para Pods e ReplicaSet.

Você descreve um estado desejado em um Deployment e a Deployment Controlador altera o estado real para o estado desejado a uma taxa controlada. 
Você pode definir implantações para criar novos ReplicaSets ou remover implantações existentes e adotar todos os seus recursos com novas implantações.






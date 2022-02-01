# Kubernetes

# KUBERNETES COM MINIKUBE (Single Node)

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
    ports:
       - containerPort: 80
```
```
kubectl port-forward pod/meupod 8080:80
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

[Saiba mais sobre manifesto](https://medium.com/@sujithar37/understanding-the-kubernetes-manifest-97f44acc2cb9)

### Criando Namespaces

K8s usa namespaces para organizar objetos no cluster através de uma divisão lógica (como se fosse uma pasta).


Por padrão, kubectl interage com o namespace padrão (default). 
Para usar um namespace específico, diferente do padrão, pode-se usar a flag --namespace=nome, ou ainda -n nome. 

Para interagir com todos os namespaces, pode-se passar a flag --all-namespaces ou -A para o comando.


* Criar um novo namespaces

```
kubectl create namespace dev

kubectl create namespace teste
```

1. Listar os namespaces?

```
?

```
2. Remover os namespaces?

```
?
```

* Filtrar Pods por namespace 

```
kubectl get pods --namespace=teste
kubectl get pods -n teste
```

3. Listar pods de todos os namespaces?

```
?
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
* Na prática: É raramente utilizado diretamente. Motivo: versionamento.

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

1. Exercicio Versionamento:
	Altera a versão da image no seu manifesto e após isso verifique se os pods foram atualizados após você aplicar essa alteração.
 
	
	
### Deployment

Um Deployment fornece atualizações declarativas para Pods e ReplicaSet.

Você descreve um estado desejado em um Deployment e a Deployment Controlador altera o estado real para o estado desejado a uma taxa controlada. 
Você pode definir implantações para criar novos ReplicaSets ou remover implantações existentes e adotar todos os seus recursos com novas implantações.

* Vantagens:
Escalabilidade: com um Deployment, pode-se especificar o número de réplicas desejado e o Deployment vai criar ou remover Pods até alcançar o número desejado.
Atualizações: é possível alterar a imagem de um container para uma nova versão e o Deployment vai gradualmente substituir os containers para a nova versão (evita downtime).
Self-healing: se um dos Pods for acidentalmente destruído, o Deployment vai imediatamente iniciar um novo Pod para substituí-lo.

	
* Exemplo:
	
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
        image: nginx
        ports:
        - containerPort: 80
```
	

* Criar Deployment, abordagem imperativa

```
kubectl create deployment http-deployment --image=nginx
```
	
* Listar Pods, ReplicaSet e Deployments

```
kubectl get pods
kubectl get rs
kubectl get deployments
```

* Aumentar a quantidade de réplicas 

```
kubectl scale --replicas 3 deployment http-deployment
```
	
* Diminuir a quantidade de réplicas 

```
kubectl scale --replicas 2 deployment http-deployment
```
	
* Listar os deployments

```
kubectl get deployments
```
	
* Mostrar detalhes de um deployment

```
kubectl describe deployment http-deployment
```

* Nova versão da aplicação
	
#### ATIVIDADE PRÁTICA
	
* Criar Deployment do nginx 1.20 utilizando um arquivo de manifesto e com 2 réplicas.

```
?
```
	
* Listar Pods, ReplicaSet e Deployments e verificar se está tudo OK.

```
kubectl get pods
kubectl get rs
kubectl get deployments
```

* Escalar o Deployment com ou comando abaixo ou através do arquivo de manifesto
	
```
kubectl scale --replicas 3 deployment <nome_do_deployment>
```

* Atualizar o Deployment com uma nova versão da aplicação. Consultar no Docker Hub uma versão do nginx diferente da atual.
	
Você pode utilizar como exemplo o comando abaixo ou alterar o arquivo de manifesto.

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.91 --record
```
	
* Mostrar detalhes do Deployment

```
kubectl describe deployment nginx-deployment
```
	
* Verificar status da atualização

```
kubectl rollout status deployment nginx-deployment
```
	
* Listar Deployments

```
kubectl get deployments
```


* Simule uma situação de erro, por exemplo, coloque uma versão inexistente do nginx. Desfazer a atualização

```
kubectl rollout undo deployment.v1.apps/<nome_do_deployment>
```

* Mostrar detalhes do Deployment

```
kubectl describe deployment nginx-deployment
```

* Atualizar o Deployment com a versão correta da aplicação

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.21 --record
```
	
* Verificar status da atualização

```
kubectl rollout status deployment/<nome_do_deployment>
```
	
* Exibir historico de deployments

```
kubectl rollout history deployment/<nome_do_deployment>
```	
	
### Service
	
[Referências](https://kubernetes.io/pt-br/docs/tutorials/kubernetes-basics/expose/expose-intro/)
	
Um serviço no Kubernetes é uma abstração que define um conjunto lógico de Pods e uma política pela qual acessá-los.
O conjunto de Pods selecionados por um Serviço é geralmente determinado por um seletor de rótulos LabelSelector.
	
Embora cada Pod tenha um endereço IP único, estes IPs não são expostos externamente ao cluster sem um Serviço. Serviços permitem que suas aplicações recebam tráfego. Serviços podem ser expostos de formas diferentes especificando um tipo type na especificação do serviço ServiceSpec:

* ClusterIP (padrão) - Expõe o serviço sob um endereço IP interno no cluster. Este tipo faz do serviço somente alcançável de dentro do cluster.
* NodePort - Expõe o serviço sob a mesma porta em cada nó selecionado no cluster usando NAT. Faz o serviço acessível externamente ao cluster usando <NodeIP>:<NodePort>. Superconjunto de ClusterIP.
* LoadBalancer - Cria um balanceador de carga externo no provedor de nuvem atual (se suportado) e assinala um endereço IP fixo e externo para o serviço. Superconjunto de NodePort.
* ExternalName - Expõe o serviço usando um nome arbitrário (especificado através de externalName na especificação spec) retornando um registro de CNAME com o nome. Nenhum proxy é utilizado. Este tipo requer v1.7 ou mais recente de kube-dns.

	
![image](https://user-images.githubusercontent.com/66180145/151550260-9f6b2264-6701-4b05-a4bd-12a8f6a15d1b.png)

* Exemplo de Service NodePort:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
spec:
  type: ?
  selector:
    app: ?
  ports:
    - protocol: TCP
      port: ?
      targetPort: ?
```
	
	
* Exemplo de Service LoadBalancer:

```
apiVersion: v1
kind: Service
metadata:
  name: mariadb-service
spec:
  type: LoadBalancer
  selector:
    app: mariadb-wp
  ports:
    - protocol: TCP
      port: 3306
      targetPort: 3306	
```


# KUBERNETES COM KUBEADM (Cluster)


## Instalando um Cluster Kubernetes utilizando o Kubeadm

__Seguindo essa documentação/tutorial você conseguirá criar um cluster Kuberntes.__

* SO:

 __Ubuntu 18.04 LTS__ ou __Ubuntu 20.04 LTS__.

Nesse laboratório vamos criar um nó master e dois worker node.

### Definições:

|Função|IP|OS|RAM|CPU|
|----|----|----|----|----|
|Master|192.168.1.100|Ubuntu 18.04|2G|2|
|Worker1|192.168.1.101|Ubuntu 18.04|1G|1|
|Worker2|192.168.1.101|Ubuntu 18.04|1G|1|

### Executar no Master e nos Workers

##### Faça login com o usuário `root` 

```
sudo su -
```

Execute todos os comandos como usuário root, a menos que especificado de outra forma

##### Desabilitar Firewall

```
ufw disable
```

##### Desabilitar swap
```
swapoff -a; sed -i '/swap/d' /etc/fstab
```

##### Atualizar as configurações do sysctl para a rede Kubernetes
```
cat >>/etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```

##### Instalar docker engine
Alternativamente, você pode fazer a instalação através do script de instalação
```
  apt install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
  add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
  apt update
  apt install -y docker-ce containerd.io

```
### Kubernetes Setup
##### Adicionar repositorio ao apt Apt
```

  curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
  echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list

```
##### Instalar componentes do Kubernetes 
```
apt update && apt install -y kubeadm=1.18.5-00 kubelet=1.18.5-00 kubectl=1.18.5-00
```


## Somente no MASTER
##### Inicializar Cluster Kubernetes 
Atualize o comando abaixo com o endereço IP do Master
```
kubeadm init --apiserver-advertise-address=172.16.16.100 --pod-network-cidr=192.168.0.0/16  --ignore-preflight-errors=all
```
##### Deploy network
[Saiba mais](https://kubernetes.io/pt-br/docs/concepts/cluster-administration/networking/#:~:text=Kubernetes%20%C3%A9%20basicamente%20o%20compartilhamento,tentem%20utilizar%20as%20mesmas%20portas.)

Escolha somente um tipo abaixo:

* Flannel

```
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

* Calico

```
kubectl --kubeconfig=/etc/kubernetes/admin.conf create -f https://docs.projectcalico.org/v3.14/manifests/calico.yaml
```

##### Consultar comando para adicionar os Workers ao Cluster 
```
kubeadm token create --print-join-command
```

##### Para poder executar comandos kubectl como usuário não root
Para poder executar comandos junto ao cluster Kubernetes 
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Workers
##### Adicionar ao Cluster
Use o comando `kubeadm token create --print-join-command` para gerar o token de acesso ao cluster.

## Verificar o Cluster (no Master)

```
kubectl get nodes
```

```
kubectl get cs
```
Muito bem, você acaba de concluir a configuração de um cluster Kubernetes. 

# Kubernetes


## Sumário

1. [KUBERNETES COM MINIKUBE (Single Node)](https://github.com/patrickjcardoso/Kubernetes#kubernetes-com-minikube-single-node)
2. [KUBERNETES COM KUBEADM (Cluster)](https://github.com/patrickjcardoso/Kubernetes#kubernetes-com-kubeadm-cluster)

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

* __ClusterIP (padrão)__ - Expõe o serviço sob um endereço IP interno no cluster. Este tipo faz do serviço somente alcançável de dentro do cluster.
* __NodePort__ - Expõe o serviço sob a mesma porta em cada nó selecionado no cluster usando NAT. Faz o serviço acessível externamente ao cluster usando <NodeIP>:<NodePort>. Superconjunto de ClusterIP.
* __LoadBalancer__ - Cria um balanceador de carga externo no provedor de nuvem atual (se suportado) e assinala um endereço IP fixo e externo para o serviço. Superconjunto de NodePort.
* __ExternalName__ - Expõe o serviço usando um nome arbitrário (especificado através de externalName na especificação spec) retornando um registro de CNAME com o nome. Nenhum proxy é utilizado. Este tipo requer v1.7 ou mais recente de kube-dns.

	
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

### EndPoints

Todo o Service deve possuir endepoints saudáveis para que possa encaminhar o tráfego, sendo esse objetivo denominado EndPoint.
Um Endpoint nada mais é que uma lista de todos os IPs dos PODs que tem Match no Selector utilizado no Service em questão.
O Controllador interno do Kubernetes chega continuamente todos os PODs checando pelas LABELS definidas nos SELECTOR e atribui via POST ao EndPoint do Service.

*  Endpoints não necessariamente apontam para um POD, um Sevice sem um SELECTOR pode ter seu Endpoint criado manualmente para apontar para um IP ou DNS qualquer a sua escolha. 


[Exemplo prático](https://theithollow.com/2019/02/04/kubernetes-endpoints/)



## Ingress Controller 
[Referências](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
)

Ao contrário de outros tipos de controladores que são executados como parte do kube-controller-managerbinário, os controladores do Ingress não são iniciados automaticamente com um cluster.

O Ingress expõe as rotas HTTP e HTTPS de fora do cluster para serviços dentro do cluster. O roteamento de tráfego é controlado por regras definidas no recurso Ingress.

__IMAGEM__

Para que o Ingress controller tenha essas informações de Rotas, precisamos criar um novo tipo de Objeto chamado Ingress.
Esse objeto irá ter as definições de DNS de Origem, Certificado, Destino...




1. Instalar o Helm

https://helm.sh/docs/intro/install/

```
#Utilizando Script de Instalação
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```

2. Instalar o Nginx Ingress

https://kubernetes.github.io/ingress-nginx/deploy/#quick-start


```
helm upgrade --install ingress-nginx ingress-nginx \
  --repo https://kubernetes.github.io/ingress-nginx \
  --namespace ingress-nginx --create-namespace
```


3.








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
apt update && apt install -y kubeadm=1.20.0-00 kubelet=1.20.0-00 kubectl=1.20.0-00
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
	
	

## Exercício prático
Acessar o [Exemplo](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) e implementar de forma prática. 
* Caso tenha dificuldade ou dívidas, solicite apoio no grupo do Whatsapp.
* Ao finalizar exercício, enviar um print da tela do aplicativo funcionando.



## DaemonSet

Um DaemonSet garante que todos (ou alguns) nós executem uma cópia de um pod. À medida que os nós são adicionados ao cluster, os pods são adicionados a eles. À medida que os nós são removidos do cluster, esses pods são coletados como lixo. A exclusão de um DaemonSet limpará os pods que ele criou.

Alguns usos típicos de um DaemonSet são:

executando um daemon de armazenamento de cluster em cada nó
executando um daemon de coleta de logs em cada nó
executando um daemon de monitoramento de nó em cada nó

[Referências e saiba mais](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

Exemplo:
```
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: example-daemonset
  namespace: default
  labels:
    app: example-daemonset
spec:
  selector:
    matchLabels:
      name: example-daemonset
  template:
    metadata:
      labels:
        name: example-daemonset
    spec:
      tolerations:
      # this toleration is to have the daemonset runnable on master nodes
      # remove it if your masters can't run pods
      - key: node-role.kubernetes.io/master
        effect: NoSchedule
      containers:
      - name: example-daemonset
        image: alpine:latest
        env:
        - name: NODE_NAME
          valueFrom:
            fieldRef:
              fieldPath: spec.nodeName
        command:
        - "bin/sh"
        - "-c"
        - "echo 'Hello! I am running on '$NODE_NAME; while true; do sleep 300s ; done;"
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
      terminationGracePeriodSeconds: 30
```
[Fonte](https://github.com/marcel-dempers/docker-development-youtube-series/tree/master/kubernetes/daemonsets)


## StateFull Set

Um StatefulSet gerência pods que são baseados em uma especificação de contêiner idêntica. Ao contrário de uma implantação(Deployment), um StatefulSet mantém uma identidade fixa para cada um de seus pods.

Esses pods são criados a partir da mesma especificação, mas não são intercambiáveis: cada um tem um identificador persistente que mantém em qualquer reprogramação.

Se quiser usar volumes de armazenamento para fornecer persistência para sua carga de trabalho, você pode usar um StatefulSet como parte da solução. Embora os pods individuais em um StatefulSet sejam suscetíveis a falhas, os identificadores de pod persistentes facilitam a correspondência dos volumes existentes com os novos pods que substituem os que falharam.

[Referências e saiba mais](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

Exemplo:

```
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  selector:
    matchLabels:
      app: nginx # has to match .spec.template.metadata.labels
  serviceName: "nginx"
  replicas: 3 # by default is 1
  minReadySeconds: 10 # by default is 0
  template:
    metadata:
      labels:
        app: nginx # has to match .spec.selector.matchLabels
    spec:
      terminationGracePeriodSeconds: 10
      containers:
      - name: nginx
        image: k8s.gcr.io/nginx-slim:0.8
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: [ "ReadWriteOnce" ]
      storageClassName: "my-storage-class"
      resources:
        requests:
          storage: 1Gi
```


## Limitar Recursos Computacionais

[Referências](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)

* __Objetivo__

Definir quanto de recurso computacional (CPU e Memória) um POD deveria/pode consumir em meu ambiente.

* __Motivação__

Evitar que um POD consuma todos os recursos computacionais de um Node;

Evitar que devido ao alto consumo de um POD outro seja degradado;

Garantir a correta distribuição de carga entres os Nodes;


* Exemplo:

O pod a seguir tem dois contêineres. Ambos os contêineres são definidos com uma solicitação de 0,25 CPU e 64MiB (2 26 bytes) de memória. Cada contêiner tem um limite de 0,5 CPU e 128MiB de memória. Você pode dizer que o Pod tem uma solicitação de 0,5 CPU e 128 MiB de memória e um limite de 1 CPU e 256MiB de memória.

```
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
  - name: app
    image: images.my-company.example/app:v4
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
  - name: log-aggregator
    image: images.my-company.example/log-aggregator:v6
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
```



## O que são configMaps e Secrets?

Secrets e ConfigMaps possuem comportamentos similares, porém com Objetivos diferentes.

* __ConfigMaps:__  Um objeto que contém dados não confidenciais, como arquivos de configuração da aplicação, esses dados podem ser montados em um ou mais __Pods__ como __Arquivo__ ou __Variáveis de ambiente__;

* __Secret:__ Um objeto que contém uma pequena quantidade de dados confidenciais, como uma senha, um token ou uma chave. Essas informações podem ser colocadas em um ou mais __Pods__ como __Arquivo__ ou __Variáveis de ambiente__;Isso evita que deixe dados confidenciais diretamente em sua aplicação;


### ConfigMaps

[configMaps](https://kubernetes.io/docs/concepts/configuration/configmap/)

Exemplo:

Fonte: https://github.com/xcad2k/boilerplates/tree/main/kubernetes/templates/cm-and-secrets

* Criar os dois arquivos e aplicar: nginx-http-cm.yaml nginx-http-deploy.yml 

* Acessar o container

```
kubectl exec -it <nome_do_container> -- /bin/bash

cd /etc/nginx/
ls
cat
cat nginx.conf
```

* Criar o arquivo do Service tipo NodePort: nginx-http-svc.yml 

Acessar a aplicação


#### Secrets

[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/)

Exemplo:

Fonte: https://github.com/xcad2k/boilerplates/tree/main/kubernetes/templates/cm-and-secrets

* Criar os dois arquivos e aplicar: mysql-deploy.yaml e mysql-secret.yml 

* Acessar o container


```
kubectl get secrets

kubectl edit secrets mysql-secret

# Acessar o pod
kubectl exec -it <nome_do_container> -- /bin/bash

#Conectar ao mySQL
mysql -p



```



## O que é NodeSelector?

[Referências](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/)

Você pode restringir um __Pod__ que ele só possa ser executado em um conjunto específico de Nós. Existem várias maneiras de fazer isso e todas as abordagens recomendadas usam seletores de rótulos para facilitar a seleção. Geralmente, __essas restrições são desnecessárias__, pois o agendador fará automaticamente um posicionamento razoável (por exemplo, espalhar seus pods entre nós para não colocar o pod em um nó com recursos livres insuficientes etc.), mas há __algumas circunstâncias__ em que você pode querer controlar em qual nó o pod é implantado - __por exemplo, para garantir que um pod termine em uma máquina com um SSD conectado__ a ele ou para colocar pods de dois serviços diferentes que se comunicam muito na mesma zona de disponibilidade.


Itens faltando:
* Subindo containers no Nó Master
* O que é um Daemon Set?
* O que é um StateFull Set? 
* O que é um EndPoint?
* O que é NodeSelector?
* Como definir limitar recursos computacionais? 
* O que são configMaps e Secrets? 


* O que é um Ingress?  -> demostrar
	* Instalando o Ingress-nginx








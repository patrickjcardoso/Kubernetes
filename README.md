# Kubernetes


## Sumário

1. [Instalar Docker](https://github.com/patrickjcardoso/Kubernetes#instalar-o-docker)
2. [Instalar Kubectl](https://github.com/patrickjcardoso/Kubernetes#instalar-o-kubectl)
3. [Kubernetes com MINIKUBE (Single Node)](https://github.com/patrickjcardoso/Kubernetes#kubernetes-com-minikube-single-node)
5. [Kubernetes com KIND](https://github.com/patrickjcardoso/Kubernetes#kubernetes-com-kind)
6. [Kubernetes com KUBEADM (Cluster)](https://github.com/patrickjcardoso/Kubernetes#kubernetes-com-kubeadm-cluster)
7. [Kubernetes](https://github.com/patrickjcardoso/Kubernetes#kubernetes-1)

### Instalar o Docker
1º Instalar o docker
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```
Usar docker sem ser root
```
sudo usermod -aG docker ${USER}
```

### Instalar o Kubectl

[Tutorial Oficial:](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)

Autocomplete Kubectl: 
```
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null
```

## KUBERNETES COM MINIKUBE (Single Node)

Instalar o MiniKube
[Tutorial Oficial:](https://minikube.sigs.k8s.io/docs/start/)


### MiniKube Comandos básicos
```
minikube start

minikube stop

minikube delete

minikube delete --all
```

## KUBERNETES COM KIND

kind é uma ferramenta para executar clusters locais do Kubernetes usando “nós” de contêiner do Docker.
kind foi projetado principalmente para testar o próprio Kubernetes, mas pode ser usado para desenvolvimento local ou CI.


[Documentação Oficial](https://kind.sigs.k8s.io/docs/user/quick-start/)

__Requisitos:__
* Docker Instalado 

On Linux:
```
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/
```

### Primeiros comandos:

```
#Verificar se o 
kind
#Ativar auto complete
source <(kind completion bash)
```

* Criando um cluster com Kind
```
kind create cluster --name o2bacademy
```

* Criando um cluster com vários nós
kind create cluster --name o2bacademy --config arquivodeconfiguração.yaml


## KUBERNETES

### Comandos básicos

[K8S Cheatsheet](https://kubernetes.io/pt-br/docs/reference/kubectl/cheatsheet/)

```
kubectl cluster-info

kubectl get nodes

kubectl get pods

kubectl get pods -n <namespaces>

kubectl get pods -A -o wide

kubectl get namespaces

kubectl describe pod etcd-minikube -n kube-system
```

## Pods

[Documentação Oficial](https://kubernetes.io/docs/concepts/workloads/pods/)

Os pods são as menores unidades de computação implantáveis que você pode criar e gerenciar no Kubernetes.

Em termos de conceitos do Docker, um Pod é semelhante a um grupo de contêineres do Docker com namespaces compartilhados e volumes de sistema de arquivos compartilhados.

### Criando meu primeiro Pod

* Abordagem Imperativa: 

```
kubectl run meunginx --image=nginx:1.14.2

# ou

kubectl run meuapache --image=httpd:2.4
```

* Deletar Pod

```
?
```
### Exportando manifesto de um Pod

* Salva manifesto de um Pod

```
kubectl get pod my-pod -o yaml > my-pod.yaml
```

[Saiba mais sobre manifesto](https://medium.com/@sujithar37/understanding-the-kubernetes-manifest-97f44acc2cb9)


### Criando meu primeiro Pod com manifesto

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
    image: nginx:1.14.2
    ports:
       - containerPort: 80
```
* Após criar um arquivo de manifesto, precisamos aplicá-lo;
```
kubectl apply -f <nomedoarquivo.yaml>

# expondo uma pod
kubectl port-forward pod/meupod 8080:80
```
Cada Pod deve executar uma única instância de um determinado aplicativo. Se você quiser dimensionar seu aplicativo horizontalmente (para fornecer mais recursos gerais executando mais instâncias), use vários pods, um para cada instância. No Kubernetes, isso geralmente é chamado de replicação . Os pods replicados geralmente são criados e gerenciados como um grupo por um recurso de carga de trabalho e seucontrolador.

Você pode usar recursos de carga de trabalho para criar e gerenciar vários pods para você. Um controlador para o recurso lida com a replicação, a distribuição e a correção automática em caso de falha do pod. Por exemplo, se um nó falhar, um controlador perceberá que os pods nesse nó pararam de funcionar e cria um pod substituto. O agendador coloca o Pod substituto em um Node.

Veja alguns exemplos de recursos de carga de trabalho que gerenciam um ou mais pods:

* Deployment
* StatefulSet
* DaemonSet


## Namespaces

K8s usa namespaces para organizar objetos no cluster através de uma divisão lógica (como se fosse uma pasta).


Por padrão, kubectl interage com o namespace padrão (default). 
Para usar um namespace específico, diferente do padrão, pode-se usar a flag --namespace=nome, ou ainda -n nome. 

Para interagir com todos os namespaces, pode-se passar a flag --all-namespaces ou -A para o comando.

![image](https://user-images.githubusercontent.com/66180145/156056187-36e6bed9-f887-4823-baa3-75f8a2b5e54b.png)
Fonte: https://stacksimplify.com/

### Criando Namespaces

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


## Labels

[Referências](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)

![image](https://user-images.githubusercontent.com/66180145/156056539-ee908352-ebcc-4bc0-b9a0-5eb10858c429.png)
Fonte: https://www.wecloudpro.com/2020/04/06/kubernetes-nodes-auto-label.html

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

## Workload Resources - Recursos de Carga de Trabalho 

Você pode usar recursos de carga de trabalho que gerenciam um conjunto de pods em seu nome (Labels e Selectors). Esses recursos configuram controladores que garantem que o número certo do tipo certo de pod esteja em execução, para corresponder ao estado que você especificou.

O Kubernetes fornece vários recursos de carga de trabalho integrados:

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
        image: gcr.io/google_samples/hello-frontend:1.0
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

Altere o nome ou a versão da image no seu manifesto, aplica as modificações e após isso verifique se os pods foram atualizados.
 

* Outras considerações:

Embora você possa criar pods vazios sem problemas, é altamente recomendável garantir que os pods vazios não tenham rótulos que correspondam ao seletor de um de seus ReplicaSets. A razão para isso é porque um ReplicaSet não se limita a possuir Pods especificados por seu modelo - ele pode adquirir outros Pods da maneira especificada nas seções anteriores.

### Deployment

Um Deployment fornece atualizações declarativas para Pods e ReplicaSet.

Você descreve um estado desejado em um Deployment e a Deployment Controlador altera o estado real para o estado desejado a uma taxa controlada. 
Você pode definir implantações para criar novos ReplicaSets ou remover implantações existentes e adotar todos os seus recursos com novas implantações.

* Vantagens:

__Escalabilidade:__ com um Deployment, pode-se especificar o número de réplicas desejado e o Deployment vai criar ou remover Pods até alcançar o número desejado.

__Atualizações:__ é possível alterar a imagem de um container para uma nova versão e o Deployment vai gradualmente substituir os containers para a nova versão (evita downtime).

__Self-healing:__ se um dos Pods for acidentalmente destruído, o Deployment vai imediatamente iniciar um novo Pod para substituí-lo.

![image](https://user-images.githubusercontent.com/66180145/156232007-72d8b9e8-427b-4bec-911f-2ed8f20b55c3.png)
Fonte: https://www.bluematador.com/blog/kubernetes-deployments-rolling-update-configuration

* Criar Deployment, abordagem imperativa

```
kubectl create deployment http-deployment --image=nginx
```

* Exemplo, abordagem declarativa:
	
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
	

* Listar Pods, ReplicaSet e Deployments

```
kubectl get pods
kubectl get rs
kubectl get deployments ou kubectl get deploy
kubectl get pods --show-labels
```
* Verificar o Status da implatanção:
```
kubectl rollout status deployment/nginx-deployment
```

* Aumentar a quantidade de réplicas 

```
kubectl scale --replicas 3 deployment http-deployment
```
	
* Diminuir a quantidade de réplicas 

```
kubectl scale --replicas 2 deployment http-deployment
```
	
* Mostrar detalhes de um deployment

```
kubectl describe deployment http-deployment
```

* Nova versão da aplicação

Siga as etapas abaixo para atualizar sua implantação:

1. Vamos atualizar os pods nginx para usar a imagem nginx:1.20 em vez da nginx:1.14.2.

Você pode fazer isso básicamente de três maneiras:

* Editando o arquivo de manifesto:

Altere o arquivo de manifesto e aplique as alterações.

* De maneira Imperativa:

```
kubectl set image deployment/nginx-deployment nginx=nginx:1.20
```

* Editar o Deployment diretamente:

```
kubectl edit deployment/nginx-deployment
```

A implantação garante que apenas um determinado número de pods fique inativo enquanto estão sendo atualizados. Por padrão, ele garante que pelo menos 75% do número desejado de pods esteja ativo (máximo de 25% indisponível).

A implantação também garante que apenas um determinado número de pods seja criado acima do número desejado de pods. Por padrão, ele garante que no máximo 125% do número desejado de Pods esteja ativo (25% de aumento máximo).
	
#### Revertendo uma implantação

Enviar a atividade para: patrick.cardoso@o2b.com.br

A cada etapa tirar um print da saída do comando: kubectl get all
* Caso seu deployment esteja em uma namespace, lembre de ajustar o comando acima.

Às vezes, você pode querer reverter uma implantação; por exemplo, quando a implantação não é estável, como loop de falha. Por padrão, todo o histórico de distribuição da implantação é mantido no sistema para que você possa reverter a qualquer momento (você pode alterar isso modificando o limite do histórico de revisões).

1. Suponha que você tenha cometido um erro de digitação ao atualizar a implantação, colocando o nome da imagem como nginx:1.161 em vez de nginx:1.16.1.
* O lançamento fica travado. Você pode verificá-lo verificando o status do lançamento:
```
kubectl rollout status deployment/nginx-deployment
kubectl get rs
```
* Para corrigir isso, você precisa reverter para uma revisão anterior do Deployment que seja estável.

* Verificar o histórico de lançamento de uma aplicação:
```
kubectl rollout history deployment/nginx-deployment
```
* Para ver os detalhes de cada revisão, execute:
```
kubectl rollout history deployment/nginx-deployment --revision=2
```
* Agora você decidiu desfazer a distribuição atual e reverter para a revisão anterior:
```
kubectl rollout undo deployment/nginx-deployment
```

#### ATIVIDADE PRÁTICA 01 - Implantando aplicação no K8S

__Etapa01__

Você está responsável por fazer a implantação de uma aplicação que vai utilizar a imagem gcr.io/google_samples/echo-go:1.0

1. Criar Deployment da aplicação utilizando um arquivo de manifesto e com 3 réplicas.

* Utilize os comandos para verificar se sua implantação está correta. Liste os deployments, rs e pods.

* Escalar o Deployment com ou comando abaixo ou através do arquivo de manifesto
	
```
kubectl scale --replicas 5 deployment <nome_do_deployment>
```

__Etapa02__

A equipe de desenvolvimento enviou para você uma nova imagem da aplicação chamada: gcr.io/google_samples/echo-go faça a atualização para a nova versão.

* Lembre-se de verificar se a atualização ocorreu normalmente.

__Etapa03__

Após um tempo, a equipe te enviou novamente uma nova versão que é: gcr.io/google_samples/echo-go:2.0 faça a atualização para a nova versão.

* Lembre-se de verificar se a atualização ocorreu normalmente.

__Etapa04__

A equipe de desenvolvimento constatou alguns problemas e solicitou que você faça um rollback para a versão 1.0, faça a reversão e verifique se está tudo correto.


__Entrega:__ Enviar para patrick.cardoso@o2b.com.br, assunto: Atividade prática K8S 01, os print de conclusão de cada etapa, junto com o arquivo de manifesto.


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


### StatefulSets

[Referências e saiba mais](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

Um StatefulSet gerência pods que são baseados em uma especificação de contêiner idêntica. Ao contrário de uma implantação(Deployment), um StatefulSet mantém uma identidade fixa para cada um de seus pods.

Esses pods são criados a partir da mesma especificação, mas não são intercambiáveis: cada um tem um identificador persistente que mantém em qualquer reprogramação.

Se quiser usar volumes de armazenamento para fornecer persistência para sua carga de trabalho, você pode usar um StatefulSet como parte da solução. Embora os pods individuais em um StatefulSet sejam suscetíveis a falhas, os identificadores de pod persistentes facilitam a correspondência dos volumes existentes com os novos pods que substituem os que falharam.

#### Criando um StatefulSets

* Utilize o arquivo exStatefulset.yaml

* Crie um Stateful set

1. Verifique o processo de criação dos pods
```
kubectl get pods -w -l app=nginx
```
2. Verifique o Serviço:
```
kubectl get svc
```
4. Verifique o Statefulset
```
kubectl get statefulsets
kubectl describe statefulset web
#Você pode editar se for necessário
kubectl edit statefulset web
```

5. Aumente e depois diminua a escala de um statefulset
```
kubectl scale statefulset web --replicas=5
```

6. Excluindo o Statefulset
```
kubectl delete statefulset web 
kubectl delete svc nginx
kubectl delete persistentvolumeclaims myclaim
```

[Fonte](https://medium.com/avmconsulting-blog/deploying-statefulsets-in-kubernetes-k8s-5924e701d327)

#### Exemplo Prático

[Implantando WordPress e MySQL com Volumes Persistentes](https://kubernetes.io/docs/tutorials/stateful-application/mysql-wordpress-persistent-volume/)

Baixe os arquivos:
* kustomization.yaml
* mysql-deployment.yaml
* wordpress-deployment.yaml

Aplicar as configurações:
```
kubectl create -k ./
```
Expor a aplicação no host
```
kubectl port-forward svc/wordpress 8080:80
```

* Testar

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





### Service
	
[Referências](https://kubernetes.io/pt-br/docs/tutorials/kubernetes-basics/expose/expose-intro/)
	
Um serviço no Kubernetes é uma abstração que define um conjunto lógico de Pods e uma política pela qual acessá-los.
O conjunto de Pods selecionados por um Serviço é geralmente determinado por um seletor de rótulos LabelSelector.
	
Embora cada Pod tenha um endereço IP único, estes IPs não são expostos externamente ao cluster sem um Serviço. Serviços permitem que suas aplicações recebam tráfego. Serviços podem ser expostos de formas diferentes especificando um tipo type na especificação do serviço ServiceSpec:

* __ClusterIP (padrão)__ - Expõe o serviço sob um endereço IP interno no cluster. Este tipo faz do serviço somente alcançável de dentro do cluster.
* __NodePort__ - Expõe o serviço sob a mesma porta em cada nó selecionado no cluster usando NAT. Faz o serviço acessível externamente ao cluster usando <NodeIP>:<NodePort>. Superconjunto de ClusterIP.
* __LoadBalancer__ - Cria um balanceador de carga externo no provedor de nuvem atual (se suportado) e assinala um endereço IP fixo e externo para o serviço. Superconjunto de NodePort.
* __ExternalName__ - Expõe o serviço usando um nome arbitrário (especificado através de externalName na especificação spec) retornando um registro de CNAME com o nome. Nenhum proxy é utilizado. Este tipo requer v1.7 ou mais recente de kube-dns.

![image](https://user-images.githubusercontent.com/66180145/157732273-09506310-1005-441d-849b-d6333b1ae1e2.png)
Fonte: [Medium](https://medium.com/devops-mojo/kubernetes-service-types-overview-introduction-to-k8s-service-types-what-are-types-of-kubernetes-services-ea6db72c3f8c)
	
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



## ATIVIDADE PRÁTICA 02
	
## Exercício prático
Acessar o [Exemplo](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/) e implementar de forma prática. Escolha qual é a melhor estratégia para implantar essa aplicação.
* Caso tenha dificuldade ou dívidas, solicite apoio no grupo do Whatsapp.
* Ao finalizar exercício, enviar um print da tela do aplicativo funcionando.
	
__Entrega:__ Enviar para patrick.cardoso@o2b.com.br, assunto: Atividade prática K8S 02, os print de conclusão da implantação.
	

	
	
## Ingress Controller 
[Referências](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
)

Ao contrário de outros tipos de controladores que são executados como parte do kube-controller-managerbinário, os controladores do Ingress não são iniciados automaticamente com um cluster.

O Ingress expõe as rotas HTTP e HTTPS de fora do cluster para serviços dentro do cluster. O roteamento de tráfego é controlado por regras definidas no recurso Ingress.

![image](https://user-images.githubusercontent.com/66180145/153482712-6081cb72-8b43-42b5-9526-32b6c83d3aa6.png)

Para que o Ingress controller tenha essas informações de Rotas, precisamos criar um novo tipo de Objeto chamado Ingress.
Esse objeto irá ter as definições de DNS de Origem, Certificado, Destino...

1. Criar o Deployment e o Service abaixo:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: simple-api
  labels:
    app: simple-api
spec:
  replicas: 5
  selector:
    matchLabels:
      app: simple-api
  template:
    metadata:
      labels:
        app: simple-api
    spec:
      containers:
      - name: simple-api
        image: gustavoleitao/simple-api:1.0.4
        ports:
        - containerPort: 3000
        readinessProbe:
          httpGet:
            path: /
            port: 3000
---
apiVersion: v1
kind: Service
metadata:
  name: simple-api
  labels:
    app: simple-api
spec:
  type: NodePort
  ports:
  - port: 3000
    targetPort: 3000
    nodePort: 30007
  selector:
    app: simple-api 
```

Não vem por padrão instalado junto com o Kubernetes.

1. Instalar o Nginx Ingress

https://kubernetes.github.io/ingress-nginx/deploy/#quick-start

Baixar o arquivo yaml e aplicar. tag 0.42.0

```
kubectl get pods -n ingress-nginx

kubectl get svc -n ingress-nginx

```
2. Testando o Ingress.

```
curl <End_IP_no_Cluster>:<Porta_alta>
```

* Você receberá uma resposta de Not Found, Ingress Controler não sabe para quem encaminhar.

3. Editar o arquivo /etc/hosts

```
#adicionar o ip e um domínio ficticio
10.0.0.71       simpleapi.com.br
```

* Pingar o domínio

* Fazer um curl no domínio com a porta alta.

4. Editar o arquivo ingresscontroler.yaml e adicionar os compos abaixo:


* procurar pela linha NodePort

```
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: http
    - name: https
      port: 443
      protocol: TCP
      targetPort: https
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/instance: ingress-nginx
    app.kubernetes.io/component: controller
#adicionar as linhas abaixo
  externalIPs:
    - 10.0.0.71


```


5. Criar o Arquivo do Ingress


```
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: simple-api-ing
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: simpleapi.com.br
    http:
      paths:
        - path: /
          backend:
            serviceName: simple-api
            servicePort: 3000

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
Alternativamente, você pode fazer a instalação através do script de instalação [Documentação Oficial](https://docs.docker.com/engine/install/ubuntu/)
```
sudo apt-get update && sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
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
	














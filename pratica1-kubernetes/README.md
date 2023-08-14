# PRÁTICA KUBERNETES 1

Repositório criado para organizar e detalhar os conceitos utilizados na prática de orquestração com Kubernetes.

Objetos Kubernetes aplicados

- Pods
- Replicaset
- Deployments
- Services
- Ingress (Ingress NGINX)

## Arquitetura

A ferramenta utilizada na implementação do cluster foi o Kind, para mais detalhes sobre a ferramenta e seus tipos de implementações, acessar [https://kind.sigs.k8s.io/](https://kind.sigs.k8s.io/)

- OBS: Ver exemplo de implementação de um cluster HA  multinode e opções avançadas de implementação com Ingress Nginx.

Para melhor entendimento sobre conceitos de alta disponibilidade, escalabilidade, self-healing e load-balancing com o Kubernetes, optei em criar um cluster contendo a seguinte configuração:

- control-plane 1
- control-plane 2
- control-plane 3
- worker 1
- worker 2
- worker 3

![kubernetes.drawio (5).png](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/kubernetes.drawio_(5).png)

## Criando o cluster

```abap
kind create cluster --name k8s-ha --config kind-config.yaml
```

![Captura de tela 2023-08-13 202542.png](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Captura_de_tela_2023-08-13_202542.png)

### Verificando nós do cluster

```abap
kubectl get nodes
```

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled.png)

## Instalação do NGINX Ingress Controller

```abap
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%201.png)

## Aplicando a configuração de deployment

```abap
kubectl apply -f --config deployment-ingress.yaml
```

### Pods sendo criados

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%202.png)

### Pods criados

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%203.png)

### Deployments criados

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%204.png)

- Replicasets criados automaticamente a partir do deployment

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%205.png)

### Services e Ingress criados

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%206.png)

### Funções de Replicação e Self healing aplicadas no cluster

- Deletando um pod enquanto o cluster está sendo executado (Simulando crash da aplicação)

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%207.png)

- Função de Self healing e Replicaset mantendo o serviço web-blue com 3 replicas, como foi definido no manifesto “deployment-ingress.yaml”

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%208.png)

- Notar que o pod “web-blue-85bf4fb9c8-f5vrw” foi criado automaticamente (13s na coluna AGE)

![Untitled](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/Untitled%209.png)

### Serviço de Balanceamento de carga aplicado no cluster

- Cada chamada do cliente chega diretamente ao NGINX ingress controller, ele direciona dinamicamente para cada pod que no momento está apto a receber requisição, aplicando assim o balanceamento de carga e evitando o enfileiramento e congestionamento de trabalhos no servidor.
- Notar que cada vez que uma requisição é feita, um novo servidor responde a chamada.

### APP BLUE

[ezgif.com-video-to-gif.mkv](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/ezgif.com-video-to-gif.mkv)

### APP GREEN

[ezgif.com-gif-maker.mkv](PRA%CC%81TICA%20KUBERNETES%201%20ada14fe944d3411391db9af48a3638c5/ezgif.com-gif-maker.mkv)

## Considerações Finais

Mesmo com as limitações de utilizar um cluster local, o Kind é uma ótima ferramenta para estudo do Kubernetes, permitindo criar arquiteturas e praticar vários conceitos importantes de sistemas distribuídos, como alta disponibilidade, self-healing, auto escalonamento.

Outro ponto importante visto na ferramenta é o suporte as chamadas ao cliente kubectl, permitindo que o usuário consiga explorar ao máximo os objetos do kubernetes, testando localmente os recursos avançados do Orquestrador.
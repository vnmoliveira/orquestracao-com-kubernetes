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


![kubernetes drawio_(5)](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/b8e73519-4aef-493c-9ac6-a8b761218e9c)


## Criando o cluster

```abap
kind create cluster --name k8s-ha --config kind-config.yaml
```

![Captura_de_tela_2023-08-13_202542](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/d80464bf-59ad-45be-8842-e0f0bcb517a5)

### Verificando nós do cluster

```abap
kubectl get nodes
```
![Untitled](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/c3c32977-1c88-45cd-a5af-b21745832c03)

## Instalação do NGINX Ingress Controller

```abap
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
```
![Untitled 1](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/23efaca5-55a1-4001-ac4c-fb5f84674540)


## Aplicando a configuração de deployment

```abap
kubectl apply -f --config deployment-ingress.yaml
```

### Pods sendo criados
![Untitled 2](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/06d7e8b4-7b65-4b65-9b4b-5ef3a7f8eb78)

### Pods criados

![Untitled 3](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/f01bd70c-3722-4861-b641-146accb1c440)

### Deployments criados
![Untitled 4](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/9a235acd-281c-45b9-ad2b-4b3bfe4502ed)

- Replicasets criados automaticamente a partir do deployment

![Untitled 5](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/2806abb3-bfcf-48b2-a60a-8e34ecfa159f)


### Services e Ingress criados

![Untitled 6](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/f147f709-e445-45fc-bedb-1addfc610967)

### Funções de Replicação e Self healing aplicadas no cluster

- Deletando um pod enquanto o cluster está sendo executado (Simulando crash da aplicação)

![Untitled 7](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/5061fa88-0efc-4851-96ed-37e88924289e)


- Função de Self healing e Replicaset mantendo o serviço web-blue com 3 replicas, como foi definido no manifesto “deployment-ingress.yaml”

![Untitled 8](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/4063381c-8391-434f-b68e-20ddf1ed2c6c)


- Notar que o pod “web-blue-85bf4fb9c8-f5vrw” foi criado automaticamente (13s na coluna AGE)

![Untitled 9](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/4a46b048-285b-428b-8a55-4f0075618bb1)


### Serviço de Balanceamento de carga aplicado no cluster

- Cada chamada do cliente chega diretamente ao NGINX ingress controller, ele direciona dinamicamente para cada pod que no momento está apto a receber requisição, aplicando assim o balanceamento de carga e evitando o enfileiramento e congestionamento de trabalhos no servidor.
- Notar que cada vez que uma requisição é feita, um novo servidor responde a chamada.

### APP BLUE

![ezgif com-video-to-gif](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/42db1572-3489-41ed-8065-995fdb41f55d)

### APP GREEN

![ezgif com-video-to-gif (1)](https://github.com/vnmoliveira/orquestracao-com-kubernetes/assets/76496863/35a6547c-4165-489f-9c31-0d71ac38e993)


## Considerações Finais

Mesmo com as limitações de utilizar um cluster local, o Kind é uma ótima ferramenta para estudo do Kubernetes, permitindo criar arquiteturas e praticar vários conceitos importantes de sistemas distribuídos, como alta disponibilidade, self-healing, auto escalonamento.

Outro ponto importante visto na ferramenta é o suporte as chamadas ao cliente kubectl, permitindo que o usuário consiga explorar ao máximo os objetos do kubernetes, testando localmente os recursos avançados do Orquestrador.

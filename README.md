
## Bem-vindo

Estamos muito felizes por você estar pensando em se juntar a nós! Esse é um teste feito para conhecer um pouco mais de cada candidato. Não se trata de um teste objetivo, capaz de gerar uma nota ou uma taxa de acerto, mas sim de um estudo de caso com o propósito de conhecer os conhecimentos, experiências e modo de trabalhar de um candidato. Não experamos que tudo seja feito perfeitamente, pois valorizamos o seu tempo. Sinta-se livre para desenvolver sua solução para o problema proposto.

Este desafio está dividido em 4 partes:

1. Implementação
2. Depuração
3. Melhorias
4. Perguntas

## Requisitos para o desafio

- Github Account

Se você encontrar possíveis melhorias a serem feitas neste desafio, fique a vontade para descreve-lás.

## Sobre o Desafio:

A ELO executa a maior parte de sua infraestrutura em Kubernetes. É um monte de microserviços conversando entre si e realizando diversas tarefas.

Nesse repositório, fornecemos a você:

- 'sre-challenge-app/': Uma aplicação com CRUD que armazena dados de funcionários em um Banco de dados MySQL 8.

### Configure o ambiente do desafio

1. Instale qualquer cluster K8s local (ex: Minikube) em sua máquina e documente sua configuração, para que possamos executar sua solução.

### Parte 1 - Configure os aplicativos

Gostariamos que essa aplicação sre-challenge-app e seu banco de dados fossem executados em um cluster K8s.

Requisitos

1. A aplicação deve ser acessível de fora do cluster.
2. Manifestos de implantação do kubernetes para executar com limitação de requests e usando HPA.

### Parte 2 - Corrigir o problema

A aplicação tem um problema. Encontre e corrija! Você saberá que corrigiu o problema quando o estado dos pods no namespaces for semelhante a este:

```
NAME                                 READY   STATUS    RESTARTS   AGE
db-5877fd4d4d-qmngl                  1/1     Running   0          6m50s
sre-challenge-app-59fd5ffc57-lm2xs   1/1     Running   0          7s
```

Requisitos

Escreva aqui sobre o problema, a solução, como você a encontrou e qualquer outra coisa que queira compartilhar sobre ela.

### Parte 3 - Melhores práticas

Essa aplicação tem uma falha de segurança e gostariamos que as credenciais do MYSQL fossem armazenadas em uma secret do Kubernetes.

Requisitos
1. Manifesto do kubernetes usando a API de secret com as credenciais do Banco para implantação.
2. Manifesto do kunernetes da aplicação com as informações da secret criada anteriormente.
2. Configuração do código da aplicação utilizando uma variável que foi referenciada no secrets do K8s (Application Properties do Java)

### Parte 4 - Perguntas

Sinta-se à vontade para expressar seus pensamentos e compartilhar suas experiências com exemplos do mundo real com os quais você trabalhou no passado.

Requisitos
O que você faria para melhorar essa configuração e torná-la “pronta para produção”?
Existem 2 microsserviços mantidos por 2 equipes diferentes. Cada equipe deve ter acesso apenas ao seu serviço dentro do cluster. Como você abordaria isso?
Como você evitaria que outros serviços em execução no cluster se comunicassem com o sre-challenge-app?


## O que é importante para nós?

É claro que esperamos que a solução funcione, mas também queremos saber como você trabalha e o que é importante para você como engenheiro. Portanto, fique à vontade para criar novos arquivos, refatorar, renomear, ...

Idealmente, gostaríamos de ver sua progressão através de commits, verbosidade em suas respostas e todos os requisitos atendidos. Não se esqueça de atualizar o README.md para explicar seu processo de pensamento.

## Entrega do desafio:

Ao terminar o desafio, convide o 'ELO-SRE' para contribuir com o seu repositório de desafios para que possamos fazer a avaliação. Boa Sorte

<p align="center">
  <img src="ca.jpg" alt="Challange accepted" />
</p>


Prezados, boa tarde.

Meu Nome é Douglas, não tenho conhecimento em Kubernets, mas tentei fazer o meu melhor. Estou a procura de um emprego e uma oportunidade de poder crescer e aprender mais com as pessoas. 
Procurei diversos vídeos e procurei ajuda, como nunca atuei na ferramenta, tive dificuldade na execução e na compreensão. Outro ponto, meu computador é bem fraco, não consegui executar o MINIKUBE ou KUBECLT, tive muito problemas em performance. 
Qualquer dúvida estou a disposição.


Instalei uma máquina virtual com o Virtual BOX
SO UBUNTU 24.04
2GB
2 Núcleos
40HD

Segue abaixo as respostas:

##Para garantir que o número de pods da aplicação escale automaticamente conforme a carga, você precisa configurar o Horizontal Pod Autoscaler (HPA)##

Vou deixar abaixo o YAML do HPA:

apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: sre-challenge-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: sre-challenge-app
  minReplicas: 1
  maxReplicas: 3
  metrics:
    - type: Resource
      resource:
        name: cpu
        targetAverageUtilization: 50
        
Após isso, aplicar o comando abaixo:

kubectl apply -f sre-challenge-app-hpa.yaml

##Armazenar as credenciais do MySQL em uma Secret do Kubernetes##

Para melhorar a segurança, podemos criar uma Secret no Kubernetes para armazenar as credenciais do banco de dados. Exemplo:
 
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: <base64-encoded-password>
  MYSQL_USER: <base64-encoded-username>
  MYSQL_PASSWORD: <base64-encoded-password>
  MYSQL_DATABASE: <base64-encoded-db-name>

Para codificar os valores em base64:
echo -n 'rootpassword' | base64
Refereciar a Secret no Deployment da aplicação

##O que você faria para melhorar essa configuração e torná-la "pronta para produção"?##

Redundância: Configurar réplicas para garantir alta disponibilidade.
Backups: Garantir que o banco de dados tenha uma estratégia de backup automatizada.
Monitoramento: Implementar soluções de monitoramento como Prometheus e Grafana.
Segurança: Configurar RBAC e Network Policies para restringir o acesso aos serviços.
Como você abordaria o isolamento entre microsserviços mantidos por equipes diferentes?
Namespaces: Criar namespaces diferentes para isolar os serviços das diferentes equipes.
Network Policies: Configurar políticas de rede para garantir que os serviços só possam se comunicar quando necessário, bloqueando comunicação não autorizada entre os serviços.
Como você evitaria que outros serviços no cluster se comunicassem com sre-challenge-app?
Usando Network Policies para restringir a comunicação com sre-challenge-app apenas a serviços autorizados.
Com isso, você tem uma configuração completa para rodar o desafio no Kubernetes, considerando tanto as correções de problema quanto boas práticas de segurança e escalabilidade.



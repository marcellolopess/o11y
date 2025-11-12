# Observability Stack — Laboratório

Objetivo: ensinar a instrumentar aplicações para monitoramento e observabilidade. O laboratório cobre:
- Envio de logs para Grafana Loki
- Exposição de métricas para Prometheus
- Visualização e dashboards no Grafana
- Alertas via Alertmanager e integração com Slack
Inclui código das aplicações, configurações e instruções para subir o ambiente com Docker Compose.


Diagrama olhando os 3 Pilares de Observabilidade (Logs, Métricas e Tracing)

![observability-stack](img/observability-stack.png)



### Metrics
As aplicações expõem as métricas para o prometheus server, que pode ser visualizada nos dashboards Spring Boot 2.1 Statistics e Python Process.

### Tracing
O OpenTelemetry e Jaeger [in progress].

### Alerts
Os alertas de disponibilidade das aplicações são enviados para o Alert Manager que envia um webhook para o canal do slack #alerts

![alarme-slack](img/slack-notification.png)

----
## Estrutura do repositório

```
.
├── app                         >> Apps
│   ├── python-app
│   └── spring-app
├── configs
│   ├── alertmanager            >> Config Webhook Slack
│   ├── grafana                 >> Configs Grafana
│   │   ├── dashboards
│   │   └── provisioning
│   │       ├── dashboards
│   │       └── datasources
│   ├── loki                    >> Configs Loki
│   ├── prometheus              >> Configs prometheus Server
│   └── promtail                >> Configs promtail
├── img
└── k6                          >> Script k6 to run test get
```

### Criando a stack

1. Acesse o console da AWS e acesse o Serviço EC2

![](img/1.png)

2. Em EC2 clique em Instances (running)

![](img/2.png)

3. No canto superior esquerdo clique em **Launch instances**

![](img/3.png)

4. Em EC2, preencha conforme abaixo:
- **Name:** observability-stack
- **Application and OS Images (Amazon Machine Image):** Ubuntu
- **Instance Type:** t2.medium
- **Key pair (login):** vokey
- **Network settings:** Create security group (x) Allow SSH traffic from
- **Configure Storage:** 20

Após alterar os parâmetros, clique em `launch`.

![](img/4.png)  
![](img/5.png)  
![](img/6.png)  
![](img/7.png)

5. Aguarde a instância ser criada

![](img/8.png)

6. Após a criação clique no ID da instância

![](img/9.png)

7. Em EC2 clique na aba `Security` e no ID do `Security Groups`

![](img/10.png)

![](img/11.png)

![](img/12.png)

![](img/13.png)

![](img/14.png)

![](img/15.png)

![](img/16.png)

![](img/17.png)

![](img/18.png)

![](img/19.png)

![](img/20.png)

![](img/21.png)

![](img/22.png)

![](img/23.png)

![](img/24.png)

![](img/25.png)

![](img/26.png)

![](img/27.png)

![](img/28.png)

![](img/29.png)

![](img/30.png)



```shell
cat > vockey.pem <<'EOF'
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEAvigWOzgdxLY3tKJM4XfdpfikojvzVa8QwNtv6NtsMFtsM9cQ
5foaOmql18SLiwC5GGDl7dzCxANQIIoNE3bMqu7c0QPHy9J0nVBGWr+h6yxqh8LH
Em3LOkzG3QzqV6oCYyTPr7Yw0d0f2asDZX15oEF2j+n+gWlx+mWpm05izOgHOurz
I7mPiGuSa7Jf4kw+HcThsjx0xDhzk8SWaIwqosIrm3afPzZlyC0CsCzq4aLL9LIU
wTJjKmr7k/GkkaLbO72qMDiA2u75EqcmvDEu7SOCSu1oqkxtzt0a8Lf7sAdjS6As
YjtBcXTFly92AKD95908EK8dY5InysEOKToMcQIDAQABAoIBAFENCRzIuHeGK4xE
uXxI+SKrLpEv+HIHIp7xaWvZSu35qc8W1k8QIWnQ10t2rPvcnQh1FMwkEqg1uUuF
TDmFo4sgMOKuN9T6qVX8R2Ca2ctBrU6ciPmj7sG0w/aocKJZBpd3s0Zz468wD0la
....
XlRxKoF64MNUTJefuHcbpCKp66+cqgNLKr6R0g/+osAsXEQz932jwEJXPZs4As3v
-----END RSA PRIVATE KEY-----
EOF
```

```
chmod 400 "vockey.pem"
```

```
docker-compose up
```

### URLs Apps:
Spring-app: http://127.0.0.1:8080/  
    Route health check: http://127.0.0.1:8080/actuator/health  
    Metrics prometheus: http://127.0.0.1:8080/actuator/prometheus

Python-app: http://127.0.0.1:8081/  
    Metrics prometheus: http://127.0.0.1:8082/metrics


### URLs tools:  
Grafana:            http://localhost:3000/  
Jaeger:             http://localhost:16686/  
Prometheus Alarms:  http://127.0.0.1:9090/alerts  
Prometheus Server:  http://127.0.0.1:9090/targets  

---

### Logging
Os Logs são enviados para o Grafana Loki utilizando logdriver do Loki, onde é possível visualizar os logs no Explorer do Grafana, selecionando o datasource Loki.

```
    logging:
      driver: loki
      options:
        loki-url: "http://localhost:3100/loki/api/v1/push"
```

![alarme-slack](img/grafana-loki.png)
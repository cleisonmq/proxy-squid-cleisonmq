# Configuração do Splunk Universal Forwarder

## 1. Configurar a porta de receção no servidor Splunk

No **servidor Splunk Enterprise**, é necessário configurar uma porta para receber os dados enviados pelo Universal Forwarder.

No Splunk Web:

**Settings → Forwarding and receiving → Receive data → Add new**

Adicione a porta de receção pretendida.

Neste exemplo:

```text
9997
```

Assim, o servidor Splunk ficará preparado para receber os dados enviados pelo Forwarder através da porta `9997`.

---

## 2. Configurar o envio de dados do Forwarder para o servidor

No **cliente onde está instalado o Universal Forwarder**, execute:

```bash
sudo /opt/splunkforwarder/bin/splunk add forward-server IP_DO_SERVIDOR:PORTA
```

Exemplo:

```bash
sudo /opt/splunkforwarder/bin/splunk add forward-server 10.0.1.75:9997
```

O comando poderá pedir as credenciais do administrador do Universal Forwarder.

> Neste exemplo, a porta `9997` é utilizada para o envio dos eventos para o servidor Splunk.

---

## 3. Configurar o Agent Management

Se pretende que o agente apareça em:

**Agent Management → Forwarders**

é necessário configurar no cliente o servidor responsável pela gestão dos Forwarders.

No cliente, execute:

```bash
sudo /opt/splunkforwarder/bin/splunk set deploy-poll 10.0.1.75:8089
```

Neste exemplo:

```text
10.0.1.75 → IP do servidor Splunk
8089       → porta utilizada para a gestão
```

> **Importante:** a porta `8089` é utilizada para a gestão do Forwarder, enquanto a porta `9997` é utilizada para receber os eventos enviados pelo Forwarder.

---

## 4. Reiniciar o Forwarder

Depois de alterar as configurações, reinicie o Universal Forwarder:

```bash
sudo /opt/splunkforwarder/bin/splunk restart
```

O **Splunk Manager/Enterprise não precisa normalmente de ser reiniciado** apenas por configurar o Forwarder. Reinicie-o apenas se tiver feito alguma alteração que exija reinício.

---

# Verificações

Depois da configuração, existem algumas verificações importantes para confirmar que o Forwarder está corretamente ligado ao servidor.

## 1. Confirmar o servidor de envio

No cliente, execute:

```bash
sudo /opt/splunkforwarder/bin/splunk list forward-server
```

Deverá aparecer algo semelhante a:

```text
Active forwards:
        10.0.1.75:9997

Configured but inactive forwards:
        None
```

Se aparecer em **Active forwards**, significa que o Forwarder tem uma ligação ativa ao servidor configurado.

---

## 2. Confirmar a configuração do Agent Management

No cliente, execute:

```bash
sudo cat /opt/splunkforwarder/etc/system/local/deploymentclient.conf
```

Deverá aparecer:

```ini
[target-broker:deploymentServer]
targetUri = 10.0.1.75:8089
```

Isto confirma que o Universal Forwarder está configurado para contactar o servidor de **Agent Management** através da porta `8089`.

---

## 3. Confirmar no Splunk

No Splunk Enterprise, aceda a:

**Settings → Agent Management → Forwarders**

O agente deverá aparecer na lista.

Também pode consultar:

**Monitoring Console → Forwarders**

para verificar o estado, ligações e atividade dos Forwarders.

---

# Resumo das portas

| Porta | Função |
|---|---|
| **8089/TCP** | Gestão do Universal Forwarder / Agent Management |
| **9997/TCP** | Receção dos eventos enviados pelo Universal Forwarder |

### Fluxo da comunicação

```text
                 SERVIDOR SPLUNK
                    10.0.1.75
                       │
             ┌─────────┴─────────┐
             │                   │
          TCP 8089            TCP 9997
          Gestão              Receção
             │                   │
             │                   ▲
             ▼                   │
       Universal Forwarder ──────┘
          CLIENTE
```

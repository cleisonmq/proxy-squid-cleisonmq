# Instalação do Splunk Universal Forwarder

## 1. Descarregar o Splunk Universal Forwarder

O Universal Forwarder pode ser descarregado no site oficial da Splunk:

https://www.splunk.com/en_us/download/universal-forwarder.html

É necessário iniciar sessão com a sua conta Splunk para efetuar o download.

Depois de descarregar o pacote, siga as instruções do manual oficial ou siga as intruções a baixo:

https://help.splunk.com/en/splunk-cloud-platform/forward-and-process-data/universal-forwarder-manual/9.4/install-the-universal-forwarder/install-a-nix-universal-forwarder#bfa92018_7238_476c_8351_2dd1ee65ef8c--en__Install_the_universal_forwarder_on_Linux

---

## 2. Preparar a instalação

Faça login como **root** na máquina onde pretende instalar o Universal Forwarder.

### Criar o utilizador e o grupo do Splunk

```bash
useradd -m splunkfwd
groupadd splunkfwd
```

> Dependendo da versão e do método de instalação, o instalador pode criar automaticamente o utilizador `splunkfwd`. Se este utilizador já existir, não é necessário criá-lo novamente.

### Definir a diretoria de instalação

Neste exemplo, o Universal Forwarder será instalado em "/opt/splunkforwarder"

Defina a variável `SPLUNK_HOME`:

```bash
export SPLUNK_HOME="/opt/splunkforwarder"
```

Crie a diretoria:

```bash
mkdir $SPLUNK_HOME
```

Se necessário, atribua a propriedade da diretoria ao utilizador do Splunk:

```bash
chown -R splunkfwd:splunkfwd $SPLUNK_HOME
```

---

## 3. Instalar o Universal Forwarder

Depois de descarregar o pacote `.deb`, aceda à diretoria onde o ficheiro foi descarregado.

Exemplo:

```bash
cd /home/squid-server
```

Instale o pacote:

```bash
dpkg -i splunkforwarder_package_name.deb
```

Substitua `splunkforwarder_package_name.deb` pelo nome real do ficheiro.

Por exemplo:

```bash
dpkg -i splunkforwarder-10.4.2-XXXXXXXX-linux-amd64.deb
```

> **Atenção:** escolha o pacote correspondente à arquitetura da máquina. Por exemplo, `amd64`/`x86_64` para máquinas Intel/AMD e `arm64` para máquinas ARM.

---

## 4. Iniciar o Splunk Universal Forwarder

Depois da instalação, inicie o Forwarder:

```bash
sudo $SPLUNK_HOME/bin/splunk start
```

No primeiro arranque, o Splunk pode pedir a criação das credenciais do administrador.

Guarde estas credenciais, pois serão necessárias para executar alguns comandos de configuração.

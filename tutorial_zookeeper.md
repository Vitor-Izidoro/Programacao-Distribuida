# Tutorial prático: ZooKeeper no Linux

Este tutorial mostra como instalar e utilizar o **Apache ZooKeeper** em um computador Linux para praticar os conceitos de **concorrência e coordenação distribuída** apresentados em aula.

---

## 1. Instalar o ZooKeeper

No Ubuntu/Debian, instale com:

```bash
sudo apt update
sudo apt install zookeeper zookeeperd -y
```

Após a instalação, o serviço já deve estar rodando automaticamente.  
Você pode verificar com:

```bash
systemctl status zookeeper
```

---

## 2. Iniciar o cliente do ZooKeeper

O ZooKeeper fornece um cliente interativo (`zkCli.sh`) que permite criar, consultar e modificar nós (*znodes*).  

Execute:

```bash
zkCli.sh
```

Isso abrirá o prompt do cliente, conectado ao servidor local (`localhost:2181` por padrão).

---

## 3. Comandos básicos no ZooKeeper

Dentro do cliente (`zkCli.sh`):

### Listar diretórios da raiz:
```bash
ls /
```

### Criar um znode persistente:
```bash
create /teste "olamundo"
```

### Ler o valor armazenado:
```bash
get /teste
```

### Atualizar o valor:
```bash
set /teste "novo_valor"
```

### Deletar o znode:
```bash
delete /teste
```

---

## 4. Criando znodes especiais

### Criar um znode **efêmero** (desaparece quando a sessão é encerrada):
```bash
create -e /ephemeral "temporario"
```

### Criar um znode **sequencial** (gera identificador único incremental):
```bash
create -s /seq "dado"
```

Isso gera algo como:
```
/seq0000000001
```

---

## 5. Watches (notificações de mudança)

Comandos de *watch* permitem observar alterações em um znode.

### Criar um znode:
```bash
create /observado "inicial"
```

### Adicionar um *watch*:
```bash
get /observado true
```

Agora, em outro terminal, altere o valor:
```bash
zkCli.sh
set /observado "mudou"
```

O terminal original será notificado da mudança.

---

## 6. Exercício: Simulando um lock distribuído

Um lock garante que apenas um processo acesse um recurso de cada vez.

### Processo 1:
```bash
create -e /meu_lock "processo1"
```

Se criado com sucesso, este processo possui o lock.

### Processo 2 (em outro terminal):
```bash
create -e /meu_lock "processo2"
```

Esse comando **falhará**, pois o lock já existe.  
Quando o **Processo 1** encerrar sua sessão, o znode efêmero será apagado, liberando o lock.

---

## 7. Mais conceitos importantes sobre ZooKeeper

Além dos comandos básicos, os slides apresentam pontos importantes:

### 🔑 Tipos de znodes
- **Persistentes**: só desaparecem se deletados.  
- **Efêmeros**: desaparecem quando a sessão do cliente acaba.  
- **Sequenciais**: recebem um número incremental único, útil para **ordenação** e **eleição de líder**.  

---

### 👀 Watches (notificações)
- Permitem reagir a mudanças sem precisar ficar consultando (evitam *polling*).  
- São **de uso único** (após disparar, precisam ser re-registrados).  
- Usados em sincronização e coordenação de processos.  

---

### 🧩 Receitas de coordenação
O ZooKeeper fornece a base para construir primitivas de coordenação, como:
- **Locks distribuídos** (um processo por vez acessa recurso).  
- **Barreiras** (processos esperam uns pelos outros antes de continuar).  
- **Eleição de líder** (um processo assume papel de coordenador).  
- **Filas distribuídas** (tarefas organizadas entre consumidores).  

---

### ⚙️ Arquitetura
- ZooKeeper roda como um **conjunto de servidores** (ensemble).  
- Garante **tolerância a falhas** via protocolo **ZAB (ZooKeeper Atomic Broadcast)**.  
- Funciona desde que haja **maioria (quórum)** disponível.  

---

### 📌 Exemplo de eleição de líder
- Cada processo cria um znode sequencial: `/lider/eleicao-XXXX`.  
- O processo com o **menor número** é eleito líder.  
- Se ele falhar, o próximo da fila assume.  

---

### 🌍 Casos de uso reais
- **Apache Kafka**: gerenciamento de líderes de partições.  
- **Hadoop**: coordenação de NameNodes.  
- **HBase**: manutenção de metadados consistentes.  

Entender ZooKeeper ajuda a compreender grande parte do ecossistema **Big Data**.

---

## 8. Resumo do aprendizado

- **znodes persistentes, efêmeros e sequenciais** para armazenamento e coordenação.  
- **watches** para notificações de mudanças.  
- **receitas de coordenação** (locks, barreiras, eleição de líder, filas).  
- **arquitetura distribuída com quórum** para tolerância a falhas.  
- Aplicações reais em **Kafka, Hadoop e HBase**.  

---

## 9. Referências
- [Apache ZooKeeper](https://zookeeper.apache.org)  
- Slides: *Paralelismo, Concorrência e Coordenação Distribuída* (PUCPR)  
- F. Junqueira, B. Reed. *ZooKeeper: Distributed Process Coordination*. O’Reilly, 2014.  

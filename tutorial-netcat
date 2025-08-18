# Tutorial de Comunicação em Cadeia com `netcat`

Este tutorial mostra como configurar quatro terminais para simular a comunicação em cadeia:  
**O (Cliente inicial) → T1 (Intermediário) → T2 (Intermediário) → D (Servidor final)**  
As respostas também percorrem o caminho inverso.

---

## 1. Terminal D (Servidor final) (utilize o comando 'tmux new' para criar varios terminais)

```bash
nc -l -p 6010
```

- Abre um servidor na porta **6010**.  
- É o destino final das mensagens enviadas pelo cliente **O**.  
- Tudo o que for digitado no cliente chega aqui.

---

## 2. Terminal T2 (Intermediário)

```bash
nc -l -p 6009 | tee /dev/tty | nc 127.0.0.1 6010
```

- Escuta na porta **6009** (recebe dados de T1).  
- O `tee /dev/tty` mostra no terminal o que chega (para debug/visualização).  
- Em seguida, repassa os dados para o servidor final **D** na porta **6010**.  
- Também recebe a resposta de D e a devolve para T1.  

---

## 3. Terminal T1 (Intermediário)

```bash
nc -l -p 6008 | tee /dev/tty | nc 127.0.0.1 6009
```

- Escuta na porta **6008** (recebe dados de O).  
- O `tee /dev/tty` mostra no terminal o que chega (para debug/visualização).  
- Em seguida, repassa os dados para T2 na porta **6009**.  
- Também recebe a resposta de T2/D e envia de volta para O.  

---

## 4. Terminal O (Cliente inicial)

```bash
nc 127.0.0.1 6008
```

- Conecta ao intermediário **T1**.  
- Tudo que for digitado aqui passa pelo fluxo:  
  **O → T1 → T2 → D**  
- As respostas de **D** retornam pelo mesmo caminho de volta para **O**.  

---

## 5. Exemplo de comunicação

No **O (Cliente inicial)** digite:

```
hello
```

Fluxo da mensagem:
```
O → T1 → T2 → D
```

No **D (Servidor final)** a mensagem aparecerá como:
```
hello
```

Se o servidor responder, por exemplo:
```
world
```

Fluxo da resposta:
```
D → T2 → T1 → O
```

E no cliente **O** aparecerá:
```
world
```

---

## 6. Resumo do fluxo

```
O (Cliente inicial) → T1 (Intermediário) → T2 (Intermediário) → D (Servidor final)
D (Servidor final) → T2 (Intermediário) → T1 (Intermediário) → O (Cliente inicial)
```

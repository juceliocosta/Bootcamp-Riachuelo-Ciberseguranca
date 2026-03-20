# Setup do Ambiente (Kali + Metasploitable 2)

Este documento mostra como configurar um ambiente isolado usando **VirtualBox** com uma rede interna (host-only) para simular ataques de força bruta com Medusa.

---

## 1) Pré-requisitos

- VirtualBox instalado (Windows/Linux/macOS).
- Imagens/ISOs:
  - Kali Linux.
  - Metasploitable 2 (VM pronta).
  - DVWA (instalado dentro do Metasploitable 2).

---

## 2) Criar rede Host-Only (VirtualBox)

1. Abrir VirtualBox -> Arquivo → Ferramentas → Gerenciador de Rede -> Criar
2. Configure o adaptador com um IP estático, por exemplo:
   - Endereço: `192.168.56.1`
   - Máscara: `255.255.255.0`

---

## 3) Configurar VMs

### Kali Linux

- Crie a VM do Kali.
- Configure 1 adaptador de rede em modo **Host-only**.

### Metasploitable 2

- Importe a VM do Metasploitable 2.
- Configure 1 adaptador de rede em modo **Host-only**.

---

## 4) Verificar os endereços IP

Na VM Kali, execute:

```bash
ip a
```

Na VM Metasploitable 2 (login: `msfadmin/msfadmin`), execute:

```bash
ip a
```

Anote os IPs na rede host-only (ex: `192.168.56.101` para Kali e `192.168.56.102` para Metasploitable).

---

## 5) Acessar o DVWA

Metasploitable 2 já vem com o DVWA instalado em `http://192.168.56.102/dvwa`.

---

## 6) Verificar conectividade

Na Kali, verifique se consegue pingar o alvo:

```bash
ping -c 3 192.168.56.102
```
![](/images/ping_alvo.png)

Use também `nmap` para mapear portas básicas:

```bash
nmap -sC -sV 192.168.56.102
```
![](/images/nmap_portas_abertas.png)
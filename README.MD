## Configurando o Linux (Mint) como cliente LDAP

#### 1 - Execute as instalações:

```bash
sudo apt install libnss-ldap libpam-ldap ldap-utils nscd -y
```

### 2 - Na tela `Please enter the URI of LDAP server to use`, informe `ldap://endereco_servidor_ldap`;



### 3 - Em `Please enter the distinguished name of the LDAP search base`, informe `dc=example,dc=org`;



### 4 - Em `Please enter wich version of the LDAP protocol`, selecione a versão de número **3**;



### 5 - Em `This option will allow you to make password utillities...`, selecione **Sim**;



### 6 - Em `Choose this option if you are required to login to the database...`, escolha **Não**;



### 7 - Em `This account will be used when root changes a password`, informe `cn=admin,dc=example,dc=org`;



### 8 - Em `Please enter the password to use when ldap-auth-config tries to login...`, informe a senha do servidor LDAP.



### 9 - Com sudo, edite o arquivo /etc/nsswitch.conf e configure os itens da seguinte forma:



> password:     compat systemd ldap
> 
> group:            compat systemd ldap
> 
> shadow:         compat



### 10 - Edite /etc/pam.d/common-password e remova `use_authtok` da linha 26;



### 11 - Edite /etc/pam.d/commom-session e inclua o seguinte:

```bash
session optional pam_mkhomedir.so skel=/etc/skel umask=077
```



### 12 - Execute os comandos abaixo para reiniciar os serviços:

```bash
sudo systemctl restart nscd
```

```bash
sudo systemctl enable nscd
```



### 13 - Realize uma busca para testar:

```bash
ldapsearch -x -H ldap://192.168.1.8 -b "dc=example,dc=org"
```



### 14 - Tente realizar login com um usuário LDAP:

```bash
sudo login
```



### 15 - Configure o LightDM para realizar logins no LDAP:

* Crie o arquivo `/etc/lightdm/lightdm.conf.d/50-custom.conf` e inclua o seguinte conteúdo:
  
  ```bash
  [SeatDefaults]
  greeter-show-manual-login=true
  allow-guest=false
  ```

* Reinicie o LightDM:
  
  ```bash
  sudo service lightdm restart
  ```

A opção `Iniciar Sessão` passará a ser exibida para que o login com usuário do LDAP seja informado.



Tutorial baseado em [LDAP - How to Install and Configure OpenLDAP Client on Ubuntu - YouTube](https://www.youtube.com/watch?v=c_JhrIKTEJg)

# Descripción
Configurar webhook en jenkins para que realice un build automático si se detecta cambio en el repositorio, exponiendo Url con ngrok usando una api en NodeJs.




<p align="center">
  <img src="https://github.com/D4nHide/Webhook/blob/main/pipeline_webhook.jpg"/>
</p>





# Configuración Jenkins

- Crear un nuevo pipeline, una vez en configuración seguir los siguientes pasos:
## Triggers:
- Seleccionar GitHub hook trigger for GITScm polling.
## Pipeline:  
- Definition: Pipeline script from SCM.
- SCM: Git.
- Repositories: Repository URL (Colocar url de repositorio).
- Branch Specifier: */main
- Script Path: Jenkinsfile

![image](https://github.com/user-attachments/assets/2bdd5a34-b3ac-4070-acce-a943f35e8378)

# Ngrok

- Tendremos que crear una cuenta o loguearnos con alguna ejemplo (github) en [Ngrok](https://ngrok.com) y seleccionar la plataforma a utilizar.

![image](https://github.com/user-attachments/assets/005073fe-b21a-43e1-964d-3607b5361242)

## Instalación, Authtoken, Despliegue

- Para este caso de uso lo haremos en linux abrir una terminal ctrl+alt+t

```
curl -sSL https://ngrok-agent.s3.amazonaws.com/ngrok.asc \
	| sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null \
	&& echo "deb https://ngrok-agent.s3.amazonaws.com buster main" \
	| sudo tee /etc/apt/sources.list.d/ngrok.list \
	&& sudo apt update \
	&& sudo apt install ngrok
```

```
ngrok config add-authtoken $YOUR_AUTHTOKEN (token que nos entrega ngrok una vez logueados)
```

![image](https://github.com/user-attachments/assets/9cca0fa0-1909-4747-9013-12448f7cc8cc)

- Una vez hayamos pusheado el token al YAML estaremos listos para exponer nuestro Jenkins

```
ngrok http http://tu_dirección_ip:8080/
```
La Url se conformará parecida a la siguiente donde los valores ocultados en "Forwarding" serán la dirección ip pública y la del entorno local de jenkins

![image](https://github.com/user-attachments/assets/19f54fb9-2e17-431b-9078-206a86f81a6f)

# Webhook

- Habiendo configurado los pasos anteriores, configuraremos el webhook para el repositorio a utilizar en este caso [Repositorio API Node Js](https://github.com/D4nHide/nodejs-helloworld-api)

- Ir al repositorio settings
- Seleccionar Webhook / Add Webhook
- Colocar en Paiload URL: La URL que nos creó Ngrok seguido de github-webhook/
- En este caso como se trata de un repositorio público no se configura el valor secret, de ser  privado se debe configurar el token y setearlo en ese apartado.

```
Ejemplo:

https://dirección_ngrok-/github-webhook/
```

![image](https://github.com/user-attachments/assets/f3d5b8b2-bf06-47b1-bba1-37953ef833d3)

# Comandos

1. Clonar Repositorio
```
git clone https://github.com/D4nHide/nodejs-helloworld-api.git
```
![image](https://github.com/user-attachments/assets/3780ff9f-f1a6-4806-bf24-45f122afdb4b)

2. Crear un commit vacío  (lo que se encuentra entre " " puede ser modificado con cualquier mensaje)
```
git commit --allow-empty -m "Trigger test jenkins"
```
![image](https://github.com/user-attachments/assets/5845b5ce-4a5a-4803-b155-0ef7fd07779e)

3. Cambiamos para transaccionar con el repositorio mediante SSH y no tener que autenticarnos cada vez que interactuemos con el repositorio (Para esto se debe tener configurada la llave ssh
```
git remote set-url origin git@github.com:D4nHide/nodejs-helloworld-api
```
![image](https://github.com/user-attachments/assets/cb0802b4-3812-4e5d-aaaa-b65708e539cf)

4. Pusheamos al Repositorio
```
git push origin main
```
![image](https://github.com/user-attachments/assets/168e7dac-7147-4c61-91de-f3624ff30940)














#docker #deployment 

>[!warning] **SELinux Volumes**: for anything volume related do not forget suffix **:z** for running locally

# Dockerfile
```Docker
# TODO: Add base image  
FROM openjdk:17-bullseye

# TODO: Set workdir  
WORKDIR /app 

# TODO: Copy the compiled jar  # also renames .jar file
COPY ./build/libs/H10E01-Containers-1.0.0.jar ./app.jar  

# TODO: Copy the start.sh script  
COPY start.sh .  

# TODO: Make start.sh executable  
RUN chmod 770 start.sh  

# TODO: Set the start command  
CMD ./start.sh
``` 

```bash
docker build -t <image_name> .
``` 
>[!danger] **DO NOT FORGET "." FOR REFERENCE TO CURRENT DIRECTORY**
``` bash
docker run --name <Container_name> <image_name> 
```
##### Optional Flags (for running):
- **ports** `-p 8080:80 ` `<Host>:<Container>`
- **-d** detached mode
- **-it** interactive terminal
- **-v** ` <host_volume>:<container_path>:z `  for mounting volumes with persistent data
### C-example
```Docker
# TODO 3.3 A

  

FROM gcc:latest

WORKDIR /usr/src/employee-manager

COPY . .

RUN make

CMD ./employee-manager
``` 



# DockerCompose
#compose #deployment 

## Used example
```yml
services:

  

	employee-manager-postgres:
		image: postgres:latest
		container_name: employee-manager-postgres

		volumes:
			- employee-manager-data:/var/lib/postgresql/data

		environment:
			- POSTGRES_DB=employee-manager
			- POSTGRES_USER=admin
			- POSTGRES_PASSWORD=pass
		ports:
			- "5432:5432"

  

	employee-manager-app:

		container_name: employee-manager-app
		build: .
		image: employee-manager
		environment:
			- DB_SERVER=employee-manager-postgres

		ports:
			- 8080:8080
		depends_on:
			- employee-manager-postgres

		stdin_open: true

		tty: true

  

volumes:

	employee-manager-data:

	#employee-manager.data:

``` 

### RUN
```bash
docker compose up -d
```
> -> **-d** for detached mode

```bash
docker compose down
```
#### their example:
```yml 
version: "2"  
services:  
  web:  
    image: eist-ngrok  
    environment:  
      JDBC_DATABASE_URL: jdbc:postgresql://db:5432/postgres  
      JDBC_DATABASE_USERNAME: postgres  
      JDBC_DATABASE_PASSWORD: postgres  
    depends_on:  
      - db  
    volumes:  
      - ./webapp/spring:/opt/webapp  
    networks:  
      - eist  
  db:  
    image: postgres:latest  
    ports:  
      - "5432:5432"  
    volumes:  
      - ./webapp/postgres:/var/lib/postgresql/data  
    environment:  
      POSTGRES_USER: postgres  
      POSTGRES_PASSWORD: postgres  
    networks:  
      - eist  
  ngrok:  
    image: ngrok/ngrok:latest  
    restart: unless-stopped  
    depends_on:  
      - db  
      - web  
    command:  
      - "http"  
      - "web:8080"  
      - "--config"  
      - "/etc/ngrok.yml"  
    volumes:  
      - ./ngrok.yml:/etc/ngrok.yml:z  
    ports:  
      - "4040:4040"  
    networks:  
      - eist  
  
networks:  
  eist:  
    driver: "bridge"
```



# Ngrok
#ngrok
>[!quote] **Auth key**
> ``` 
>2VexD4xsSlk1VvlqFQr6jDXq2dB_AyksTQ4i1wyzMghqWSm3
>```
>



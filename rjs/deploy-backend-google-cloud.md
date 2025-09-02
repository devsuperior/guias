# Implantação do backend Spring Boot no Google Cloud

## 1. Preparar o projeto e salvar no Github

- Incluir arquivo `Dockerfile` na pasta raiz do projeto (código abaixo)
- Incluir o host do frontend na lista de CORS
- Salvar o projeto no Github

```dockerfile
# Build stage
FROM maven:3.9.6-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean install

# Package stage
FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

## 2. Criar projeto no Google Cloud

https://console.cloud.google.com

Menu superior → Cloud Run → Services

Aba: Conectar repositório

Opção: Realizar a implantação contínua com um repositório

Configurar com o Cloud Build
- Acessar Github, selecionar repositório, avançar
- Dockerfile 

Configurar
- Região: us-central1
- Autenticação: Permitir acesso público
- Faturamento: Baseada em solicitações
- Escalonamento de serviços: mínimo 0, máximo 1
- Entrada: todos


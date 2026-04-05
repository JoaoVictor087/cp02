# Checkpoint 2 - DevOps Tools & Cloud Computing

Esse projeto consiste numa API SpringBoot com deploy WebApp via AzureCLI

## Integrantes

| Integrantes             | RM       |
|-------------------------|----------|
| João Victor Alves       | RM559726 |
| Vinicius Kenzo Tocuyosi | RM559982 |
| Juan Pablo Rebelo       | RM560445 |

## Como realizar o Deploy (How to)

### Pré-requisitos
```text
AzureCLI (com autenticação pronta)
Java 21
```
### Definicação das variáveis
```shell
RESOURCE_GROUP="rg-cp02"
LOCATION="brazilsouth"
SQL_SERVER_NAME="sql-dimdim-$RANDOM"
SQL_DB_NAME="db_dimdim"
ADMIN_USER="azureuser"
ADMIN_PASSWORD="Fiap@2tdspa"
APP_SERVICE_PLAN="plan-dimdim"
WEB_APP_NAME="webapp-dimdim-$RANDOM"
APP_INSIGHTS_NAME="insights-dimdim"
```

### Criação do banco de dados + WebApi com monitoração e firewall liberado

```shell
az group create --name $RESOURCE_GROUP --location $LOCATION
az sql server create --name $SQL_SERVER_NAME --resource-group $RESOURCE_GROUP \
    --admin-user $ADMIN_USER --admin-password $ADMIN_PASSWORD
az sql db create --resource-group $RESOURCE_GROUP --server $SQL_SERVER_NAME \
    --name $SQL_DB_NAME --service-objective S0
az sql server firewall-rule create --resource-group $RESOURCE_GROUP \
    --server $SQL_SERVER_NAME --name AllowAzure --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
az appservice plan create --name $APP_PLAN --resource-group $RESOURCE_GROUP --is-linux --sku B1
az webapp create --resource-group $RESOURCE_GROUP --plan $APP_PLAN \
    --name $WEB_APP_NAME --runtime "JAVA:21-java21"
az monitor app-insights component create --app $WEB_APP_NAME-insights \
    --location $LOCATION --resource-group $RESOURCE_GROUP
```

### Clone do projeto

```shell
git clone https://github.com/JoaoVictor087/cp02.git
cd cp02
gradlew build -x test (Windows)
./gradlew build -x test (Linux)
```

### Deploy do projeto

```shell
az webapp config appsettings set --name $WEB_APP_NAME --resource-group $RESOURCE_GROUP \
  --settings SPRING_DATASOURCE_URL="jdbc:sqlserver://$SQL_SERVER_NAME.database.windows.net:1433;database=$SQL_DB_NAME" \
             SPRING_DATASOURCE_USERNAME="$ADMIN_USER" \
             SPRING_DATASOURCE_PASSWORD="$ADMIN_PASSWORD"
```

```shell
JAR_PATH="build/libs/cp02-0.0.1-SNAPSHOT.jar"

az webapp deploy --name $WEB_APP_NAME --resource-group $RESOURCE_GROUP --src-path $JAR_PATH --type jar
```

Agora o WebApp estará disponível no endereço provido pela Microsoft Azure
deploy do API: webapp-dimdim-17893.azurewebsites.net


[Link do video](www.google.com)

---
title: Azure Spring Cloud CI/CD GitHub-műveletekkel
description: CI/CD-munkafolyamatok létrehozása az Azure Spring Cloud-hoz a GitHub-műveletekkel
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.custom: devx-track-java
ms.openlocfilehash: 960e480bc3271a24f1fa898e1af6c55bcaaada7b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037559"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD GitHub-műveletekkel

A GitHub-műveletek egy automatizált szoftverfejlesztési életciklus-munkafolyamatot támogatnak. Az Azure Spring Cloud GitHub-műveleteivel munkafolyamatokat hozhat létre a tárházban az Azure-ban történő létrehozásához, teszteléséhez, csomagolásához, kiadásához és üzembe helyezéséhez. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez a példához az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)szükséges.

## <a name="set-up-github-repository-and-authenticate"></a>GitHub-adattár beállítása és hitelesítése
Szüksége lesz egy Azure-szolgáltatás egyszerű hitelesítő adataira az Azure bejelentkezési műveletének engedélyezéséhez. Azure-beli hitelesítő adatok beszerzéséhez hajtsa végre a következő parancsokat a helyi gépen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Egy adott erőforráscsoport eléréséhez csökkentheti a hatókört:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
A parancsnak egy JSON-objektumot kell kiadnia:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [Piggy metrikai](https://github.com/Azure-Samples/piggymetrics) mintát használja a githubon.  Elágazás a minta, nyissa meg a GitHub-adattár lapot, majd kattintson a **Beállítások** fülre. Nyissa meg a **titkok** menüt, és kattintson **az új titkos kulcs hozzáadása**lehetőségre:

 ![Új titok hozzáadása](./media/github-actions/actions1.png)

Állítsa be a titkos nevet `AZURE_CREDENTIALS` és annak értékét a *GitHub-tárház beállítása és hitelesítése*fejléc alatt található JSON-karakterláncra.

 ![Titkos adatértékek beállítása](./media/github-actions/actions2.png)

Azt is megteheti, hogy az Azure-beli bejelentkezési hitelesítő adatokat Key Vault a GitHub-műveletekben, ahogy az az [Azure Spring hitelesítése Key Vault a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Szolgáltatási példány kiépítése
Az Azure Spring Cloud Service-példány kiépítéséhez futtassa az alábbi parancsokat az Azure CLI használatával.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>A munkafolyamat összeállítása
A munkafolyamat a következő beállításokkal van definiálva.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az Azure CLI-vel való üzembe helyezésre
A parancs `az spring-cloud app create` jelenleg nem idempotens.  Ezt a munkafolyamatot a meglévő Azure Spring Cloud-alkalmazásokon és-példányokon ajánljuk.

Az előkészítéshez használja az alábbi Azure CLI-parancsokat:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés az Azure CLI-vel közvetlenül
Hozza létre a `.github/workflow/main.yml` fájlt a tárházban:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Üzembe helyezés az Azure CLI-vel művelettel
Az az `run` parancs az Azure CLI legújabb verzióját fogja használni. A változtatások megszakításakor az Azure CLI egy adott verzióját is használhatja az Azure/CLI-vel `action` . 

> [!Note] 
> Ez a parancs egy új tárolóban fog futni, ezért `env` nem fog működni, és a Többműveletes fájlhoz való hozzáférés további korlátozásokkal rendelkezhet.

Hozza létre a. GitHub/munkafolyamat/Main. YML fájlt az adattárban:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Üzembe helyezés a Maven beépülő modullal
Egy másik lehetőség, hogy a [Maven beépülő modult](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) használja a jar üzembe helyezéséhez és az alkalmazás beállításainak frissítéséhez. A parancs `mvn azure-spring-cloud:deploy` idempotens, és szükség esetén automatikusan hozza létre az alkalmazásokat. Nem kell előre létrehoznia a megfelelő alkalmazásokat.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>A munkafolyamat futtatása
A GitHub- **műveleteket** automatikusan engedélyezni kell `.github/workflow/main.yml` a githubra való leküldés után. A művelet akkor aktiválódik, amikor új véglegesítet küld. Ha ezt a fájlt a böngészőben hozza létre, a műveletnek már futnia kell.

Annak ellenőrzéséhez, hogy engedélyezve van-e a művelet, kattintson a **műveletek** fülre a GitHub-adattár lapon:

 ![A művelet engedélyezésének ellenőrzése](./media/github-actions/actions3.png)

Ha a művelet hibát jelez, például ha még nem állította be az Azure-beli hitelesítő adatokat, akkor a hiba kijavítása után újrafuttathatja az ellenőrzéseket. A GitHub-adattár lapon kattintson a **műveletek**elemre, jelölje ki az adott munkafolyamat-feladatot, majd kattintson az **ellenőrzések újrafuttatása** gombra az ellenőrzések újrafuttatásához:

 ![Ellenőrzések újrafuttatása](./media/github-actions/actions4.png)

## <a name="next-steps"></a>További lépések
* [Key Vault a Spring Cloud GitHub-műveletekhez](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory egyszerű szolgáltatások](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub-műveletek az Azure-hoz](https://github.com/Azure/actions/)

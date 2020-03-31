---
title: Azure Spring Cloud CI/CD GitHub-műveletekkel
description: CI/CD-munkafolyamat kiépítése az Azure Spring Cloud számára a GitHub-műveletekkel
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538464"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD GitHub-műveletekkel

A GitHub-műveletek támogatják az automatizált szoftverfejlesztési életciklus-munkafolyamatot. Az Azure Spring Cloud hoz létre munkafolyamatokat a GitHub-műveletek az Azure Tavaszi Felhőhöz használatával munkafolyamatokat hozhat létre az Azure-ban, tesztelheti, csomagolhatja, felszabadíthatja és üzembe helyezheti az Azure-ban. 

## <a name="prerequisites"></a>Előfeltételek
Ebben a példában az [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="set-up-github-repository-and-authenticate"></a>A GitHub-tárház beállítása és hitelesítése
Az Azure bejelentkezési művelet engedélyezéséhez szüksége van egy Azure-szolgáltatás elvi hitelesítő adataira. Azure-hitelesítő adatok beszerezéséhez hajtsa végre a következő parancsokat a helyi számítógépen:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Egy adott erőforráscsoporthoz való hozzáféréshez csökkentheti a hatókört:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
A parancsnak JSON-objektumot kell kiadnia:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ez a példa a [Piggy metrikák](https://github.com/Azure-Samples/piggymetrics) mintát használja a GitHubon.  Elágazás, nyissa meg a GitHub-tárház lapját, és kattintson a **Beállítások** fülre. **Titkok** megnyitása menüben kattintson **az Új titkos adat hozzáadása**parancsra:

 ![Új titkos titok hozzáadása](./media/github-actions/actions1.png)

Állítsa be a `AZURE_CREDENTIALS` titkos nevet, és annak értékét a JSON-karakterláncra, amelyet a *GitHub-tárház beállítása és hitelesítés*i do-cím alatt talált.

 ![Titkos adatok beállítása](./media/github-actions/actions2.png)

Az Azure-bejelentkezési hitelesítő adatokat a Key Vault ból is beszerezheti a GitHub-műveletekben az [Azure Spring hitelesítése a Key Vaultsegítségével a GitHub-műveletekben](./spring-cloud-github-actions-key-vault.md)című részében leírtak szerint.

## <a name="provision-service-instance"></a>Szolgáltatáspéldány kiépítése
Az Azure Spring Cloud szolgáltatáspéldány kiépítéséhez futtassa a következő parancsokat az Azure CLI használatával.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>A munkafolyamat létrehozása
A munkafolyamat a következő beállításokkal van definiálva.

### <a name="prepare-for-deployment-with-azure-cli"></a>Felkészülés az Azure CLI-vel való telepítésre
A `az spring-cloud app create` parancs jelenleg nem idempotens.  Ezt a munkafolyamatot a meglévő Azure Spring Cloud-alkalmazások és-példányok esetén javasoljuk.

A következő Azure CLI-parancsok at használhatja az előkészítéshez:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Üzembe helyezés közvetlenül az Azure CLI-vel
Hozza `.github/workflow/main.yml` létre a fájlt a tárházban:

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
        mvn clean package -D skipTests
    
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
### <a name="deploy-with-azure-cli-action"></a>Üzembe helyezés az Azure CLI művelettel
Az `run` az parancs az Azure CLI legújabb verzióját fogja használni. Ha vannak törésmódosítások, az Azure CLI egy adott verzióját is `action`használhatja az azure/CLI-vel. 

> [!Note] 
> Ez a parancs egy új `env` tárolóban fog futni, így nem fog működni, és a keresztműveletfájlok hoz való hozzáférés további korlátozásokkal rendelkezhet.

Hozza létre a .github/workflow/main.yml fájlt a tárházban:
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
        mvn clean package -D skipTests
        
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
Egy másik lehetőség a [Maven Plugin](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) használata a Jar telepítéséhez és az alkalmazásbeállítások frissítéséhez. A `mvn azure-spring-cloud:deploy` parancs idempotens, és szükség esetén automatikusan létrehozza az alkalmazásokat. Nem kell előre létrehoznia a megfelelő alkalmazásokat.

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
        mvn clean package -D skipTests
        
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
A GitHub-műveleteket automatikusan `.github/workflow/main.yml` engedélyezni kell, miután leküldéses A GitHub. **Actions** A művelet akkor aktiválódik, amikor új véglegesítést kezdeményez. Ha ezt a fájlt a böngészőben hozza létre, a műveletnek már futnia kellett volna.

Annak ellenőrzéséhez, hogy a művelet engedélyezve van-e, kattintson a GitHub-tárház lapján a **Műveletek** fülre:

 ![Engedélyezett művelet ellenőrzése](./media/github-actions/actions3.png)

Ha a művelet hibásan fut, például ha nem állította be az Azure hitelesítő adatait, a hiba kijavítása után újra futtathatja az ellenőrzéseket. A GitHub-tárház lapján kattintson a **Műveletek**elemre, jelölje ki az adott munkafolyamat-feladatot, majd kattintson a **csekkek újrafuttatása** gombra az ellenőrzések újbóli futtatásához:

 ![Ellenőrzések ismétlése](./media/github-actions/actions4.png)

## <a name="next-steps"></a>További lépések
* [Key Vault a tavaszi felhőbeli GitHub-műveletekhez](./spring-cloud-github-actions-key-vault.md)
* [Az Azure Active Directory egyszerű szolgáltatásai](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [GitHub-műveletek az Azure-hoz](https://github.com/Azure/actions/)

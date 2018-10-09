---
title: Az Azure CLI végrehajtása Jenkins használatával
description: Megtudhatja, hogyan használhatja az Azure CLI-t egy Java-webalkalmazás üzembe helyezéséhez az Azure-ban Jenkins-folyamat részeként
ms.service: jenkins
keywords: jenkins, azure, devops, app service, cli
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: b9ca8848da543bbfb27246109c3a4ab97eb6bc58
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974918"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Üzembe helyezés az Azure App Service-ben a Jenkinsszel és az Azure CLI használatával
Java-webalkalmazás Azure-beli üzembe helyezéséhez használhatja az Azure CLI-t egy [Jenkins-folyamatban](https://jenkins.io/doc/book/pipeline/). Ebben az oktatóanyagban létrehozhat egy CI/CD folyamatot egy Azure-beli virtuális gépen, továbbá megismerkedhet a következőkkel is:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * GitHub-adattár előkészítése
> * Jenkins-folyamat létrehozása
> * A folyamat futtatása és a webalkalmazás ellenőrzése

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Jenkins-példány létrehozása és konfigurálása
Ha még nem rendelkezik Jenkins-főkiszolgálóval, kezdje a [megoldássablonnal](install-jenkins-solution-template.md), amely alapértelmezés szerint tartalmazza az [Azure Credentials](https://plugins.jenkins.io/azure-credentials) beépülő modult. 

Az Azure Credentials beépülő modul segítségével tárolhatóak Microsoft Azure-szolgáltatásnevek a Jenkinsben használt hitelesítő adatai. Az 1.2-es verzió támogatja, hogy a Jenkins-folyamat lekérje az Azure-beli hitelesítő adatokat. 

Győződjön meg róla, hogy 1.2-es vagy újabb verziót használ:
* A Jenkins irányítópultján kattintson a **Manage Jenkins (Jenkins kezelése) -> Plugin Manager (Beépülőmodul-kezelő)** lehetőségre, és keressen az **Azure Credential** kifejezésre. 
* Frissítse a beépülő modult, ha 1.2-esnél korábbi verziójú.

A Jenkins-főkiszolgálóban emellett a Java JDK és a Maven is szükséges. A telepítéshez jelentkezzen be a Jenkins-főkiszolgálóra SSH használatával, és futtassa a következő parancsokat:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Azure-szolgáltatásnév hozzáadása Jenkinsbeli hitelesítő adathoz

Az Azure CLI végrehajtásához Azure-beli hitelesítő adatok szükségesek.

* A Jenkins irányítópultján kattintson a **Credentials (Hitelesítő adatok) -> System (Rendszer)** lehetőségre. Kattintson a **Global credentials (unrestricted) (Globális hitelesítő adatok (korlátlan))** elemre.
* [Microsoft Azure-szolgáltatásnév](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) hozzáadásához kattintson az **Add Credentials (Hitelesítő adatok hozzáadása)** lehetőségre, és töltse ki a következő mezőket: Subscription ID (Előfizetés azonosítója), Client ID (Ügyfél-azonosító), Client Secret (Titkos ügyfélkód) és OAuth 2.0 Token Endpoint (Jogkivonatcserélő OAuth 2.0-végpont). Adjon meg egy azonosítót a következő lépésekben történő használathoz.

![Hitelesítő adatok hozzáadása](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Azure App Service létrehozása Java-webalkalmazás üzembe helyezéséhez

Az [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) parancssori felületi paranccsal hozzon létre egy Azure App Service-csomagot az **INGYENES** tarifacsomaggal. Az App Service-csomag határozza meg az alkalmazások üzemeltetéséhez használt fizikai erőforrásokat. Az App Service-csomaghoz rendelt összes alkalmazás ugyanezeket az erőforrásokat használja, így több alkalmazás üzemeltetése esetén is csökkenthetők a költségek. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Amikor készen áll a csomag, az Azure CLI az alábbi példához hasonló kimenetet jelenít meg:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Azure-webalkalmazás létrehozása

 Az [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) parancssori felületi paranccsal hozzon létre egy webalkalmazás-definíciót a `myAppServicePlan` App Service-csomagban. A webalkalmazás-definíció egy URL-címet biztosít, amelyen keresztül az alkalmazás elérhető, valamint több beállítást is konfigurál a kód az Azure-ban történő üzembe helyezéséhez. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Az `<app_name>` helyőrző helyébe a saját egyedi alkalmazásnevét írja. Ez az egyedi név a webalkalmazás alapértelmezett tartománynevének részét képezi majd, így egyedi alkalmazásnévnek kell lennie a teljes Azure-ban. Leképezhet egy egyéni tartománynév-bejegyzést a webalkalmazásához, mielőtt elérhetővé teszi a felhasználók számára.

A webalkalmazás-definíció megadása után az Azure CLI az alábbi példához hasonló információkat jelenít meg: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>A Java konfigurálása 

Az [az appservice web config update](/cli/azure/webapp/config#az-appservice-web-config-update) paranccsal konfigurálja a Java-futtatókörnyezetet az alkalmazás számára.

Az alábbi parancs a webalkalmazást arra konfigurálja, hogy az újabb Java 8 JDK-n és az [Apache Tomcat](http://tomcat.apache.org/) 8.0-s verzióján fusson.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>GitHub-adattár előkészítése
Nyissa meg az [Azure-hoz készült egyszerű Java-webalkalmazást](https://github.com/azure-devops/javawebappsample) bemutató adattárat. Ahhoz, hogy elágaztassa a példatárat a saját GitHub-fiókja felé, kattintson a **Fork** (Elágaztatás) gombra a jobb felső sarokban.

* A GitHub webes felhasználói felületén nyissa meg a **Jenkinsfile** fájlt. A ceruza ikonra kattintva szerkessze a fájlt, és módosítsa a webalkalmazás erőforráscsoportját és nevét a 20. és a 21. sorban.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Módosítsa a 23. sort, hogy a hitelesítési azonosító frissüljön a Jenkins-példányban.

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Jenkins-folyamat létrehozása
Nyissa meg a Jenkinst egy webböngészőben, és kattintson a **New Item** (Új elem) lehetőségre. 

* Adja meg a feladat nevét, és válassza a **Pipeline** (Folyamat) lehetőséget. Kattintson az **OK** gombra.
* Ezután kattintson a **Pipeline (Folyamat)** lapra. 
* A **Definition (Definíció)** értékeként válassza a **Pipeline script from SCM (Folyamatszkript SCM-ből)** lehetőséget.
* Az **SCM** értékeként válassza a **Git** lehetőséget.
* Adja meg az elágaztatott adattár GitHub URL-címét: https:\<elágaztatott adattár\>.git
* Kattintson a **Mentés** gombra.

## <a name="test-your-pipeline"></a>A folyamat tesztelése
* Lépjen a létrehozott folyamatra, majd kattintson a **Build Now** (Buildelés most) lehetőségre.
* A buildelés néhány másodperc alatt befejeződik. Ezután a buildhez lépve és a **Console Output (Konzolkimenet)** lehetőségre kattintva megtekintheti a részleteket.

## <a name="verify-your-web-app"></a>Webalkalmazás ellenőrzése
Ezzel az eljárással ellenőrizhető, hogy a WAR-fájl sikeresen lett-e üzembe helyezve a webalkalmazásban. Nyisson meg egy webböngészőt:

* Lépjen a http://&lt;alkalmazás_neve>.azurewebsites.net/api/calculator/ping helyre.  
A következőt fogja látni:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Lépjen a http://&lt;alkalmazás_neve>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> helyre (&lt;x> és &lt;y> helyére írjon be egy-egy tetszőleges számot), hogy megkapja x és y összegét.

![Kalkulátor: hozzáadás](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Üzembe helyezés az Azure Web App on Linuxban
Most, hogy megismerkedett az Azure CLI használatával Jenkins-folyamatban, módosíthatja a szkriptet az Azure Web App on Linuxba való üzembe helyezéshez.

A Web App on Linux egy eltérő üzembehelyezési módszert támogat: a Docker használatát. Az üzembe helyezéshez meg kell adnia egy Docker-fájlt, amely a webalkalmazást a szolgáltatási futtatókörnyezettel együtt egy Docker-rendszerképbe csomagolja. A beépülő modul ezután létrehozza a rendszerképet, leküldi a Docker regisztrációs adatbázisba, majd üzembe helyezi a rendszerképet a webalkalmazásban.

* Kövesse az [itt](../app-service/containers/quickstart-nodejs.md) megadott lépéseket Linuxon futó Azure-webalkalmazás létrehozásához.
* Telepítse a Dockert a Jenkins-példányon [ezen cikk](https://docs.docker.com/engine/installation/linux/ubuntu/) utasításait követve.
* Hozzon létre egy tárolóregisztrációs adatbázist az Azure Portalon az [itt](/azure/container-registry/container-registry-get-started-azure-cli) megadott lépéseket követve.
* Ugyanabban az elágaztatott [Azure-hoz készült egyszerű Java-webalkalmazást](https://github.com/azure-devops/javawebappsample) bemutató adattárban szerkessze a **Jenkinsfile2** fájlt:
    * A 18–21. sorban frissítse az erőforráscsoport, a webalkalmazás és az ACR nevét. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * A 24. sorban frissítse az \<azsrvprincipal\> azonosítót a hitelesítő adat azonosítójára.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Hozzon létre egy új Jenkins-folyamatot ugyanúgy, ahogyan Windows rendszeren az Azure-webalkalmazásba való üzembe helyezéskor tette, ezúttal azonban használja a **Jenkinsfile2** fájlt.
* Futtassa az új feladatot.
* Az ellenőrzéshez az Azure CLI-n futtassa a következőt:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    A következő eredményt kapja:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Lépjen a http://&lt;alkalmazás_neve>.azurewebsites.net/api/calculator/ping helyre. A következő üzenet jelenik meg: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Lépjen a http://&lt;alkalmazás_neve>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> helyre (&lt;x> és &lt;y> helyére írjon be egy-egy tetszőleges számot), hogy megkapja x és y összegét.
    
## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy Jenkins-folyamatot konfigurált, amely ellenőrzi a forráskódot a GitHub-adattárban. A Maven futtatásával létrehoz egy War-fájlt, majd az Azure CLI használatával üzembe helyezést végez az Azure App Service-be. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * GitHub-adattár előkészítése
> * Jenkins-folyamat létrehozása
> * A folyamat futtatása és a webalkalmazás ellenőrzése

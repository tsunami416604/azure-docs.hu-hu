---
title: Hajtsa végre a Jenkins az Azure CLI |} A Microsoft Docs
description: 'Útmutató: Java webalkalmazás üzembe helyezése az Azure-folyamat Jenkins az Azure CLI használatával'
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 1796e9f76e39334c8bbdd03463a0f91e9b47cb17
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421304"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>A Jenkins és az Azure CLI az Azure App Service üzembe helyezése
Egy Java webes alkalmazás üzembe helyezése az Azure-ba, használhatja az Azure CLI-vel [Jenkins folyamat](https://jenkins.io/doc/book/pipeline/). Ebben az oktatóanyagban létrehozhat egy CI/CD folyamatot egy Azure-beli virtuális gépen, továbbá megismerkedhet a következőkkel is:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * Egy GitHub-tárház előkészítése
> * A Jenkins-folyamat létrehozása
> * A folyamat, és ellenőrizze a webalkalmazás

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Hozzon létre és konfigurálja a Jenkins-példány
Ha Ön még nem rendelkezik Jenkins-főkiszolgáló, kezdje a [Megoldássablon](install-jenkins-solution-template.md), amely tartalmazza a szükséges [Azure hitelesítő adatok](https://plugins.jenkins.io/azure-credentials) beépülő modul alapértelmezés szerint. 

Az Azure Credential beépülő modul a Microsoft Azure szolgáltatásnév hitelesítő adatainak tárolása Jenkins teszi lehetővé. Az 1.2-es verzióban hozzáadtunk a támogatási így, hogy a Jenkins-folyamat az Azure-beli hitelesítő. 

Győződjön meg arról, hogy a 1.2-es vagy újabb verziója:
* A Jenkins irányítópulton kattintson **Jenkins kezelése-kezelő beépülő modul > ->** és keressen rá a **Azure Credential**. 
* Frissítse a beépülő modul, 1.2-es verziónál korábbi verzió esetén.

A Jenkins-főkiszolgáló Java JDK és a maven használatával is szükséges. Szeretné telepíteni, jelentkezzen be a Jenkins-főkiszolgáló SSH-val, és futtassa a következő parancsokat:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Egyszerű szolgáltatás Azure Jenkins hitelesítő adat hozzáadása

Hajtsa végre az Azure CLI az Azure hitelesítő adat van szükség.

* A Jenkins irányítópulton kattintson **hitelesítő adatok -> rendszer ->**. Kattintson a **globális credentials(unrestricted)**.
* Kattintson a **hitelesítő adatok hozzáadása** hozzáadása egy [Microsoft Azure-szolgáltatásnév](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) az előfizetés-azonosító, ügyfél-azonosító, titkos Ügyfélkód és OAuth 2.0 jogkivonat-végpont kitöltésével. Adja meg egy Azonosítót későbbi lépésben való használatra.

![Hitelesítő adatok hozzáadása](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Hozzon létre egy Azure App Service-ben a Java-webalkalmazás üzembe helyezéséhez

Hozzon létre egy Azure App Service-csomagot a **ingyenes** tarifacsomaggal a [az App Service-csomag létrehozása](/cli/azure/appservice/plan#az-appservice-plan-create) CLI-parancsot. Az App Service-csomag határozza meg az alkalmazások üzemeltetéséhez használt fizikai erőforrásokat. Az App Service-csomaghoz rendelt összes alkalmazás ugyanezeket az erőforrásokat használja, így több alkalmazás üzemeltetése esetén is csökkenthetők a költségek. 

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

 Használja a [az webapp létrehozása](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) CLI-paranccsal hozhat létre egy webalkalmazás-definíciót a `myAppServicePlan` App Service-csomag. A webalkalmazás-definíció egy URL-címet biztosít, amelyen keresztül az alkalmazás elérhető, valamint több beállítást is konfigurál a kód az Azure-ban történő üzembe helyezéséhez. 

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

Az alkalmazás által igényelt a Java-futtatókörnyezet konfiguráció beállítása a [az appservice web config update](/cli/azure/appservice/web/config#az-appservice-web-config-update) parancsot.

Az alábbi parancs a webalkalmazást arra konfigurálja, hogy az újabb Java 8 JDK-n és az [Apache Tomcat](http://tomcat.apache.org/) 8.0-s verzióján fusson.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Egy GitHub-tárház előkészítése
Nyissa meg a [egyszerű Java Web App for Azure](https://github.com/azure-devops/javawebappsample) adattárat. Adattárház elágaztatása a saját GitHub-fiókhoz, kattintson a **elágazás** gombra a jobb felső sarokban.

* A GitHub webes felületén, nyissa meg **Jenkinsfile** fájlt. Kattintson a ceruza ikonra kattintva szerkessze a fájlt az erőforráscsoportot és a 20. és 21 sorban a webalkalmazás neve rendre frissíteni.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Sor frissítése a Jenkins-példány a hitelesítőadat-Azonosítóval 23 módosítása

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>A Jenkins-folyamat létrehozása
Egy webböngészőben nyissa meg a Jenkins, kattintson **új elem**. 

* Adjon meg egy nevet a feladat, és válasszon **folyamat**. Kattintson az **OK** gombra.
* Kattintson a **folyamat** következő lapra. 
* A **definíció**válassza **SCM parancsfájlt folyamat**.
* A **SCM**válassza **Git**.
* Adja meg az elágaztatott GitHub URL-címe: https:\<az elágaztatott\>.git
* Kattintson a **Mentés** gombra.

## <a name="test-your-pipeline"></a>A folyamat tesztelése
* Nyissa meg a létrehozott folyamat, kattintson a **létrehozása most**
* Néhány másodpercen belül a build sikeres legyen, és nyissa meg a build, és kattintson a **konzolkimenet** a részletek megtekintéséhez

## <a name="verify-your-web-app"></a>Ellenőrizze a webalkalmazás
Ellenőrizze a WAR fájl sikeresen telepítve lett a webalkalmazás. Nyisson meg egy webböngészőt:

* Go to http://&lt;app_name>.azurewebsites.net/api/calculator/ping  
Lásd:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Lépjen a http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (helyettesítse be &lt;x > és &lt;y > bármely számokkal) beolvasni az összeg, az x és y

![A Számológép: hozzáadása](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Az Azure Web App on Linux üzembe helyezése
Most, hogy megismerte az Azure CLI használata a Jenkins folyamatban, módosíthatja a szkript egy Azure-webalkalmazást Linux rendszeren történő üzembe helyezéséhez.

A Web App on Linux egy másik módja a központi telepítését, ami a dockerrel történő támogatja. Üzembe helyezéséhez meg kell adnia, hogy a webalkalmazás-futtatókörnyezet csomagokat egy Docker-rendszerképet egy docker-fájlban. A beépülő modul ezután állítsa össze a rendszerképet, leküldése a Docker-tárolójegyzék és üzembe helyezése a rendszerkép a webalkalmazáshoz.

* Kövesse a lépéseket [Itt](../app-service/containers/quickstart-nodejs.md) a linuxon futó Azure-webalkalmazás létrehozása.
* A jelen utasításokat követve telepítheti a Dockert a Jenkins-példány a [cikk](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Tároló-beállításjegyzék létrehozása az Azure Portalon a lépésekkel [Itt](/azure/container-registry/container-registry-get-started-azure-cli).
* Az egyazon [egyszerű Java Web App for Azure](https://github.com/azure-devops/javawebappsample) elágaztatott adattárat, módosítsa a **Jenkinsfile2** fájlt:
    * 18-21. sor, frissítse az erőforráscsoport, a web app és az ACR nevei jelölik. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Sor 24, a frissítés \<azsrvprincipal\> , a hitelesítőadat-Azonosítóval
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* A Jenkins új folyamat létrehozása a volt ekkor csak a Windows, az Azure-webalkalmazást való üzembe helyezés esetén használata **Jenkinsfile2** helyette.
* Az új feladat futtatása.
* Annak ellenőrzéséhez, az Azure CLI-ben futtassa:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    A következő eredményt kapja:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Lépjen a http://&lt;app_name >.azurewebsites.net/api/calculator/ping. Az üzenet jelenik meg: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Lépjen a http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (helyettesítse be &lt;x > és &lt;y > bármely számokkal) beolvasni az összeg, az x és y
    
## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy Jenkins-folyamatot, amely a GitHub-adattárat a forráskódja kivétele konfigurálva. Maven war-fájl összeállításához fut, és az Azure CLI használatával az Azure App Service üzembe helyezése. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * A Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * Egy GitHub-tárház előkészítése
> * A Jenkins-folyamat létrehozása
> * A folyamat, és ellenőrizze a webalkalmazás

---
title: "Hajtsa végre az Azure parancssori felület a Jenkins |} Microsoft Docs"
description: "Azure CLI használata Java-webalkalmazás telepítése az Azure-bA Jenkins folyamat"
services: app-service\web
documentationcenter: 
author: mlearned
manager: douge
editor: 
ms.assetid: 
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 490112417870fb3bfdb75abdb82f9adfff550f0a
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Az Azure App Service Jenkins és az Azure parancssori felület telepítése
Java-webalkalmazás telepítése az Azure-ba, használja az Azure CLI [Jenkins csővezeték](https://jenkins.io/doc/book/pipeline/). Ebben az oktatóanyagban létrehoz egy CI/CD folyamat egy Azure virtuális gépen történő is beleértve:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * A GitHub-tárházban előkészítése
> * Jenkins folyamat létrehozása
> * A folyamat fut, és ellenőrizze a webes alkalmazás

Az oktatóanyaghoz az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha frissíteni szeretne: [Az Azure CLI 2.0 telepítése]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Hozzon létre és Jenkins konfigurálása
Ha még nem rendelkezik egy Jenkins master, kezdje a [Megoldássablonban](install-jenkins-solution-template.md), mely tartalmazza a szükséges [Azure hitelesítő adatok](https://plugins.jenkins.io/azure-credentials) beépülő modul alapértelmezés szerint. 

Az Azure hitelesítő adat beépülő modul a Microsoft Azure szolgáltatás egyszerű hitelesítő adatok tárolása Jenkins teszi lehetővé. 1.2-es verziója, az azt támogatása a úgy, hogy Jenkins csővezeték beszerezheti az Azure hitelesítő adataival. 

Győződjön meg arról, hogy 1.2-es vagy újabb verziót:
* Belül Jenkins irányítópulton kattintson **Jenkins kezelése beépülő modul Manager -> ->** keresse meg a **Azure hitelesítő**. 
* Frissítse a beépülő modul, ha a régebbi, mint 1.2-es.

A Jenkins fő Java JDK és Maven is szükséges. Telepítéséhez Jenkins fő SSH használatával bejelentkezni, és futtassa a következő parancsokat:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Adja hozzá az Azure szolgáltatás egyszerű Jenkins hitelesítő adatok

Egy Azure hitelesítő adatok Azure CLI végrehajtására van szükség.

* Belül Jenkins irányítópulton kattintson **hitelesítő adatok -> rendszer ->**. Kattintson a **globális credentials(unrestricted)**.
* Kattintson a **adja hozzá a hitelesítő adatok** hozzáadása egy [Microsoft Azure szolgáltatás egyszerű](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) a előfizetés-azonosító, az ügyfél-azonosító, a titkos Ügyfélkulcs és a OAuth 2.0 Token-végpont kitöltésével. Adja meg a Azonosítót az ezt követő lépés felhasználásra.

![Adja hozzá a hitelesítő adatok](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Az Azure App Service üzembe helyezéséhez a Java-webalkalmazás létrehozása

Az Azure App Service-csomagot hozzon létre a **szabad** árképzési szint használatával a [az App Service-csomagot hozzon létre](/cli/azure/appservice/plan#create) CLI parancsot. Az App Service-csomagot határoz meg a fizikai erőforrásokat, az alkalmazások tárolására szolgálnak. Az App Service-csomagra hozzárendelt összes alkalmazás ossza meg ezeket az erőforrásokat, lehetővé téve költség menthetők, ha több alkalmazást üzemeltető. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Amikor készen áll a csomag, az Azure parancssori felület az alábbi példához hasonló kimenetét mutatja be:

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

 Használja a [az webalkalmazás létrehozása](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) CLI parancs futtatásával hozzon létre egy webalkalmazás az app-definíciót a `myAppServicePlan` App Service-csomag. A web app-definíciót érhetik el az alkalmazást az URL-CÍMÉT tartalmazza, és beállítja a kód telepítésére, az Azure számos lehetőség közül választhat. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Helyettesítő a `<app_name>` helyőrző a saját egyedi alkalmazás nevével. A egyedi név része a webes alkalmazás esetén az alapértelmezett tartomány nevét, a nevének egyedinek kell lennie az Azure-ban minden alkalmazások között. A web app egy egyéni tartomány bejegyzése előtt a felhasználók számára közzétenni leképezheti.

Amikor készen áll a web app-definíciót, az Azure parancssori felület információkat jeleníti meg az alábbi példához hasonló: 

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

### <a name="configure-java"></a>Java konfigurálása 

A Java runtime konfiguráció az alkalmazást igénylő a [az App Service web config frissítés](/cli/azure/appservice/web/config#update) parancsot.

A következő parancsot konfigurálása a webalkalmazás a legújabb Java 8 JDK futtathatnak és [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>A GitHub-tárházban előkészítése
Nyissa meg a [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample) tárházban. A tárház a saját GitHub-fiók oszthatja ketté, kattintson a **elágazás** gombra a jobb felső sarkában.

* Nyissa meg a GitHub webes felhasználói felület, **Jenkinsfile** fájlt. Kattintson a ceruza ikonra az erőforráscsoportot és a sor 20 és 21 webalkalmazás neve rendre frissíteni a fájl szerkesztéséhez.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Módosítsa a sor 23-hitelesítő adat azonosító Jenkins betűtípusainak módosítása

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Jenkins folyamat létrehozása
Nyissa meg webböngészővel Jenkins, kattintson **új elem**. 

* Adjon meg egy nevet a feladatot, és válasszon **csővezeték**. Kattintson az **OK** gombra.
* Kattintson a **csővezeték** lapon mellett. 
* A **Definition**, jelölje be **parancsfájl az SCM-feldolgozási folyamat**.
* A **SCM**, jelölje be **Git**.
* Adja meg a GitHub URL-címet a villás tárház: https:\<a villás tárház\>.git
* Kattintson a **mentése**

## <a name="test-your-pipeline"></a>A folyamat tesztelése
* Nyissa meg a létrehozott folyamat, kattintson a **Build most**
* Néhány másodpercen belül a build sikeres legyen, és nyissa meg a buildre, és kattintson a **konzol kimeneti** a részletek megtekintéséhez

## <a name="verify-your-web-app"></a>A webalkalmazás ellenőrzése
A WAR ellenőrzése fájl sikeresen telepítve lett a webes alkalmazást. Nyisson meg egy webböngészőt:

* Keresse fel a http://&lt;alkalmazás_neve >.azurewebsites.net/api/calculator/ping  
Lásd:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Keresse fel a http://&lt;alkalmazás_neve >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (helyettesítse &lt;x > és &lt;y > bármely számokkal) lekérni az összegük az x és y

![A Számológép: hozzáadása](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Az Azure Web Apphoz Linux telepítése
Most, hogy ismeri az Azure parancssori felület használata a Jenkins folyamat, a parancsfájl központi telepítése Linux Azure Web Apps módosíthatja.

Webes alkalmazás Linux egy másik módja a környezetben, amely Docker használandó támogatja. Ha szeretné telepíteni, meg kell adnia egy Dockerfile, amely csomagokat a webalkalmazás szolgáltatás futtatási idő mellett a Docker-lemezkép. A beépülő modul majd elkészíti a kép, hogy egy Docker-beállításjegyzék és a webalkalmazás a lemezkép telepítéséhez.

* Kövesse a lépéseket [Itt](../app-service/containers/quickstart-nodejs.md) egy Azure Linux rendszeren futó webalkalmazás létrehozásához.
* Ezen cikk utasításait követve telepítse Docker Jenkins példány [cikk](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Egy tároló beállításjegyzék létrehozása az Azure portálon lépések segítségével [Itt](/azure/container-registry/container-registry-get-started-azure-cli).
* Ugyanazon [egyszerű Azure Java-webalkalmazás](https://github.com/azure-devops/javawebappsample) tárház ágazik el, módosítsa a **Jenkinsfile2** fájlt:
    * 18-21. sor, illetve frissítse az erőforráscsoport, a web app és az ACR nevét. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Sor 24, a frissítés \<azsrvprincipal\> a hitelesítő adat azonosító
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Hozzon létre egy új Jenkins folyamat adott ezúttal csak a Windows Azure-webalkalmazásban való üzembe helyezés esetén használhatja **Jenkinsfile2** helyette.
* Az új feladat futtatása.
* Az Azure parancssori felület futtatásával ellenőrizheti:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    A következő eredményt kapja:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Keresse fel a http://&lt;alkalmazás_neve >.azurewebsites.net/api/calculator/ping. Az üzenet jelenik meg: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Keresse fel a http://&lt;alkalmazás_neve >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (helyettesítse &lt;x > és &lt;y > bármely számokkal) lekérni az összegük az x és y
    
## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy Jenkins folyamatot, amely a forráskódot, a GitHub-tárház kivesz konfigurálva. A war-fájl létrehozásához Maven fut, és az Azure parancssori felület használatával telepítése az Azure App Service. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Jenkins virtuális gép létrehozása
> * Jenkins konfigurálása
> * Webalkalmazás létrehozása az Azure-ban
> * A GitHub-tárházban előkészítése
> * Jenkins folyamat létrehozása
> * A folyamat fut, és ellenőrizze a webes alkalmazás

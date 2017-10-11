---
title: "Az első alkalmazás telepítése a Microsoft Azure felhőbe Foundry |} Microsoft Docs"
description: "Azure Cloud Foundry alkalmazás központi telepítése"
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b617127fc0a3f8dcae293e356ea669edcfa5deff
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>A Microsoft Azure felhőbe Foundry az első alkalmazás üzembe helyezése

[A felhő Foundry](http://cloudfoundry.org) érhető el egy népszerű nyílt forráskódú platform Microsoft Azure-on. Ebben a cikkben megmutatjuk, hogyan helyezheti üzembe és felügyelheti a felhő Foundry egy alkalmazást az Azure környezetben.

## <a name="create-a-cloud-foundry-environment"></a>A felhő Foundry környezet létrehozása

Nincsenek a felhő Foundry környezet létrehozása az Azure számos lehetőség közül választhat:

- Használja a [döntő felhő Foundry ajánlat] [ pcf-azuremarketplace] egy szabványos környezet, amely tartalmazza az PCF Ops Manager és az Azure Service Broker létrehozása az Azure piactéren. Található [utasításokkal] [ pcf-azuremarketplace-pivotaldocs] a piactér üzembe helyezéséhez nyújtanak a döntő dokumentációjában.
- Hozzon létre egy testreszabott környezetet által [döntő felhő Foundry manuális telepítése][pcf-custom].
- [Közvetlenül a nyílt forráskódú felhő Foundry csomagok központi telepítése] [ oss-cf-bosh] be kell állítania egy [BOSH](http://bosh.io) igazgató, amely koordinálja a felhő Foundry környezet központi telepítését.

> [!IMPORTANT] 
> Ha az Azure piactérről PCF telepíti, jegyezze fel a SYSTEMDOMAINURL és a rendszergazda eléréséhez szükséges hitelesítő adatokat a kulcsfontosságú alkalmazások Manager mindkettőnek a piactér telepítési útmutatóban leírt. Az oktatóanyag elvégzéséhez szükség. Piactér telepítések esetén a képernyő https://system a SYSTEMDOMAINURL van. *ip-cím*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>A felhő vezérlő kapcsolódni

A felhő tartományvezérlő a felhő Foundry környezet központi telepítéséhez és alkalmazások kezelése az elsődleges belépési pontot. Az alapvető felhőalapú vezérlő API (CCAPI) a REST API-t, de különböző eszközök keresztül érhető el. Ebben az esetben azt interaktívan keresztül a [felhő Foundry CLI][cf-cli]. A CLI-t telepítheti Linux, MacOS vagy a Windows, de ha szeretné, hogy nem telepíti azt minden, akkor érhető el az előre telepített a [Azure Cloud rendszerhéj][cloudshell-docs].

A bejelentkezéshez, illesztenie `api` a SYSTEMDOMAINURL a piactér telepítésből beszerzett számára. Mivel az alapértelmezett telepítési egy önaláírt tanúsítványt használ, akkor is tartalmaznia kell a `skip-ssl-validation` váltani.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Jelentkezzen be a felhő vezérlő kéri. A rendszergazdai fiók hitelesítő adatait, amely a piactér üzembe helyezés lépései beszerzett használja.

Felhő Foundry biztosít *szervezethez* és *szóközöket* , a csoportok és a megosztott telepítési belül környezetek elkülönítése névtereket. A PCF piactér központi telepítést magában foglalja az alapértelmezett *rendszer* szervezeti és a szóközöket a alapkomponensek tartalmazza, például az automatikus skálázást szolgáltatás és az Azure service broker. Most, válassza ki a *rendszer* terület.


## <a name="create-an-org-and-space"></a>Hozzon létre egy szervezeti és lemezterület

Ha `cf apps`, megjelenik egy adott rendszer alkalmazáscsoportra, a rendszer területen belül. a rendszer szervezeti alkalmazott 

Érdemes megtartani a *rendszer* rendszer alkalmazások számára fenntartott szervezeti így szervezeti és a mintaalkalmazás elhelyezésére terület létrehozása.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

A cél paranccsal váltson át az új szervezeti és lemezterület:

```bash
cf target -o testorg -s dev
```

Most az alkalmazás központi telepítésekor automatikusan létrejön az új szervezeti és. Győződjön meg arról, hogy jelenleg nincs alkalmazásokat az új szervezeti/tárhely, írja be a következőt `cf apps` újra.

> [!NOTE] 
> További információ a szervezethez és szóközöket és hogyan használhatók a szerepköralapú hozzáférés-vezérlést (RBAC): a [felhő Foundry dokumentáció][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Most használja a felhő Foundry mintaalkalmazás nevű Hello rugó felhő, amelyet a rendszer a Java nyelven írt alapján a [rugó keretrendszer](http://spring.io) és [rugó rendszerindító](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>A Hello rugó felhő tárház klónozása

A Hello rugó felhő mintaalkalmazást a Githubon érhető el. Klónozza a környezetben, és váltson át az új könyvtár:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

Build, az alkalmazás használatával [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Az alkalmazás cf leküldéses telepítését

A legtöbb alkalmazásokat felhő Foundry használatával helyezhet üzembe a `push` parancs:

```bash
cf push
```

Ha Ön *leküldéses* egy alkalmazást, a felhő Foundry (ebben az esetben egy alkalmazásban Java) alkalmazás észleli, és annak függőségeit (ebben az esetben az rugó keretében) azonosítja. Majd csomagok mindent lemezképpel egy önálló tároló, úgynevezett Ön kódjának futtatásához szükséges egy *feldolgozó*. Végül felhő Foundry ütemezi az alkalmazás egy, a rendelkezésre álló környezetében levő készülékek a, és létrehoz egy URL-címet, ahol érhető el, a parancs kimenetében elérhető.

![Cf leküldéses parancs kimenete][cf-push-output]

A rugó-felhő hello alkalmazás megtekintéséhez nyissa meg a böngészőben a megadott URL-cím:

![Alapértelmezett felhasználói felület Hello rugó felhő][hello-spring-cloud-basic]

> [!NOTE] 
> További információt, hogy mi történik a során `cf push`, lásd: [hogyan vannak előkészített alkalmazások] [ cf-push-docs] a felhő Foundry dokumentációjában.

## <a name="view-application-logs"></a>Alkalmazás-naplók megtekintése

A felhő Foundry CLI segítségével megtekintheti a naplók az alkalmazás neve:

```bash
cf logs hello-spring-cloud
```

Alapértelmezés szerint a naplók parancs által használt *utóhívás*, amely jeleníti meg az új naplók az oktatóprogram. Megjelenik az új naplók megtekintéséhez frissítse a hello felhőalapú rugó alkalmazást a böngészőben.

Naplók írt már megtekintéséhez adja hozzá a `recent` váltani:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Az alkalmazás skálázása

Alapértelmezés szerint `cf push` csak hoz létre az alkalmazás egyetlen példányát. Magas rendelkezésre állásának biztosításához, és nagyobb átviteli sebesség eléréséhez kibővítési engedélyezéséhez, általában futtatni kívánt alkalmazás több példánya. Már telepített alkalmazások segítségével könnyen lehet horizontálisan a `scale` parancs:

```bash
cf scale -i 2 hello-spring-cloud
```

Fut a `cf app` az alkalmazás a parancs megjeleníti, hogy felhőalapú Foundry hoz létre az alkalmazás egy másik példánya. Után az alkalmazás elindult, a felhő Foundry terheléselosztási hozzá forgalom automatikusan elindul.


## <a name="next-steps"></a>Következő lépések

- [Olvassa el a felhő Foundry dokumentációt][cloudfoundry-docs]
- [Felhő Foundry a Visual Studio Team Services beépülő modul beállítása][vsts-plugin]
- [A Microsoft napló Analytics dugulásellenőrzési felhő Foundry konfigurálása][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: http://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png
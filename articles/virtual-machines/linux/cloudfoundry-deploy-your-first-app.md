---
title: Az első alkalmazás üzembe helyezése Cloud Foundry on Microsoft Azure
description: Alkalmazás üzembe helyezése az Azure-ban Cloud Foundry
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: gwallace
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: b1f9ab5289a41aacb5514e954f1ca01f6ad66152
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036828"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Az első alkalmazás üzembe helyezése Cloud Foundry on Microsoft Azure

A [Cloud Foundry](https://cloudfoundry.org) egy népszerű, nyílt forráskódú alkalmazás-platform Microsoft Azure elérhető. Ebben a cikkben bemutatjuk, hogyan helyezhet üzembe és kezelhet egy alkalmazást Cloud Foundry Azure-környezetben.

## <a name="create-a-cloud-foundry-environment"></a>Cloud Foundry környezet létrehozása

Az Azure-ban több lehetőség is létezik Cloud Foundry környezet létrehozására:

- A PCF Ops Managert és az Azure Service Brokert tartalmazó szabványos környezet létrehozásához használja a [pivotal Cloud Foundry ajánlatot][pcf-azuremarketplace] az Azure piactéren. A Marketplace-ajánlat a Pivotal dokumentációban történő üzembe helyezésével kapcsolatos [teljes útmutatást][pcf-azuremarketplace-pivotaldocs] talál.
- Hozzon létre egy testreszabott környezetet a [pivotal Cloud Foundry manuális üzembe helyezésével][pcf-custom].
- [A nyílt forráskódú Cloud Foundry csomagok közvetlen üzembe helyezéséhez][oss-cf-bosh] hozzon létre egy [Bosh](https://bosh.io) -IGAZGATÓT, egy virtuális gépet, amely összehangolja a Cloud Foundry környezet központi telepítését.

> [!IMPORTANT] 
> Ha az Azure Marketplace-en helyez üzembe PCF, jegyezze fel a SYSTEMDOMAINURL, valamint a Pivotal apps Manager eléréséhez szükséges rendszergazdai hitelesítő adatokat, amelyek közül mindkettőt a piactér telepítési útmutatója ismerteti. Ezek az oktatóanyag elvégzéséhez szükségesek. A piactéren üzemelő példányok esetében a SYSTEMDOMAINURL az űrlapon https://system. *IP-cím*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Kapcsolódás a felhőalapú vezérlőhöz

A felhőalapú vezérlő az alkalmazások üzembe helyezésére és kezelésére szolgáló Cloud Foundry környezet elsődleges belépési pontja. Az alapszintű Cloud Controller API (CCAPI) egy REST API, de különböző eszközökön keresztül érhető el. Ebben az esetben a [Cloud FOUNDRY CLI][cf-cli]-n keresztül kommunikálunk vele. Telepítheti a CLI-t Linux, MacOS vagy Windows rendszerre, de ha nem szeretné, hogy egyáltalán ne telepítse, a [Azure Cloud Shell][cloudshell-docs]előre telepítve lesz.

A bejelentkezéshez a `api` a piactér-telepítésből beszerzett SYSTEMDOMAINURL. Mivel az alapértelmezett telepítés önaláírt tanúsítványt használ, meg kell adnia a `skip-ssl-validation` kapcsolót is.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

A rendszer felszólítja, hogy jelentkezzen be a felhőalapú vezérlőbe. Használja a piactér üzembe helyezési lépéseiben beszerzett rendszergazdai fiók hitelesítő adatait.

Cloud Foundry *szervezethez* és *szóközöket* biztosít a névterek számára a csoportok és a környezetek elkülönítéséhez egy megosztott telepítésen belül. A PCF Marketplace üzembe helyezése magában foglalja az alapértelmezett *rendszer* szervezeti egységét, valamint az Alapösszetevők, például az automatikus skálázási szolgáltatás és az Azure Service Broker tárolására létrehozott szóközöket. Most válassza ki a *rendszer* területet.


## <a name="create-an-org-and-space"></a>Szervezeti és tárhely létrehozása

Ha beírja a `cf apps`t, a rendszer olyan rendszeralkalmazásokat lát, amelyek a rendszerszervezeten belül vannak telepítve a Rendszerterületen. 

Tartsa fenn a rendszeralkalmazások számára fenntartott rendszerszervezetet, ezért hozzon létre egy szervezetet és egy *helyet a minta* alkalmazásához.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

A TARGET parancs használatával váltson át az új szervezeti felületre és területre:

```bash
cf target -o testorg -s dev
```

Az alkalmazások központi telepítésekor a rendszer automatikusan létrehozza az új szervezetet és helyet. Annak ellenőrzéséhez, hogy az új szervezet/hely jelenleg nem rendelkezik alkalmazásokkal, írja be `cf apps` újra.

> [!NOTE] 
> A szervezethez és a Spaces szolgáltatással, valamint a szerepköralapú hozzáférés-vezérlés (RBAC) használatával kapcsolatos további információkért tekintse meg az [Cloud Foundry dokumentációját][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Használjuk a Hello Spring Cloud nevű minta Cloud Foundry alkalmazást, amely Java nyelven íródott, a Spring [Framework](https://spring.io) és a [Spring boot](https://projects.spring.io/spring-boot/)alapján.

### <a name="clone-the-hello-spring-cloud-repository"></a>A Hello Spring Cloud repository klónozása

A Hello Spring Cloud minta alkalmazás a GitHubon érhető el. Klónozás a környezetbe, és váltás az új könyvtárba:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

Az alkalmazás létrehozása az [Apache Maven](https://maven.apache.org)használatával.

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Az alkalmazás üzembe helyezése a CF leküldéses paranccsal

A legtöbb alkalmazást telepítheti Cloud Foundry a `push` parancs használatával:

```bash
cf push
```

Egy alkalmazás *leküldésekor* Cloud Foundry észleli az alkalmazás típusát (ebben az esetben egy Java-alkalmazást), és azonosítja a függőségeit (ebben az esetben a Spring Framework). Ezután csomagokat helyez el a kód futtatásához egy önálló tároló-rendszerképbe, amely egy *feldolgozó*. Végezetül Cloud Foundry a környezet egyik elérhető számítógépén ütemezze az alkalmazást, és létrehoz egy URL-címet, ahol elérheti azt, amely a parancs kimenetében is elérhető.

![Kimenet a CF push parancsból][cf-push-output]

A Hello-Spring-Cloud alkalmazás megjelenítéséhez nyissa meg a megadott URL-címet a böngészőben:

![A Hello Spring Cloud alapértelmezett felhasználói felülete][hello-spring-cloud-basic]

> [!NOTE] 
> Ha többet szeretne megtudni arról, hogy mi történik a `cf push`során, tekintse meg a Cloud Foundry dokumentációjában található [alkalmazások előkészítését][cf-push-docs] ismertető témakört.

## <a name="view-application-logs"></a>Alkalmazás naplófájljainak megtekintése

A Cloud Foundry CLI használatával megtekintheti az alkalmazás naplóit a nevével:

```bash
cf logs hello-spring-cloud
```

Alapértelmezés szerint a naplók parancs a *tail*parancsot használja, amely a megírt új naplókat jeleníti meg. Az új naplók megjelenítéséhez frissítse a Hello-Spring-Cloud alkalmazást a böngészőben.

A már megírt naplók megtekintéséhez adja hozzá a `recent` kapcsolót:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Az alkalmazás méretezése

Alapértelmezés szerint a `cf push` csak az alkalmazás egyetlen példányát hozza létre. A magas rendelkezésre állás biztosításához és a nagyobb átviteli sebesség érdekében az alkalmazások több példányának futtatásához általában érdemes futtatni az alkalmazásokat. A már telepített alkalmazásokat a `scale` paranccsal könnyedén bővítheti:

```bash
cf scale -i 2 hello-spring-cloud
```

Az `cf app` parancs futtatása az alkalmazáson azt mutatja, hogy Cloud Foundry az alkalmazás egy másik példányát hozza létre. Az alkalmazás elindítása után Cloud Foundry automatikusan elindítja a terheléselosztási forgalmat.


## <a name="next-steps"></a>Következő lépések

- [A Cloud Foundry dokumentációjának elolvasása][cloudfoundry-docs]
- [Az Cloud Foundry Azure DevOps Services beépülő moduljának beállítása][vsts-plugin]
- [A Microsoft Log Analytics szórófej konfigurálása Cloud Foundry][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: https://docs.microsoft.com/azure/cloud-shell/overview
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png

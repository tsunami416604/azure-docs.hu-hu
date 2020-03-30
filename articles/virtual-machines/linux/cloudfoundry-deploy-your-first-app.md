---
title: Első alkalmazás üzembe helyezése a Cloud Foundry szolgáltatásban a Microsoft Azure-ban
description: Alkalmazás üzembe helyezése az Azure-beli Cloud Foundry szolgáltatásban
author: seanmck
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 793a8f291be4fcca6fad19d486849253dddc089f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294794"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Első alkalmazás üzembe helyezése a Cloud Foundry szolgáltatásban a Microsoft Azure-ban

[A Cloud Foundry](https://cloudfoundry.org) egy népszerű nyílt forráskódú alkalmazásplatform, amely elérhető a Microsoft Azure-ban. Ebben a cikkben bemutatjuk, hogyan telepíthet és kezelhet egy alkalmazást a Cloud Foundry-n egy Azure-környezetben.

## <a name="create-a-cloud-foundry-environment"></a>Felhőalapú öntödei környezet létrehozása

Az Azure-beli felhőalapú öntödei környezet létrehozásának számos lehetősége van:

- Használja a [Pivotal Cloud Foundry ajánlat][pcf-azuremarketplace] az Azure Marketplace-en, hogy hozzon létre egy szabványos környezet, amely magában foglalja a PCF Ops Manager és az Azure Service Broker. A piactéri ajánlat üzembe helyezésére [vonatkozó teljes körű utasításokat][pcf-azuremarketplace-pivotaldocs] a Pivotal dokumentációban találja.
- Hozzon létre egy testreszabott környezetet [a Pivotal Cloud Foundry manuális telepítésével.][pcf-custom]
- [A nyílt forráskódú Cloud Foundry-csomagok at közvetlenül telepítheti][oss-cf-bosh] egy [BOSH-igazgató,](https://bosh.io) egy virtuális gép beállításával, amely koordinálja a Cloud Foundry környezet üzembe helyezését.

> [!IMPORTANT] 
> Ha a PCF-et az Azure Piactérről telepíti, jegyezze fel a SYSTEMDOMAINURL-t és a Pivotal Apps Manager eléréséhez szükséges rendszergazdai hitelesítő adatokat, amelyek mindegyike a piactér üzembe helyezési útmutatójában található. Ezek szükségesek az oktatóanyag befejezéséhez. A piactéri telepítések esetében a `https://system.*ip-address*.cf.pcfazure.com`SYSTEMDOMAINURL a .

## <a name="connect-to-the-cloud-controller"></a>Csatlakozás a felhővezérlőhöz

A cloud controller az elsődleges belépési pont a Cloud Foundry környezetben alkalmazások üzembe helyezéséhez és kezeléséhez. A core Cloud Controller API (CCAPI) egy REST API, de különböző eszközökön keresztül érhető el. Ebben az esetben a Cloud [Foundry CLI-n][cf-cli]keresztül kommunikálunk vele. A CLI telepíthető Linuxra, macOS-re vagy Windowsrendszerre, de ha egyáltalán nem szeretné telepíteni, akkor az előre telepítve érhető el az [Azure Cloud Shellben.][cloudshell-docs]

A bejelentkezéshez készítse elő `api` a piactér központi telepítéséből beszerzett SYSTEMDOMAINURL-t. Mivel az alapértelmezett központi telepítés önaláírt tanúsítványt `skip-ssl-validation` használ, a kapcsolót is tartalmaznia kell.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

A rendszer kéri, hogy jelentkezzen be a felhővezérlőbe. Használja a rendszergazdai fiók hitelesítő adatait, hogy a piactér telepítési lépéseit szerzett.

A Cloud Foundry *orgs-okat* és *tereket* biztosít névterekként a csapatok és a környezetek elkülönítéséhez egy megosztott telepítésen belül. A PCF piactér üzembe helyezése tartalmazza az alapértelmezett *rendszer* org és az alap összetevők, például az automatikus skálázási szolgáltatás és az Azure-szolgáltatás közvetítő tartalmazó terek készletét. Most válassza ki a *rendszerteret.*


## <a name="create-an-org-and-space"></a>Org és tér létrehozása

Ha a `cf apps`beírást írja be, megjelenik a rendszerszervezeti szervezet rendszerterületén telepített rendszeralkalmazások készlete. 

A *rendszerorgát* a rendszeralkalmazások számára kell fenntartania, ezért hozzon létre egy org-ot és helyet a mintaalkalmazásunk elhelyezésére.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

A célparanccsal váltson át az új szervezeti területre és területre:

```bash
cf target -o testorg -s dev
```

Most, amikor telepít egy alkalmazást, automatikusan létrejön az új szervezeti és tér. Annak ellenőrzéséhez, hogy jelenleg nincsenek alkalmazások az `cf apps` új org/térben, írja be újra a következőt:

> [!NOTE] 
> A szervezeti felekkel és szóközökkel, valamint a szerepköralapú hozzáférés-vezérlésre (RBAC) való használatra vonatkozó további információkért tekintse meg a [Cloud Foundry dokumentációját.][cf-orgs-spaces-docs]

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Használjuk a hello spring cloud nevű mintafelhő-öntödei alkalmazást, amely Java nyelven íródott, és a [tavaszi keretrendszeren](https://spring.io) és a [tavaszi rendszerindításon](https://projects.spring.io/spring-boot/)alapul.

### <a name="clone-the-hello-spring-cloud-repository"></a>Klónozza a Hello Spring Cloud adattárat

A Hello Spring Cloud mintaalkalmazás elérhető a GitHubon. Klónozza a környezetébe, és váltson az új könyvtárba:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

Készítsd el az alkalmazást az [Apache Maven](https://maven.apache.org)használatával.

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Az alkalmazás üzembe helyezése cf push-tal

A legtöbb alkalmazást a cloud foundry-ba telepítheti a `push` következő paranccsal:

```bash
cf push
```

Amikor *leválaszt* egy alkalmazást, a Cloud Foundry észleli az alkalmazás típusát (ebben az esetben egy Java-alkalmazást), és azonosítja a függőségeket (ebben az esetben a tavaszi keretrendszer). Ezután mindent becsomagol, ami a kód futtatásához szükséges egy önálló tárolólemezképbe, az úgynevezett *cseppbe.* Végül a Cloud Foundry ütemezi az alkalmazást a környezetében elérhető egyik gépen, és létrehoz egy URL-címet, ahol elérheti, amely elérhető a parancs kimenetében.

![Kimenet cf leküldéses parancsból][cf-push-output]

A hello-spring-cloud alkalmazás megtekintéséhez nyissa meg a megadott URL-t a böngészőben:

![A Hello Spring Cloud alapértelmezett felhasználói felülete][hello-spring-cloud-basic]

> [!NOTE] 
> Ha többet szeretne megtudni `cf push`arról, hogy mi történik a során, olvassa el az [Alkalmazások szakaszlásának módját][cf-push-docs] a Cloud Foundry dokumentációjában.

## <a name="view-application-logs"></a>Alkalmazásnaplók megtekintése

A Cloud Foundry CLI segítségével megtekintheti egy alkalmazás naplóit a neve alapján:

```bash
cf logs hello-spring-cloud
```

Alapértelmezés szerint a naplók parancs a *tail -t*használja, amely az új naplókat jeleníti meg, ahogy azok írásra vannak írva. Az új naplók megtekintéséhez frissítse a hello-spring-cloud alkalmazást a böngészőben.

A már megírt naplók megtekintéséhez `recent` adja hozzá a kapcsolót:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Az alkalmazás méretezése

Alapértelmezés szerint `cf push` csak az alkalmazás egyetlen példányát hozza létre. A magas rendelkezésre állás biztosítása és a nagyobb átviteli sebességű horizontális felskálázás engedélyezése érdekében általában több példányt szeretne futtatni az alkalmazásokból. A már telepített alkalmazásokat a `scale` következő paranccsal skálázhatja:

```bash
cf scale -i 2 hello-spring-cloud
```

A `cf app` parancs futtatása az alkalmazáson azt mutatja, hogy a Cloud Foundry az alkalmazás egy másik példányát hozza létre. Az alkalmazás indítása után a Cloud Foundry automatikusan elindítja a terheléselosztási forgalmat.


## <a name="next-steps"></a>További lépések

- [Olvassa el a Cloud Foundry dokumentációját][cloudfoundry-docs]
- [Az Azure DevOps Services beépülő modul beállítása a Cloud Foundry számára][vsts-plugin]
- [A Microsoft Log Analytics fúvóka konfigurálása a Cloud Foundry számára][loganalytics-nozzle]

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

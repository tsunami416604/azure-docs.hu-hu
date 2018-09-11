---
title: Az első alkalmazás üzembe helyezése a Cloud Foundry-hoz a Microsoft Azure-on |} A Microsoft Docs
description: Cloud Foundry az Azure-alkalmazás üzembe helyezése
services: virtual-machines-linux
documentationcenter: ''
author: seanmck
manager: jeconnoc
editor: ''
tags: ''
keywords: ''
ms.assetid: 8fa04a58-56ad-4e6c-bef4-d02c80d4b60f
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 6e2fa77273ef35fae6c3b232cb36fa913faf879d
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299049"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Az első alkalmazás üzembe helyezése a Cloud Foundry-hoz a Microsoft Azure

[Cloud Foundry](http://cloudfoundry.org) egy népszerű nyílt forráskódú platform a Microsoft Azure-ban érhető el. Ebben a cikkben bemutatjuk, hogyan helyezheti üzembe és kezelheti egy alkalmazás a Cloud Foundry Azure-környezet.

## <a name="create-a-cloud-foundry-environment"></a>A Cloud Foundry-környezet létrehozása

A Cloud Foundry környezet létrehozásához az Azure-ban számos lehetőség áll rendelkezésre:

- Használja a [Pivotal Cloud Foundry ajánlat] [ pcf-azuremarketplace] hozhat létre egy standard szintű környezet, amely tartalmazza a PCF az Ops Manager és az Azure Service Broker az Azure piactéren. Annak [utasításokkal] [ pcf-azuremarketplace-pivotaldocs] üzembe helyezéséhez a Marketplace-en kínálnak a Pivotal dokumentációjában.
- Hozzon létre egy testre szabott környezetet által [Pivotal Cloud Foundry Feladatütemezőből][pcf-custom].
- [Közvetlenül a nyílt forráskódú Cloud Foundry-csomagok üzembe helyezése] [ oss-cf-bosh] beállításával egy [BOSH](http://bosh.io) igazgató, egy virtuális Gépet, amely a központi telepítés, a Cloud Foundry környezet koordinálja.

> [!IMPORTANT] 
> Ha az Azure Marketplace-ről a PCF helyez üzembe, jegyezze fel a SYSTEMDOMAINURL és a rendszergazdai hitelesítő adatait a Pivotal kezelő alkalmazások eléréséhez szükséges, amelyek a Marketplace-en üzembe helyezési útmutatóban ismertetett. Ezek az oktatóanyag elvégzéséhez szükségesek. Marketplace-en történő telepítés esetén a SYSTEMDOMAINURL szerepel az űrlap https://system. *IP-cím*. cf.pcfazure.com.

## <a name="connect-to-the-cloud-controller"></a>Csatlakozás a felhőalapú vezérlő

A felhő vezérlő a Cloud Foundry környezet üzembe helyezéséhez és felügyeletéhez alkalmazásokat az elsődleges belépési pontjához. Az alapvető felhőalapú adatkezelő API (CCAPI) REST API-t, de különböző eszközöket keresztül érhető el. Ebben az esetben kezelése során dinamikusabb keresztül a [Cloud Foundry parancssori felület][cf-cli]. Telepítheti a parancssori felület Linux, MacOS vagy Windows rendszeren, de ha inkább nem szeretne minden telepítheti, elérhető előre telepítve van az a [Azure Cloud Shell][cloudshell-docs].

A bejelentkezéshez illesztenie `api` , a SYSTEMDOMAINURL a Marketplace-en telepítési beszerzett. Mivel az alapértelmezett üzembe helyezés egy önaláírt tanúsítványt használ, akkor is tartalmaznia kell a `skip-ssl-validation` váltani.

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Jelentkezzen be a felhő vezérlő kéri. Vásárolta meg a Marketplace-en üzembe helyezési lépéseket a rendszergazdai fiók hitelesítő adatait használja.

A cloud Foundry biztosít *cégek vagy intézmények* és *tárolóhelyek* , a teams és a egy megosztott telepítésben található környezetek elkülönítése névterek. A PCF üzemelő példányát marketplace magában foglalja az alapértelmezett *rendszer* szervezeti és a tárolóhelyek létrehozni tartalmazza az alapvető összetevői számos, például az automatikus skálázás szolgáltatás és az Azure service broker. Most válassza ki a *rendszer* terület.


## <a name="create-an-org-and-space"></a>Hozzon létre egy szervezeti és terület

Ha `cf apps`, láthatja, hogy a rendszer területen belül. a rendszer szervezeti alkalmazott rendszer alkalmazások 

Érdemes megtartani az *rendszer* szervezeti fenntartott systému, így hozhat létre egy szervezeti és a terület helyet adhat meg a mintaalkalmazást.

```bash
cf create-org myorg
cf create-space dev -o myorg
```

A cél paranccsal váltson át az új szervezeti és terület:

```bash
cf target -o testorg -s dev
```

Most ha telepít egy alkalmazást, automatikusan létrejön az új szervezeti és a szükséges. Győződjön meg arról, hogy jelenleg nincs alkalmazás az új szervezeti/tárhely, írja be a következőt `cf apps` újra.

> [!NOTE] 
> Cégek vagy intézmények és a tárolóhelyek és hogyan használhatók a szerepköralapú hozzáférés-vezérlés (RBAC) kapcsolatos további információkért lásd: a [Cloud Foundry – dokumentáció][cf-orgs-spaces-docs].

## <a name="deploy-an-application"></a>Alkalmazás üzembe helyezése

Cloud Foundry mintaalkalmazás nevű Hello Spring Cloud, amely Java nyelven íródtak, és alapján használja a [Spring-keretrendszert használó](http://spring.io) és [Spring Boot](http://projects.spring.io/spring-boot/).

### <a name="clone-the-hello-spring-cloud-repository"></a>A Hello Spring Cloud tárház klónozása

A Hello Spring Cloud mintaalkalmazás a Githubon érhető el. Klónozza a környezetben, és módosítsa az új könyvtárba:

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>Az alkalmazás létrehozása

Az alkalmazás használatával [Apache Maven](http://maven.apache.org).

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>Az alkalmazást a leküldéses cf-hez

A Cloud Foundry használata a legtöbb alkalmazásokat helyezhet üzembe a `push` parancsot:

```bash
cf push
```

Ha Ön *leküldéses* egy alkalmazást, a Cloud Foundry (az ebben az esetben egy Java-alkalmazás) az alkalmazás észleli, és annak függőségeit, (az ebben az esetben a Spring-keretrendszert használó) azonosítja. Majd csomagot minden szükséges futtathatja a kódot egy tárolórendszerképbe önálló, más néven egy *feldolgozó*. Végül a Cloud Foundry ütemezi az alkalmazás egy, a rendelkezésre álló gépek a környezetben, és létrehoz egy URL-címet, ha elérhetővé válik, amely is elérhető, a parancs kimenetében.

![CF-hez leküldéses parancs kimenete][cf-push-output]

A hello spring felhőbe alkalmazás megtekintéséhez nyissa meg a megadott URL-CÍMÉT a böngészőben:

![Alapértelmezett felhasználói felület a Hello Spring Cloud][hello-spring-cloud-basic]

> [!NOTE] 
> További információ arról, mi történik, során `cf push`, lásd: [hogyan alkalmazásokat elő van készítve] [ cf-push-docs] a Cloud Foundry-dokumentáció.

## <a name="view-application-logs"></a>Alkalmazás-naplók megtekintése

A Cloud Foundry parancssori felület használatával megtekinthetők a naplófájlok az alkalmazás neve:

```bash
cf logs hello-spring-cloud
```

Alapértelmezés szerint a naplók parancsot használ *tail*, amely jelenik meg az új naplók megírásának. Megjelenik az új naplók megtekintéséhez frissítse a hello spring felhőbe alkalmazás a böngészőben.

Már minden bizonnyal naplók megtekintéséhez adja hozzá a `recent` váltani:

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>Az alkalmazás méretezése

Alapértelmezés szerint `cf push` csak hoz létre az alkalmazás egy példánya. A magas rendelkezésre állás biztosítása érdekében, és engedélyezi a horizontális felskálázás nagyobb átviteli sebességet, általában érdemes az alkalmazások több példányának futtatása. Már üzembe helyezett alkalmazások segítségével könnyedén horizontálisan a `scale` parancsot:

```bash
cf scale -i 2 hello-spring-cloud
```

Fut a `cf app` az alkalmazás a parancs megjeleníti, hogy a Cloud Foundry létrehozza-e az alkalmazás egy másik példánya. Az alkalmazás megkezdése után a Cloud Foundry terheléselosztási rá forgalmat automatikusan elindul.


## <a name="next-steps"></a>További lépések

- [Olvassa el a Cloud Foundry – dokumentáció][cloudfoundry-docs]
- [Állítsa be az Azure DevOps Services beépülő modul a Cloud Foundry-hoz][vsts-plugin]
- [Konfigurálja a Microsoft Log Analytics Nozzle a Cloud Foundry-hoz][loganalytics-nozzle]

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

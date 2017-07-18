---
title: "Privát Docker-tárolójegyzékek az Azure-ban | Microsoft Docs"
description: "Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít."
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: ee2b652b-fb7c-455b-8275-b8d4d08ffeb3
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: 2ad539c85e01bc132a8171490a27fd807c8823a4
ms.openlocfilehash: f9dff5384838521c309a2d2a5ebb5376c90159fb
ms.contentlocale: hu-hu
ms.lasthandoff: 07/12/2017

---
# <a name="introduction-to-private-docker-container-registries"></a>A privát Docker-tárolójegyzékek bemutatása


Az Azure Container Registry egy felügyelt [Docker jegyzékszolgáltatás](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. Az Azure-beli tároló-beállításjegyzékek létrehozásával és fenntartásával tárolhatja és kezelheti privát [Docker-tárolóinak](https://www.docker.com/what-docker) rendszerképeit. Az Azure-beli tároló-beállításjegyzékeit meglévő tárolófejlesztési és üzembe helyezési folyamataival együtt használhatja, és közben számíthat a Docker közösség szakértelmére.

A Dockerrel és a tárolókkal kapcsolatos háttérinformációk:

* [Docker felhasználói útmutató](https://docs.docker.com/engine/userguide/)




## <a name="use-cases"></a>Használati esetek
Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) és [Kubernetes](http://kubernetes.io/docs/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve a [Container Service](../container-service/index.yml), az [App Service](/app-service/index.md), a [Batch](../batch/index.md), a [Service Fabric](../service-fabric/index.md) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy olyan folyamatos integrációs és üzembe helyezési eszközből, mint a [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) vagy a [Jenkins](https://jenkins.io/).





## <a name="key-concepts"></a>Fő fogalmak
* **Beállításjegyzék** – Létrehozhat egy vagy több tároló-beállításjegyzéket Azure-előfizetésében. Mindegyik beállításjegyzék egy, ugyanazon a helyen található standard Azure [Storage-fiókra](../storage/storage-introduction.md) épül. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. A teljes tartománynév `myregistry.azurecr.io` formában van.

  A tároló-beállításjegyzékhez való [hozzáférés szabályozása](container-registry-authentication.md) egy, az Azure Active Directory által támogatott [egyszerű szolgáltatással](../active-directory/active-directory-application-objects.md) vagy a rendszergazdai fiókkal lehetséges. A beállításjegyzéken való hitelesítéshez futtassa a szabványos `docker login` parancsokat.

* **Felügyelt beállításjegyzék** – A beállításjegyzékekhez további képességeket nyújtó szint három termékváltozatban – Basic, Standard és Premium – érhető el. A termékváltozatokban lévő rendszerképek az Azure Container Registry szolgáltatás által felügyelt tárfiókokban vannak tárolva, ami növeli a megbízhatóságot és új funkciókat tesz elérhetővé. Az új képességek közé tartozik a webhook-integráció, az Azure Active Directoryval való adattár-hitelesítés és a törlési funkció támogatása. A felhasználók a beállításjegyzékek létrehozásakor választhatnak a felügyelt beállításjegyzékek vagy a saját tárfiókjuk által támogatott beállításjegyzék létrehozása között.

* **Tár** – A beállításjegyzékek egy vagy több tárat tartalmaznak, amelyek tárolórendszerképek csoportjai. Az Azure Container Registry támogatja a többszintű adattárnévtereket. Ezzel a szolgáltatással egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit csoportba rendezheti az egyes fejlesztői és üzemeltetői csoportok számára. Példa:

  * A(z) `myregistry.azurecr.io/aspnetcore:1.0.1` egy, a teljes vállalatban elérhető rendszerképet jelöl
  * A(z) `myregistry.azurecr.io/warrantydept/dotnet-build` egy .NET-alkalmazások felépítéséhez használt rendszerképet jelöl, amely a jótállási részlegen van megosztva
  * A(z) `myregistry.azrecr.io/warrantydept/customersubmissions/web` egy, az ügyfélbeadványok alkalmazásban csoportosított webes rendszerképet jelöl, amely a jótállási részleg tulajdona

* **Rendszerkép** – Az adattárakban tárolt egyes rendszerképek egy-egy Docker-tároló csak olvasható pillanatfelvételei. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan.

* **Tároló** – A tároló egy szoftveralkalmazást határoz meg annak függőségeivel együtt egy teljes fájlrendszerbe csomagolva, beleértve a kódot, a futtatókörnyezetet, a rendszereszközöket és a könyvtárakat. A Docker-tárolókat a tároló-beállításjegyzékekből előhívott Windows- vagy Linux-rendszerképek alapján futtathatja. Az egy gépen futó tárolók osztoznak az operációs rendszer kernelén. A Docker-tárolók teljes mértékben portolhatók az összes nagyobb Linux-disztribúcióra, Macre és Windowsra is.




## <a name="next-steps"></a>Következő lépések
* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)
* Egy folyamatos integrációt és üzembe helyezést megvalósító munkafolyamat a Visual Studio Team Services, az Azure Container Service és az Azure Container Registry használatával történő kiépítéséhez lásd [ezt az oktatóanyagot](../container-service/container-service-docker-swarm-setup-ci-cd.md).
* Ha privát Docker-beállításjegyzéket szeretne beállítani az Azure-ban (nyilvános végpont nélkül), tekintse meg [a saját privát Docker-beállításjegyzék Azure-beli üzembe helyezésével kapcsolatos](../virtual-machines/virtual-machines-linux-docker-registry-in-blob-storage.md) cikket.


---
title: "Privát Docker-tárolójegyzékek az Azure-ban"
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
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 664696d2f355609c76477765c2238c6d62253482
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

Az Azure Container Registry egy felügyelt [Docker jegyzékszolgáltatás](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. Az Azure-beli tároló-beállításjegyzékek létrehozásával és fenntartásával tárolhatja és kezelheti privát [Docker-tárolóinak](https://www.docker.com/what-docker) rendszerképeit. Az Azure-beli tároló-beállításjegyzékeit meglévő tárolófejlesztési és üzembe helyezési folyamataival együtt használhatja, és közben számíthat a Docker közösség szakértelmére.

A Dockerrel és a tárolókkal kapcsolatos háttér-információkért lásd a [Docker felhasználói útmutatóját](https://docs.docker.com/engine/userguide/).

## <a name="use-cases"></a>Használati esetek
Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [DC/OS](https://docs.mesosphere.com/), [Docker Swarm](https://docs.docker.com/swarm/) és [Kubernetes](http://kubernetes.io/docs/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve a [Container Service](../container-service/index.yml), az [App Service](/app-service/index.md), a [Batch](../batch/index.md), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy olyan folyamatos integrációs és üzembe helyezési eszközből, mint a [Visual Studio Team Services](https://www.visualstudio.com/docs/overview) vagy a [Jenkins](https://jenkins.io/).

## <a name="key-concepts"></a>Fő fogalmak
* **Beállításjegyzék** – Létrehozhat egy vagy több tároló-beállításjegyzéket Azure-előfizetésében. Mindegyik beállításjegyzék egy, ugyanazon a helyen található standard Azure [Storage-fiókra](../storage/common/storage-introduction.md) épül. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. A teljes tartománynév `myregistry.azurecr.io` formában van.

  A tároló-beállításjegyzékhez való [hozzáférés szabályozása](container-registry-authentication.md) egy, az Azure Active Directory által támogatott [egyszerű szolgáltatással](../active-directory/active-directory-application-objects.md) vagy a rendszergazdai fiókkal lehetséges. A beállításjegyzéken való hitelesítéshez futtassa a szabványos `docker login` parancsokat.

* **Felügyelt beállításjegyzék** – Ha úgy dönt, létrehozhat egy felügyelt beállításjegyzéket, vagy egy olyan beállításjegyzéket, amelyről a saját tárfiókjára készít biztonsági másolatot a beállításjegyzék létrehozásakor. A felügyelt beállításjegyzékek három termékváltozata, az Alapszintű, a Standard és a Prémium, további képességeket nyújt. A termékváltozatokban lévő rendszerképek az Azure Container Registry szolgáltatás által felügyelt Azure Storage-tárfiókokban vannak tárolva, ami növeli a megbízhatóságot, és új funkciókat tesz elérhetővé. Az új képességek közé tartozik a webhook-integráció, az Azure Active Directoryval való adattár-hitelesítés és a törlési funkció támogatása.

* **Tár** – A beállításjegyzékek egy vagy több tárat tartalmaznak, amelyek tárolórendszerképek csoportjai. Az Azure Container Registry támogatja a többszintű adattárnévtereket. A többszintű névterekkel csoportba rendezheti egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit az egyes fejlesztői és üzemeltetői csoportok számára. Példa:

  * A(z) `myregistry.azurecr.io/aspnetcore:1.0.1` egy, a teljes vállalatban elérhető rendszerképet jelöl
  * A(z) `myregistry.azurecr.io/warrantydept/dotnet-build` egy .NET-alkalmazások felépítéséhez használt rendszerképet jelöl, amely a jótállási részlegen van megosztva
  * A(z) `myregistry.azurecr.io/warrantydept/customersubmissions/web` egy, az ügyfélbeadványok alkalmazásban csoportosított webes rendszerképet jelöl, amely a jótállási részleg tulajdona

* **Rendszerkép** – Az adattárakban tárolt egyes rendszerképek egy-egy Docker-tároló csak olvasható pillanatfelvételei. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan.

* **Tároló** – A tároló egy szoftveralkalmazást határoz meg annak függőségeivel együtt egy teljes fájlrendszerbe csomagolva, beleértve a kódot, a futtatókörnyezetet, a rendszereszközöket és a könyvtárakat. A Docker-tárolókat a tároló-beállításjegyzékekből előhívott Windows- vagy Linux-rendszerképek alapján futtathatja. Az egy gépen futó tárolók osztoznak az operációs rendszer kernelén. A Docker-tárolók teljes mértékben használhatók az összes nagyobb Linux-disztribúción, MacOS-en és Windowson is.

## <a name="next-steps"></a>Következő lépések
* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Az első rendszerkép leküldése a Docker parancssori felületével](container-registry-get-started-docker-cli.md)
* Egy folyamatos integrációt és üzembe helyezést megvalósító munkafolyamat a Visual Studio Team Services, az Azure Container Service és az Azure Container Registry használatával történő kiépítéséhez lásd: [CI/CD – Docker Swarm és VSTS](../container-service/dcos-swarm/container-service-docker-swarm-setup-ci-cd.md).
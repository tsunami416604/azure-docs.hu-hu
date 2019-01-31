---
title: Privát Docker-tárolójegyzékek az Azure-ban – áttekintés
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 09/25/2018
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 0118c17045b86c88d8d92048787a20bd7d309c0b
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55298347"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

Az Azure Container Registry egy felügyelt [Docker jegyzékszolgáltatás](https://docs.docker.com/registry/), amely a nyílt forráskódú Docker Registry 2.0 technológiára épül. Az Azure-beli tároló-beállításjegyzékek létrehozásával és fenntartásával tárolhatja és kezelheti privát [Docker-tárolóinak](https://www.docker.com/what-docker) rendszerképeit.

Az Azure-beli tároló-beállításjegyzékeit meglévő tárolófejlesztési és üzembe helyezési folyamataival együtt használhatja. A tárolórendszerképek Azure-ban történő létrehozásához használja az Azure Container Registry Buildet (ACR Build). Igény szerinti vagy teljesen automatizált összeállítás a forráskód véglegesítésével és az alapszintű rendszerkép frissítésével.

A Dockerrel és a tárolókkal kapcsolatos háttér-információkért lásd a [Docker áttekintő ismertetését](https://docs.docker.com/engine/docker-overview/).

## <a name="use-cases"></a>Használati esetek

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](http://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy olyan folyamatos integrációs és üzembe helyezési eszközből, mint az [Azure DevOps Services](https://docs.microsoft.com/azure/devops/) vagy a [Jenkins](https://jenkins.io/).

Konfigurálja úgy az [ACR Tasks](#azure-container-registry-build) szolgáltatást, hogy az alapszintű rendszerképek frissítésekor a rendszer automatikusan újraépítsék az alkalmazás-rendszerképeket. Az ACR Tasks használatával automatizálhatja a rendszerképek összeállítását, ha a csoport kódot véglegesít egy Git-adattárban.

## <a name="key-concepts"></a>Fő fogalmak

* **Beállításjegyzék** – Létrehozhat egy vagy több tároló-beállításjegyzéket Azure-előfizetésében. Beállításjegyzékek három termékváltozatban érhetők el: [Alapszintű, Standard és prémium szintű](container-registry-skus.md), minden egyes, amely támogatja a webhook-integrációt, az Azure Active Directoryval, beállításjegyzék-hitelesítést és a törlési funkciót. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. A teljes tartománynév `myregistry.azurecr.io` formában van.

  Ön [hozzáférésének](container-registry-authentication.md) továbbíthat egy tárolóregisztrációs adatbázisba, egy Azure-identitás, az Azure Active Directory-alapú használatával [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md), vagy rendszergazdai fiókkal. Jelentkezzen be a beállításjegyzék használatával az Azure parancssori felület vagy a standard szintű `docker login` parancsot.

* **Tárház** -beállításjegyzék tartalmaz egy vagy több tárat, amelyek tárolórendszerképek csoportjai tárolja. Az Azure Container Registry támogatja a többszintű adattárnévtereket. A többszintű névterekkel csoportba rendezheti egy adott alkalmazáshoz vagy alkalmazások gyűjteményéhez kapcsolódó rendszerképek gyűjteményeit az egyes fejlesztői és üzemeltetői csoportok számára. Példa:

  * A(z) `myregistry.azurecr.io/aspnetcore:1.0.1` egy, a teljes vállalatban elérhető rendszerképet jelöl
  * A(z) `myregistry.azurecr.io/warrantydept/dotnet-build` egy .NET-alkalmazások felépítéséhez használt rendszerképet jelöl, amely a jótállási részlegen van megosztva
  * A(z) `myregistry.azurecr.io/warrantydept/customersubmissions/web` egy, az ügyfélbeadványok alkalmazásban csoportosított webes rendszerképet jelöl, amely a jótállási részleg tulajdona

* **Kép** – az adattárakban tárolt egyes rendszerképek egy Docker-kompatibilis tároló csak olvasható pillanatkép. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. Tárolórendszerképek mellett az Azure Container Registry tárolja [kapcsolódó tartalom formátumok](container-registry-image-formats.md) például [Helm-diagramok](container-registry-helm-repos.md)szolgál Kubernetes-alkalmazások központi telepítése.

* **Tároló** – A tároló egy szoftveralkalmazást határoz meg annak függőségeivel együtt egy teljes fájlrendszerbe csomagolva, beleértve a kódot, a futtatókörnyezetet, a rendszereszközöket és a könyvtárakat. A Docker-tárolókat a tároló-beállításjegyzékekből előhívott Windows- vagy Linux-rendszerképek alapján futtathatja. Az egy gépen futó tárolók osztoznak az operációs rendszer kernelén. A Docker-tárolók teljes mértékben használhatók az összes nagyobb Linux-disztribúción, MacOS-en és Windowson is.

## <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Az [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) az Azure Container Registry egy szolgáltatáscsomagja, amely lehetővé teszi a Docker-tárolórendszerképek zökkenőmentes és hatékony összeállítását az Azure-ban. Az ACR Tasks használatával kiterjesztheti a felhőbe a belső fejlesztési ciklust a `docker build`-műveletek Azure-ba való áthelyezése által. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

A [többlépéses feladatok](container-registry-tasks-overview.md#multi-step-tasks-preview) az ACR Tasks egyik előzetes verziós funkciója, amelyekkel lépésekre osztott feladatdefiníciókat és buildelési, tesztelési és felhőbeli tárolólemezkép-javítást végezhet. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>További lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Operációs rendszer és keretrendszer javításának automatizálása az ACR Tasksszal](container-registry-tasks-overview.md)
---
title: Privát Docker-tárolójegyzékek az Azure-ban – áttekintés
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
services: container-registry
author: stevelas
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: b8b4b5fc3ec15d921ff5580aff4d0202be1d38b9
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797899"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

Az Azure Container Registry egy felügyelt, privát Docker-regisztrációs szolgáltatás a nyílt forráskódú Docker Registry 2.0 technológiára alapján. Létrehozása és kezelése az Azure container Registry-példányok tárolhatja és kezelheti a privát Docker-tárolórendszerképekhez.

Az Azure tároló-beállításjegyzékek használata a meglévő tárolófejlesztési és üzembe helyezési, vagy használja az Azure Container Registry feladatokat hozhat létre tárolórendszerképeket az Azure-ban. Hozhat létre igény szerinti, vagy teljesen automatizálhatja az eseményindítók buildek például forráskódját véglegesítésére, és kiinduló rendszerképének frissítése.

A Docker és a beállításjegyzék fogalmak kapcsolatos további információkért tekintse meg a [a Docker áttekintése](https://docs.docker.com/engine/docker-overview/) és [beállításjegyzékek, adattárak és rendszerképek](container-registry-concepts.md).

## <a name="use-cases"></a>Használati esetek

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például Megcélozhat egy tároló-beállításjegyzéket egy folyamatos integrációt és teljesítést eszközből, mint [Azure folyamatok](/azure/devops/pipelines/get-started/what-is-azure-pipelines.md) vagy [Jenkins](https://jenkins.io/).

Alkalmazás-lemezképek automatikusan újraépítheti a saját alaplemezképek frissítésekor ACR feladatok konfigurálása, illetve a rendszerképek létrehozásának automatizálása, ha a csapata véglegesíti a kódot egy Git-tárházba. Többlépéses feladatok automatizálásához készítése, tesztelése és a javítással, a felhőben, párhuzamosan több tároló-rendszerkép létrehozása.

Az Azure biztosítja az eszközöket, beleértve az Azure parancssori felület, az Azure portal és API-támogatás kezelése az Azure container registryk. Szükség esetén telepítse a [Docker-bővítmény a Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) és a [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítmény használata az Azure container registryk. Lekéréses és a egy Azure container registry rendszerképek leküldése vagy ACR feladatok, mindezt a Visual Studio Code-ot.

## <a name="key-features"></a>A legfontosabb jellemzők

* **Beállításjegyzék Termékváltozatai** – hozzon létre egy vagy több tároló-beállításjegyzékek az Azure-előfizetésében. Beállításjegyzékek három termékváltozatban érhetők el: [Alapszintű, Standard és prémium szintű](container-registry-skus.md), amelyek mindegyike támogatja a webhook-integrációt, a beállításjegyzék-hitelesítés az Azure Active Directory és a törlési funkció. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. 

  Ön [hozzáférésének](container-registry-authentication.md) továbbíthat egy tárolóregisztrációs adatbázisba, egy Azure-identitás, az Azure Active Directory-alapú használatával [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md), vagy rendszergazdai fiókkal. Jelentkezzen be a beállításjegyzék használatával az Azure CLI vagy a standard szintű `docker login` parancsot.

* **Képek és összetevők támogatott** – egy csoportosított, minden egyes képe egy Docker-kompatibilis tároló csak olvasható pillanatkép. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. Docker-tárolórendszerképekhez mellett az Azure Container Registry tárolja [kapcsolódó tartalom formátumok](container-registry-image-formats.md) például [Helm-diagramok](container-registry-helm-repos.md) és a készített rendszerképekből a [Open Container kezdeményezés (OCI) kép Specifikáció formázása](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Az Azure Container Registry feladatok** -használat [Azure Container Registry feladatok](container-registry-tasks-overview.md) (ACR feladatok) egyszerűbb fejlesztés, tesztelés, hogyan lehet továbbítani rá, és az Azure-beli rendszerképek üzembe helyezése. Például az ACR feladatok segítségével történő kiszervezésével a fejlesztési belső-hurok a felhőbe kiterjeszteni `docker build` műveleteket az Azure-bA. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

  [Több lépésből álló feladatokat](container-registry-tasks-overview.md#multi-step-tasks) adja meg lépés alapú feladatdefiníció és végrehajtási létrehozásához, teszteléséhez és a javítással tárolórendszerképeket a felhőben. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>További lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [Tároló-buildek és a karbantartás az ACR-feladatok automatizálása](container-registry-tasks-overview.md)

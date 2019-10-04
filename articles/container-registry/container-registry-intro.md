---
title: Privát Docker-tárolók nyilvántartása az Azure-ban – áttekintés
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 2262948a8368f9448d876166b712ebaf670b5a56
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994364"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

A Azure Container Registry egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely a nyílt forráskódú Docker beállításjegyzék 2,0-es számán alapul. Létrehozhatja és karbantarthatja az Azure Container-nyilvántartásokat a privát Docker-tárolók rendszerképeinek tárolásához és kezeléséhez.

Az Azure Container-jegyzékeket meglévő tároló-fejlesztési és üzembe helyezési folyamataival használhatja, vagy Azure Container Registry feladatok használatával készíthet tároló-lemezképeket az Azure-ban. Igény szerint építhető, vagy teljesen automatizálható az eseményindítók, például a forráskód-véglegesítés és az alaprendszerkép frissítései.

A Docker-és a beállításjegyzék-fogalmakkal kapcsolatos [](https://docs.docker.com/engine/docker-overview/) további információkért tekintse meg a Docker áttekintését, valamint a [jegyzékek, a adattárak és a lemezképek ismertetését](container-registry-concepts.md).

## <a name="use-cases"></a>Használati esetek

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például megcélozhat egy tároló-beállításjegyzéket egy folyamatos integrációs és kézbesítési eszközről, például [Azure](/azure/devops/pipelines/ecosystems/containers/acr-template) -folyamatokból vagy [Jenkins](https://jenkins.io/)-ből.

Az ACR-feladatok konfigurálásával automatikusan újraépítheti az alkalmazás lemezképeit az alaplemezképek frissítésekor, vagy automatizálhatja a lemezképeket, amikor a csapata egy git-tárházban véglegesíti a kódot. Többlépéses feladatok létrehozásával automatizálhatja a felhőben párhuzamosan több tároló lemezképének létrehozását, tesztelését és javítását.

Az Azure olyan eszközöket biztosít, mint az Azure parancssori felület, a Azure Portal és az API-támogatás az Azure Container-nyilvántartások kezeléséhez. Telepítse a Docker-bővítményt a [Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) -hoz, és az [Azure-fiók](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) bővítményét az Azure-beli tároló-beállításjegyzékkel való együttműködéshez. Lemezképek lekérése és leküldése egy Azure Container registrybe, vagy az ACR-feladatok futtatása a Visual Studio Code-ban.

## <a name="key-features"></a>A legfontosabb jellemzők

* **Beállításjegyzékbeli SKU** – hozzon létre egy vagy több tároló-beállításjegyzéket az Azure-előfizetésében. A kibocsátásiegység-forgalmi jegyzékek három SKU-ban érhetők el: [Alapszintű, standard és prémium](container-registry-skus.md), amelyek mindegyike támogatja a webhook-integrációt, a beállításjegyzék hitelesítését Azure Active Directory és a törlési funkciót. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. 

  Egy tároló-beállításjegyzékhez [való hozzáférést](container-registry-authentication.md) egy Azure-identitás, egy Azure Active Directory-alapú [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md)vagy egy megadott rendszergazdai fiók használatával szabályozhatja. Jelentkezzen be a beállításjegyzékbe az Azure CLI vagy a standard `docker login` parancs használatával.

* **Támogatott lemezképek és** összetevők – egy adattárba csoportosítva minden rendszerkép egy Docker-kompatibilis tároló írásvédett pillanatképe. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. A Docker-tároló rendszerképein kívül a Azure Container Registry a [kapcsolódó tartalom formátumait](container-registry-image-formats.md) , például [Helm](container-registry-helm-repos.md) -diagramokat és rendszerképeket is tartalmaz, amelyek az [Open Container Initiative (OCI) képformátum](https://github.com/opencontainers/image-spec/blob/master/spec.md)-specifikációra épülnek.

* **Azure Container Registry feladatok** – a lemezképek Azure-ban való létrehozásának, tesztelésének, leküldésének és üzembe helyezésének egyszerűsítése [Azure Container Registry feladatok](container-registry-tasks-overview.md) (ACR-feladatok) használatával. Például az ACR-feladatok használatával kiterjesztheti a fejlesztési belső hurkot a felhőbe a `docker build` műveletek Azure-ba való kiszervezésével. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

  A többlépéses [tevékenységek](container-registry-tasks-overview.md#multi-step-tasks) a Felhőbeli tároló-lemezképek létrehozásához, teszteléséhez és javításához szükséges lépéseken alapuló feladatok meghatározását és végrehajtását teszik lehetővé. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>További lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [A tárolók és a karbantartás ACR-feladatokkal való automatizálása](container-registry-tasks-overview.md)

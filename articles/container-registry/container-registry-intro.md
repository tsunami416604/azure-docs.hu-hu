---
title: Felügyelt tárolónyilvántartások
description: Bevezetés az Azure Container Registry szolgáltatásba, amely felhőalapú, felügyelt és magán Docker-beállításjegyzékeket biztosít.
author: stevelas
ms.topic: overview
ms.date: 02/10/2020
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 1992a2a63d16a955d136459f5dbaece7df815c71
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77132032"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Az Azure-beli privát Docker-tárolójegyzékek bemutatása

Az Azure Container Registry egy felügyelt, privát Docker beállításjegyzék-szolgáltatás, amely a nyílt forráskódú Docker Registry 2.0-s adatbázison alapul. Azure-tároló-beállításjegyzékeket hozhat létre és tarthat karban a privát Docker-tárolórendszerképek és a kapcsolódó összetevők tárolásához és kezeléséhez.

Azure-tároló-beállításjegyzékek a meglévő tároló fejlesztési és üzembe helyezési folyamatok, vagy az Azure Container Registry feladatok segítségével tárolórendszerképek et az Azure-ban. Igény szerint hozhat létre, vagy teljesen automatizálhatja a buildeket olyan eseményindítókkal, mint a forráskód véglegesítése és az alaprendszerkép-frissítések.

A Docker- és beállításjegyzék-fogalmakról a [Docker áttekintése](https://docs.docker.com/engine/docker-overview/) és [a Beállításjegyzékek, adattárak és lemezképek című témakörben olvashat bővebben.](container-registry-concepts.md)

## <a name="use-cases"></a>Használati esetek

Rendszerképek lekérése egy Azure-beli tároló-beállításjegyzékből különféle telepítési célokra:

* **Méretezhető előkészítési rendszerek**, amelyek tárolóalapú alkalmazásokat kezelnek gazdagépfürtökben (többek között [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/) és [Docker Swarm](https://docs.docker.com/swarm/)).
* **Azure-szolgáltatások**, amelyek támogatják az alkalmazások építését és nagy mennyiségű alkalmazás futtatását, beleértve az [Azure Kubernetes Service (AKS)](../aks/index.yml), az [App Service](../app-service/index.yml), a [Batch](../batch/index.yml), a [Service Fabric](/azure/service-fabric/) és egyéb szolgáltatásokat.

A fejlesztők emellett le is küldhetik a tároló-beállításjegyzékeket a tárolófejlesztési munkafolyamatok részeként. Például egy tároló beállításjegyzék-jegyzéket egy folyamatos integrációs és kézbesítési eszközből, például [az Azure Pipelines](/azure/devops/pipelines/ecosystems/containers/acr-template) vagy a [Jenkins.](https://jenkins.io/)

Konfigurálja az ACR-feladatokat az alkalmazáslemezképek automatikus újraépítéséhez az alaplemezképek frissítésekor, vagy automatizálja a lemezképeket, amikor a csapat egy Git-tárházba véglegesíti a kódot. Többlépéses feladatokat hozhat létre több tárolórendszerkép létrehozásának, tesztelésének és javításának automatizálásához a felhőben.

Az Azure olyan eszközökkel rendelkezik, mint az Azure parancssori felülete, az Azure Portal és az API-támogatás az Azure-tárolók beállítási adatainak kezeléséhez. Igény szerint telepítheti a [Docker-bővítmény t a Visual Studio-kódhoz](https://code.visualstudio.com/docs/azure/docker) és az [Azure-fiókbővítményt](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) az Azure-tároló-beállításjegyzékekkel való együttműködésre. Lekérése és leküldéses lemezképek egy Azure-tároló beállításjegyzékbe, vagy futtassa az ACR-feladatok, mindezt a Visual Studio-kód.

## <a name="key-features"></a>A legfontosabb jellemzők

* **Beállításjegyzék-adatbázis –** hozzon létre egy vagy több tároló-beállításjegyzéket az Azure-előfizetésben. A regisztika három termékkészletben érhető el: [alapszintű, standard és prémium verzió,](container-registry-skus.md)amelyek mindegyike támogatja a webhook-integrációt, az Azure Active Directoryval való beállításjegyzék-hitelesítést és a törlési funkciókat. Hozzon létre egy beállításjegyzéket az üzemelő példányaival megegyező Azure-beli helyen, hogy kiaknázhassa tárolórendszerképei helyi, hálózatközeli tárolásának előnyeit. Haladó szintű replikációs és tárolórendszerkép-elosztási forgatókönyvekhez használja a Prémium szintű beállításjegyzékek [georeplikációs](container-registry-geo-replication.md) funkcióját. 

* **Biztonság és hozzáférés** – Az Azure CLI vagy a `docker login` standard parancs használatával jelentkezik be egy beállításjegyzékbe. Az Azure Container Registry átviszi a tárolórendszerképeket HTTPS-kapcsolaton keresztül, és támogatja a TLS-t az ügyfélkapcsolatok védelméhez. 

  > [!IMPORTANT]
  > 2020. január 13-tól kezdődően az Azure Container Registry a TLS 1.2 használatához a kiszolgálók és alkalmazások összes biztonságos kapcsolatára lesz szüksége. Engedélyezze a TLS 1.2-t bármely újabb docker-ügyfél használatával (18.03.0-s vagy újabb verzió). A TLS 1.0 és 1.1 támogatása megszűnik. 

  Azure-identitás, egy Azure-Active Directory által támogatott [egyszerű szolgáltatás](../active-directory/develop/app-objects-and-service-principals.md)vagy egy megadott rendszergazdai fiók használatával [szabályozhatja](container-registry-authentication.md) a tárolóbeállítás-beállításjegyzékhez való hozzáférést. A szerepköralapú hozzáférés-vezérlés (RBAC) használatával felhasználókat vagy rendszereket rendelhet a beállításjegyzékhez.

  A prémium termékváltozat biztonsági szolgáltatásai közé tartozik a képcímke-aláírás [tartalommegbízhatósága,](container-registry-content-trust.md) valamint a [tűzfalak és a virtuális hálózatok (előzetes verzió)](container-registry-vnet.md) a beállításjegyzékhez való hozzáférés korlátozására. Az Azure Security Center szükség esetén integrálható az Azure Container Registry rendszerképpel [a lemezképek bekéseléséhez,](../security-center/azure-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) amikor egy lemezképet egy beállításjegyzékbe tolnak.

* **Támogatott rendszerképek és összetevők** – egy tárházban csoportosítva, minden egyes rendszerkép egy Docker-kompatibilis tároló csak olvasható pillanatképe. Az Azure tároló-beállításjegyzékek Windows- és Linux-rendszerképeket is tartalmazhatnak. A rendszerképek neveit Ön határozza meg mindegyik tárolókörnyezetben. A rendszerképek szabványos [Docker-parancsokkal](https://docs.docker.com/engine/reference/commandline/) küldhetők le egy adattárba, vagy hívhatók elő onnan. A Docker-tárolórendszerképek mellett az Azure Container Registry [a kapcsolódó tartalomformátumokat,](container-registry-image-formats.md) például a [Helm-diagramokat](container-registry-helm-repos.md) és az [Open Container Initiative (OCI) image format specification számára](https://github.com/opencontainers/image-spec/blob/master/spec.md)készített lemezképeket is tárolja.

* **Automatikus lemezképek** – Az [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR-feladatok) használatával egyszerűsítheti a lemezképek készítését, tesztelését, lenyomását és üzembe helyezését az Azure-ban. Az ACR-feladatok használatával például kiterjesztheti a fejlesztési belső `docker build` hurkot a felhőre a műveletek Azure-ba való kiszervezésével. Konfigurálhat összeállítási feladatokat a tároló operációs rendszerének és a keretrendszer javítási folyamatának automatizálására, valamint a rendszerképek automatikus összeállítására, ha a csoport kódot véglegesít a forráskezelőben.

  [A többlépéses feladatok](container-registry-tasks-overview.md#multi-step-tasks) lépésalapú feladatdefiníciót és -végrehajtást biztosítanak a tárolóképek felhőbeli létrehozásához, teszteléséhez és javításához. A feladatlépések tárolólemezképek különálló buildelési és leküldéses műveleteit határozzák meg. Emellett egy vagy több tároló végrehajtását is definiálhatják; a lépések a tárolót használják végrehajtási környezetnek.

## <a name="next-steps"></a>További lépések

* [Tároló-beállításjegyzék létrehozása az Azure Portalon](container-registry-get-started-portal.md)
* [Tároló beállításjegyzék létrehozása az Azure CLI-vel](container-registry-get-started-azure-cli.md)
* [A tárolók építésének és karbantartásának automatizálása az ACR-feladatokkal](container-registry-tasks-overview.md)
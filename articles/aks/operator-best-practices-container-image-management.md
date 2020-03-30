---
title: Operátori gyakorlati tanácsok – Tárolórendszerkép-kezelés az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a fürtoperátorok ajánlott eljárásokat az Azure Kubernetes-szolgáltatás (AKS) tárolólemezképek kezeléséhez és biztonságossá tétele érdekében
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594744"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások a tárolórendszerkép-kezeléshez és -biztonsághoz az Azure Kubernetes-szolgáltatásban (AKS)

Az Azure Kubernetes-szolgáltatás (AKS) alkalmazások fejlesztése és futtatása során a tárolók és a tárolórendszerképek biztonsága kulcsfontosságú szempont. Az elavult alaprendszerképeket vagy nem javított alkalmazás-futási időszakokat tartalmazó tárolók biztonsági kockázatot és lehetséges támadási vektort jelentenek. Ezek a kockázatok minimalizálása érdekében integrálnia kell azokat az eszközöket, amelyek a tárolókban a build és a futásidejű problémákat keresik és orvosolják. Minél korábban a folyamat a biztonsági rés vagy elavult alaprendszerkép fogott, annál biztonságosabb a fürt. Ebben a *cikkben a tárolók* a tárolóbeállításjegyzékben tárolt tárolórendszerképeket és a futó tárolókat is jelentik.

Ez a cikk az AKS-ben lévő tárolók védelmére összpontosít. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Képbiztonsági rések felderítése és kiújítása
> * Tárolórendszerkép automatikus aktiválása és újratelepítése az alaprendszerkép frissítésekor

A [fürtbiztonsággal][best-practices-cluster-security] és a [podbiztonsággal][best-practices-pod-security]kapcsolatos gyakorlati tanácsokat is elolvashatja.

A Security [Center tárolóbiztonság szolgáltatásával][security-center-containers] a tárolók biztonsági réseit is átkezheti.  Az [Azure Container Registry integráció][security-center-acr] a Security Centerrel is létezik, amely segít megvédeni a rendszerképeket és a beállításjegyzéket a biztonsági résektől.

## <a name="secure-the-images-and-run-time"></a>Biztosítsa a képeket és a futási időt

**Ajánlott eljárások –** A tárolórendszerképek biztonsági rései után késést keres, és csak az érvényesítésen átadott lemezképeket telepíthet. Rendszeresen frissítse az alaplemezképeket és az alkalmazás futási idejét, majd telepítse újra a számítási feladatokat az AKS-fürtben.

A tárolóalapú számítási feladatok bevezetésének egyik problémája a saját alkalmazások létrehozásához használt lemezképek és futásidejű alkalmazások biztonságának ellenőrzése. Hogyan győződheti meg arról, hogy nem vezet be biztonsági réseket a központi telepítések? A telepítési munkafolyamatnak tartalmaznia kell egy folyamatot a tárolóképek beszaporítására olyan eszközökkel, mint a [Twistlock][twistlock] vagy [az Aqua,][aqua]és csak ellenőrzött lemezképek telepítését kell engedélyeznie.

![Tárolórendszerképek bekéselése és kijavítása, ellenőrzés és üzembe helyezés](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Egy valós példában a folyamatos integráció és a folyamatos üzembe helyezési (CI/CD) folyamat segítségével automatizálhatja a lemezképek, -ellenőrzés és -üzembe helyezés. Az Azure Container Registry tartalmazza ezeket a biztonsági réseket szkennelési képességek.

## <a name="automatically-build-new-images-on-base-image-update"></a>Új képek automatikus létrehozása az alapkép frissítésén

**Ajánlott eljárásokra vonatkozó útmutatás** – Alaplemezképek alkalmazásképek használatakor az automatizálás segítségével új lemezképeket hozhat létre az alaplemezkép frissítésekor. Mivel ezek az alaplemezképek általában biztonsági javításokat tartalmaznak, frissítse az alsóbb rétegbeli alkalmazástároló-rendszerképeket.

Minden alkalommal, amikor egy alaprendszerkép frissül, az alsóbb rétegbeli tárolórendszerképeket is frissíteni kell. Ezt a buildelési folyamatot integrálni kell az érvényesítési és üzembe helyezési folyamatokba, például az [Azure-folyamatokba][azure-pipelines] vagy a Jenkinsbe. Ezek a folyamatok gondoskodnak arról, hogy az alkalmazások továbbra is futnak a frissített alapú lemezképeken. Az alkalmazástároló-rendszerképek ellenőrzése után az AKS-telepítések ezután frissíthetők a legújabb, biztonságos lemezképek futtatásához.

Az Azure Container Registry Tasks is automatikusan frissítheti a tárolórendszerképeket, amikor az alaprendszerkép frissül. Ez a funkció lehetővé teszi, hogy kis számú alaplemezképet hozzon létre, és rendszeresen folyamatosan frissítse őket a hiba- és biztonsági javításokkal.

Az alaprendszerkép-frissítésekről az [Automatikus lemezképek alaplemezkép-frissítésre][acr-base-image-update]épülaz Azure Container Registry Tasks című témakörben talál további információt.

## <a name="next-steps"></a>További lépések

Ez a cikk a tárolók védelmére összpontosított. Az alábbi témakörcikkekben az alábbi cikkekben valósíthatja meg a területeket:

* [Az Azure Container beállításjegyzék-feladatokkal az alaprendszerkép-frissítésre épülő lemezképek automatizálása][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: /azure/security-center/container-security
[security-center-acr]: /azure/security-center/azure-container-registry-integration
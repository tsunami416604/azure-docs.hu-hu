---
title: Ajánlott eljárások operátor - tároló rendszerképek kezelése az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárások az kezelését és biztonságos tárolórendszerképek az Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: efe72157f598c336248e407c57bce92fe87da23a
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594744"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások tárolókezelés kép és a biztonság az Azure Kubernetes Service (AKS)

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a tárolók és a tárolórendszerképek biztonságát egy fő szempont. Elavult tartalmazó lemezképek kiinduló vagy veszéllyel alkalmazás modulok bevezetni egy biztonsági kockázatokat és a lehetséges támadási felület. Ez a kockázat minimalizálása érdekében integrálnia kell keresése, és a tárolókat a problémák elhárítására összeállítás, valamint a futásidejű eszközöket. A korábban az a folyamat a biztonsági rés vagy elavult alaplemezkép történt annál biztonságosabb a fürtöt. Ebben a cikkben a *tárolók* a tároló-beállításjegyzékben tárolt tároló-lemezképeket és a futó tárolókat jelentik.

Ez a cikk foglalkozik, hogyan teheti biztonságossá a tárolókat az aks-ben a. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Keressen és a kép a biztonsági rések
> * Automatikus aktiválása és ismételt üzembe helyezése tárolórendszerképek, amikor frissül egy alaplemezkép

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

A [tárolók biztonsága Security Center][security-center-containers] is használható a tárolók biztonsági rések vizsgálatához.  Az Security Center- [integrációval is Azure Container Registry][security-center-acr] , hogy megvédje a lemezképeket és a beállításjegyzéket a biztonsági rések ellen.

## <a name="secure-the-images-and-run-time"></a>A képek biztonságos, és a futási idő

**Ajánlott eljárási útmutató** – a biztonsági rések beolvasása a tárolók rendszerképein, és csak az ellenőrzésen átadott lemezképek telepítése. Rendszeresen frissítse az alaprendszerképek lekéréshez és az alkalmazás futtatókörnyezete, majd ismételt üzembe helyezés az AKS-fürtöt a számítási feladatokat.

Tárolóalapú számítási feladatok a bevezetésével egy potenciálisan veszélyes ellenőrzi, lemezképek és a futtatókörnyezet segítségével hozhatók létre a saját alkalmazások biztonságát. Hogyan, arról is, hogy, hogy nem indít-e biztonsági réseket, az üzemelő példányokat? Az üzembe helyezési munkafolyamatnak tartalmaznia kell egy olyan folyamatot, amely a [Twistlock][twistlock] vagy az [Aqua][aqua]használatával olyan eszközökkel vizsgálja meg a tárolók lemezképeit, amelyek csak az ellenőrzött lemezképek telepítését teszik lehetővé.

![Vizsgálat és javíthatja a tárolórendszerképek, ellenőrzésének és üzembe helyezése](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Egy való életből vett példában használhatja egy folyamatos integrációs és folyamatos üzembe helyezés (CI/CD) folyamatokat a lemezkép vizsgálatok, ellenőrzési és központi telepítések automatizálásához. Az Azure Container Registry tartalmazza a képességek ellenőrzése a biztonsági rések.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatikusan hozhat létre az új képek a rendszerkép alapszintű frissítésének

**Ajánlott eljárási útmutató** – az alaplemezképek alkalmazás-lemezképekhez való használatakor az Automation használatával új rendszerképeket hozhat létre az alaprendszerkép frissítésekor. Ezen alaplemezképek általában biztonsági javításokat tartalmaznak, frissítse minden olyan alárendelt alkalmazás tárolórendszerképek.

Minden alkalommal, amikor egy alaplemezkép frissül, bármely alsóbb rétegbeli tárolórendszerképek is frissíteni kell. Ezt az összeállítási folyamatot integrálni kell az ellenőrzési és üzembe helyezési folyamatokkal, például az [Azure][azure-pipelines] -folyamatokba vagy a jenkinsbe. Ezek a folyamatok gondoskodik arról, hogy az alkalmazások továbbra is futtassa a frissített alapján képeken. Után az alkalmazás tárolórendszerképek érvényesíti, az AKS-telepítések majd frissíthető a legújabb, biztonságos képek futtatásához.

Az Azure Container Registry feladatok is automatikusan frissítheti tárolórendszerképek az alaprendszerképet frissítésekor. Ez a funkció lehetővé teszi, hogy hozhat létre alaplemezképek kis számú, és rendszeresen tartsa a hibajavításokat és biztonsági javításokat frissítését.

További információ az alapszintű rendszerkép frissítéseiről: [a rendszerkép-buildek automatizálása az alapszintű rendszerkép frissítése Azure Container Registry feladatokkal][acr-base-image-update].

## <a name="next-steps"></a>Következő lépések

Ez a cikk biztonságossá tétele a tárolókat összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Rendszerkép-buildek automatizálása Azure Container Registry feladatokkal az alapszintű rendszerkép frissítése során][acr-base-image-update]

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
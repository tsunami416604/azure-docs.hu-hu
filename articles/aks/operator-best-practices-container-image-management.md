---
title: Ajánlott eljárások operátor - tároló rendszerképek kezelése az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárások az kezelését és biztonságos tárolórendszerképek az Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: 3feadaca361950df2a09f8da33fe380fc3763763
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "67614817"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások tárolókezelés kép és a biztonság az Azure Kubernetes Service (AKS)

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a tárolók és a tárolórendszerképek biztonságát egy fő szempont. Elavult tartalmazó lemezképek kiinduló vagy veszéllyel alkalmazás modulok bevezetni egy biztonsági kockázatokat és a lehetséges támadási felület. Ez a kockázat minimalizálása érdekében integrálnia kell keresése, és a tárolókat a problémák elhárítására összeállítás, valamint a futásidejű eszközöket. A korábban az a folyamat a biztonsági rés vagy elavult alaplemezkép történt annál biztonságosabb a fürtöt. Ebben a cikkben *tárolók* azt jelenti, hogy mindkét a tárolórendszerképeket tárolt egy tároló-beállításjegyzéket, és a futó tárolók.

Ez a cikk foglalkozik, hogyan teheti biztonságossá a tárolókat az aks-ben a. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Keressen és a kép a biztonsági rések
> * Automatikus aktiválása és ismételt üzembe helyezése tárolórendszerképek, amikor frissül egy alaplemezkép

Emellett elolvashatja a [fürt biztonságával][best-practices-cluster-security] és a [Pod biztonsággal][best-practices-pod-security]kapcsolatos ajánlott eljárásokat is.

## <a name="secure-the-images-and-run-time"></a>A képek biztonságos, és a futási idő

**Ajánlott eljárásokkal kapcsolatos útmutatás** – a tároló kiszűrhető a biztonsági réseket, és központi telepítése kizárólag a lemezképek, amelyek megfeleltek az érvényesítés. Rendszeresen frissítse az alaprendszerképek lekéréshez és az alkalmazás futtatókörnyezete, majd ismételt üzembe helyezés az AKS-fürtöt a számítási feladatokat.

Tárolóalapú számítási feladatok a bevezetésével egy potenciálisan veszélyes ellenőrzi, lemezképek és a futtatókörnyezet segítségével hozhatók létre a saját alkalmazások biztonságát. Hogyan, arról is, hogy, hogy nem indít-e biztonsági réseket, az üzemelő példányokat? Az üzembe helyezési munkafolyamatnak tartalmaznia kell egy olyan folyamatot, amely a [Twistlock][twistlock] vagy az [Aqua][aqua]használatával olyan eszközökkel vizsgálja meg a tárolók lemezképeit, amelyek csak az ellenőrzött lemezképek telepítését teszik lehetővé.

![Vizsgálat és javíthatja a tárolórendszerképek, ellenőrzésének és üzembe helyezése](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Egy való életből vett példában használhatja egy folyamatos integrációs és folyamatos üzembe helyezés (CI/CD) folyamatokat a lemezkép vizsgálatok, ellenőrzési és központi telepítések automatizálásához. Az Azure Container Registry tartalmazza a képességek ellenőrzése a biztonsági rések.

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatikusan hozhat létre az új képek a rendszerkép alapszintű frissítésének

**Ajánlott eljárásokkal kapcsolatos útmutatás** – alkalmazás-lemezképek esetén az alaprendszerképet frissítésekor, hozhat létre új képek használata automation használata alaplemezképek. Ezen alaplemezképek általában biztonsági javításokat tartalmaznak, frissítse minden olyan alárendelt alkalmazás tárolórendszerképek.

Minden alkalommal, amikor egy alaplemezkép frissül, bármely alsóbb rétegbeli tárolórendszerképek is frissíteni kell. Ezt az összeállítási folyamatot integrálni kell az ellenőrzési és üzembe helyezési folyamatokkal, például az [Azure][azure-pipelines] -folyamatokba vagy a jenkinsbe. Ezek a folyamatok gondoskodik arról, hogy az alkalmazások továbbra is futtassa a frissített alapján képeken. Után az alkalmazás tárolórendszerképek érvényesíti, az AKS-telepítések majd frissíthető a legújabb, biztonságos képek futtatásához.

Az Azure Container Registry feladatok is automatikusan frissítheti tárolórendszerképek az alaprendszerképet frissítésekor. Ez a funkció lehetővé teszi, hogy hozhat létre alaplemezképek kis számú, és rendszeresen tartsa a hibajavításokat és biztonsági javításokat frissítését.

További információ az alapszintű rendszerkép frissítéseiről: a rendszerkép-buildek automatizálása az alapszintű [rendszerkép frissítése Azure Container Registry][acr-base-image-update]feladatokkal.

## <a name="next-steps"></a>További lépések

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

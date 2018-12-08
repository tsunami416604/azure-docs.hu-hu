---
title: Ajánlott eljárások operátor - tároló rendszerképek kezelése az Azure Kubernetes-szolgáltatások (AKS)
description: A fürt operátor ajánlott eljárások az kezelését és biztonságos tárolórendszerképek az Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 20d3d3d755a35927550064ddfdf3b983348f93d3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109767"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Ajánlott eljárások tárolókezelés kép és a biztonság az Azure Kubernetes Service (AKS)

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a tárolók biztonságát kulcs veszi figyelembe. Elavult alaprendszerképek lekéréshez vagy az alkalmazás futtatókörnyezete tartalmazó bevezetni egy biztonsági kockázatokat és a lehetséges támadási felület. Ez a kockázat minimalizálása érdekében integrálnia kell eszközöket, amelyek keresése, és a tárolókat a problémák elhárítására.

Fejlesztés és alkalmazások futtatásához az Azure Kubernetes Service (AKS), a tárolók és a tárolórendszerképek biztonságát egy fő szempont. Elavult tartalmazó lemezképek kiinduló vagy veszéllyel alkalmazás modulok bevezetni egy biztonsági kockázatokat és a lehetséges támadási felület. Ez a kockázat minimalizálása érdekében integrálnia kell keresése, és a tárolókat a problémák elhárítására összeállítás, valamint a futásidejű eszközöket. A korábban az a folyamat a biztonsági rés vagy elavult alaplemezkép történt annál biztonságosabb a fürtöt. Ebben a cikkben *tárolók* azt jelenti, hogy mindkét a tárolórendszerképeket tárolt egy tároló-beállításjegyzéket, és a futó tárolók.

Ez a cikk foglalkozik, hogyan teheti biztonságossá a tárolókat az aks-ben a. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Keressen és a kép a biztonsági rések
> * A digitálisan aláírt tárolórendszerképeket egy megbízható registry használata
> * Automatikus aktiválása és ismételt üzembe helyezése tárolórendszerképek, amikor frissül egy alaplemezkép

Ajánlott eljárást is olvashatja [biztonsági fürt] [ best-practices-cluster-security] és [pod biztonsági][best-practices-pod-security].

## <a name="secure-the-images-and-run-time"></a>A képek biztonságos, és a futási idő

**Ajánlott eljárásokkal kapcsolatos útmutatás** – a tároló kiszűrhető a biztonsági réseket, és központi telepítése kizárólag a lemezképek, amelyek megfeleltek az érvényesítés. Rendszeresen frissítse az alaprendszerképek lekéréshez és az alkalmazás futtatókörnyezete, majd ismételt üzembe helyezés az AKS-fürtöt a számítási feladatokat.

Tárolóalapú számítási feladatok a bevezetésével egy potenciálisan veszélyes ellenőrzi, lemezképek és a futtatókörnyezet segítségével hozhatók létre a saját alkalmazások biztonságát. Hogyan, arról is, hogy, hogy nem indít-e biztonsági réseket, az üzemelő példányokat? Az üzembe helyezést megvalósító munkafolyamat tartalmaznia kell egy folyamatot például eszközökkel tárolórendszerképek vizsgálata [Twistlock] [ twistlock] vagy [Aqua][aqua], majd hogy csak ellenőrzött rendszerképek üzembe helyezni.

![Vizsgálat és javíthatja a tárolórendszerképek, ellenőrzésének és üzembe helyezése](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Egy való életből vett példában használhatja egy folyamatos integrációs és folyamatos üzembe helyezés (CI/CD) folyamatokat a lemezkép vizsgálatok, ellenőrzési és központi telepítések automatizálásához. Az Azure Container Registry tartalmazza a képességek ellenőrzése a biztonsági rések.

## <a name="use-a-trusted-registry"></a>Megbízható beállításjegyzék

**Ajánlott eljárásokkal kapcsolatos útmutatás** – korlátozza a lemezkép-regisztrációs adatbázisok, amelyek podok és központi telepítések használhatja. Csak a megbízható beállításjegyzékek, amelyben ellenőrizni és szabályozhatja az elérhető rendszerképek engedélyezi.

A fokozott biztonság érdekében használatával is írhatják a tárolólemezképek ugyanúgy, mint az alkalmazás kódja is digitális aláírással. Majd csak akkor engedélyezik az AKS üzembe helyezhetnek aláírt lemezképeket. Ez a folyamat egy kiegészítő biztonsági réteget nyújt, abban, hogy csak digitálisan aláírt és megbízhatónak tekintenek meg, nem csak képeket, hogy egy biztonsági rés ellenőrzés rendszerképeket AKS korlátozza. Akkor győződjön meg arról is, hogy a tároló rendszerképét nem módosultak-e, és pontosan azonos nevű kép helyébe.

A digitálisan aláírt tárolórendszerképek biztosító megbízható beállításjegyzékek összetettebbé a környezetben, de lehet szükség az egyes házirend vagy az előírásoknak való megfelelés. Az Azure Container Registry támogatja a megbízható beállításjegyzékek használatát, és írja alá a lemezképeket.

A digitálisan aláírt lemezképek kapcsolatos további információkért lásd: [tartalom az Azure Container Registry megbízhatósági][acr-content-trust].

## <a name="automatically-build-new-images-on-base-image-update"></a>Automatikusan hozhat létre az új képek a rendszerkép alapszintű frissítésének

**Ajánlott eljárásokkal kapcsolatos útmutatás** – alkalmazás-lemezképek esetén az alaprendszerképet frissítésekor, hozhat létre új képek használata automation használata alaplemezképek. Ezen alaplemezképek általában biztonsági javításokat tartalmaznak, frissítse minden olyan alárendelt alkalmazás tárolórendszerképek.

Minden alkalommal, amikor egy alaplemezkép frissül, bármely alsóbb rétegbeli tárolórendszerképek is frissíteni kell. A létrehozási folyamat integrálni kell például érvényesítési és üzembe helyezési [Azure folyamatok] [ azure-pipelines] vagy a jenkins használatával. Ezek a folyamatok gondoskodik arról, hogy az alkalmazások továbbra is futtassa a frissített alapján képeken. Után az alkalmazás tárolórendszerképek érvényesíti, az AKS-telepítések majd frissíthető a legújabb, biztonságos képek futtatásához.

Az Azure Container Registry feladatok is automatikusan frissítheti tárolórendszerképek az alaprendszerképet frissítésekor. Ez a funkció lehetővé teszi, hogy hozhat létre alaplemezképek kis számú, és rendszeresen tartsa a hibajavításokat és biztonsági javításokat frissítését.

Alaplemezkép frissítésekkel kapcsolatos további információkért lásd: [automatizálása kép épül, amely az Azure Container Registry feladatok rendszerkép alapszintű frissítésének][acr-base-image-update].

## <a name="next-steps"></a>További lépések

Ez a cikk biztonságossá tétele a tárolókat összpontosít. Néhány ilyen területet végrehajtásához a következő cikkekben talál:

* [Az Azure Container Registry feladatokat az rendszerkép alapszintű frissítésének rendszerképek létrehozásának automatizálása][acr-base-image-update]
* [Az Azure Container Registry tartalom bizalmi kapcsolat][acr-content-trust]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/?view=vsts
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-content-trust]: ../container-registry/container-registry-content-trust.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md

---
title: A tárolójavaslatok az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum ismerteti az Azure Security Center javaslatokat, hogyan védheti meg a tárolók.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2e76c7f7-a3dd-4d9f-add9-7e0e10e9324d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: memildin
ms.openlocfilehash: b1e1402f58b103570d6a98a5f9a01c8168abf749
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77912360"
---
# <a name="understand-azure-security-center-container-recommendations"></a>Az Azure Security Center tárolókra vonatkozó ajánlásainak értelmezése

A monolit-alkalmazások áttelepítése során kritikus fontosságú, konténeres, felhőalapú natív alkalmazásokat futtathat éles környezetben, kihasználhatja a tárolók szolgáltatásait, beleértve az egyszerű és gyors üzembe helyezést és frissítést. Mivel a telepített tárolók száma folyamatosan növekszik, biztonsági megoldásokat kell elhelyezni, hogy az Ön számára láthatóak a biztonsági állapotát a tárolók, és segít megvédeni őket a fenyegetésektől.

Az Azure Security Center a következő képességekkel segíti a tárolók biztonságossá tétele it:

- **Az IaaS Linux-gépeken tárolt tárolók láthatósága**<br>Az Azure Security Centerben a Tárolók lap megjeleníti a Dockerrel telepített összes virtuális gépet. A virtuális gépek biztonsági problémáinak feltárása során a Security Center további információkat nyújt a számítógépen lévő tárolókról, például a Docker-verzióról és a gazdagépen futó lemezképek számáról.

    ![tároló lap](./media/security-center-container-recommendations/docker-recommendation.png)


- **A Docker CIS-referenciaértékén alapuló biztonsági javaslatok**<br>A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. A Security Center útmutatást nyújt a problémák gyors megoldásához és az időmegtakarításhoz. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.

    ![tároló lap](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Valós idejű konténerfenyegetés elleni védelem**<br> A Security Center valós idejű veszélyforrások elleni védelmet biztosít a Linux-összetevőkkel rendelkező Linux-gépeken lévő tárolók számára. A riasztások számos gyanús Docker-tevékenységet azonosítanak, például egy kiemelt tároló létrehozását a gazdagépen, a Docker-tárolóban futó Secure Shell (SSH) kiszolgáló jelzését, vagy a kriptográfiai bányászok használatát. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

    ![tároló lap](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Javaslatok
Az alábbi táblázatok segítségével megismerheti az IaaS Linux-gépeken tárolt rendelkezésre álló tárolókat és a Docker-konfigurációk biztonsági értékelését.

| Ajánlás | Leírás | Kockázatcsökkentés |
| --- | --- | --- |
|A tárolóbiztonsági konfigurációk biztonsági réseinek elhárítása |A tárolóbiztonsági konfigurációk biztonsági rései a konfigurációs gyakorlati tanácsok alapján.| A tároló biztonsági beállításainak biztonsági rései elhárítása:<br>1. Tekintse át a hibás szabályok listáját.<br>2. Rögzítse az egyes szabályokat a megadott utasítások szerint.|


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a más Azure-erőforrástípusokra vonatkozó javaslatokról, olvassa el az alábbi témakört:

* [Identitás és hozzáférés figyelése az Azure Security Centerben](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás védelme az Azure Security Centerben](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.
---
title: A Container ajánlásai a Azure Security Centerban | Microsoft Docs
description: Ez a dokumentum ismerteti a Azure Security Center javaslatokat a tárolók védelmének biztosításához.
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
ms.openlocfilehash: 96efe5271b91f87e30ba26aabe69a0de76e1a791
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202392"
---
# <a name="understand-azure-security-center-container-recommendations"></a>A Azure Security Center-tárolóra vonatkozó javaslatok ismertetése

Ha a Monolith-alkalmazásait úgy telepíti át, hogy üzleti szempontból kritikus, tároló Felhőbeli natív alkalmazásokat futtasson, kihasználhatja a tárolók funkcióit, beleértve az egyszerű és gyors üzembe helyezést és a frissítést is. Ahogy az üzembe helyezett tárolók száma továbbra is növekszik, a biztonsági megoldásokat biztosítani kell a tárolók biztonsági állapotának áttekintéséhez, és a fenyegetések elleni védelemhez.

A Azure Security Center a következő képességeket biztosítja a tárolók biztonságossá tételéhez:

- **IaaS Linux rendszerű gépeken üzemeltetett tárolók láthatósága**<br>Azure Security Center a containers (tárolók) lap megjeleníti a Docker-vel üzembe helyezett összes virtuális gépet. A virtuális gépek biztonsági problémáinak feltárásakor Security Center a gépen lévő tárolókkal kapcsolatos további információkat nyújt, például a Docker-verziót és a gazdagépen futó rendszerképek számát.

    ![tároló lap](./media/security-center-container-recommendations/docker-recommendation.png)


- **Biztonsági javaslatok a Docker-hez készült CIS-teljesítményteszt alapján**<br>A Security Center megvizsgálja a Docker-konfigurációkat, és egy listát készít az összes átvizsgált hibás szabályról, így láthatóvá teszi a konfigurációs hibákat. Security Center útmutatást nyújt a problémák gyors megoldásához és az idő megtakarításához. A Security Center folyamatosan értékeli a Docker-konfigurációkat, és tájékoztatja a legfrissebb állapotukról.

    ![tároló lap](./media/security-center-container-recommendations/container-cis-benchmark.png)

- **Valós idejű tároló fenyegetésének észlelése**<br> Security Center valós idejű veszélyforrások észlelését teszi lehetővé az auditált összetevővel rendelkező Linux rendszerű gépeken tárolt tárolók esetében. A riasztások számos gyanús Docker-tevékenységet azonosítanak, például egy emelt szintű tároló létrehozását a gazdagépen, a Docker-tárolóban futó Secure Shell-(SSH-) kiszolgáló, illetve a kriptográfiai bányászok használatának jeleként. Ezen adatok alapján gyorsan elháríthatja a biztonsági problémákat, és javíthatja tárolói védelmét.

    ![tároló lap](./media/security-center-container-recommendations/docker-threat-detection.png)

## <a name="recommendations"></a>Javaslatok
Az alábbi táblázatokat hivatkozásként használva megismerheti a IaaS Linux rendszerű gépeken tárolt elérhető tárolókat, valamint a Docker-konfigurációk biztonsági értékelését.

| Ajánlás | Leírás | Szervizelés |
| --- | --- | --- |
|Biztonsági rések szervizelése a tároló biztonsági beállításaiban |A biztonsági rések javítása a tároló biztonsági konfigurációjában az ajánlott eljárások alapján.| A biztonsági rések szervizelése a tároló biztonsági konfigurációjában:<br>1. Tekintse át a sikertelen szabályok listáját.<br>2. Javítsa ki az egyes szabályokat a megadott utasítások szerint.|


## <a name="next-steps"></a>További lépések
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Identitás és hozzáférés az Azure Security Center figyelése](security-center-identity-access.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)
* [Az Azure SQL-szolgáltatás az Azure Security Center védelme](security-center-sql-service-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [A gépek és alkalmazások védelme az Azure Security Centerben](security-center-virtual-machine-protection.md)
* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.


---
title: "Frissítés az operációs rendszer verziója, az Azure Security Centerben |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan valósítja meg az Azure Security Center ajánlás ** frissítés operációs rendszer verziója **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: aa372492-ecdb-4368-8fdd-d8ed31e216ee
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: ce0d178914907750e5da59f223a4b1e04b9bb6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="update-os-version-in-azure-security-center"></a>Frissítse az operációs rendszer verziója, az Azure Security Centerben
A virtuális gépek (VM), a felhőalapú szolgáltatások Azure Security Center javasolni fogja, hogy az operációs rendszer frissíteni kell, ha van újabb verzió.  Csak felhőalapú szolgáltatások üzembe helyezési ponti figyelt éles környezetben futó webes és feldolgozói szerepköröket.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást.  A dokumentum nem tartalmaz lépésenkénti útmutatót.
> 
> 

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok** panelen válassza **frissítés operációsrendszer-verzió**.
   ![Operációs rendszer verziójának frissítése][1]
2. Ekkor megnyílik a panel **frissítés operációsrendszer-verzió**. Kövesse a lépéseket, ezen a panelen frissíteni az operációs rendszer verzióját.

## <a name="see-also"></a>Lásd még:
Ez a cikk bemutatta megvalósításához a Security Center ajánlás "Frissítés az operációs rendszer verzió." Cloud services és az operációs rendszer verzióját egy felhőalapú szolgáltatás frissítése kapcsolatos további információkért lásd:

* [A Cloud Services áttekintése](../cloud-services/cloud-services-choose-me.md)
* [Egy felhőalapú szolgáltatás frissítése](../cloud-services/cloud-services-update-azure-service.md)
* [A Cloud Services Konfigurálása](../cloud-services/cloud-services-how-to-configure-portal.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) --az Azure biztonsági legfrissebb hírek és információ.

<!--Image references-->
[1]: ./media/security-center-update-os-version/update-os-version.png

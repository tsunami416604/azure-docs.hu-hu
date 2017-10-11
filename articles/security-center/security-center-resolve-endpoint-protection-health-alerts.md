---
title: "Hárítsa el az endpoint protection állapotát riasztások az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás ** hárítsa el az Endpoint Protection állapotát riasztások **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4050f453-98fc-4314-8438-d476469757fb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/01/2016
ms.author: terrylan
ms.openlocfilehash: 5e6b136d6bd3b11fb82126d104fd0cb149255118
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Hárítsa el az endpoint protection állapotát riasztások az Azure Security Centerben
Azure Security Center javasolni fogja megoldani az észlelt endpoint protection állapotát riasztások.  Biztonsági központ lehetővé teszi a virtuális gépek (VM), az endpoint protection hibáinak és hány hibák vannak.

> [!NOTE]
> Ez a dokumentum egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A dokumentum nem tartalmaz lépésenkénti útmutatót.
> 
> 

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
1. Az a **javaslatok panel**, jelölje be **hárítsa el az Endpoint Protection állapotát riasztások**.
   ![Endpoint Protection-állapotriasztások feloldása][1]
2. Ekkor megnyílik a panel **Endpoint Protection hiba** amely felsorolja azokat a virtuális gépek hibák és a hibák száma az egyes virtuális gépek. Jelöljön ki egy virtuális Gépet a listából.
   ![Az Endpoint protection hiba][2]
3. A **hibák lista** ekkor megnyílik a kiválasztott virtuális géphez, hibák listáját megjelenítő panel. Válassza ki a hibát további a listából. Ekkor megnyílik egy panel a kijelölt hibával kapcsolatos információkat.
   ![Hibák lista][3]
   ![esemény][4]

## <a name="see-also"></a>Lásd még:
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági házirendek beállítása az Azure Security Centerben](security-center-policies.md) – Annak bemutatása, hogy miként konfigurálhat biztonsági házirendeket Azure-előfizetéseihez és az erőforráscsoportokhoz.
* [Biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Miként könnyítik meg a javaslatok az Azure-erőforrások védelmét?
* [Biztonsági állapotmonitorozás az Azure Security Centerben](security-center-monitoring.md) – Útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és reagálás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partneri megoldások monitorozása az Azure Security Centerrel](security-center-partner-solutions.md) – Útmutató a partneri megoldások biztonsági állapotának monitorozásához.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – Tájékozódás az Azure biztonságával kapcsolatos legfrissebb hírekről és információkról.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

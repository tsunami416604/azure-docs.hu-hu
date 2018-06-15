---
title: Állapotának és az Azure-készletben riasztások figyelése |} Microsoft Docs
description: Megtudhatja, hogyan állapotának és az Azure-készletben riasztások figyelése.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: mabrigg
ms.openlocfilehash: 446df7922422ccfcf3fbb92ecf153c6dec2f6197
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
ms.locfileid: "26640378"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>A figyelő állapotát és az Azure-készletben riasztások

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Azure verem infrastruktúra-figyelési funkciókat nyújtanak, amelyek lehetővé teszik állapotának és az Azure-verem régió riasztások megjelenítését tartalmazza. A **régió felügyeleti** csempe, a felügyeleti portálon alapértelmezés szerint az alapértelmezett szolgáltató előfizetés rögzítve a telepített régiók Azure verem sorolja fel. A csempe mindegyik régióhoz aktív kritikus és a figyelmeztető riasztások számát jeleníti meg, és a belépési pont állapotának és Azure verem riasztási funkcióit.

 ![A régió felügyeleti csempe](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Azure-készletben állapotfigyelő ismertetése

 Állapot és riasztások állapotfigyelő erőforrás-szolgáltató kezeli. Az állapotfigyelő erőforrás-szolgáltató Azure verem központi telepítése és konfigurálása során regisztrálja verem Azure infrastruktúra-összetevőihez. Ez a regisztráció lehetővé teszi az állapot és riasztások az egyes összetevők. Azure-készletben állapotfigyelő fogalma egyszerű. Ha egy összetevő regisztrált példányának riasztások, adott összetevő állapotát tükrözi a legrosszabb aktív riasztás súlyossága; figyelmeztető vagy kritikus.
 
 ## <a name="view-and-manage-component-health-state"></a>Megtekintheti és kezelheti az összetevő állapotát
 
 Azure verem kezelőként összetevők állapotát, a felügyeleti portálon, és a REST API-t és a PowerShell segítségével tekintheti meg.
 
A portál az állapot megtekintéséhez kattintson a régiót, amelyben a megtekinteni kívánt a **régió felügyeleti** csempére. Infrastruktúra-szerepkörök és erőforrás-szolgáltatók állapotát tekintheti meg.

![Infrastruktúra-szerepkörök listája](media/azure-stack-monitor-health/image2.png)

Egy erőforrás-szolgáltató vagy az infrastruktúra-szerepkör részletes információk megtekintéséhez rákattinthat.

> [!WARNING]
>Ha kattintson egy infrastruktúra-szerepkört, majd a szerepkörpéldányt nincsenek indításához indítsa újra a beállításokat, vagy -leállítás. Ne használja ezeket a műveleteket, amikor a frissítések alkalmazása az integrált rendszer. Is, megteheti **nem** Azure verem szoftverfejlesztői készlet környezetben használja az alábbi beállításokat. Ezek a beállítások készültek, csak egy integrált rendszerek környezetben, ahol van egy infrastruktúra-szerepkör egynél több szerepkörpéldányt. A szerepkör példánya (különösen AzS-Xrp01) a csomagban újraindít rendszer instabil. A hibaelhárítási segítséget, a problémát a post a [Azure verem fórum](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Riasztások megtekintése

Minden Azure verem régió aktív riasztások listáját érhető el közvetlenül a **régió felügyeleti** panelen. Az alapértelmezett beállítás az első csempe a **riasztások** csempe, amelyen a kritikus összefoglalását és figyelmeztető riasztások léptek fel a régióhoz. A riasztások csempe, mint bármilyen más csempét a panel, az irányítópultot, gyorsan elérheti őket is rögzíti.   

![Riasztások csempe, amely akkor vált figyelmeztetési állapotra](media/azure-stack-monitor-health/image3.png)

A felső részén kiválasztásával a **riasztások** csempe, akkor keresse meg a listában az összes aktív riasztás a régióhoz. Ha bejelöli, vagy a **kritikus** vagy **figyelmeztetés** sorelemet a a csempén, akkor keresse meg a riasztások (kritikus vagy figyelmeztetés) szűrt listája. 

![Szűrt figyelmeztető riasztások](media/azure-stack-monitor-health/image4.png)
  
A **riasztások** panel segítségével állapot (aktív vagy lezárt) és a súlyosság (kritikus vagy figyelmeztetés) is támogatja. Az alapértelmezett nézet az összes aktív riasztásokat jeleníti meg. Az összes lezárt riasztások hét nap elteltével törlődnek a rendszerből.

![A keresőablak szűrés kritikus vagy figyelmeztetési állapot](media/azure-stack-monitor-health/image5.png)

A **nézet API** műveletet a REST API-t a lista nézet létrehozásához használt jeleníti meg. Ez a művelet a REST API használatával, amely lekérdezési értesítések segítségével megismerkedhet gyors lehetőséget kínál. Használható az API az automatizálás és az integráció a meglévő datacenter, figyelési, jelentéskészítési és jegykezelési megoldásokat. 

![A nézet API-beállítást, amely tartalmazza a REST API](media/azure-stack-monitor-health/image6.png)

Egy adott riasztást a riasztás részleteinek megtekintése gombra. A riasztás részleteinek megjelenítése minden mezőben, amely a riasztás tartoznak, és gyors navigációs, az érintett összetevőt, és a riasztás forrásához tartozó engedélyezése. A következő riasztás például akkor jelentkezik, ha egy infrastruktúra-szerepkör példánya offline állapotba kerül, vagy nem érhető el.  

![A riasztás részleteit megjelenítő panelen](media/azure-stack-monitor-health/image7.png)

Után az infrastruktúra-szerepkör példánya újra online állapotba kerül, a riasztás automatikusan bezáródik. Számos, de nem minden riasztás automatikusan bezáródik a mögöttes probléma megoldása után. Azt javasoljuk, hogy kiválassza **Bezárás riasztási** szervizelési lépések végrehajtása után. Ha a probléma továbbra is fennáll, az Azure-verem létrehoz egy új riasztást. Ha a probléma megoldásához a riasztás lezárt marad, és nincs szükség további felhasználói beavatkozásra.

## <a name="next-steps"></a>Következő lépések

[Azure-készletben frissítések kezelése](azure-stack-updates.md)

[Azure-készletben régió kezelése](azure-stack-region-management.md)

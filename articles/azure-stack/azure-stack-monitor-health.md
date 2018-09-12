---
title: Figyelni és riasztásokat az Azure Stackben |} A Microsoft Docs
description: Ismerje meg, hogyan figyelni és riasztásokat az Azure Stackben.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2018
ms.author: mabrigg
ms.openlocfilehash: 69ed08e8f6c820790c432bfa25988e038fd0efbd
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379689"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>A figyelő állapotát és a riasztások az Azure Stackben

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek és az Azure Stack fejlesztői készlete*

Az Azure Stack infrastruktúra-figyelési képességek, amelyek segítségével megtekintheti az állapotát és a riasztásokat az adott Azure Stack-régióban tartalmazza. A **régiók kezelése** csempe, a felügyeleti portálon alapértelmezés szerint az alapértelmezett szolgáltatója előfizetés ki van tűzve, sorolja fel az Azure Stack üzembe helyezett minden régióban. A csempe minden régió aktív kritikus és figyelmeztetési riasztások számát jeleníti meg, és a belépési pont állapotával és riasztási funkcióit az Azure Stack.

 ![A régiók kezelése csempe](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Az Azure Stackben health ismertetése

 Állapot és riasztások az egészségügyi erőforrás-szolgáltató felügyeli. Az Azure Stack infrastruktúra-összetevőket az egészségügyi erőforrás-szolgáltató Azure Stack központi telepítése és konfigurálása során regisztrálja. Ez a regisztráció lehetővé teszi az az állapot és riasztások az egyes összetevők. Az Azure Stackben üzemállapota egy egyszerű fogalomra utal. Ha egy összetevő regisztrált példánya esetében riasztások létezik, az összetevő állapotát tükrözi a legrosszabb aktív riasztás súlyossága; figyelmeztető vagy kritikus fontosságú.

## <a name="alert-severity-definition"></a>Riasztás súlyossága definíciója

Csak két súlyossági szinten pedig a kiváltott riasztások az Azure Stack: **figyelmeztetés** és **kritikus**.

- **Figyelmeztetés**  
  Az operátornak kezelheti a figyelmeztető riasztás ütemezett módon. A riasztás általában nem érinti a felhasználó számítási feladatokhoz.

- **Kritikus**  
  Az operátornak olyan sürgősen foglalkoznia kell kritikus riasztást. Ezek a problémák, amelyek jelenleg hatással van, vagy hamarosan befolyásolja az Azure Stack felhasználóinak. 

 
 ## <a name="view-and-manage-component-health-state"></a>Megtekintheti és kezelheti az összetevő állapotát
 
 Az Azure Stack operátorait szerint összetevők állapotadatait a felügyeleti portálon, és a REST API-t és a PowerShell segítségével tekintheti meg.
 
A portálon az állapot megtekintéséhez kattintson a régiót, amelyben meg szeretné tekinteni a a **régiók kezelése** csempére. Infrastruktúra-szerepkörök és az erőforrás-szolgáltatók állapotát tekintheti meg.

![Infrastruktúra-szerepkörök listája](media/azure-stack-monitor-health/image2.png)

Kattinthat, hogy egy erőforrás-szolgáltató vagy az infrastruktúra-szerepkör részletesebb adatok megtekintéséhez.

> [!WARNING]  
> Kattintson egy infrastruktúra-szerepkört, majd a szerepkörpéldány van-e lehetőséget a Start, indítsa újra, vagy -leállítás. Amikor a frissítések alkalmazása az integrált rendszer ne használja ezeket a műveleteket. Ezenkívül tegye **nem** Azure Stack Development Kit környezetben használja az alábbi beállításokat. Tervezték, hogy ezek a beállítások csak az integrált rendszerek környezetekben, ahol az infrastruktúra-szerepkör példányonként egynél több szerepkörhöz. A csomagban (különösen AzS-Xrp01) szerepkörpéldány újraindítása hatására a rendszer instabil. Hibaelhárítási, közzé a problémát a [Azure Stack-fórum](https://aka.ms/azurestackforum).
>
 
## <a name="view-alerts"></a>Riasztások megtekintése

Aktív riasztások az egyes Azure Stack régiók listája érhető el közvetlenül a **régiók kezelése** panelen. Az alapértelmezett beállítás az első csempe a **riasztások** csempe megjeleníti a kritikus fontosságú összegzését, és a régió figyelmeztető riasztások. A riasztások csempén, mint bármely más csempe ezen a panelen a gyors hozzáférés az irányítópulton is rögzíthet.   

![Riasztások csempe, amely akkor vált figyelmeztetési állapotra](media/azure-stack-monitor-health/image3.png)

A felső részén válassza a **riasztások** csempe, lépjen a régió összes aktív riasztás listáját. Ha bármelyik gombra a **kritikus** vagy **figyelmeztetés** sortételt belül a csempét, lépjen a riasztások (kritikus vagy figyelmeztetés) szűrt listáját. 

![Szűrt figyelmeztető riasztások](media/azure-stack-monitor-health/image4.png)
  
A **riasztások** panel képes szűrést, egyszerre (aktív vagy lezárt) állapot és súlyosság (kritikus vagy figyelmeztetés) támogatja. Az alapértelmezett nézet az összes aktív riasztásokat jeleníti meg. Az összes lezárt riasztások hét nap után törlődnek a rendszerből.

![Szűrő ablaktábla szűrés kritikus vagy figyelmeztetési állapot](media/azure-stack-monitor-health/image5.png)

A **nézet API** műveletet a REST API, amely a lista nézet létrehozásához használt jeleníti meg. Ez a művelet itt gyorsan megismerkedhet a lekérdezési riasztások segítségével REST API-val szintaxist. Az automation vagy -integráció a figyelési, jelentéskészítési és megoldások jegykiadás növelje meglévő adatközpontjának is használhat az API-t. 

![A nézet API lehetőséget, amely tartalmazza a REST API](media/azure-stack-monitor-health/image6.png)

Kattintson egy meghatározott riasztást a riasztás részleteinek megtekintéséhez. A riasztás részleteinek megjelenítése társítva a riasztást, és engedélyezze az érintett összetevőt, és a riasztás forrására gyors navigációs mezők. Például a következő riasztás történik, ha egy, az infrastruktúra-szerepkör példányainak offline állapotba kerül, vagy nem érhető el.  

![A riasztás részleteit megjelenítő panelen](media/azure-stack-monitor-health/image7.png)

Miután infrastruktúra szerepkörpéldány újra online állapotba kerül, ez a riasztás automatikusan bezáródik. Számos, de nem minden riasztás automatikusan bezárja, ha a mögöttes probléma megoldódott. Azt javasoljuk, hogy kiválasztotta **riasztás bezárása** javítási lépések végrehajtása után. Ha a probléma tartósan fennáll, az Azure Stack egy új riasztást állít elő. A probléma megoldásához, ha a riasztás lezárt marad, és a szükséges további művelet.

## <a name="next-steps"></a>További lépések

[Az Azure Stack frissítéseinek kezelése](azure-stack-updates.md)

[Régiók kezelése az Azure Stackben](azure-stack-region-management.md)

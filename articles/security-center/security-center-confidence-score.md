---
title: Megbízhatósági pontszám, az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan használható az Azure Security Center konfidencia-pontszám a. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: d2998e9fe264d7919c8f74a98aaf8da6d701c0da
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43133011"
---
# <a name="alert-confidence-score"></a>A riasztásmegbízhatósági pontszám 

Az Azure Security Center biztosít az Azure-ban futtatja az erőforrások között látható-e, és riasztást küld, ha azt észleli, hogy a lehetséges problémákat. A riasztások mennyisége történő külön-külön biztonsági műveletek csapatok számára kihívást jelenthet, és vizsgálja meg, melyik riasztások prioritásának megállapítása szükségessé válik a. A riasztások kivizsgálásának összetett és időigényes lehet, és ennek eredményeképpen egyes riasztások figyelmen kívül hagyja.

A megbízhatósági pontszám a Security Center segítséget nyújtanak a csapat osztályozási és riasztások prioritásának megállapítása. A Security Center automatikusan alkalmazza az iparági gyakorlati ajánlások, intelligens algoritmusok és folyamatok elemzők által használt megállapítására, hogy fenyegetés indokolt-e, és a egy megbízhatósági pontszám formájában jelentéssel bíró elemzéseket biztosít.

## <a name="how-the-confidence-score-is-triggered"></a>Hogyan akkor aktiválódik, a megbízhatósági pontszám

Gyanús folyamat észlelése esetén a virtuális gépeken futó riasztások jönnek létre. A Security Center ellenőrzi, és elemzi ezeket a riasztásokat az Azure-ban futó Windows virtuális gépekre. Hajt végre automatikus ellenőrzés és a speciális algoritmusok használatával több entitást és az adatforrásokat a szervezetben, és az Azure-erőforrások közötti összefüggéseket, és a egy megbízhatósági pontszám, amely azt méri, hogy abban a Security Center jeleníti az, hogy a riasztás valódi, és meg kell vizsgálni.

## <a name="understanding-the-confidence-score"></a>A megbízhatósági pontszám ismertetése

A megbízhatósági pontszám tartomány 1 és 100 közötti, illetve a Security Center azt, hogy a riasztás kell vizsgálni magabiztosan jelöli. Minél nagyobb a pontszám, a sokkal magabiztosabb a munkában a Security Center, hogy a riasztás azt jelzi, hogy valódi kártékony tevékenységek. A megbízhatósági pontszám a leggyakoribb okok, miért érdemes a riasztás érkezett-e a megbízhatósági pontszám listáját tartalmazza. A megbízhatósági pontszám megkönnyíti a biztonsági elemzőknek rangsorolhatja a válasz riasztások, és oldja meg a legjobban, először lenyomásával támadások, végső soron csökkentve a választ támadások és megsértése szükséges időt.

A megbízhatósági pontszám megtekintése:
- A Security Center portálon nyissa meg a biztonsági riasztások panel.
-  A riasztások és incidensek vannak rendszerezve a legmagasabbtól a legalacsonyabb, tehát a sokkal magabiztosabb a munkában a Security Center, hogy a riasztás egy fenyegetést, minél közelebb van az oldal tetejére jelöli. 


 ![Megbízhatósági pontszám][1]

A riasztás a Security Center biztonsággal hozzájáruló adatok megtekintése:
- A biztonsági riasztás panel alatt **megbízhatósági**, megtekintheti a megbízhatósági pontszám hozzájáruló észrevételek és információkhoz juthat a riasztással kapcsolatban. Ezzel biztosítható az nagyobb betekintést kaphat a tevékenységek a riasztást kiváltó jellegét.

 ![Gyanús megbízhatósági pontszám][2]

Használja a Security Center magabiztossági pontszámot rangsorolására riasztási osztályozási a környezetben. A megbízhatósági pontszám, időt és energiát takarít automatikusan a riasztások kivizsgálásának, iparági ajánlott eljárások és intelligens algoritmusok alkalmazásával, és valós milyen fenyegetéseket, és a figyelmet kell meghatározni a virtuális elemző átjáróként.


## <a name="next-steps"></a>További lépések
Ez a cikk ismertetése, hogyan használható a megbízhatósági pontszám riasztási vizsgálat rangsorolása. A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Azure Security Center: gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) –Tanulja meg az Azure-erőforrások állapotfigyelésének módját.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png

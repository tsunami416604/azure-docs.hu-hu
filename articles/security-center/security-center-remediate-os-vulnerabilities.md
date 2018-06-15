---
title: Az Azure Security Center biztonsági beállításokkal szervizelése |} Microsoft Docs
description: Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslat, a "Szervizelése biztonsági beállításokkal."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 477973298d8cc9d99da78e36274933e0bb737c4f
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
ms.locfileid: "28103931"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Az Azure Security Center biztonsági beállításokkal javítása
Az Azure Security Center naponta elemzi az operációs rendszer (operációs rendszer) a virtuális gépek (VM) és a konfiguráció esetén, hogy a virtuális gépek és a számítógépek támadásokkal szemben. A Security Center azt javasolja, hogy a biztonsági rések feloldása, amikor az operációs rendszer konfigurációja nem egyezik meg a javasolt biztonsági konfigurációs szabályokat, és azt javasolja, hogy konfigurációmódosításokat javasol, a biztonsági rések.

Figyelt konfigurációkkal kapcsolatos további információkért tekintse meg a [ajánlott konfiguráció szabályok listája](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Biztonsági konfiguráció értékelések testreszabása, lásd: [testreszabása operációsrendszer-biztonsági beállítások az Azure Security Centerben (előzetes verzió)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
"Biztonsági beállításokkal szervizelése" jelölőnégyzetként jelenik meg a Security Center ajánlást. A javaslat jelenik meg **javaslatok** > **számítási**.

Ez a példa tartalmazza a "Biztonsági beállításokkal szervizelése" javaslat alapján **számítási**.
1. Biztonsági központ, a bal oldali panelen válassza ki **számítási**.  
  A **számítási** ablak nyílik meg.

   ![Biztonsági konfigurációk javítása][1]

2. Válassza ki **biztonsági beállításokkal szervizelése**.  
  A **biztonsági beállításokkal** ablak nyílik meg.

   ![A "Biztonsági beállításokkal" ablak][2]

  Az irányítópult megjelenítése felső részén:

  - **Nem sikerült a szabályok súlyosság szerint**: szabályokat, hogy az operációs rendszer konfigurációja sikertelen volt a virtuális gépek és a számítógépek, súlyosság szerint bontottuk ki teljes száma.
  - **Nem sikerült a szabályok típus szerinti**: szabályokat, hogy az operációs rendszer konfigurációja sikertelen volt a virtuális gépek és a számítógépek, típus szerint bontottuk ki teljes száma.
  - **Nem sikerült a Windows-szabályok**: sikertelen a Windows operációs rendszer beállításai alapján a szabályok száma.
  - **Nem sikerült a Linux szabályok**: a Linux operációs rendszer-konfigurációja által sikertelen a szabályok száma.

  Az irányítópult alsó részén a virtuális gépek és a számítógépek és a hiányzó frissítés súlyosságát összes sikertelen szabályokat sorolja fel. A lista a következő elemeket tartalmazza:

  - **CCEID**: A CCE a szabály egyedi azonosítója. A Security Center egyedi azonosítók hozzárendelése a konfigurációs szabályok Common Configuration Enumeration (CCE) használja.
  - **Név**: sikertelen a szabály nevét.
  - **Szabály típusa**: A *beállításkulcs*, *biztonsági házirend*, vagy *naplórend* szabály típusa.
  - **nem. a virtuális gépek és a számítógépek**: A virtuális gépek és a sikertelen szabályt alkalmazó számítógépek teljes száma.
  - **Súlyossági szabály**: A CCE érték *kritikus*, *fontos*, vagy *figyelmeztetés*.
  - **Állapot**: a javaslat aktuális állapota:

    - **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
    - **A folyamatban lévő**: A javaslat jelenleg vonatkozik az erőforrásokat, és nincs szükség felhasználói műveletek is.
    - **Megoldott**: A javaslat alkalmazták. Ha a probléma megoldódott, a bejegyzés szürkén jelenik meg.

3. Sikertelen szabály részleteinek megtekintéséhez válassza ki a listából.

   ![Sikertelen konfigurációs szabály részletes nézete][3]

   A részletes nézetben megtekinthető a következő információkat:

   - **Név**: a szabály nevét.
   - **CCIED**: A CCE a szabály egyedi azonosítója.
   - **Operációs rendszer verziója**: a virtuális gép vagy a számítógép az operációs rendszer verzióját.
   - **Súlyossági szabály**: A CCE érték *kritikus*, *fontos*, vagy *figyelmeztetés*.
   - **Teljes körű ismertetését**: a szabály leírása.
   - **A biztonsági rés**: biztonsági rés vagy kockázat, ha nincs telepítve a szabály leírását.
   - **Célgyűjtemény**: az üzletmenetre gyakorolt hatás, a szabály alkalmazásakor.
   - **Ellenintézkedés részeként gondosan**: A javítási lépéseket.
   - **Várt érték**: az érték, ha a Security Center elemzi a virtuális gép operációs rendszer konfigurációja a szabállyal szemben vár.
   - **Tényleges érték**: az a virtuális gép operációs rendszer konfigurációja a szabállyal szemben elemzése után visszaadott érték.
   - **Szabály művelet**: A Security Center által a virtuális gép operációs rendszere konfiguráció, a szabállyal szemben az elemzés során használt szabály műveletet.

4. A részletes nézet ablak tetején válassza **keresési**.  
  Keresési munkaterületek rendelkező virtuális gépek és a kijelölt biztonsági konfigurációk eltérés rendelkező számítógépek listáját nyitja meg. A munkaterület kiválasztása csak akkor jelenik meg ha a kiválasztott szabály vonatkozik-e több virtuális gépek különböző munkaterületekkel csatlakoznak.

   ![Felsorolt munkaterületek][4]

5. Jelöljön ki egy munkaterületet.  
  Naplóelemzési keresési lekérdezés megnyitja a munkaterületet, és a biztonsági konfigurációkat eltérés szűrt.

   ![Az operációs rendszer biztonsági rése munkaterület][5]

6. Válasszon ki egy számítógépet a listában.  
  Új keresési eredmény információ csak az adott számítógépen szűrt nyílik meg.

   ![A kijelölt számítógéppel kapcsolatos részletes információk][6]

## <a name="next-steps"></a>További lépések
Ez a cikk bemutatta megvalósításához a Security Center javaslat "Szervizelése biztonsági beállításokkal." Biztonsági konfiguráció értékelések testreszabása, lásd: [testreszabása operációsrendszer-biztonsági beállítások az Azure Security Centerben (előzetes verzió)](security-center-customize-os-security-config.md).

Figyelt konfigurációkkal ellenőrzéséhez tekintse meg [ajánlott konfiguráció szabályok listája](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). A Security Center egyedi azonosítók hozzárendelése a konfigurációs szabályok Common Configuration Enumeration (CCE) használja. További információkért látogasson el a [CCE](https://nvd.nist.gov/cce/index.cfm) hely.

Security Centerrel kapcsolatos további tudnivalókért lásd a következőket:

* Támogatott Windows és Linux virtuális gépek listáját lásd: [az Azure Security Center által támogatott platformok](security-center-os-coverage.md). 
* Az Azure-előfizetések és az erőforráscsoportok biztonsági szabályzatok konfigurálásával kapcsolatban lásd: [biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md). 
* Hogyan javaslatok segítenek az Azure-erőforrások védelme, lásd: [biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md). 
* Az Azure-erőforrások állapotának figyeléséről további tudnivalókért lásd: [biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md). 
* Kezelése és válaszadás a biztonsági riasztásokra kapcsolatban [kezelése és az Azure Security Centerben a biztonsági riasztásokra való reagálásról](security-center-managing-and-responding-alerts.md).
* Figyelheti a partnermegoldások biztonsági állapotát, lásd: [partnermegoldások figyelése az Azure Security Center](security-center-partner-solutions.md).
* A szolgáltatás használatával kapcsolatban gyakran feltett kérdésekre adott válaszokat, lásd: [Azure Security Center: GYIK](security-center-faq.md).
* Az Azure biztonsági és megfelelőségi blogbejegyzéseket, lásd: [Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

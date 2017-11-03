---
title: "Az Azure Security Center az operációs rendszer biztonsági réseivel kapcsolat javítása |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center ajánlás ** szervizelje az operációs rendszer biztonsági rések **."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 39879c22278a55f841e294cda5a89bec2bdf6988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="remediate-os-vulnerabilities-in-azure-security-center"></a>Az Azure Security Center az operációs rendszer biztonsági réseivel kapcsolat javítása
Az Azure Security Center naponta elemzi az operációs rendszer (operációs rendszer) a virtuális gépek (VM) és a konfiguráció esetén, hogy a virtuális gépek és a számítógépek támadásokkal szemben. A Security Center azt javasolja, hogy a biztonsági rések oldható fel, amikor az operációs rendszer konfigurációja nem egyezik meg az ajánlott konfiguráció szabályok és konfigurációmódosításokat javasol, a biztonsági rések javasolja.

> [!NOTE]
> Által figyelt konfigurációkkal kapcsolatban lásd: a [ajánlott konfiguráció szabályok listája](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
>
>

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
Javítsa az operációs rendszer biztonsági rések jelölőnégyzetként jelenik meg a Security Center ajánlást. Ez a javaslat jelenik meg a **javaslatok** és a **számítási**.

Ebben a példában követően áttekintjük a **szervizelje az operációs rendszer biztonsági rés (a Microsoft)** ajánlás szerint **számítási**.
1. Válassza ki **számítási** a Security Center fő menüjében.

   ![Operációs rendszerek sebezhetőségeinek javítása][1]

2. A **számítási**, jelölje be **szervizelje az operációs rendszer biztonsági rés (a Microsoft)**. A **az operációs rendszer biztonsági rés (a Microsoft) eltérő** irányítópult megnyitása.

   ![Operációs rendszerek sebezhetőségeinek javítása][2]

  Az irányítópult tetején biztosítja:

  - A szabályok, amelyek az operációs rendszer konfigurálása nem sikerült a virtuális gép, mind a számítógép súlyosság szerint összesített száma
  - A szabályok típus, amely az operációs rendszer konfigurálása nem sikerült a virtuális gép, mind a számítógép teljes száma.
  - A Windows operációs rendszer azon konfigurációit és a Linux operációs rendszer azon konfigurációinak sikertelen a szabályok száma.

  Az irányítópult alsó összes sikertelen szabályokat sorolja fel a virtuális gépek és a számítógépek és a hiányzó frissítés súlyosságát. A lista a következőket tartalmazza:

  - **CCEID**: CCE a szabály egyedi azonosítója. A Security Center Common Configuration Enumeration (CCE) használja a konfigurációs szabályok egyedi azonosítót hozzárendelni.
  - **NÉV**: sikertelen szabály neve
  - **SZABÁLY típusa**: beállításkulcs, a biztonsági házirend, illetve a naplózási házirend
  - **NO. A virtuális gépek és a számítógépek**: virtuális gépek és a nem kizárni számítógépek teljes száma a következőkre vonatkozik
  - **A szabály SÚLYOSSÁGI**: kritikus, fontos vagy figyelmeztető CCE fontossági értékét
  - **STATE** (Állapot): a javaslat aktuális állapota:

    - **Open** (Nyitott): a javaslattal egyelőre még nem foglalkoztak.
    - **A folyamatban lévő**: A javaslat jelenleg vonatkozik ezeket az erőforrásokat, és nincs szükség felhasználói műveletek is
    - **Resolved** (Megoldott): a javaslat alkalmazása megtörtént. (Ha a probléma megoldódott, a bejegyzés szürkén jelenik meg)

3. A listában, a részletek megtekintéséhez válasszon egy sikertelen szabályt.

   ![Konfigurációs szabályok, amelyek nem tudták][3]

  A következő információ a panel:

  - NÉV – Szabály neve
  - CCIED--CCE egyedi azonosítója a szabályhoz
  - Operációs rendszer verziója – a virtuális gép vagy a számítógép operációs rendszer verziója
  - SZABÁLY SÚLYOSSÁGI--Kritikus, fontos vagy figyelmeztetési CCE súlyossági értéke
  - TELJES leírás--Szabály leírása
  - A biztonsági RÉS--Magyarázat biztonsági rés vagy kockázat, ha a szabály nem alkalmazható
  - CÉLGYŰJTEMÉNY--Szabály alkalmazása Üzletmenetre gyakorolt hatás
  - ELLENINTÉZKEDÉS – javítási lépéseket
  - VÁRT érték--Értéket várt, amikor a Security Center elemzi a virtuális gép operációs rendszer konfigurációja a szabállyal szemben
  - TÉNYLEGES érték--Értéket adott vissza a szabállyal szemben a virtuális gép operációs rendszere konfiguráció elemzése után
  - --A szabály szabály művelet során a virtuális gép operációs rendszer konfigurációja a szabállyal szemben elemzése a Security Center által használt

4. Válassza ki a **keresési** ikonra a felső szalagon. Keresési listát munkaterületek rendelkező virtuális gépek és azokét, amelyekre a kijelölt operációs rendszer biztonsági rése nyílik meg. Ha a kiválasztott szabály vonatkozik-e több virtuális gépek különböző munkaterületekkel csatlakozó munkaterület kiválasztása panel csak látható.

  ![Felsorolt munkaterületek][4]

5. Jelöljön ki egy munkaterületet. A Naplóelemzési keresési lekérdezés nyílik meg az operációs rendszer biztonsági rése szűrt a munkaterületre.

  ![Az operációs rendszer biztonsági rése munkaterület][5]

6. További információ a listából válasszon ki egy számítógépet. Egy másik keresési eredmény információ csak az adott számítógépen szűrt nyílik meg.

  ![Az adott számítógépen szűrve][6]

## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatta megvalósításához a Security Center javaslat "Szervizelése az operációs rendszer biztonsági rések." Tekintse át a konfigurációs szabályok készletét [Itt](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). A Security Center CCE (közös konfigurációs enumerálási) használatával rendelje hozzá a konfigurációs szabályok egyedi azonosítót. Látogasson el a [CCE](https://nvd.nist.gov/cce/index.cfm) webhelyen olvashat.

Security Centerrel kapcsolatos további tudnivalókért lásd a következőket:

* [Az Azure Security Center által támogatott platformok](security-center-os-coverage.md) -támogatott Windows és Linux virtuális gépek listáját tartalmazza.
* [Biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md) -Útmutató: az Azure-előfizetések és -erőforráscsoportok biztonsági szabályzatainak konfigurálásához.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) -megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) -Útmutató: az Azure-erőforrások állapotának figyelésére.
* [Kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) -útmutató kezelése és válaszadás a biztonsági riasztásokra.
* [Partnermegoldások figyelése az Azure Security Center](security-center-partner-solutions.md) -megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center: GYIK](security-center-faq.md) -gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png

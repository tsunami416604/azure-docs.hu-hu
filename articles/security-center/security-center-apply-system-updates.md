---
title: Rendszerfrissítések alkalmazása az Azure Security Centerben | Microsoft dokumentumok
description: Ez a dokumentum bemutatja, hogyan valósíthatja meg az Azure Security Center ajánlásait **A rendszerfrissítések alkalmazása** és **újraindítása után.**
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604553"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Rendszerfrissítések alkalmazása az Azure Security Centerben
Az Azure Security Center naponta figyeli a Windows és Linux rendszerű virtuális gépeket és számítógépeket, az operációs rendszer hiányzó frissítéseit keresve. A Security Center az elérhető biztonsági és fontos frissítések listáját a Windows Update-tól vagy a Windows Server Update Servicestől (WSUS) kéri le attól függően, hogy melyik szolgáltatás van konfigurálva egy Windows rendszerű számítógépen. A Security Center a Linux rendszerek legújabb frissítéseit is ellenőrzi. Ha a virtuális vagy fizikai számítógépről hiányzik egy rendszerfrissítés, akkor a Security Center javaslatot tesz a rendszerfrissítések alkalmazására.

## <a name="implement-the-recommendation"></a>Az ajánlás végrehajtása
A rendszerfrissítések alkalmazása javaslatként jelenik meg a Security Centerben. Ha a virtuális gép vagy a számítógép hiányzik egy rendszerfrissítés, ez a javaslat jelenik meg **a javaslatok és** a **Számítási**csoportban.  A javaslat kiválasztásával megnyílik a **Rendszerfrissítések alkalmazása** irányítópult.

Ebben a példában a **Compute**.

1. Válassza a Biztonsági központ főmenüjének **Számítási parancsát.**

   ![Számítási válasz kiválasztása][1]

2. A **Számítás**csoportban válassza **a Hiányzó rendszerfrissítések lehetőséget.** Megnyílik **a Rendszerfrissítések alkalmazása** irányítópult.

   ![Rendszerfrissítések irányítópultjának alkalmazása][2]

   Az irányítópult teteje a következőket tartalmazza:

    - A rendszerfrissítéseket nem jelző Windows- és Linux-virtuális gépek és számítógépek teljes száma.
    - A virtuális gépeken és a számítógépeken hiányzó kritikus frissítések teljes száma.
    - A virtuális gépeken és a számítógépeken hiányzó biztonsági frissítések teljes száma.

   Az irányítópult alján felsorolja az összes hiányzó frissítéseket a virtuális gépek és a számítógépek, és a hiányzó frissítés súlyosságát.  A lista a következőket tartalmazza:

    - NÉV – A hiányzó frissítés neve.
    - nem. Virtuális gépek & számítógépek: A frissítésből hiányzó virtuális gépek és számítógépek száma összesen.
    - STATE (Állapot): a javaslat aktuális állapota:

      - Nyitott: Az ajánlásmég nem lett megoldva.
      - In Progress (Folyamatban): folyamatban van a javaslat alkalmazása az érintett erőforrásokra, további lépésekre nincs szükség.
      - Resolved (Megoldott): a javaslat alkalmazása megtörtént. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

    - SEVERITY (Súlyosság): az adott javaslat súlyosságát határozza meg:

      - High (Magas): biztonsági rés található egy fontos erőforrásnál (alkalmazás, virtuális gép, hálózati biztonsági csoport) és beavatkozást igényel.
      - Medium (Közepes): nem kritikus vagy kiegészítő lépések elvégzése szükséges egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
      - Low (Alacsony): a biztonsági rést be kell tömni, de a probléma nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

3. A részletek megtekintéséhez jelöljön ki egy hiányzó frissítést a listában.

   ![Hiányzó biztonsági frissítés][3]

4. A felső menüszalagon válassza a **Keresés** ikont.  Az Azure Monitor naplói lekérdezések keresési lekérdezés megnyílik szűrve a számítógépek hiányzik a frissítés.

   ![Az Azure Monitor naplóinak keresése][4]

5. További információért jelöljön ki egy számítógépet a listából. Egy másik keresési eredmény csak az adott számítógépre szűrt adatokkal nyílik meg.

    ![Az Azure Monitor naplóinak keresése][5]

## <a name="next-steps"></a>További lépések
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [A biztonsági javaslatok kezelése az Azure Security Centerben](security-center-recommendations.md) – Ismerje meg, hogyan segíthetnek a javaslatok az Azure-erőforrások védelmében.
* [Biztonsági állapotfigyelés az Azure Security Centerben](security-center-monitoring.md) – Ismerje meg, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – Ismerje meg, hogyan kezelheti és válaszolhat azokra a biztonsági riasztásokra.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések keresése az Azure biztonságáról és megfelelőségéről.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png

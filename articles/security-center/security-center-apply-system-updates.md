---
title: "Alkalmazza a rendszer frissítéseket az Azure Security Centerben |} Microsoft Docs"
description: "Ez a dokumentum azt ismerteti, hogyan valósítja meg az Azure Security Center javaslatait ** alkalmazza a rendszer frissítések ** és ** rendszer frissítések ** után indítsa újra."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 9f7924f3f0975dc32fdf5b8e1b89a1fb8e9b7d57
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="apply-system-updates-in-azure-security-center"></a>Alkalmazza a rendszer frissítéseket az Azure Security Centerben
Az Azure Security Center napi Windows és Linux rendszerű virtuális gépek (VM) és az operációs rendszer frissítéseinek hiányzó számítógépek figyeli. A Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját a Windows Update webhelyről vagy a Windows Server Update Services (WSUS), attól függően, amelyek a Windows rendszerű számítógépeken a szolgáltatás van konfigurálva. Biztonsági központ is ellenőrzi, hogy a legújabb frissítések Linux rendszereken. Ha a virtuális gép vagy a számítógép hiányzik a rendszer frissítését, Security Center javasolni fogja, hogy a rendszer frissítéseinek alkalmazása.

## <a name="implement-the-recommendation"></a>A javaslat megvalósítása
Alkalmazza a rendszer frissítések jelölőnégyzetként jelenik meg a Security Center ajánlást. Ha a virtuális gép vagy a számítógép hiányzik a rendszer frissítését, ez a javaslat jelenik meg a **javaslatok** és a **számítási**.  A javaslat kiválasztásával megnyílik a **rendszer frissítéseinek alkalmazása** irányítópult.

A jelen példában használjuk **számítási**.

1. Válassza ki **számítási** a Security Center fő menüjében.

   ![Válassza ki a számítási][1]

2. A **számítási**, jelölje be **hiányzó rendszerfrissítések**. A **rendszer frissítéseinek alkalmazása** irányítópult megnyitása.

   ![Alkalmazza a rendszer frissítések irányítópult][2]

   Az irányítópult tetején biztosítja:

    - A Windows és a Linux virtuális gépek és a hiányzó rendszerfrissítések számítógépek teljes száma.
    - Kritikus frissítések hiányoznak a virtuális gépek és a számítógépek teljes száma.
    - A virtuális gépek és a számítógépek között hiányzó biztonsági frissítések teljes száma.

  Az irányítópult alsó összes hiányzó frissítések a virtuális gépek és a számítógépek és a hiányzó frissítés súlyosságát sorolja fel.  A lista a következőket tartalmazza:

    - : Név a hiányzó frissítés.
    - NEM. A virtuális gépek és a számítógépek: virtuális gépek és a frissítés hiányzó számítógépek teljes száma.
    - ÁLLAPOT: A javaslat aktuális állapotát:

      - Nyissa meg: A javaslat még nem foglalkoztak még.
      - Folyamatban: A javaslat jelenleg vonatkozik ezeket az erőforrásokat, és nincs szükség felhasználói műveletek is.
      - Megoldott: A javaslat már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

    - SÚLYOSSÁG:, Hogy adott javaslat súlyosságát mutatja be:

      - Magas: A biztonsági rés fontos erőforrásnál (alkalmazás, virtuális géphez vagy hálózati biztonsági csoport) létezik, ezért beavatkozást igényel.
      - Közepes: Nem kritikus vagy kiegészítő lépések elvégzése szükséges egy folyamat befejeződik, vagy egy biztonsági rés megszüntetéséhez.
      - Alacsony: A biztonsági rés kell tömni, de nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

3. Jelöljön ki egy hiányzó frissítés a listából, a részletek megtekintéséhez.

   ![Hiányzó frissítés][3]

4. Válassza ki a **keresési** ikonra a felső szalagon.  Naplóelemzési keresési lekérdezés megnyitja szűrt azokhoz a számítógépekhez, a frissítés hiányzik.

   ![A Naplóelemzési keresése][4]

5. További információ a listából válasszon ki egy számítógépet. Egy másik keresési eredmény információ csak az adott számítógépen szűrt nyílik meg.

    ![A Naplóelemzési keresése][5]

## <a name="reboot-after-system-updates"></a>Rendszerfrissítések utáni újraindítás
1. Lépjen vissza a **javaslatok** panelen. Egy új bejegyzést készítésének rendszerfrissítések hívása után **rendszerfrissítések után indítsa újra**. Ez a bejegyzés lehetővé teszi, hogy újra kell indítania a befejezéséhez a rendszer frissítéseinek alkalmazása a virtuális Gépet.

   ![Rendszerfrissítések utáni újraindítás][6]
2. Válassza ki **rendszerfrissítések után indítsa újra**. Ekkor megnyílik **rendszerfrissítések befejezéséhez függőben egy újraindítás** virtuális gépeket újra kell indítania a alkalmaz rendszer befejezéséhez listáját megjelenítő panelen frissíti a folyamat.

   ![Újraindítása függőben van.][7]

Indítsa újra a virtuális Gépet, a folyamat végrehajtásához Azure-ból.

## <a name="next-steps"></a>Következő lépések
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, miként könnyítik meg a javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának figyelésére.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure biztonsági blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png

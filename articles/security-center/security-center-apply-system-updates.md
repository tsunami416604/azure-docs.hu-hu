---
title: Az Azure Security Center rendszer frissítéseinek alkalmazása |} A Microsoft Docs
description: Ez a dokumentum bemutatja, hogyan valósíthat meg az Azure Security Center javaslatainak **rendszerfrissítések alkalmazása** és **rendszerfrissítések utáni újraindítás**.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 5f6747629139e85f1ae50364da807636937a464a
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44301932"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Az Azure Security Center rendszerfrissítések alkalmazása
Az Azure Security Center figyeli a napi Windows és Linux rendszerű virtuális gépeken (VM) és a hiányzó operációsrendszer-frissítések a számítógépeken. A Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját a Windows Update webhelyről vagy a Windows Server Update Services (WSUS), függően szolgáltatás van konfigurálva, egy Windows-számítógépen. A Security Center is ellenőrzi a legújabb frissítéseket Linux rendszerekben. Ha a virtuális gép vagy a számítógép egy rendszer frissítés hiányzik, a Security Center javasolni fogja, hogy a rendszerfrissítések alkalmazása.

## <a name="implement-the-recommendation"></a>A javaslatok megvalósítása
Alkalmazza a rendszer frissítések jeleníti meg a Security Center egy javaslatot. A virtuális gép vagy a számítógép hiányzik egy rendszer frissítését, ha a javaslat területen látható **javaslatok** és **számítási**.  Az ajánlás kiválasztásával megnyílik az **rendszerfrissítések alkalmazása** irányítópultot.

Ebben a példában használjuk **számítási**.

1. Válassza ki **számítási** a Security Center főmenüjében.

   ![Válassza ki a számítási][1]

2. A **számítási**válassza **hiányzó rendszerfrissítések**. A **rendszerfrissítések alkalmazása** irányítópult nyílik meg.

   ![Alkalmazza a rendszer irányítópultja][2]

   Az irányítópult tetején biztosítja:

    - A Windows és Linux rendszerű virtuális gépek és számítógépek hiányzó rendszerfrissítésekkel teljes száma.
    - A virtuális gépek és számítógépek hiányzó kritikus frissítések teljes száma.
    - A virtuális gépek és számítógépek hiányzó biztonsági frissítések teljes száma.

  Az irányítópult alján felsorolja az összes hiányzó frissítések a virtuális gépek és számítógépek és a hiányzó frissítés súlyosságát.  A lista a következőket tartalmazza:

    - NAME: A hiányzó frissítés neve.
    - NEM. Virtuális gépek és számítógépek: virtuális gépek és számítógépek, amelyekről frissítések hiányoznak a frissítés teljes száma.
    - ÁLLAPOT: A javaslat aktuális állapota:

      - Nyissa meg: Az ajánlás még nem foglalkoztak még.
      - Folyamatban van: A javaslat jelenleg érvényesül ezeket az erőforrásokat, és ilyenkor Ön nem szükséges.
      - Megoldott: A javaslat volt már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

    - SÚLYOSSÁG: Azt ismerteti, hogy az adott javaslat súlyosságát:

      - Nagy: A biztonsági rés fontos erőforrásnál (alkalmazás, virtuális géphez vagy hálózati biztonsági csoport) létezik, és beavatkozást igényel.
      - Közepes: Nem kritikus vagy kiegészítő lépések szükségesek egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
      - Alacsony: A biztonsági rés beavatkozást igényel, de nem igényel azonnali beavatkozást. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

3. A listában a részletek megtekintéséhez válassza ki a hiányzó frissítés.

   ![Hiányzó biztonsági frissítés][3]

4. Válassza ki a **keresési** ikonra a felső szalagon.  A Log Analytics keresési lekérdezés megnyitása szűrt a számítógépekre, a frissítés hiányzik.

   ![A log Analytics-keresés][4]

5. További információ a listából válasszon ki egy számítógépet. Csak az adott számítógépen a szűrt adatokat egy másik keresési eredmény nyílik meg.

    ![A log Analytics-keresés][5]

## <a name="reboot-after-system-updates"></a>Rendszerfrissítések utáni újraindítás
1. Lépjen vissza a **javaslatok** panelen. Egy új bejegyzést jött létre, a rendszerfrissítések nevű alkalmazása után **rendszerfrissítések utáni újraindítás**. Ez a bejegyzés jelzi, hogy újra kell indítania a virtuális Gépet a rendszerfrissítések alkalmazási folyamatának befejezéséhez.

   ![Rendszerfrissítések utáni újraindítás][6]
2. Válassza ki **rendszerfrissítések utáni újraindítás**. Ez megnyitja **rendszerfrissítések befejezéséhez függőben egy újraindítás** virtuális gépeket újra kell indítania a befejezéséhez a rendszer alkalmazása listáját megjelenítő panelen frissíti a folyamat.

   ![Újraindítás függőben][7]

Indítsa újra a virtuális gép az Azure és a folyamat befejezéséhez.

## <a name="next-steps"></a>További lépések
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](security-center-policies.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md) – megtudhatja, hogyan javaslatok az Azure-erőforrások védelme.
* [Biztonsági állapotfigyelés az Azure Security Center](security-center-monitoring.md) – útmutató az Azure-erőforrások állapotának monitorozásához.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Az Azure Security blog](http://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png

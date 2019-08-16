---
title: Rendszerfrissítések alkalmazása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a Azure Security Center javaslatokat a rendszerfrissítések **alkalmazása** és a rendszerfrissítések **újraindítása után**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: v-mohabe
ms.openlocfilehash: 7f9c5b702c0da8de4476508114be911a9d74f78a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531449"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Rendszerfrissítések alkalmazása Azure Security Center
Azure Security Center a napi Windows és Linux rendszerű virtuális gépeket (VM) és számítógépeket figyeli az operációs rendszer frissítéseinek hiányában. Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját Windows Update vagy Windows Server Update Services (WSUS) szolgáltatásból attól függően, hogy melyik szolgáltatást konfigurálja a Windows rendszerű számítógépen. A Security Center a Linux rendszerekben a legújabb frissítéseket is ellenőrzi. Ha a virtuális gép vagy számítógép nem rendelkezik rendszerfrissítéssel, Security Center javasolja, hogy alkalmazza a rendszerfrissítéseket.

## <a name="implement-the-recommendation"></a>A javaslat implementálása
A rendszerfrissítések alkalmazása a Security Center javaslata. Ha a virtuális gép vagy számítógép nem rendelkezik rendszerfrissítéssel, akkor ez a javaslat a **javaslatok** és a **számítás**területen jelenik meg.  Az ajánlás kiválasztásával megnyílik a rendszerfrissítések **alkalmazása** irányítópult.

Ebben a példában a **számítást**fogjuk használni.

1. A Security Center Főmenüben válassza a **számítás** lehetőséget.

   ![Számítás kiválasztása][1]

2. A **számítás**területen válassza a **hiányzó**rendszerfrissítések elemet. Megnyílik a rendszerfrissítések **alkalmazása** irányítópult.

   ![Rendszerfrissítések irányítópultjának alkalmazása][2]

   Az irányítópult tetején a következőket nyújtja:

    - A Windows-és Linux-alapú virtuális gépek és számítógépek teljes száma rendszerfrissítéssel.
    - A virtuális gépeken és számítógépeken hiányzó kritikus frissítések teljes száma.
    - A virtuális gépeken és számítógépeken hiányzó biztonsági frissítések teljes száma.

   Az irányítópult alján a virtuális gépek és számítógépek összes hiányzó frissítése szerepel, valamint a hiányzó frissítés súlyossága.  A lista a következőket tartalmazza:

    - NÉV: A hiányzó frissítés neve.
    - NEM. Virtuális gépek & számítógépek: A frissítésből hiányzó virtuális gépek és számítógépek száma összesen.
    - ÁLLAPOT A javaslat jelenlegi állapota:

      - Nyissa meg A javaslat még nem lett megcímezve.
      - Folyamatban: A javaslatot jelenleg az adott erőforrásokra alkalmazza a rendszer, és nincs szükség beavatkozásra.
      - Oldható Az ajánlás már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

    - SÚLYOSSÁG Az adott javaslat súlyosságát írja le:

      - Magas A biztonsági rés egy értelmes erőforrással (alkalmazás, virtuális gép vagy hálózati biztonsági csoport) van, és figyelmet igényel.
      - Közepes A folyamat elvégzéséhez vagy a biztonsági rések megszüntetéséhez nem kritikus vagy további lépések szükségesek.
      - Alacsony A biztonsági rést meg kell oldani, de nincs szükség azonnali beavatkozásra. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

3. A részletek megtekintéséhez válasszon ki egy hiányzó frissítést a listából.

   ![Hiányzó biztonsági frissítés][3]

4. Kattintson a **Keresés** ikonra a felső menüszalagon.  A rendszer megnyit egy Azure Monitor naplókat a keresési lekérdezésben, amelyből hiányzik a frissítés.

   ![Azure Monitor naplók keresése][4]

5. További információért válasszon egy számítógépet a listából. Megnyílik egy másik keresési eredmény, amely csak az adott számítógéphez szűrt adatokat.

    ![Azure Monitor naplók keresése][5]

## <a name="next-steps"></a>További lépések
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Megtudhatja, hogyan figyelheti a partnermegoldások biztonsági állapotát.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png

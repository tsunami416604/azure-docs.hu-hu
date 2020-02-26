---
title: Rendszerfrissítések alkalmazása a Azure Security Centerban | Microsoft Docs
description: Ebből a dokumentumból megtudhatja, hogyan implementálhatja a Azure Security Center javaslatokat a rendszerfrissítések **alkalmazása** és a **rendszerfrissítések újraindítása után**.
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
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604553"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Rendszerfrissítések alkalmazása Azure Security Center
Azure Security Center a napi Windows és Linux rendszerű virtuális gépeket (VM) és számítógépeket figyeli az operációs rendszer frissítéseinek hiányában. Security Center lekéri az elérhető biztonsági és kritikus frissítések listáját Windows Update vagy Windows Server Update Services (WSUS) szolgáltatásból attól függően, hogy melyik szolgáltatást konfigurálja a Windows rendszerű számítógépen. A Security Center a Linux rendszerekben a legújabb frissítéseket is ellenőrzi. Ha a virtuális gép vagy számítógép nem rendelkezik rendszerfrissítéssel, Security Center javasolja, hogy alkalmazza a rendszerfrissítéseket.

## <a name="implement-the-recommendation"></a>A javaslat implementálása
A rendszerfrissítések alkalmazása a Security Center javaslata. Ha a virtuális gép vagy számítógép nem rendelkezik rendszerfrissítéssel, akkor ez a javaslat a **javaslatok** és a **számítás**területen jelenik meg.  Az ajánlás kiválasztásával megnyílik a **rendszerfrissítések alkalmazása** irányítópult.

Ebben a példában a **számítást**fogjuk használni.

1. A Security Center Főmenüben válassza a **számítás** lehetőséget.

   ![Számítás kiválasztása][1]

2. A **számítás**területen válassza a **hiányzó rendszerfrissítések**elemet. Megnyílik a **rendszerfrissítések alkalmazása** irányítópult.

   ![Rendszerfrissítések irányítópultjának alkalmazása][2]

   Az irányítópult tetején a következőket nyújtja:

    - A Windows-és Linux-alapú virtuális gépek és számítógépek teljes száma rendszerfrissítéssel.
    - A virtuális gépeken és számítógépeken hiányzó kritikus frissítések teljes száma.
    - A virtuális gépeken és számítógépeken hiányzó biztonsági frissítések teljes száma.

   Az irányítópult alján a virtuális gépek és számítógépek összes hiányzó frissítése szerepel, valamint a hiányzó frissítés súlyossága.  A lista a következőket tartalmazza:

    - NÉV: a hiányzó frissítés neve.
    - nem. Virtuális gépek & számítógépek: azon virtuális gépek és számítógépek teljes száma, amelyek esetében ez a frissítés hiányzik.
    - ÁLLAPOT: a javaslat jelenlegi állapota:

      - Megnyitás: a javaslat még nincs megcímezve.
      - Folyamatban: a javaslat jelenleg az adott erőforrásokra van alkalmazva, és nincs szükség beavatkozásra.
      - Megoldott: a javaslat már befejeződött. (A probléma megoldása után a bejegyzés halványan jelenik meg.)

    - SÚLYOSSÁG: az adott javaslat súlyosságát írja le:

      - High (magas): egy értelmes erőforrás (alkalmazás, virtuális gép vagy hálózati biztonsági csoport) biztonsági rése van, és figyelmet igényel.
      - Közepes: nem kritikus vagy további lépések szükségesek egy folyamat befejezéséhez vagy egy biztonsági rés megszüntetéséhez.
      - Alacsony: A biztonsági rést orvosolni kell, de nincs szükség azonnali beavatkozásra. (Az alacsony súlyosságú javaslatok alapértelmezés szerint nem láthatók, de a szűrővel bekapcsolhatja megjelenítésüket.)

3. A részletek megtekintéséhez válasszon ki egy hiányzó frissítést a listából.

   ![Hiányzó biztonsági frissítés][3]

4. Kattintson a **Keresés** ikonra a felső menüszalagon.  A rendszer megnyit egy Azure Monitor naplókat a keresési lekérdezésben, amelyből hiányzik a frissítés.

   ![Azure Monitor naplók keresése][4]

5. További információért válasszon egy számítógépet a listából. Megnyílik egy másik keresési eredmény, amely csak az adott számítógéphez szűrt adatokat.

    ![Azure Monitor naplók keresése][5]

## <a name="next-steps"></a>Következő lépések
A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági javaslatok kezelése Azure Security Centerban](security-center-recommendations.md) – megtudhatja, hogyan segítheti az ajánlásokat az Azure-erőforrások védelmében.
* [Biztonsági állapot figyelése Azure Security Centerban](security-center-monitoring.md) – megtudhatja, hogyan figyelheti az Azure-erőforrások állapotát.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Partnermegoldások figyelése az Azure Security Centerrel](security-center-partner-solutions.md) – Ez a cikk ismerteti a partnermegoldások biztonsági állapotának figyelését.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – blogbejegyzések az Azure biztonsági és megfelelőségi funkcióiról.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png

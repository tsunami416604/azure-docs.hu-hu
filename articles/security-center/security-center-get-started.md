---
title: "Az Azure Security Center használatába |} Microsoft Docs"
description: "Ez a cikk segíti az Azure Security Center használata során megtett első lépéseket, miközben végigvezeti a biztonságfigyelés és a biztonsági szabályzat kezelésének összetevőin, valamint átvezeti a további lépésekre."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: terrylan
ms.openlocfilehash: c28f92af96f31d1c386cf072f83fc142b9a7f588
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-quick-start-guide"></a>Bevezetés az Azure Security Center használatába
Ez a cikk segíti az Azure Security Center használata során megtett első lépéseket, miközben végigvezeti a biztonságfigyelés és a biztonsági szabályzat kezelésének összetevőin.

## <a name="prerequisites"></a>Előfeltételek
A Security Center használatához Microsoft Azure-előfizetéssel kell rendelkeznie. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes fiókkal](https://azure.microsoft.com/pricing/free-trial/).

A Security Center ingyenes szint automatikusan engedélyezve van minden Azure-előfizetés, és biztonsági házirend, folyamatos biztonsági értékelést és végrehajthatóként biztonsági javaslatok az Azure-erőforrások védelméhez.

A Security Center az [Azure Portalról](https://azure.microsoft.com/features/azure-portal/) érhető el. További információt az Azure Portalról a [portál dokumentációjában](https://azure.microsoft.com/documentation/services/azure-portal/) talál.

## <a name="permissions"></a>Engedélyek
A biztonsági központban csak akkor jelenik meg egy erőforrás tulajdonos, közreműködő vagy olvasó szerepkört az előfizetés vagy az erőforráscsoportot, amelyhez tartozik egy erőforrás hozzárendelése esetén kapcsolatos adatokat. Lásd: [engedélyek az Azure Security Centerben](security-center-permissions.md) tudhat meg többet a szerepkörök és a Security Center engedélyezett műveletek.

## <a name="data-collection"></a>Adatgyűjtés
A Security Center az Azure virtuális gépek (VM) és a nem Azure számítógépek számára figyeli, hogy a biztonsági réseket és a fenyegetések adatait gyűjti. Adatgyűjtés használata a Microsoft Monitoring Agent, különböző biztonsági konfigurációkat és Eseménynapló beolvassa a számítógépről, és másolja az adatokat a munkaterületre elemzés céljából. A Security Center kiosztja a Microsoft Monitoring Agent összes meglévő támogatott Azure virtuális gépek és bármely új gazdarendszerhez jönnek létre. Lásd: [az adatgyűjtést](security-center-enable-data-collection.md) tudhat meg többet az adatgyűjtés működése.

Automatikus kiépítés erősen ajánlott, és az előfizetések a Security Center a Standard szint szükséges. Az automatikus létesítési korlátok biztonsági megfigyelés letiltása az erőforrások.

Lásd: [Security Center árképzési](security-center-pricing.md) tudhat meg többet a szabad és a Standard tarifacsomag szükséges.

A következő lépések azt mutatják be, hogyan férhet hozzá és használhatja a Security Center összetevőit.

> [!NOTE]
> Ez a cikk egy üzembe helyezést szemléltető példa segítségével mutatja be a szolgáltatást. A cikk nem tartalmaz lépésenkénti útmutatót.
>
>

## <a name="access-security-center"></a>A Security Center elérése
Hajtsa végre az alábbi lépéseket a portálon a Security Center eléréséhez:

1. A **Microsoft Azure** menüben válassza a **Security Center** elemet.

   ![Az Azure menü][1]
2. Ha először használja a Security Centert, akkor az **Üdvözöljük** panel fog megnyílni. Válassza ki **indítsa el a Security Center** megnyitásához **Security Center**.
   ![Üdvözlőképernyő][10]
3. Indítsa el a Security Center üdvözlő paneljén, vagy a Microsoft Azure menüben válassza ki a Security Center után **Security Center** nyílik meg. A **Security Center** panel jövőbeli könnyebb elérésének érdekében válassza a **Panel rögzítése az irányítópulton** parancsot (jobb felül).
   ![Panel rögzítése az irányítópulton lehetőség][2]

## <a name="use-security-center"></a>A Security Center használata
Biztonsági szabályzatokat állíthat be Azure-előfizetéseihez és -erőforráscsoportjaihoz. Állítsunk be egy biztonsági szabályzatot az előfizetéséhez:

1. Válassza ki a Security Center főmenü **biztonsági házirend**.
2. A **Security Center - biztonsági házirend**, válasszon egy előfizetést.
3. A **biztonsági házirend - adatgyűjtés**, **automatikus kiépítés** be van kapcsolva. A Security Center kiosztja a Microsoft Monitoring Agent összes meglévő támogatott Azure virtuális gépek és bármely új gazdarendszerhez jönnek létre.

    ![Biztonsági házirend][12]

4. Az a **biztonsági házirend** panelen válassza ki a házirend **biztonsági házirend**.

     ![Biztonsági házirend][11]

5. A **javaslatok megjelenítése**, kapcsolja be a javaslatok, hogy meg szeretné tekinteni a biztonsági házirend részeként. Példák:

   * Beállítás **rendszerfrissítések** való **a** vizsgálatok az összes támogatott virtuális gépek hiányzik az operációs rendszer frissítése érdekében.
   * Beállítás **az operációs rendszer biztonsági rések** való **a** vizsgálatok az összes támogatott virtuális gépek olyan operációsrendszer-beállítások, amelyek a virtuális Gépet sebezhetőbbé tehetik azonosításához.

6. Kattintson a **Mentés** gombra.

### <a name="view-recommendations"></a>Javaslatok megtekintése
1. Lépjen vissza a **Security Center** panelhez, és válassza a **Javaslatok** csempét. A Security Center rendszeresen elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosította a lehetséges biztonsági sebezhetőségeket, megjeleníti a javaslatokat a **Javaslatok** panelen.
   ![Javaslatok az Azure Security Centerben][5]
2. Válasszon ki egy javaslatot a **Javaslatok** panelen a további információk megtekintéséhez és/vagy a probléma megoldásához szükséges műveletek végrehajtásához.

### <a name="view-the-security-state-of-your-resources"></a>Az erőforrások biztonsági állapotának megtekintése
1. Lépjen vissza a **Security Center** panelhez. A **megelőzési** irányítópult a szakasz biztonsági állapotának virtuális gépekhez, hálózati, adatokhoz és alkalmazásokhoz.
2. Válassza ki **számítási** további információk megjelenítéséhez. A **számítási** panel megnyitása megjelenítő három lappal:

  - **Áttekintés** -tartalmaz figyelési és a virtuális gépekre vonatkozó javaslatok.
  - **Virtuális gépek és a számítógépek** -sorolja fel a virtuális gépek és a számítógép aktuális biztonsági állapotát.
  - **A felhőalapú szolgáltatások** -Security Center által figyelt webes és feldolgozói szerepkörök listája.

    ![Számítási biztonsági állapota][6]

3. Az a **áttekintése** lapra, válassza ki a javaslat a további információk megtekintéséhez és/vagy a szükséges beállítások konfigurálásához.
4. Az a **virtuális gépek és a számítógépek** lapján válasszon ki egy erőforrástípust a további részletek megtekintéséhez.

### <a name="view-security-alerts"></a>Biztonsági riasztások megtekintése
1. Lépjen vissza a **Security Center** panelhez, és válassza a **Biztonsági riasztások** csempét. A **Biztonsági riasztások** panel megnyitja és megjeleníti a riasztások listáját. A Security Center biztonsági naplókhoz és a hálózati tevékenységhez kapcsolódó elemzése hozza létre ezeket a riasztásokat. Továbbá az integrált partnermegoldásoktól érkező riasztásokat is tartalmazza.
   ![Biztonsági riasztások az Azure Security Centerben][7]

2. Jelölje ki a kívánt riasztást a további információk megjelenítéséhez. Ebben a példában most válasszon **módosította a bináris rendszer memóriakép szűrő felderített**. Ez a riasztással kapcsolatos további információkat tartalmazó paneleket nyit meg.
   ![Biztonsági riasztások részletei az Azure Security Centerben][8]

### <a name="view-the-health-of-your-partner-solutions"></a>A partnermegoldások állapotának megtekintése
1. Lépjen vissza a **Security Center** panelhez. A **biztonsági megoldások** csempe lehetővé teszi, hogy egy pillanat alatt figyelése az Azure-előfizetésében integrált partnermegoldások biztonsági állapotát.
2. Válassza ki a **biztonsági megoldások** csempére. A panel megnyitja és megjeleníti a Security Centerhez csatlakoztatott partnermegoldások listáját.
   ![Partnermegoldások][9]
3. Válasszon ki egy partneri megoldást. A panel megnyitja és megjeleníti a partnermegoldás állapotát és a megoldáshoz társított erőforrásokat. A **Megoldáskonzol** kiválasztásával megnyílik az adott megoldáshoz tartozó partnerfelügyeleti felület.

   ![Partneri megoldások][13]

## <a name="next-steps"></a>Következő lépések
Ez a cikk bemutatja a Security Center biztonságfigyelését és a biztonsági szabályzat kezelésének összetevőit. Most, hogy már ismeri a Security Centert, próbálja ki a következő lépéseket:

* Az Azure-előfizetéshez tartozó biztonsági házirend konfigurálása című [biztonsági szabályzatok beállítása az Azure Security Center](security-center-policies.md).
* A Security Center a javaslatok használatával alakítsa ki az Azure-erőforrások védelme című [biztonsági javaslatok kezelése az Azure Security Center](security-center-recommendations.md).
* Tekintse át és az aktuális biztonsági riasztások kezelése című [kezelése és az Azure Security Centerben a biztonsági riasztásokra való reagálásról](security-center-managing-and-responding-alerts.md).
- További tudnivalók a partnerek számára, hogy az általános biztonsági javítása, lásd: integrálása [partnerek és a megoldások integrációs](security-center-partner-integration.md).
- Ismerje meg, hogyan kezeli az adatokat, és a Security Center biztosítani, lásd: [az Azure Security Center adatainak biztonsági](security-center-data-security.md).
* Tudjon meg többet a [speciális fenyegetésészlelési funkciókról](security-center-detection-capabilities.md), amelyek a Security Center [standard szintű csomagjában](security-center-pricing.md) érhetők el. A Standard szint 60 napig ingyenesen használható.
* Ha kérdése van a Security Center használatával kapcsolatban, tekintse meg az [Azure Security Center – gyakori kérdések](security-center-faq.md) című cikket.

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png
[11]: ./media/security-center-get-started/show-recommendations-for.png
[12]: ./media/security-center-get-started/automatic-provisioning.png
[13]: ./media/security-center-get-started/partner-solutions-detail.png

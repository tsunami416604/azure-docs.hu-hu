---
title: Alkalmazásproxy-alkalmazáshoz nem található munkacsoport-csoport
description: Problémák kezelése, ha előfordulhat, ha nincs működő összekötő az alkalmazás az Azure AD alkalmazásproxyval az alkalmazás összekötő csoportjában
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96ab0d1b3bbab9c97c04da4f918f3aaa2f1d07e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275621"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Alkalmazásproxy-alkalmazáshoz nem található munkacsoport-összekötő csoport

Ez a cikk segít megoldani a gyakori problémákat, amelyek akkor szembesülnek, ha nincs összekötő az Azure Active Directoryba integrált alkalmazásproxy-alkalmazáshoz.

## <a name="overview-of-steps"></a>Lépések áttekintése
Ha az alkalmazás összekötőcsoportjában nincs működő összekötő, a probléma többféleképpen is megoldható:

-   Ha nincs összekötő a csoportban, a következőket teheti:

    -   Új összekötő letöltése a megfelelő kiszolgálón, és hozzárendelése ehhez a csoporthoz

    -   Aktív összekötő áthelyezése a csoportba

-   Ha nincs aktív összekötő a csoportban, a következőket teheti:

    -   Az összekötő inaktív állapotának azonosítása és megoldása

    -   Aktív összekötő áthelyezése a csoportba

A probléma megoldásához nyissa meg az alkalmazás "Alkalmazásproxy" menüjét, és tekintse meg az Összekötőcsoport figyelmeztető üzenetet. Ha nincsenek összekötők a csoportban, a figyelmeztető üzenet azt adja meg, hogy a csoportnak legalább egy összekötőre van szüksége. Ha nincs aktív összekötők, a figyelmeztető üzenet ismerteti ezt. Gyakori, hogy inaktív összekötők. 

   ![Összekötőcsoport kiválasztása az Azure Portalon](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Az egyes lehetőségekről az alábbi szakaszban olvashat részletesen. Az utasítások feltételezik, hogy az összekötő felügyeleti lapról indul. Ha a fenti hibaüzenetet nézi, a figyelmeztető üzenetre kattintva ezt az oldalt használhatja. A lap hoz is elérhetővé az **Azure Active Directory,** kattintson a **vállalati alkalmazások**, majd **az alkalmazásproxy.**

   ![Összekötőcsoport-kezelés az Azure Portalon](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Új összekötő letöltése

Új összekötő letöltéséhez használja az oldal tetején található "Összekötő letöltése" gombot.

Telepítse a csatlakozót egy olyan gépre, amely közvetlen rálátással rendelkezik a háttéralkalmazásra. Az összekötő általában ugyanarra a kiszolgálóra van telepítve, mint az alkalmazás. A letöltés után az összekötőnek meg kell jelennie ebben a menüben. kattintson az összekötőre, és az "Összekötőcsoport" legördülő menüsegítségével győződjön meg arról, hogy a megfelelő csoporthoz tartozik. Mentse a módosításokat.

   ![Az összekötő letöltése az Azure Portalról](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Aktív összekötő áthelyezése

Ha van egy aktív összekötő, amely a csoporthoz tartozik, és a cél háttéralkalmazás látótávolsága, áthelyezheti az összekötőa hozzárendelt csoportba. Ehhez kattintson az összekötőre. Az "Összekötőcsoport" mezőben a legördülő menüben válassza ki a megfelelő csoportot, és kattintson a Mentés gombra.

## <a name="resolve-an-inactive-connector"></a>Inaktív összekötő feloldása

Ha a csoportban csak az összekötők inaktívak, akkor valószínűleg olyan gépen vannak, amelyen nincs feloldva az összes szükséges port.

A probléma kivizsgálásával kapcsolatos részleteket a portok hibaelhárítási dokumentumban találja.

## <a name="next-steps"></a>További lépések
[Az Azure AD alkalmazásproxy-összekötők megismerése](application-proxy-connectors.md)



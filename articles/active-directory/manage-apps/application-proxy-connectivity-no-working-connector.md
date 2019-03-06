---
title: Nem működő összekötőcsoport található Application Proxy-alkalmazások |} A Microsoft Docs
description: Nem működik az olyan Összekötőcsoportot, az alkalmazás az Azure AD-alkalmazásproxy-összekötő esetén esetleg felmerülő problémák megoldása
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96608853d2eec3cf90f1425992d755cbe6c2e8dd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437904"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nem található egy alkalmazásproxy-alkalmazáshoz működő összekötőcsoport

Ez a cikk segít az szembesülnek, ha nem, akkor egy összekötőt, az Application Proxy-alkalmazások az Azure Active Directoryval integrált észlelt leggyakoribb hibák elhárításához.

## <a name="overview-of-steps"></a>Lépések áttekintése
Ha nem működik olyan Összekötőcsoportot, az alkalmazás-összekötőjét, néhány módja a probléma megoldásához:

-   Ha a csoportban nincsenek összekötők, akkor a következőket teheti:

    -   A jobb oldalon, a helyi kiszolgálón lévő új összekötő letöltéséhez, és rendelje hozzá a csoporthoz

    -   Egy aktív összekötőt áthelyezni a csoportot

-   Ha nincs aktív összekötő a csoportban található, akkor a következőket teheti:

    -   Az összekötő nem aktív az OK azonosítása és megoldása

    -   Egy aktív összekötőt áthelyezni a csoportot

Felderíthesse a problémát, az alkalmazás a "Application Proxy" menü megnyitásához, és tekintse meg a Összekötőcsoport figyelmeztető üzenetet. Ha a csoportban nincsenek összekötők, a figyelmeztető üzenetet adja meg a csoport kell legalább egy összekötő. Ha nincs aktív összekötő, a figyelmeztető üzenetet, amely ismerteti. Célszerű a gyakori, hogy inaktív összekötőket tartalmaz. 

   ![Az Azure Portalon összekötő csoport kijelölése](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

A felsorolt lehetőségek részletes ismertetéséért tekintse meg a megfelelő szakaszt. Az utasítások feltételezik, hogy az összekötő-felügyeleti oldal kezdve. Ha a fenti hibaüzenetet helyzet, ezen a lapon léphet a figyelmeztető üzenetet kattintva. Letöltheti az oldalra a **Azure Active Directory**, kattintson a **vállalati alkalmazások**, majd **alkalmazásproxy.**

   ![Összekötő eszközcsoport-kezelés az Azure Portalon](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Új összekötő letöltéséhez

Új összekötő letöltéséhez, használja a "-összekötő letöltése" gombra a lap tetején.

Telepítse az egy gépen a közvetlen üzemel, a háttéralkalmazás. Általában az összekötő telepítve van az alkalmazás ugyanazon a kiszolgálón. Az összekötő a letöltés után meg kell jelennie ebből a menüből. Kattintson az összekötőt, és a "Összekötő csoport" legördülő menü használatával ellenőrizze, hogy a megfelelő csoporthoz tartozik. A módosítás mentéséhez.

   ![Töltse le az összekötő az Azure Portalról](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Helyezze át egy aktív összekötő

Ha egy aktív összekötőt, a csoporthoz kell tartoznia, és a háttérrendszer célalkalmazásnak üzemel, továbbléphet az összekötő a hozzárendelt csoportba. Ehhez kattintson arra az összekötőre. A "Összekötőcsoport" mezőjében használatával a listából válassza ki a megfelelő csoportba, és kattintson a Mentés gombra.

## <a name="resolve-an-inactive-connector"></a>Az inaktív csatlakozó feloldása

Ha a csoportban csak az összekötők inaktív, valószínűleg olyan gépen, amely nem rendelkezik a szükséges portokat feloldva.

a portok hibaelhárítás dokumentum részletes tekintse meg a probléma kivizsgálása.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)



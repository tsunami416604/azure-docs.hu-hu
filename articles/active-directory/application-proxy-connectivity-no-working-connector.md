---
title: Nincs munkacsoport összekötő található az alkalmazásproxy-alkalmazás |} Microsoft Docs
description: Ha nem működik egy összekötő csoportot az alkalmazásba az Azure AD-alkalmazásproxy összekötőjét esetleg felmerülő problémák megoldása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: f6baea8c2702fc2a86c75d32c7b4ca958fd913b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591745"
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nem található az alkalmazásproxy alkalmazáshoz működő összekötő csoport

Ez a cikk segít a nincs észlelte az alkalmazásproxy-alkalmazás az Azure Active Directoryval integrált összekötő tapasztalt gyakori problémák megoldásában.

## <a name="overview-of-steps"></a>A lépések – áttekintés
Ha nem működő alkalmazás összekötő csoportban összekötő, néhány módon a probléma megoldása:

-   Ha nincs összekötők a csoportban található, akkor a következőket teheti:

    -   A megfelelő helyszíni kiszolgálón új összekötő letöltése, és rendelje hozzá ehhez a csoporthoz

    -   Az aktív csatlakozó áthelyezi a csoport

-   Ha nincs aktív összekötők a csoportban található, akkor a következőket teheti:

    -   Az összekötő nem aktív ok azonosítása és elhárítása

    -   Az aktív csatlakozó áthelyezi a csoport

Mérje fel, a probléma, az alkalmazásban a "Alkalmazásproxy" menü megnyitásához, és nézze meg az összekötő csoport figyelmeztető üzenet. Ha a csoport nincs összekötők, a figyelmeztető üzenetet kell legalább egy összekötő a csoportot határozza meg. Ha még nem aktív összekötők, a figyelmeztető üzenet, amely ismerteti. Gyakori, hogy olyan inaktív összekötők. 

   ![Azure-portálon összekötő csoport kijelölése](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Ezen lehetőségek a részletekért lásd: az alábbi megfelelő szakaszához. Az utasítások azt feltételezik, hogy az összekötő felügyeleti oldalról indítja. Ha a fenti hibaüzenet nézi, ezen a lapon elvégezheti a figyelmeztető üzenet kattintva. Letöltheti az oldalra a **Azure Active Directory**a gombra, majd **vállalati alkalmazások**, majd **alkalmazásproxy.**

   ![Összekötő csoportok kezelése az Azure portálon](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Új összekötő letöltése

Új összekötő letöltése, használja a "-összekötő letöltése" gombra az oldal tetején.

Telepítse az összekötőt a háttéralkalmazás közvetlen sor a láthatáron virtuális gép. Általában az összekötő telepítve van az alkalmazás ugyanazon a kiszolgálón. Az összekötő a letöltés után meg kell jelennie az ebben a menüben. Kattintson az összekötő, és a "összekötő csoport" legördülő győződjön meg arról, hogy a megfelelő csoporthoz tartozik. A módosítás mentéséhez.

   ![Az összekötő letöltése Azure portálon](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Helyezze át egy aktív összekötő

Ha az aktív csatlakozó, a csoporthoz kell tartoznia, és a háttér célalkalmazásnak történő sor a láthatáron, áthelyezheti az összekötő a hozzárendelt csoportjához. Ehhez kattintson az összekötőt. "Összekötő csoport" mezőben használja a legördülő listán válassza ki a megfelelő csoportot, és kattintson a Mentés gombra.

## <a name="resolve-an-inactive-connector"></a>Hárítsa el az inaktív csatlakozó

Ha a csoportban csak összekötők nem működnek, valószínűleg olyan gépen, amely nem rendelkezik a szükséges portok feloldva.

a portok kapcsolatos problémák elhárítása a dokumentum a részletekért tekintse meg a probléma kivizsgálása.

## <a name="next-steps"></a>További lépések
[Az Azure AD-alkalmazásproxy összekötők ismertetése](manage-apps/application-proxy-connectors.md)



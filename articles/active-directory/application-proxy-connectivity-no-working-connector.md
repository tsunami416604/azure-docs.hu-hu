---
title: "Nincs munkacsoport összekötő található az alkalmazásproxy-alkalmazás |} Microsoft Docs"
description: "Ha nem működik egy összekötő csoportot az alkalmazásba az Azure AD-alkalmazásproxy összekötőjét esetleg felmerülő problémák megoldása"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 4945958deedc8a1d9989ff901192c03a5363b4dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="no-working-connector-group-found-for-an-application-proxy-application"></a>Nem található az alkalmazásproxy alkalmazáshoz működő összekötő csoport

Ez a cikk segít nincs észlelhető a következőnél: az alkalmazásproxy alkalmazás összekötő tapasztalt gyakori problémák megoldásához Azure Active Directory integrált része.

## <a name="overview-of-steps"></a>A lépések – áttekintés
Ha nem működő alkalmazás összekötő csoportban összekötő, néhány módon a probléma megoldása:

-   Ha nincs összekötők a csoportban található, akkor a következőket teheti:

    -   A megfelelő helyszíni kiszolgálón új összekötő letöltése, és rendelje hozzá ehhez a csoporthoz

    -   Az aktív csatlakozó áthelyezi a csoport

-   Ha nincs aktív összekötők a csoportban található, akkor a következőket teheti:

    -   Az összekötő nem aktív ok azonosítása és elhárítása

    -   Az aktív csatlakozó áthelyezi a csoport

Tudjuk, hogy ezek közül a probléma, az alkalmazásban a "Alkalmazásproxy" menü megnyitásához, és nézze meg az összekötő csoport figyelmeztető üzenet. Azt adja meg, hogy a csoport kell legalább egy összekötő (rendelkezik a csoport nincs), vagy nem aktív összekötők rendelkezik (bár valószínűleg inaktív összekötők).

   ![Az Azure portálon összekötő csoport kijelölése](./media/application-proxy-connectivity-no-working-connector/no-active-connector.png)

Ezen lehetőségek a részletekért lásd: az alábbi megfelelő szakaszához. Ezek mindegyikének azt feltételezi, hogy az összekötő felügyeleti oldalról indítja. Ha a fenti hibaüzenet nézi, ezen a lapon elvégezheti a figyelmeztető üzenet kattintva. Ellenkező esetben ez található címen **Azure Active Directory**a gombra, majd **vállalati alkalmazások**, majd **alkalmazásproxy.**

   ![Összekötő csoportok kezelése az Azure portálon](./media/application-proxy-connectivity-no-working-connector/app-proxy.png)

## <a name="download-a-new-connector"></a>Új összekötő letöltése

Új összekötő letöltése, használja a "-összekötő letöltése" gombra az oldal tetején.

Megjegyzés: az összekötő a háttéralkalmazás közvetlen sor a láthatáron a gépen kell telepíteni, és általában helyezkedik el ugyanazon a kiszolgálón, megegyezik az alkalmazáséval. Az összekötő a letöltés után meg kell jelennie az ebben a menüben. Kattintson az összekötő, és a "összekötő csoport" legördülő győződjön meg arról, hogy a megfelelő csoporthoz tartozik. A módosítás mentéséhez.

   ![Az összekötő letöltése az Azure portálról](./media/application-proxy-connectivity-no-working-connector/download-connector.png)
   
## <a name="move-an-active-connector"></a>Helyezze át egy aktív összekötő

Ha az aktív csatlakozó, a csoporthoz kell tartoznia, és a háttér célalkalmazásnak történő sor a láthatáron, áthelyezheti az összekötő a hozzárendelt csoportjához. Ehhez kattintson az összekötőt. "Összekötő csoport" mezőben használja a legördülő listán válassza ki a megfelelő csoportot, és kattintson a Mentés gombra.

## <a name="resolve-an-inactive-connector"></a>Hárítsa el az inaktív csatlakozó

Ha a csoportban csak összekötők nem működnek, valószínűleg olyan gépen, amely nem rendelkezik a szükséges portok feloldva.

a portok kapcsolatos problémák elhárítása a dokumentum a részletekért tekintse meg a probléma kivizsgálása.

## <a name="next-steps"></a>Következő lépések
[Az Azure AD-alkalmazásproxy összekötők ismertetése](application-proxy-understand-connectors.md)



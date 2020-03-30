---
title: Az Azure Monitor jelentéseihez tartozó munkafüzetek | Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja az Azure Monitor munkafüzetek az Azure Active Directory-jelentések.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014364"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Az Azure Monitor munkafüzeteinek használata az Azure Active Directory-jelentésekhez

> [!IMPORTANT]
> A munkafüzet alapjául szolgáló lekérdezések optimalizálásához kattintson a "Szerkesztés" gombra, kattintson a Beállítások ikonra, és válassza ki azt a munkaterületet, ahol futtatni szeretné ezeket a lekérdezéseket. Munkafüzetek alapértelmezés szerint kiválasztja az összes munkaterületet, ahol az Azure AD-naplók at. 

Szeretné:

- Ismerje meg a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) hatását a felhasználók bejelentkezési élményére?

- A bejelentkezési hibák elhárításával jobbképet kaphat a szervezet bejelentkezési állapotáról, és gyorsan megoldhatja a problémákat?

- Tudja, hogy ki használ örökölt hitelesítéseket a környezetébe való bejelentkezéshez? (Az [örökölt hitelesítés letiltásával](../conditional-access/block-legacy-authentication.md)javíthatja a bérlő védelmét.)

- Szüksége van a feltételes hozzáférési szabályzatok a bérlőben gyakorolt hatásának megértéséhez?

- Szeretné áttekinteni: bejelentkezési naplólekérdezések, a munkafüzet jelenti, hogy hány felhasználó kapott vagy megtagadták a hozzáférést, valamint hány felhasználó megkerülte a feltételes hozzáférési házirendeket az erőforrások elérésekor?

- Szeretne mélyebb ismereteket kidolgozni: a munkafüzet részletei feltételenként, hogy a szabályzat hatása feltételenként is környezetfüggővé lehessen tenni, beleértve az eszközplatformot, az eszközállapotát, az ügyfélalkalmazást, a bejelentkezési kockázatot, a helyet és az alkalmazást?

- A munkafüzet a bejelentkezési naplólekérdezések mélyebb betekintést nyerhet, így a munkafüzet jelenti, hogy hány felhasználó kapott vagy nem kapott hozzáférést, valamint azt, hogy hány felhasználó kerülte meg a feltételes hozzáférési házirendeket az erőforrások elérésekor.

- A kérdések megválaszolása érdekében az Active Directory figyelési munkafüzeteket biztosít. [Az Azure Monitor munkafüzetei](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) a szöveget, az elemzési lekérdezéseket, a metrikákat és a paramétereket gazdag interaktív jelentésekké egyesítik.



A cikk tartalma:

- Feltételezi, hogy ismeri az [interaktív jelentések létrehozásának módját a Monitor munkafüzetek használatával.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)

- A cikk azt ismerteti, hogy miként használhatja a Munkafüzetek figyelése a feltételes hozzáférési házirendek hatását, a bejelentkezési hibák elhárítását és az örökölt hitelesítések azonosítását.
 


## <a name="prerequisites"></a>Előfeltételek

A Monitor munkafüzetek használatához a következőkre van szükség:

- Prémium (P1 vagy P2) licenccel rendelkező Active Directory-bérlő. Ismerje meg, hogyan [juthat hozzá prémium licenchez.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)

- [A Log Analytics munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Hozzáférés](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) a naplóelemzési munkaterülethez
- Szerepkörök követése az Azure Active Directoryban (ha az Azure Active Directory portálon keresztül éri el a Log Analytics szolgáltatást)
    - Biztonsági rendszergazda
    - Biztonsági olvasó
    - Jelentésolvasó
    - Globális rendszergazda

## <a name="roles"></a>Szerepkörök
A munkafüzetek kezeléséhez a következő szerepkörök egyikében kell lennie, valamint [hozzáféréssel](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) kell rendelkeznie az alapul szolgáló Log Analytics-munkaterülethez:
-   Globális rendszergazda
-   Biztonsági rendszergazda
-   Biztonsági olvasó
-   Jelentésolvasó
-   Alkalmazás-rendszergazda

## <a name="workbook-access"></a>Munkafüzet-hozzáférés 

Munkafüzetek elérése:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

1. Nyissa meg az **Azure Active Directory** > **figyelési** > **munkafüzeteit.** 

1. Jelöljön ki egy jelentést vagy sablont, vagy az eszköztáron válassza a **Megnyitás**gombot. 

![Az Azure Monitor munkafüzeteinek megkeresése az Azure AD-ben](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Bejelentkezési elemzés

A bejelentkezési elemző munkafüzet eléréséhez a **Használat** csoportban válassza a **Bejelentkezések**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja be:

- Minden bejelentkezés

- Sikeres

- Függőben lévő felhasználói művelet

- Hiba

Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezési elemzés](./media/howto-use-azure-monitor-workbooks/43.png)


Az egyes tirányzatok hoz egy bontást a következő kategóriák szerint:

- Hely

    ![Bejelentkezések hely szerint](./media/howto-use-azure-monitor-workbooks/45.png)

- Eszköz

    ![Bejelentkezések eszköz szerint](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Bejelentkezések örökölt hitelesítéssel 


Az [örökölt hitelesítést](../conditional-access/block-legacy-authentication.md)használó bejelentkezések munkafüzetének eléréséhez a **Használat** csoportban válassza a **Bejelentkezések örökölt hitelesítéssel**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja be:

- Minden bejelentkezés

- Sikeres


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

- Protokollok

![Bejelentkezések örökölt hitelesítéssel](./media/howto-use-azure-monitor-workbooks/47.png)


Minden trend, kapsz egy bontást az alkalmazás és a protokoll.

![Örökölt hitelesítési bejelentkezések alkalmazás és protokoll szerint](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Bejelentkezések feltételes hozzáférés sel 


Ha [feltételes hozzáférési házirendek](../conditional-access/overview.md)szerint szeretne bejelentkezni a munkafüzetben, a **Feltételes hozzáférés** szakaszban válassza a **Bejelentkezések feltételes hozzáféréssel**lehetőséget. 

Ez a munkafüzet a letiltott bejelentkezések trendjeit mutatja be. Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezés feltételes hozzáféréssel](./media/howto-use-azure-monitor-workbooks/49.png)


A letiltott bejelentkezések esetén a feltételes hozzáférés állapota lebontja.

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Feltételes hozzáférés – Insights

### <a name="overview"></a>Áttekintés

A munkafüzetek bejelentkezési naplólekérdezéseket tartalmaznak, amelyek segítségével a rendszergazdák figyelhetik a feltételes hozzáférési házirendek hatását a bérlőben. Arról is beszámolhatsz, hogy hány felhasználó kapott volna hozzáférést vagy tagadott volna meg. A munkafüzet betekintést nyújt arra vonatkozóan, hogy hány felhasználó került volna meg feltételes hozzáférési házirendeket az adott felhasználó attribútumai alapján a bejelentkezés időpontjában. Feltételenként részleteket tartalmaz, így a szabályzat hatása feltételenként is környezetfüggővé válhat, beleértve az eszközplatformot, az eszközállapotát, az ügyfélalkalmazást, a bejelentkezési kockázatot, a helyet és az alkalmazást.

### <a name="instructions"></a>Utasítások 
A feltételes hozzáférés-elemzési adatok munkafüzetének eléréséhez jelölje ki a **Feltételes hozzáférés elemzési** adatok munkafüzetet a Feltételes hozzáférés szakaszban. Ez a munkafüzet a bérlő ben az egyes feltételes hozzáférési házirendek várható hatását mutatja. Jelöljön ki egy vagy több feltételes hozzáférési házirendet a legördülő listából, és szűkítse a munkafüzet hatókörét a következő szűrők alkalmazásával: 

- **Időtartomány**

- **Felhasználó**

- **Alkalmazások**

- **Adatnézet**

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/access-insights.png)


A Hatásösszegzés azoknak a felhasználóknak vagy bejelentkezéseknek a számát jeleníti meg, amelyekhez a kiválasztott házirendek adott eredményt kaptak. Összesen azoknak a felhasználóknak vagy bejelentkezéseknek a száma, amelyeknél a kiválasztott házirendeket kiértékelték a kiválasztott időtartományban. Kattintson egy csempére, hogy a munkafüzet ben lévő adatokat az adott eredménytípus szerint szűrje. 

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Ez a munkafüzet a kiválasztott házirendek hatását is bemutatja hat feltétel szerinti bontásban: 
- **Eszköz állapota**
- **Eszköz platform**
- **Ügyfélalkalmazások**
- **Bejelentkezési kockázat**
- **Helyen**
- **Alkalmazások**

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/device-platform.png)

A munkafüzetben kiválasztott paraméterek szerint szűrve az egyes bejelentkezéseket is megvizsgálhatja. Keresse meg az egyes felhasználók, a bejelentkezési gyakoriság szerint rendezve, és tekintse meg a megfelelő bejelentkezési eseményeket. 

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Bejelentkezések támogatási vezérlők kel

Ha [támogatási vezérlőkkel](../conditional-access/controls.md)szeretné elérni a munkafüzetet a bejelentkezésekhez, a **Feltételes hozzáférés** szakaszban válassza a **Bejelentkezések a támogatási vezérlőkkel**lehetőséget. 

Ez a munkafüzet a következő letiltott bejelentkezési trendeket mutatja:

- MFA megkövetelése
 
- Használati feltételek megkövetelése

- Adatvédelmi nyilatkozat megkövetelése

- Egyéb


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések támogatási vezérlők kel](./media/howto-use-azure-monitor-workbooks/50.png)


Minden trend, kapsz egy bontást az alkalmazás és a protokoll.

![A legutóbbi bejelentkezések bontása](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Bejelentkezési hibák elemzése

A **Bejelentkezési hibák elemzésére használt** munkafüzet segítségével elháríthatja a következő kkel kapcsolatos hibákat:

- Bejelentkezések
- Feltételes hozzáférési házirendek
- Örökölt hitelesítés 


A feltételes hozzáférés adatainak bejelentkezéséhez a **Hibaelhárítás** csoportban válassza a **Bejelentkezések örökölt hitelesítéssel**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja be:

- Minden bejelentkezés

- Sikeres

- Függőben lévő művelet

- Hiba


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések – hibaelhárítás](./media/howto-use-azure-monitor-workbooks/52.png)


A bejelentkezések hibaelhárításának segítése érdekében az Azure Monitor a következő kategóriák szerinti bontást biztosít:

- A legfontosabb hibák

    ![A legfontosabb hibák összefoglalása](./media/howto-use-azure-monitor-workbooks/53.png)

- Felhasználói műveletre váró bejelentkezések

    ![A felhasználói műveletre váró bejelentkezések összegzése](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>További lépések

[Interaktív jelentéseket hozhat létre a Munkafüzetek figyelése segítségével.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)

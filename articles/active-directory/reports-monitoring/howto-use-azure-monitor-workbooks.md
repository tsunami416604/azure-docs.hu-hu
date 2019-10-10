---
title: Azure Monitor munkafüzetek használata Azure Active Directory jelentésekhez | Microsoft Docs
description: Megtudhatja, hogyan használhatja Azure Monitor munkafüzeteket Azure Active Directory jelentésekhez.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 9bea8da4f0d694be3a39a8f5dfaca8e54ce2773d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255662"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor munkafüzetek használata Azure Active Directory jelentésekhez

Kívánja:

- Megtudhatja, milyen hatással vannak a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) a felhasználói bejelentkezési élményre?

- A bejelentkezési hibák elhárításával jobban áttekintheti a szervezet bejelentkezési állapotát, és gyorsan megoldhatja a problémákat?

- Tudja, kik használják a örökölt hitelesítéseket a környezetbe való bejelentkezéshez? (Az [örökölt hitelesítés blokkolásával](../conditional-access/block-legacy-authentication.md)javíthatja a bérlők védelmét.)

A kérdések megválaszolásához Active Directory a munkafüzeteket biztosít a figyeléshez. A [Azure monitor munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) gazdag interaktív jelentésekben egyesítik a szöveges, elemzési és mérőszámokat, valamint a paramétereket. 

A cikk tartalma:

- Feltételezi, hogy tisztában van azzal, hogyan [hozhat létre interaktív jelentéseket a figyelő munkafüzetek használatával](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- A cikk azt ismerteti, hogyan használhatók a figyelő munkafüzetek a feltételes hozzáférési szabályzatok hatásának megértéséhez, a bejelentkezési hibák elhárításához és a régi hitelesítések azonosításához.
 


## <a name="prerequisites"></a>Előfeltételek

A figyelő munkafüzetek használatához a következőkre lesz szüksége:

- Egy Active Directory bérlő prémium szintű (P1 vagy P2) licenccel. Megtudhatja, hogyan [szerezhet be prémium szintű licencet](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- [Log Analytics munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Hozzáférés](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) a log Analytics-munkaterülethez
- A következő szerepkörök a Azure Active Directoryban (ha a Log Analytics a Azure Active Directory portálon keresztül éri el)
    - Biztonsági rendszergazda
    - Biztonsági olvasó
    - Jelentés olvasója
    - Globális rendszergazda

## <a name="roles"></a>Szerepkörök
A következő szerepkörök egyikének kell lennie, valamint hozzáféréssel kell rendelkeznie az [alapul szolgáló log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) munkaterülethez a munkafüzetek kezeléséhez:
-   Globális rendszergazda
-   Biztonsági rendszergazda
-   Biztonsági olvasó
-   Jelentés olvasója
-   Alkalmazás-rendszergazda


## <a name="workbook-access"></a>Munkafüzet-hozzáférés 

A munkafüzetek eléréséhez:

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. A bal oldali navigációs panelen válassza a **Azure Active Directory**lehetőséget.

3. A **figyelés** szakaszban válassza a **munkafüzetek**elemet. 

    ![Áttekintések kiválasztása](./media/howto-use-azure-monitor-workbooks/41.png)

4. Válasszon ki egy jelentést vagy sablont, vagy kattintson a **Megnyitás**gombra az eszköztáron. 

    ![Válassza a Megnyitás lehetőséget](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Bejelentkezési elemzés

A bejelentkezési elemzési munkafüzet eléréséhez a **használat** szakaszban válassza a **bejelentkezések**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Sikeres

- Függőben lévő felhasználói művelet

- Hiba

Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezési elemzés](./media/howto-use-azure-monitor-workbooks/43.png)


Minden egyes trend esetében a következő kategóriák szerinti részletezést kapja:

- Földrajzi egység

    ![Bejelentkezések hely szerint](./media/howto-use-azure-monitor-workbooks/45.png)

- Eszköz

    ![Bejelentkezések eszköz szerint](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Korábbi hitelesítéssel történő bejelentkezések 


Ha a munkafüzetet [örökölt hitelesítést](../conditional-access/block-legacy-authentication.md)használó bejelentkezésekhez szeretné elérni, a **használat** szakaszban válassza a **Bejelentkezés örökölt hitelesítés használatával**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Sikeres


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

- Protokollok

![Bejelentkezések örökölt hitelesítéssel](./media/howto-use-azure-monitor-workbooks/47.png)


Az egyes trendek esetében az alkalmazás és a protokoll részletezést kap.

![Örökölt hitelesítési bejelentkezések alkalmazás és protokoll szerint](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Bejelentkezések feltételes hozzáféréssel 


Ha a munkafüzetet a [feltételes hozzáférési házirendek](../conditional-access/overview.md)alapján szeretné elérni a bejelentkezésekhez, a **feltételes hozzáférés** szakaszban válassza a **bejelentkezések feltételes hozzáférés alapján**lehetőséget. 

Ez a munkafüzet a letiltott bejelentkezések trendjét jeleníti meg. Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezés feltételes hozzáféréssel](./media/howto-use-azure-monitor-workbooks/49.png)


A letiltott bejelentkezések esetében a feltételes hozzáférési állapot szerinti részletezést kap.

![Feltételes hozzáférés állapota](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Bejelentkezések engedélyezési vezérlőkkel

Ha [vezérlőket ad](../conditional-access/controls.md)meg a bejelentkezéshez, a **feltételes hozzáférés** szakaszban válassza a **bejelentkezések engedélyezése vezérlők alapján**lehetőséget. 

Ez a munkafüzet a következő letiltott bejelentkezési trendeket mutatja:

- MFA megkövetelése
 
- Használati feltételek megkövetelése

- Adatvédelmi nyilatkozat szükséges

- Egyéb


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések engedélyezési vezérlőkkel](./media/howto-use-azure-monitor-workbooks/50.png)


Az egyes trendek esetében az alkalmazás és a protokoll részletezést kap.

![Legutóbbi bejelentkezések részletezése](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Sikertelen bejelentkezések elemzése

A **bejelentkezések sikertelen elemzése** munkafüzettel a következő hibákkal kapcsolatos hibaelhárítást végezheti el:

- Bejelentkezések
- Feltételes hozzáférési szabályzatok
- Örökölt hitelesítés 


A bejelentkezés feltételes hozzáférési adatai alapján való eléréséhez a **hibakeresés** szakaszban válassza az **örökölt hitelesítés használatával történő bejelentkezések**lehetőséget. 

Ez a munkafüzet a következő bejelentkezési trendeket mutatja:

- Minden bejelentkezés

- Sikeres

- Függőben lévő művelet

- Hiba


Az egyes trendeket a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezések hibaelhárítása](./media/howto-use-azure-monitor-workbooks/52.png)


Ha segítségre van szüksége a bejelentkezések hibakereséséhez, Azure Monitor a következő kategóriák szerinti bontást biztosít:

- Leggyakoribb hibák

    ![Leggyakoribb hibák összefoglalása](./media/howto-use-azure-monitor-workbooks/53.png)

- Felhasználói műveletre váró bejelentkezések

    ![Felhasználói műveletre váró bejelentkezések összefoglalása](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Következő lépések

[Interaktív jelentéseket hozhat létre a munkafüzetek figyelése használatával](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

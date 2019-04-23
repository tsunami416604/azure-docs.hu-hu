---
title: Használja az Azure Monitor munkafüzetek a jelentések az Azure Active Directory |} A Microsoft Docs
description: Az Azure Monitor munkafüzetek használata az Azure Active Directory-jelentések
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2c9b3d0ef110fea0629af345a71d0d7b7cce7313
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013926"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Útmutató: Az Azure Monitor munkafüzetek használata az Azure Active Directory-jelentések

Szeretné:

- Megismerheti a hatását a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) a felhasználók bejelentkezési élmény?

- Jelentkezzen be kapcsolatos hibák elhárítása, jelentkezzen be az egészségügyi szervezet jobban láthatja, valamint a problémák gyors megoldásához?

- Tudja meg, ki az örökölt hitelesítés segítségével jelentkezzen be a környezet? Által [örökölt hitelesítés](../conditional-access/block-legacy-authentication.md), a bérlő védelmi javítása érdekében.


[Az Azure Monitor munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) szöveg, elemzési lekérdezések, az Azure-mérőszámok és paraméterek egyesítendő gazdag, interaktív jelentésekkel. Az Azure Active Directory munkafüzetek figyelésére, amelyek segítenek az alábbi kérdésekre a fenti biztosít.

A cikk tartalma:

- Feltételezi, hogy megismerkedett az [interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- Ismerteti, hogyan használhatja az Azure Monitor munkafüzetek figyelésével kapcsolatos fenti kérdések megválaszolása céljából.
 


## <a name="prerequisites"></a>Előfeltételek

A szolgáltatás használatához a következőkre lesz szüksége:

- Az Azure Active Directory-bérlő, prémium verzió (P1 vagy P2) licenccel. Ismerje meg, hogyan [premium licenc](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="access-workbooks"></a>Hozzáférés munkafüzetek 

Munkafüzetek elérése:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com).

2. A bal oldali navigációs sávon kattintson **Azure Active Directory**.

3. Az a **figyelés** területén kattintson **Insights**. 

    ![Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Kattintson a jelentés vagy sablon vagy a **nyílt** az eszköztáron. 

    ![Katalógus](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Jelentkezzen be elemzés

A bejelentkezési elemzési munkafüzet eléréséhez kattintson **bejelentkezések** a a **használati** szakaszban. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Az összes bejelentkezés

- Sikeres

- Folyamatban lévő felhasználói művelet

- Hiba

Minden egyes trend szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Katalógus](./media/howto-use-azure-monitor-workbooks/43.png)


Minden egyes trendek akkor egy bontása használhatja:

- Location egység

    ![Katalógus](./media/howto-use-azure-monitor-workbooks/45.png)

- Eszköz

    ![Katalógus](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Bejelentkezések örökölt hitelesítés használata 


A hozzáférés a bejelentkezések [örökölt hitelesítési](../conditional-access/block-legacy-authentication.md) munkafüzetet, kattintson a **bejelentkezések örökölt hitelesítés használata** a a **használati** szakaszban. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Az összes bejelentkezés

- Sikeres


Minden egyes trend szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

- Protokollok 

![Katalógus](./media/howto-use-azure-monitor-workbooks/47.png)


Az egyes trendek kap egy alkalmazás és a protokoll szerinti bontásban.

![Katalógus](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Feltételes hozzáférés bejelentkezések 


A bejelentkezések alapján eléréséhez [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) munkafüzetet, kattintson a **bejelentkezések alapján feltételes hozzáférési** a a **feltételes hozzáférési** szakasz. 

Ez a munkafüzet letiltott bejelentkezések alkalmazható trendjét jeleníti meg.

Minden egyes trend szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Katalógus](./media/howto-use-azure-monitor-workbooks/49.png)


A letiltott bejelentkezések akkor egy bontása a feltételes hozzáférési állapot szerint kapnak.

![Feltételes hozzáférési állapot](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Engedélyezési vezérlők bejelentkezések

A bejelentkezések alapján eléréséhez [vezérlők megadása](../conditional-access/controls.md) munkafüzetet, kattintson a **bejelentkezések engedély által** a a **feltételes hozzáférési** szakaszban. 

Ez a munkafüzet jeleníti meg következő letiltott jelentkezzen be:

- MFA megkövetelése
 
- Használati feltételek megkövetelése

- Adatvédelmi nyilatkozat megkövetelése

- Egyéb


Minden egyes trend szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Katalógus](./media/howto-use-azure-monitor-workbooks/50.png)


Az egyes trendek kap egy alkalmazás és a protokoll szerinti bontásban.

![Katalógus](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Bejelentkezések alkalmazáshiba-elemzés

Használja a **bejelentkezések hibaelemzés** kapcsolatos hibák elhárítása a munkafüzet:

- Bejelentkezések
- Feltételes hozzáférési szabályzatok
- Az örökölt hitelesítés. 


A bejelentkezések alapján feltételes hozzáférési adatok eléréséhez kattintson **bejelentkezések örökölt hitelesítés használata** a a **hibaelhárítás** szakaszban. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Az összes bejelentkezés

- Sikeres

- Folyamatban lévő művelet

- Hiba


Minden egyes trend szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Katalógus](./media/howto-use-azure-monitor-workbooks/52.png)


Bejelentkezések hibaelhárítása, kap egy bontása:

- Legfontosabb hibák

    ![Katalógus](./media/howto-use-azure-monitor-workbooks/53.png)

- Felhasználói beavatkozásra vár a bejelentkezések

    ![Katalógus](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>További lépések

* [Interaktív jelentések létrehozása az Azure Monitor-munkafüzetekkel](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)
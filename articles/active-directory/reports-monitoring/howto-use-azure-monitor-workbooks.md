---
title: Használja az Azure Monitor munkafüzetek a jelentések az Azure Active Directory |} A Microsoft Docs
description: Ismerje meg az Azure Active Directory-jelentések használata az Azure Monitor-munkafüzetek.
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
ms.openlocfilehash: 9433714c06dfad09270a6033f38a99471bcd517a
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513621"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Hogyan használható az Azure Monitor-munkafüzetek a Azure Active Directory-jelentések

Szeretné:

- Megismerheti a hatását a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) a bejelentkezési élmény a felhasználók?

- Bejelentkezési hibák elhárítása a munkahelyi bejelentkezési állapot jobb képet kaphat, és a problémák gyors megoldásához?

- Tudja meg, ki az örökölt hitelesítés segítségével jelentkezzen be a környezet? (A [örökölt hitelesítés](../conditional-access/block-legacy-authentication.md), növelheti a bérlő védelmi.)

Érdekében, hogy oldja meg ezeket a kérdéseket, az Active Directory munkafüzetek figyelő tartalmaz. [Az Azure Monitor munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) szöveg, az elemzési lekérdezések, a metrikák és a paraméterek egyesítendő gazdag, interaktív jelentésekkel. 

A cikk tartalma:

- Azt feltételezi, hogy megismerkedett az [interaktív jelentéseket készített munkafüzetek figyelő](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- A feltételes hozzáférési szabályzatokat, a bejelentkezési hibák elhárítása, és az örökölt hitelesítés azonosításához hatásának megértéséhez figyelő munkafüzetek használatát ismerteti.
 


## <a name="prerequisites"></a>Előfeltételek

Figyelő munkafüzetek használatához az alábbiak szükségesek:

- Active Directory-bérlő (P1 vagy P2) prémium szintű licenccel. Ismerje meg, hogyan [premium licenc](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- A [Log Analytics-munkaterület](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

## <a name="workbook-access"></a>A munkafüzet-hozzáférés 

Munkafüzetek elérése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory**.

3. Az a **figyelés** szakaszban jelölje be **munkafüzetek**. 

    ![Válassza ki a Insights](./media/howto-use-azure-monitor-workbooks/41.png)

4. Válasszon ki egy jelentés vagy a sablont, vagy válassza az eszköztár **nyílt**. 

    ![Válassza a Megnyitás](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Jelentkezzen be elemzés

A bejelentkezési elemzési munkafüzet elérését a a **használati** szakaszban jelölje be **bejelentkezések**. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Összes bejelentkezés

- Siker

- Függőben lévő felhasználói művelet

- Hiba

Minden egyes trend a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Jelentkezzen be elemzés](./media/howto-use-azure-monitor-workbooks/43.png)


Minden egyes trend meg információkat a következő kategóriák szerint használhatja:

- Location

    ![Bejelentkezések hely szerint](./media/howto-use-azure-monitor-workbooks/45.png)

- Eszköz

    ![Az eszköz bejelentkezések](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Bejelentkezések örökölt hitelesítés használata 


A munkafüzet a bejelentkezések, amelyek eléréséhez [örökölt hitelesítési](../conditional-access/block-legacy-authentication.md), a a **használati** szakaszban jelölje be **bejelentkezések örökölt hitelesítés használata**. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Összes bejelentkezés

- Siker


Minden egyes trend a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

- Protokollok

![Az örökölt hitelesítés bejelentkezések](./media/howto-use-azure-monitor-workbooks/47.png)


Az egyes trendek kap egy alkalmazás és a protokoll szerinti bontásban.

![Örökölt-hitelesítési bejelentkezéseket a alkalmazást, és protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Feltételes hozzáférés bejelentkezések 


A munkafüzetet a bejelentkezések alapján eléréséhez [feltételes hozzáférési szabályzatok](../conditional-access/overview.md), a a **feltételes hozzáférési** szakaszban jelölje be **bejelentkezések feltételes hozzáférés**. 

Ez a munkafüzet jeleníti meg a letiltott bejelentkezések. Minden egyes trend a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezés feltételes hozzáféréssel](./media/howto-use-azure-monitor-workbooks/49.png)


A letiltott bejelentkezések a részletezés a feltételes hozzáférési állapot szerint kap.

![Feltételes hozzáférési állapot](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Engedélyezési vezérlők bejelentkezések

A munkafüzetet a bejelentkezések alapján eléréséhez [vezérlők megadása](../conditional-access/controls.md), a a **feltételes hozzáférési** szakaszban jelölje be **bejelentkezések engedély által**. 

Ez a munkafüzet jeleníti meg következő letiltott jelentkezzen be:

- MFA megkövetelése
 
- Használati feltételek megkövetelése

- Adatvédelmi nyilatkozat megkövetelése

- Egyéb


Minden egyes trend a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Engedélyezési vezérlők bejelentkezések](./media/howto-use-azure-monitor-workbooks/50.png)


Az egyes trendek kap egy alkalmazás és a protokoll szerinti bontásban.

![Legutóbbi bejelentkezések áttekintését](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Bejelentkezések alkalmazáshiba-elemzés

Használja a **bejelentkezések hibaelemzés** munkafüzet hibaelhárítás a következőkkel:

- Bejelentkezések
- Feltételes hozzáférési szabályzatok
- Az örökölt hitelesítés 


A feltételes hozzáférési adatokat, a bejelentkezések elérni a **hibaelhárítás** szakaszban jelölje be **bejelentkezések örökölt hitelesítés használata**. 

Ez a munkafüzet a következő bejelentkezési trendeket jelenít meg:

- Összes bejelentkezés

- Siker

- Függőben lévő művelet

- Hiba


Minden egyes trend a következő kategóriák szerint szűrheti:

- Időtartomány

- Alkalmazások

- Felhasználók

![Bejelentkezés hibaelhárítása](./media/howto-use-azure-monitor-workbooks/52.png)


Segíthetnek a bejelentkezések, az Azure Monitor lebontva a következő kategóriák szerint:

- Legfontosabb hibák

    ![Legfontosabb hibák összefoglalása](./media/howto-use-azure-monitor-workbooks/53.png)

- Felhasználói beavatkozásra vár a bejelentkezések

    ![Felhasználói beavatkozásra vár a bejelentkezések összefoglalása](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>További lépések

[Hozzon létre interaktív jelentéseket figyelő munkafüzetek](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

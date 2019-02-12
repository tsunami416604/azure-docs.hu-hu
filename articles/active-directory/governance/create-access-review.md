---
title: Hozzáférési felülvizsgálat létrehozása egy csoport vagy az Azure AD-alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai számára |} A Microsoft Docs
description: Ismerje meg a hozzáférési felülvizsgálat létrehozása egy csoporthoz vagy alkalmazáshoz való hozzáféréssel rendelkező felhasználók tagjai számára.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 11/15/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 2393aaec32f2813860ab422b42772eef11ccd8a3
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099156"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Az Azure AD-csoport tagjainak vagy alkalmazás-hozzáférés hozzáférési felülvizsgálat létrehozása

Hozzáférési csoportoknak és alkalmazásoknak az alkalmazottak és a vendégek változik az idő múlásával. A régi hozzáférési hozzárendelések kockázatának csökkentése érdekében a rendszergazdák az Azure Active Directory (Azure AD) használhatják a hozzáférési felülvizsgálatok létrehozása csoporttagok vagy alkalmazás-hozzáférés. Rendszeresen ellenőrizze a hozzáférés van szüksége, ha ismétlődő a hozzáférési felülvizsgálatok is létrehozhat. Ilyen forgatókönyvekkel kapcsolatos további információkért lásd: [felhasználói hozzáférés felügyelete](manage-user-access-with-access-reviews.md) és [vendégfelhasználói hozzáférés felügyelete](manage-guest-access-with-access-reviews.md).

Ez a cikk ismerteti, hogyan hozhat létre egy új hozzáférési felülvizsgálat csoporttagok vagy alkalmazás-hozzáférést.

## <a name="prerequisites"></a>Előfeltételek

- [A hozzáférési felülvizsgálatok engedélyezve](access-reviews-overview.md)
- Globális rendszergazda vagy a fiók rendszergazdája

## <a name="create-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása

1. Jelentkezzen be az Azure Portalon, és nyissa meg a [a hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. Kattintson a **vezérlők**.

1. Kattintson a **új hozzáférési felülvizsgálat** egy új hozzáférési felülvizsgálat létrehozása.

    ![Hozzáférési felülvizsgálat - vezérlők](./media/create-access-review/controls.png)

1. A hozzáférési felülvizsgálat neve. A felülvizsgálat igény szerint adjon meg leírást. A nevet és leírást a felülvizsgálóknak jelennek meg.

    ![Hozzáférési felülvizsgálat létrehozása – felülvizsgálat neve és leírása](./media/create-access-review/name-description.png)

1. Állítsa be a **kezdő dátum**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer végbemegy, létrejön egy időben kezdődik, és egy hónap alatt végződik. Módosíthatja a kezdő és záró dátumát férnek hozzá egy tekintse át kezdő legutóbbi és a jövőben azonban hány nappal kívánja.

    ![Hozzáférési felülvizsgálat - létrehozása kezdési és befejezési dátuma](./media/create-access-review/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődési, módosítsa a **gyakorisága** beállítást **egyszer** való **heti**, **havi**,  **Negyedéves** vagy **évente**, és használja a **időtartama** csúszka vagy beviteli mező határozza meg, hány napig lesz az ismétlődő sorozatának kritika nyissa meg a felülvizsgálók a bemenetet. Például a maximális időtartam, amely lehet a havi felülvizsgálat 27 nap, kerülje az átfedésben lévő értékelések.

1. Használja a **záró** a beállítás határozza meg az ismétlődő hozzáférés befejezése tekintse át a sorozat. A sorozat fejezheti be háromféleképpen: folyamatosan felülvizsgálatok határozatlan ideig, amíg egy adott dátumot, vagy egy meghatározott előfordulások száma befejezése után el futtatja. Azt, egy másik felhasználóifiók-adminisztrátor vagy egy másik globális rendszergazda állíthatja le a sorozat létrehozása után a dátumot a módosításával **beállítások**, így az adott dátumon végződik.

1. Az a **felhasználók** területén adja meg a felhasználók, a hozzáférési felülvizsgálat vonatkozik. A hozzáférési felülvizsgálatok lehet a felhasználók számára, akik hozzá lett rendelve egy alkalmazás vagy egy csoport tagjai számára. A hozzáférés tekintse át a felülvizsgálati csak a vendégfelhasználók akik tagjai (vagy az alkalmazáshoz hozzárendelt), ahelyett, hogy tekintse át az összes felhasználó számára a tagja, vagy hozzáférni az alkalmazáshoz rendelkező további körét.

    ![Hozzáférési felülvizsgálat - felhasználók létrehozása](./media/create-access-review/users.png)

1. Az a **felülvizsgálók** területen válassza ki egy vagy több személyeket, és tekintse át a hatókörben lévő összes felhasználó. Vagy tekintse át a saját hozzáférését tagokból ki. Ha az erőforrás-csoport, megkérheti a csoport tulajdonosai áttekintéséhez. Azt is megkövetelheti, hogy a felülvizsgálók szabályzataival OK, a hozzáférés jóváhagyása.

    ![Hozzáférési felülvizsgálat - felülvizsgálók létrehozása](./media/create-access-review/reviewers.png)

1. Az a **programok** területen válassza ki a használni kívánt programot. Egyszerűsítheti a nyomon követni, és a hozzáférési felülvizsgálatok gyűjtése a különböző felhasználási célokra szervezetspecifikus programokban rendezve kezelje. **Alapértelmezett Program** mindig jelen, vagy létrehozhat egy másik program. Választhat, például, hogy az egyes megfelelőségi-kezdeményezéshez egy program vagy üzleti cél.

    ![Hozzáférési felülvizsgálat - programok létrehozása](./media/create-access-review/programs.png)

### <a name="upon-completion-settings"></a>A befejezést követő művelet beállításai

1. Adja meg, mi történik, a felülvizsgálat befejezése után, bontsa ki a **befejezést követő művelet beállításai** szakaszban.

    ![A befejezést követő művelet beállításai](./media/create-access-review/upon-completion-settings.png)

1. Ha szeretne automatikusan remove hozzáférést a felhasználók számára, hogy el lett utasítva, **eredmények automatikus alkalmazása az erőforrás** való **engedélyezése**. Ha szeretné manuálisan alkalmazza az eredményeket, ha a felülvizsgálatot követően, állítsa a kapcsolót **letiltása**.

1. Használja a **a felülvizsgáló nem válaszol** listát használva adhatja meg, mi történik, a felhasználók számára, nem ellenőrzött felülvizsgálója ellenőrzi a felülvizsgálati időszak alatt. Ez a beállítás nem befolyásolja a felhasználók, akik korábban a többi felhasználó által manuálisan. Ha a végső felülvizsgáló döntési megtagadás, akkor a felhasználó hozzáférése törlődni fog.

    - **Nincs változás** -hagyja változatlanul a felhasználó hozzáférése
    - **Hozzáférés eltávolítása** – felhasználói hozzáférés letiltása
    - **Hozzáférés jóváhagyása** – felhasználói hozzáférés jóváhagyása
    - **Ajánlatok elfogadása** – a rendszer elutasítja az ajánlást igénybe, vagy hagyja jóvá a felhasználó a folyamatos hozzáférést

### <a name="advanced-settings"></a>Speciális beállítások

1. Szeretne megadni további beállításokat, bontsa ki a **speciális beállítások** szakaszban.

    ![Speciális beállítások](./media/create-access-review/advanced-settings.png)

1. Állítsa be **javaslatok megjelenítése** való **engedélyezése** lehet megjeleníteni a felülvizsgálók a rendszer javaslatok alapján a felhasználó hozzáférési adatokat.

1. Állítsa be **a jóváhagyás oka szükséges** való **engedélyezése** , adja meg a jóváhagyás okát a felülvizsgálónak kötelező.

1. Állítsa be **az E-mail értesítések** való **engedélyezése** szeretné, hogy az Azure AD küldjön értesítő felülvizsgálóknak a hozzáférési felülvizsgálatok megkezdésekor, valamint a rendszergazdák felülvizsgálatok befejezésekor.

1. Állítsa be **emlékeztetők** való **engedélyezése** , az Azure AD emlékeztetőt küldjön a hozzáférési felülvizsgálatok folyamatban felülvizsgálóknak, akik még nem fejezték be a felülvizsgálatot.

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start**.

Alapértelmezés szerint az Azure AD-e-mailt küld felülvizsgálók elindítja a felülvizsgálatot követően rövid időn belül. Ha nem rendelkezik Azure ad-ben az e-mailt, mindenképpen tájékoztatja a felülvizsgálatot, amely a hozzáférési felülvizsgálat várakozik, amíg befejeződnek. Mutathat nekik az utasításokat, hogy hogyan [hozzáférés felülvizsgálata](perform-access-review.md). Ha áttekintésre vendégek tekintse át a saját hozzáférését, azokat az utasítások megjelenítése, hogy hogyan [a saját hozzáférés felülvizsgálata](perform-access-review.md).

Ha a felülvizsgálók néhány Vendégek, Vendégek értesítést kap e-mailen keresztül csak akkor, ha korábban már fogadta el a meghívót.

## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Követheti a folyamat állapotát, ahogy a felülvizsgálatot, végezze el az Azure ad-ben irányítópultján található áttekintette a **hozzáférési felülvizsgálatok** szakaszban. Nincs hozzáférési jogosultsága változnak, amíg a könyvtárban [a felülvizsgálat befejeződött](complete-access-review.md).

Ha ez egy egyszeri tekintse át, majd a hozzáférési felülvizsgálati időszak felett van, vagy a rendszergazda a hozzáférési felülvizsgálat leállítása után kövesse a lépéseket a [hozzáférési felülvizsgálat befejezése](complete-access-review.md) megtekintéséhez és a alkalmazni az eredményeket.  

A hozzáférési felülvizsgálatok sorozatát kezeléséhez, keresse meg a hozzáférési felülvizsgálatot, az **vezérlők**, és fog közelgő események keresése az ütemezett ellenőrzések, és módosítsa a záró dátumot vagy hozzáadása/eltávolítása felülvizsgálóknak annak megfelelően. 

A beállításokat a befejezést követő művelet beállításai alapján, automatikusan érvénybe lesz a felülvizsgálat záró dátuma, vagy ha manuálisan leállítja a felülvizsgálatot követően hajtható végre. Az állapot, a felülvizsgálat befejezve változik keresztül köztes Államokban például az alkalmazása és végül alkalmazott állapotra. Tekintse meg a letiltott felhasználók, ha bármely, a csoport tagsági és alkalmazás-hozzárendelés néhány perc múlva távolít el kell látnia.

## <a name="create-reviews-via-apis"></a>API-kon keresztül felülvizsgálat létrehozása

A hozzáférési felülvizsgálatok API-k használatával is létrehozhat. A csoportok kezelésére mit ellenőrzi, és alkalmazás felhasználói számára az Azure Portalon is elvégezhető a Microsoft Graph API-k használatával. További információkért lásd: a [Azure AD hozzáférési felülvizsgálatok API-referencia](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). A kód minta: [példa beolvasása az Azure AD hozzáférési felülvizsgálatok segítségével a Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>További lépések

- [Hozzáférési felülvizsgálat indítása az Azure AD hozzáférési felülvizsgálatokkal](perform-access-review.md)
- [Tagok egy csoport vagy az Azure AD-alkalmazásokhoz való felhasználói hozzáférés hozzáférési felülvizsgálat befejezése](complete-access-review.md)

---
title: Hozzon létre a csoportokat vagy alkalmazásokat – Azure Active Directory hozzáférési felülvizsgálatok |} A Microsoft Docs
description: Ismerje meg a csoport tagjainak vagy alkalmazás-hozzáférés hozzáférési felülvizsgálat létrehozása az Azure Active Directory hozzáférési felülvizsgálatok.
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
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ef72f1649c3f3e0af7fba53b2e8dbcee49d4b59
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734564"
---
# <a name="create-an-access-review-of-groups-or-applications-in-azure-ad-access-reviews"></a>A csoportok a hozzáférési felülvizsgálat létrehozása és alkalmazások az Azure AD hozzáférési felülvizsgálatokkal

Hozzáférési csoportoknak és alkalmazásoknak az alkalmazottak és a vendégek változik az idő múlásával. A régi hozzáférési hozzárendelések kockázatának csökkentése érdekében a rendszergazdák az Azure Active Directory (Azure AD) használhatják a hozzáférési felülvizsgálatok létrehozása csoporttagok vagy alkalmazás-hozzáférés. Rendszeresen ellenőrizze a hozzáférés van szüksége, ha ismétlődő a hozzáférési felülvizsgálatok is létrehozhat. Ilyen forgatókönyvekkel kapcsolatos további információkért lásd: [felhasználói hozzáférés felügyelete](manage-user-access-with-access-reviews.md) és [vendégfelhasználói hozzáférés felügyelete](manage-guest-access-with-access-reviews.md).

Ez a cikk ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatok csoporttagok vagy alkalmazás-hozzáférést.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda vagy felhasználói rendszergazda

További információkért lásd: [mely felhasználók rendelkeznie kell licencekkel?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="create-one-or-more-access-reviews"></a>Hozzon létre egy vagy több hozzáférési felülvizsgálatok

1. Jelentkezzen be az Azure Portalon, és nyissa meg a [Identitáskezelést oldal](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

1. A bal oldali menüben kattintson a **hozzáférési felülvizsgálatokkal**.

1. Kattintson a **új hozzáférési felülvizsgálat** egy új hozzáférési felülvizsgálat létrehozása.

    ![Hozzáférési felülvizsgálat - vezérlők](./media/create-access-review/access-reviews.png)

1. A hozzáférési felülvizsgálat neve. A felülvizsgálat igény szerint adjon meg leírást. A nevet és leírást a felülvizsgálóknak jelennek meg.

    ![Hozzáférési felülvizsgálat létrehozása – felülvizsgálat neve és leírása](./media/create-access-review/name-description.png)

1. Állítsa be a **kezdő dátum**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer végbemegy, létrejön egy időben kezdődik, és egy hónap alatt végződik. Módosíthatja a kezdő és záró dátumát férnek hozzá egy tekintse át kezdő legutóbbi és a jövőben azonban hány nappal kívánja.

    ![Hozzáférési felülvizsgálat - létrehozása kezdési és befejezési dátuma](./media/create-access-review/start-end-dates.png)

1. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődési, módosítsa a **gyakorisága** beállítást **egyszer** való **heti**, **havi**,  **Negyedéves** vagy **évente**. Használja a **időtartama** meghatározásához, hány napig lesz az ismétlődő sorozatának kritika nyissa meg a felülvizsgálóknak a bemenetet a csúszka vagy a beviteli mezőben. Például a maximális időtartam, amely lehet a havi felülvizsgálat 27 nap, kerülje az átfedésben lévő értékelések.

1. Használja a **záró** a beállítás határozza meg az ismétlődő hozzáférés befejezése tekintse át a sorozat. A sorozat fejezheti be háromféleképpen: folyamatosan felülvizsgálatok határozatlan ideig, amíg egy adott dátumot, vagy egy meghatározott előfordulások száma befejezése után el futtatja. Azt, egy másik felhasználó rendszergazda vagy egy másik globális rendszergazda állíthatja le a sorozat létrehozása után a dátumot a módosításával **beállítások**, így az adott dátumon végződik.

1. Az a **felhasználók** szakaszban adja meg a felhasználók, a hozzáférési felülvizsgálat vonatkozik. A hozzáférési felülvizsgálatok lehet a felhasználók számára, akik hozzá lett rendelve egy alkalmazás vagy egy csoport tagjai számára. A hozzáférés tekintse át a felülvizsgálati csak a vendégfelhasználók akik tagjai (vagy az alkalmazáshoz hozzárendelt), ahelyett, hogy tekintse át az összes felhasználó számára a tagja, vagy hozzáférni az alkalmazáshoz rendelkező további körét.

    ![Hozzáférési felülvizsgálat - felhasználók létrehozása](./media/create-access-review/users.png)

1. Az a **csoport** területen válassza ki egy vagy több olyan csoportot, amelyet szeretne, tekintse át a tagságát.

    > [!NOTE]
    > Több csoport kiválasztásakor több hozzáférési felülvizsgálatok hoz létre. Ha például öt csoportokra hoz létre öt külön a hozzáférési felülvizsgálatok.
    
    ![Hozzáférési felülvizsgálat létrehozása – csoport kijelölése](./media/create-access-review/select-group.png)

1. Az a **alkalmazások** szakaszban (Ha a kiválasztott **alkalmazáshoz hozzárendelt** 8. lépés), válassza ki az alkalmazásokat, amelyeket szeretne, tekintse át a hozzáférést.

    > [!NOTE]
    > Kiválasztásával egynél több alkalmazást hoz létre több hozzáférési felülvizsgálatok. Ha például öt alkalmazások kiválasztása hoz létre öt külön a hozzáférési felülvizsgálatok.
    
    ![Hozzáférési felülvizsgálat létrehozása – alkalmazás választása](./media/create-access-review/select-application.png)

1. Az a **felülvizsgálók** területen válassza ki egy vagy több személyeket, és tekintse át a hatókörben lévő összes felhasználó. Vagy tekintse át a saját hozzáférését tagokból ki. Ha az erőforrás-csoport, megkérheti a csoport tulajdonosai áttekintéséhez. Azt is megkövetelheti, hogy a felülvizsgálók szabályzataival OK, a hozzáférés jóváhagyása.

    ![Hozzáférési felülvizsgálat - felülvizsgálók létrehozása](./media/create-access-review/reviewers.png)

1. Az a **programok** területen válassza ki a használni kívánt programot. **Alapértelmezett Program** mindig szerepel.

    ![Hozzáférési felülvizsgálat - programok létrehozása](./media/create-access-review/programs.png)

    Egyszerűsítheti a nyomon követni, és a hozzáférési felülvizsgálatok gyűjtése a különböző felhasználási célokra szervezetspecifikus programokban rendezve kezelje. Minden hozzáférési felülvizsgálat program lehet kapcsolódni. Ezután egy auditor jelentések előkészületeként, összpontosíthat a hozzáférési felülvizsgálatok egy adott kezdeményezés terjed ki. Programok és a hozzáférési felülvizsgálat eredményei láthatók a globális rendszergazdai, felhasználó rendszergazdai, biztonsági rendszergazdai vagy biztonsági olvasói szerepkör felhasználói számára.

    Programok listájának megtekintéséhez nyissa meg a hozzáférési felülvizsgálatok lapot, és válassza ki **programok**. Ha Ön globális rendszergazda vagy felhasználói rendszergazda szerepkört, az további programokat is létrehozhat. Választhat, például, hogy az egyes megfelelőségi-kezdeményezéshez egy program vagy üzleti cél. Ha már nincs szüksége egy programot, és nem kell minden olyan vezérlőelemek, hozzá kell kapcsolni, törölheti azt.

### <a name="upon-completion-settings"></a>Befejezést követő művelet beállításai

1. Adja meg, mi történik, a felülvizsgálat befejezése után, bontsa ki a **befejezést követő művelet beállításai** szakaszban.

    ![Befejezést követő művelet beállításai](./media/create-access-review/upon-completion-settings.png)

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

    Alapértelmezés szerint az Azure AD a rendelkezésre álló idő felénél automatikusan emlékeztetőt küld azoknak a felülvizsgálóknak, akik még nem tettek eleget a kérésnek.

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési felülvizsgálat beállításait, kattintson a **Start**. A hozzáférési felülvizsgálat megjelenik a listában, az azt jelzi, hogy annak állapotát.

![A hozzáférési felülvizsgálatok listája](./media/create-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD-e-mailt küld felülvizsgálók elindítja a felülvizsgálatot követően rövid időn belül. Ha nem rendelkezik Azure ad-ben az e-mailt, mindenképpen tájékoztatja a felülvizsgálatot, amely a hozzáférési felülvizsgálat várakozik, amíg befejeződnek. Mutathat nekik az utasításokat, hogy hogyan [csoportokhoz vagy alkalmazásokhoz való hozzáférés felülvizsgálata](perform-access-review.md). Ha áttekintésre vendégek tekintse át a saját hozzáférését, azokat az utasítások megjelenítése, hogy hogyan [hozzáférés felülvizsgálata maga a csoportokat vagy alkalmazásokat](review-your-access.md).

Hozzárendelt vendégek felülvizsgálók, és azok nem fogadta el a meghívást, ha azok nem kapják e-mailt a hozzáférési felülvizsgálatok mivel először el kell fogadniuk előtt tekintse át a meghívást.

## <a name="create-reviews-via-apis"></a>API-kon keresztül felülvizsgálat létrehozása

A hozzáférési felülvizsgálatok API-k használatával is létrehozhat. A csoportok kezelésére mit ellenőrzi, és alkalmazás felhasználói számára az Azure Portalon is elvégezhető a Microsoft Graph API-k használatával. További információkért lásd: a [az Azure AD hozzáférési felülvizsgálatok API-referencia](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta). A kód minta: [példa beolvasása az Azure AD hozzáférési felülvizsgálatok keresztül a Microsoft Graph](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096).

## <a name="next-steps"></a>További lépések

- [A csoportokat vagy alkalmazásokat hozzáférés felülvizsgálata](perform-access-review.md)
- [Tekintse át a hozzáférést a saját maga csoportokat vagy alkalmazásokat](review-your-access.md)
- [Csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat befejezése](complete-access-review.md)

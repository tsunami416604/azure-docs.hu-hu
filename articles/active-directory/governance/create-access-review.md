---
title: Csoportok & alkalmazások hozzáférési felülvizsgálatának létrehozása – Azure AD
description: Megtudhatja, hogyan hozhat létre hozzáférés-áttekintést a csoporttagokról vagy az alkalmazások hozzáféréséről az Azure Active Directory-hozzáférési felülvizsgálatokban.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/08/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e01951b6147cfc39fe6c46035db822071bda3aa
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984070"
---
# <a name="create-an-access-review-of-groups-and-applications-in-azure-ad-access-reviews"></a>Csoportok és alkalmazások hozzáférés-felülvizsgálatának létrehozása az Azure AD-hozzáférés-felülvizsgálatokban

Az alkalmazottak és a vendégek csoportjaihoz és alkalmazásaihoz való hozzáférés idővel változik. Az elavult hozzáférési hozzárendelések kockázatának csökkentése érdekében a rendszergazdák az Azure Active Directory (Azure AD) segítségével hozzáférési felülvizsgálatokat hozhatnak létre a csoporttagok vagy az alkalmazás-hozzáférés számára. Ha rendszeresen át kell tekintenie a hozzáférést, ismétlődő hozzáférési felülvizsgálatokat is létrehozhat. Ezekről az esetekről a [Felhasználói hozzáférés kezelése](manage-user-access-with-access-reviews.md) és a [Vendéghozzáférés kezelése című](manage-guest-access-with-access-reviews.md)témakörben talál további információt.

Ez a cikk azt ismerteti, hogyan hozhat létre egy vagy több hozzáférési felülvizsgálatot a csoporttagok vagy az alkalmazás-hozzáférés számára.

## <a name="prerequisites"></a>Előfeltételek

- Prémium szintű Azure AD P2
- Globális rendszergazda vagy felhasználó

További információt a [Licenckövetelmények című témakörben talál.](access-reviews-overview.md#license-requirements)

## <a name="create-one-or-more-access-reviews"></a>Egy vagy több hozzáférési ellenőrzés létrehozása

1. Jelentkezzen be az Azure Portalra, és nyissa meg az [Identitáscégcég-kezelő lapot.](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/)

1. A bal oldali menüben kattintson az **Access-ellenőrzések parancsra.**

1. Új hozzáférési felülvizsgálat létrehozásához kattintson az **Új hozzáférés-ellenőrzés** gombra.

    ![Az Access-ellenőrzések ablaktáblája az Identitáskezelő](./media/create-access-review/access-reviews.png)

1. Nevezze el a hozzáférési felülvizsgálatot. Opcionálisan adjon leírást a felülvizsgálatnak. A név és a leírás megjelenik a véleményezők számára.

    ![Hozzáférési ellenőrzés létrehozása - Név és leírás áttekintése](./media/create-access-review/name-description.png)

1. Állítsa be a **kezdési dátumot**. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer történik, a létrehozáskor kezdődik, és egy hónap múlva ér véget. Módosíthatja a kezdési és befejezési dátumokat, hogy a hozzáférési felülvizsgálat a jövőben kezdődjön, és a kívánt hány napig tart.

    ![Hozzáférési ellenőrzés létrehozása – Kezdési és befejezési dátumok](./media/create-access-review/start-end-dates.png)

1. A hozzáférési felülvizsgálat ismétlődővé tenni, módosítsa a **Gyakoriság** beállítást **egy időpontról** **hetire**, **havira,** **negyedévesre**, **félévente**vagy **évente.** Az **Időtartam** csúszka vagy a szövegmező segítségével határozhatja meg, hogy az ismétlődő adatsorok egyes felülvizsgálatai hány napig legyenek megnyitva az ellenőrzők tőlük érkező adatok számára. A havi ellenőrzéshez beállíthatj maximális időtartam például 27 nap, hogy elkerülje az egymást átfedő értékeléseket.

1. A **Befejezés** beállítással megadhatja, hogyan lehet befejezni az ismétlődő hozzáférés-ellenőrzési sorozatot. A sorozat háromféleképpen végződhet: folyamatosan fut, hogy határozatlan ideig, egy adott dátumig vagy meghatározott számú előfordulás befejezéséig kezdje el az értékeléseket. Ön, egy másik felhasználó rendszergazdája vagy egy másik globális rendszergazda leállíthatja a sorozatot a létrehozás után, ha módosítja a dátumot a **Beállítások**ban, hogy az az adott napon végződjön.

1. A **Felhasználók** csoportban adja meg azokat a felhasználókat, akikre a hozzáférési felülvizsgálat vonatkozik. A hozzáférési felülvizsgálatok lehetnek a csoport tagjai, vagy az alkalmazáshoz rendelt felhasználók számára. A hozzáférési felülvizsgálat ot csak azalkalmazáshoz tartozó (vagy az alkalmazáshoz rendelt) vendégfelhasználók áttekintésére is lehetővé teheti, ahelyett, hogy az összes olyan felhasználót ellenőrizné, aki tag, vagy akik hozzáférnek az alkalmazáshoz.

    ![Hozzáférési ellenőrzés létrehozása – Felhasználók](./media/create-access-review/users.png)

1. A **Csoport** csoportban jelöljön ki egy vagy több olyan csoportot, amelynek tagságát felül szeretné vizsgálni.

    > [!NOTE]
    > Ha egynél több csoportot választ ki, több hozzáférési felülvizsgálat jön létre. Ha például öt csoportot választ ki, öt külön hozzáférési felülvizsgálatot hoz létre.
    
    ![Hozzáférési ellenőrzés létrehozása – Csoport kijelölése](./media/create-access-review/select-group.png)

1. Az **Alkalmazások** szakaszban (ha a 8. lépésben kiválasztotta az **alkalmazáshoz rendelve** lehetőséget), válassza ki azokat az alkalmazásokat, amelyekhez át szeretné tekinteni a hozzáférést.

    > [!NOTE]
    > Ha egynél több alkalmazást választ ki, több hozzáférési felülvizsgálatot hoz létre. Ha például öt alkalmazást választ ki, öt külön hozzáférési felülvizsgálatot hoz létre.
    
    ![Hozzáférési ellenőrzés létrehozása – Alkalmazás kiválasztása](./media/create-access-review/select-application.png)

1. Az **Ellenőrzők** csoportban jelöljön ki egy vagy több felhasználót a hatókörösszes felhasználó áttekintéséhez. Vagy választhatja ki, hogy a tagok áttekintsék a saját hozzáférésüket. Ha az erőforrás egy csoport, megkérheti a csoport tulajdonosokat, hogy vizsgálják felül. Azt is megkövetelheti, hogy az ellenőrzők adjanak meg egy okot, amikor jóváhagyják a hozzáférést.

    ![Hozzáférési ellenőrzés létrehozása - Véleményezők](./media/create-access-review/reviewers.png)

1. A **Programok csoportban** jelölje ki a használni kívánt programot. **Az alapértelmezett program** mindig jelen van.

    ![Hozzáférési ellenőrzés létrehozása - Programok](./media/create-access-review/programs.png)

    Leegyszerűsítheti a hozzáférési ellenőrzések különböző célokra történő nyomon követését és gyűjtését, ha programokba szervezi őket. Minden hozzáférési felülvizsgálat programhoz csatolható. Ezt követően, amikor jelentéseket készít egy auditor számára, az adott kezdeményezés hatókörében lévő hozzáférési felülvizsgálatokra összpontosíthat. A programok és a hozzáférés-ellenőrzési eredmények a globális rendszergazda, a rendszergazda, a biztonsági rendszergazda vagy a biztonsági olvasó szerepkör felhasználói számára láthatók.

    A programok listájának megtekintéséhez lépjen a hozzáférési vélemények lapra, és válassza a **Programok lehetőséget.** Ha globális rendszergazdai vagy felhasználói szerepkörben van, további programokat is létrehozhat. Választhatja például, hogy minden megfelelőségi kezdeményezéshez vagy üzleti célhoz egy programot szeretne. Ha már nincs szüksége programra, és nincs hozzá csatolva vezérlő, törölheti azt.

### <a name="upon-completion-settings"></a>A befejezési beállítások után

1. Ha meg szeretné adni, hogy mi történjen az ellenőrzés befejezése után, bontsa ki a **Befejezéskor beállítás szakaszt.**

    ![Hozzáférési ellenőrzés létrehozása – Befejezési beállítások](./media/create-access-review/upon-completion-settings.png)

1. Ha automatikusan el szeretné távolítani a hozzáférést a megtagadott felhasználók számára, állítsa be az **Automatikus alkalmazás az eredményeket az erőforrásra** **az Engedélyezés beállításhoz.** Ha az ellenőrzés befejezésekor manuálisan szeretné alkalmazni az eredményeket, állítsa a kapcsolót **Letiltás gombra.**

1. A **Véleményező nem válaszol** listában adhatja meg, hogy mi történjen azon felhasználók esetében, akiket a véleményező nem ellenőriz a felülvizsgálati időszakon belül. Ez a beállítás nincs hatással azokat a felhasználókat, akiket az ellenőrzők manuálisan ellenőriznek. Ha a végső véleményező döntése Megtagadás, akkor a felhasználó hozzáférése törlődik.

    - **Nincs változás** - Hagyja változatlanul a felhasználó hozzáférését
    - **Hozzáférés eltávolítása** – A felhasználó hozzáférésének eltávolítása
    - **Hozzáférés jóváhagyása** – A felhasználó hozzáférésének jóváhagyása
    - **Javaslatok ajánlásai** – A rendszer ajánlása a felhasználó folyamatos hozzáférésének megtagadására vagy jóváhagyására

### <a name="advanced-settings"></a>Speciális beállítások

1. További beállítások megadásához bontsa ki a **Speciális beállítások szakaszt.**

    ![Hozzáférési ellenőrzés létrehozása – Speciális beállítások](./media/create-access-review/advanced-settings.png)

1. Állítsa be a **Javaslatok megjelenítése** **az Engedélyezés beállításhoz,** hogy a rendszerjavaslatok a felhasználó hozzáférési adatai alapján jelenítse meg a véleményezőknek.

1. Állítsa **be a Jóváhagyás okának megkövetelése** **beállítást: Annak engedélyezése,** hogy a véleményezőnek meg kell adnia a jóváhagyás okát.

1. Állítsa be **a Mail-értesítéseket** **annak engedélyezése** beállításra, hogy az Azure AD e-mail-értesítéseket küldjön a véleményezőknek, amikor egy hozzáférési felülvizsgálat elindul, és a rendszergazdáknak, amikor az ellenőrzés befejeződik.

1. **Emlékeztetők** **beállítása az engedélyezéshez,** hogy az Azure AD emlékeztetők küldése a folyamatban lévő hozzáférési felülvizsgálatokról olyan ellenőrzőknek, akik nem fejezték be az értékelést.

    Alapértelmezés szerint az Azure AD a rendelkezésre álló idő felénél automatikusan emlékeztetőt küld azoknak a felülvizsgálóknak, akik még nem tettek eleget a kérésnek.

## <a name="start-the-access-review"></a>A hozzáférési felülvizsgálat indítása

Miután megadta a hozzáférési ellenőrzés beállításait, kattintson a **Start**gombra. A hozzáférési felülvizsgálat megjelenik a listában az állapotjelzővel.

![A hozzáférési felülvizsgálatok listája és állapotuk](./media/create-access-review/access-reviews-list.png)

Alapértelmezés szerint az Azure AD e-mailt küld az ellenőrzőknek röviddel az ellenőrzés megkezdése után. Ha úgy dönt, hogy nem az Azure AD küldje el az e-mailt, győződjön meg róla, hogy tájékoztassa az ellenőrzők, hogy egy hozzáférési felülvizsgálat vár rájuk, hogy teljes. Megjelenítheti nekik a [csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintésére](perform-access-review.md)vonatkozó utasításokat. Ha az értékelés a vendégek számára, hogy vizsgálja felül a saját hozzáférés, mutasd meg nekik az utasításokat, hogyan [kell áttekinteni a hozzáférést magad csoportok vagy alkalmazások](review-your-access.md).

Ha vendéget rendelt látogatónak, és nem fogadták el a meghívást, akkor nem kapnak e-mailt a hozzáférési ellenőrzésekből, mert először el kell fogadniuk a meghívást a véleményezés előtt.

## <a name="access-review-status-table"></a>Hozzáférés-ellenőrzési állapottábla

| status | Meghatározás |
|--------|------------|
|Nincs beindítva | Az ellenőrzés létrejött, a felhasználói felderítés indításra vár. |
|Inicializálás   | A felhasználófelderítés folyamatban van az ellenőrzés ben részt vevő összes felhasználó azonosítására. |
|Indítás | A felülvizsgálat elkezdődött. Ha az e-mail értesítések engedélyezve vannak, a rendszer e-maileket küld az ellenőrzőknek. |
|Folyamatban | A felülvizsgálat megkezdődött. Ha az e-mail értesítések engedélyezve vannak, e-maileket küldött az ellenőrzőknek. Az ellenőrzők az esedékesség napjáig nyújthatnak be döntéseket. |
|Befejezése | Az ellenőrzés befejeződik, és e-maileket küld a felülvizsgálat tulajdonosának. |
|Automatikus ellenőrzés | Felülvizsgálat van a rendszer felülvizsgálata fázisban. A rendszer rögzíti a döntéseket azon felhasználók számára, akiket nem ajánlások vagy előre konfigurált döntések alapján vizsgáltak felül. |
|Automatikus ellenőrzés alatt | A rendszer minden olyan felhasználó számára rögzítette a döntést, akit nem vizsgáltak meg. Az ellenőrzés készen áll az **Alkalmazás** ra, ha az Automatikus alkalmazás engedélyezve van. |
|Alkalmazása | A jóváhagyott felhasználók hozzáférése nem változik. |
|Alkalmazva | A megtagadott felhasználókat (ha vannak ilyenek) eltávolították az erőforrásból vagy a könyvtárból. |

## <a name="create-reviews-via-apis"></a>Vélemények létrehozása API-kon keresztül

Hozzáférési felülvizsgálatokat API-k használatával is létrehozhat. A csoportok és az alkalmazásfelhasználók hozzáférés-felülvizsgálatainak az Azure Portalon történő kezelésével végzett teendők a Microsoft Graph API-k használatával is elvégezhetők. További információ: az [Azure AD access reviews API-referencia.](https://docs.microsoft.com/graph/api/resources/accessreviews-root?view=graph-rest-beta) A kódminta című [témakörben az Azure AD-hozzáférési felülvizsgálatok Microsoft Graph-on keresztüli beolvasásának példája](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-of-retrieving-Azure-AD-access-reviews-via-Microsoft/m-p/236096)című témakörben van.

## <a name="next-steps"></a>További lépések

- [Csoportokhoz vagy alkalmazásokhoz való hozzáférés áttekintése](perform-access-review.md)
- [Saját hozzáférés áttekintése csoportokhoz vagy alkalmazásokhoz](review-your-access.md)
- [Csoportok vagy alkalmazások hozzáférés-felülvizsgálatának befejezése](complete-access-review.md)

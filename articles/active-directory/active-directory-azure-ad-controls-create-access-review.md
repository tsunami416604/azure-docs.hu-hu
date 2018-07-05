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
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 853d8f09a94e46db218553500a50dc4ef1ec3d23
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448271"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Az Azure AD-csoport tagjainak vagy alkalmazás-hozzáférés hozzáférési felülvizsgálat létrehozása

Hozzáférési hozzárendelések "elavult" válnak, amikor a felhasználók többé nem kell hozzáféréssel rendelkeznek. A régi hozzáférési hozzárendelések kockázatának csökkentése érdekében a rendszergazdák segítségével az Azure Active Directory (Azure AD) csoport tagjai, vagy az alkalmazáshoz hozzárendelt felhasználók hozzáférési felülvizsgálat létrehozása. Ismétlődő a hozzáférési felülvizsgálatok létrehozása, lehet, hogy időt. Ha rendszeresen tekintse át a felhasználókat, akik hozzáférést egy alkalmazáshoz, vagy egy csoport tagjai van szüksége, ezek felülvizsgálatokról azoknak a gyakoriság határozhat meg. Ezekben az esetekben további információkért lásd: [felhasználói hozzáférés felügyelete](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) és [vendégfelhasználói hozzáférés felügyelete](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása

1. Egy globális rendszergazdai vagy a felhasználóifiók-adminisztrátor, nyissa meg a [hozzáférési felülvizsgálatok lapot](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), és válassza ki **programok**.

2. Válassza ki a program, amely tartalmazza a hozzáférési felülvizsgálat vezérlő szeretne létrehozni. **Alapértelmezett Program** mindig jelen, vagy létrehozhat egy másik program. Választhat, például, hogy az egyes megfelelőségi-kezdeményezéshez egy program vagy üzleti cél.

3. Az alkalmazáson belül válassza ki a **vezérlők**, majd válassza ki **Hozzáadás** vezérlőelem felvétele.

4. A hozzáférési felülvizsgálat neve. A felülvizsgálat igény szerint adjon meg leírást. A nevet és leírást a felülvizsgálóknak jelennek meg.

5. Állítsa be a kezdő dátum. Alapértelmezés szerint a hozzáférési felülvizsgálat egyszer végbemegy, létrejön egy időben kezdődik, és egy hónap alatt végződik. Módosíthatja a kezdő és záró dátumát férnek hozzá egy tekintse át kezdő legutóbbi és a jövőben azonban hány nappal kívánja.

6. Ahhoz, hogy a hozzáférési felülvizsgálat ismétlődési, és a egy időpont között gyakoriságának módosítása, hetente, havonta, negyedévente vagy évente, és a csúszka vagy szöveges mező segítségével határozza meg, hány napig az ismétlődő sorozatának kritika nyissa meg a véleményezők bemeneti lesz. Például a maximális időtartamot is megadhatja a havi felülvizsgálat az 27 nap, kerülje az átfedésben lévő értékelések. 

7.  Az ismétlődő hozzáférési felülvizsgálati sorozat 3 módon fejezheti be: folyamatosan felülvizsgálatok határozatlan ideig, amíg egy adott dátumot, vagy egy meghatározott előfordulások száma befejezése után el futtatja. Azt, egy másik felhasználóifiók-adminisztrátor vagy egy másik globális rendszergazda leállíthatja a sorozat létrehozása után a dátumot a beállítások módosításával, hogy az adott dátumon végződik.

8. A hozzáférési felülvizsgálatok lehet a felhasználók számára, akik hozzá lett rendelve egy alkalmazás vagy egy csoport tagjai számára. A hozzáférés tekintse át a felülvizsgálati csak a vendégfelhasználók akik tagjai (vagy az alkalmazáshoz hozzárendel), ahelyett, hogy tekintse át az összes felhasználó számára a tagja, vagy hozzáférni az alkalmazáshoz rendelkező további körét.

9. Tekintse át a hatókörben lévő összes felhasználó egy vagy több személyek kiválasztása. Vagy tekintse át a saját hozzáférését tagokból ki. Ha az erőforrás-csoport, megkérheti a csoport tulajdonosai áttekintéséhez. Azt is megkövetelheti, hogy a felülvizsgálók szabályzataival OK, a hozzáférés jóváhagyása.

10. Ha szeretné manuálisan alkalmazza az eredményeket, ha a felülvizsgálat befejeződött, kattintson a **Start**.  Ellenkező esetben a következő szakasz azt ismerteti, hogyan konfigurálja az automatikus felülvizsgálat alkalmazása.

### <a name="configuring-an-access-review-with-auto-apply"></a>Hozzáférési felülvizsgálat auto-apply konfigurálása

1.  Bontsa ki a befejezést követő művelet beállításai, és engedélyezze az automatikus eredményei alkalmazásának erőforráshoz. 

2.  Azokban az esetekben, ahol felhasználókat nem áttekintett felülvizsgálója ellenőrzi a felülvizsgálati időszak alatt, akkor a hozzáférési felülvizsgálat végrehajtása a megtagadása vagy a felhasználó további hozzáférés jóváhagyását javasolta a rendszer ajánlást (Ha engedélyezve van), hagyja változatlanul a hozzáférésüket, vagy távolítsa el a a hozzáférés. Felhasználók, akik korábban a többi felhasználó által manuálisan – a változás nincs hatással, ha a végső felülvizsgáló döntési megtagadás, akkor a felhasználó hozzáférése törlődni fog.

3.  Ahhoz, hogy a beállítás érvénybe javaslatok kell teszik a felülvizsgálók nem válaszol, bontsa ki a speciális beállításokat, és Show javaslatok engedélyezéséhez.
 
4.  Végül kattintson **Start**.

A beállításokat a befejezést követő művelet beállításai alapján, automatikusan érvénybe lesz a felülvizsgálat záró dátuma, vagy ha manuálisan leállítja a felülvizsgálatot követően hajtható végre. Az állapot, a felülvizsgálat befejezve változik keresztül köztes Államokban például az alkalmazása és végül alkalmazott állapotra. Tekintse meg a letiltott felhasználók, ha bármely, a csoport tagságát és alkalmazás-hozzárendelés néhány perc múlva távolít el kell látnia.


## <a name="manage-the-access-review"></a>A hozzáférési felülvizsgálat kezelése

Alapértelmezés szerint az Azure AD-e-mailt küld felülvizsgálók elindítja a felülvizsgálatot követően rövid időn belül. Ha nem rendelkezik Azure ad-ben az e-mailt, mindenképpen tájékoztatja a felülvizsgálatot, amely a hozzáférési felülvizsgálat várakozik, amíg befejeződnek. Mutathat nekik az utasításokat, hogy hogyan [hozzáférés felülvizsgálata](active-directory-azure-ad-controls-perform-access-review.md). Ha áttekintésre vendégek tekintse át a saját hozzáférését, azokat az utasítások megjelenítése, hogy hogyan [a saját hozzáférés felülvizsgálata](active-directory-azure-ad-controls-perform-access-review.md).

Ha a felülvizsgálók néhány Vendégek, Vendégek értesítést kap e-mailen keresztül csak akkor, ha korábban már fogadta el a meghívót.

A hozzáférési felülvizsgálatok sorozatát kezeléséhez, keresse meg a hozzáférési felülvizsgálatot, az **vezérlők**, és fog közelgő események keresése az ütemezett ellenőrzések, és módosítsa a záró dátumot vagy hozzáadása/eltávolítása felülvizsgálóknak annak megfelelően. 

Követheti a folyamat állapotát, ahogy a felülvizsgálatot, végezze el az Azure ad-ben irányítópultján található áttekintette a **hozzáférési felülvizsgálatok** szakaszban. Nincs hozzáférési jogosultsága változnak, amíg a könyvtárban [a felülvizsgálat befejeződött](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>További lépések

Hozzáférési felülvizsgálat indításakor az Azure ad-ben, amely kéri a hozzáférés felülvizsgálata e-mailt küld automatikusan felülvizsgálók. Ha a felhasználó nem tudták beszerezni egy e-mailt, küldhet nekik az utasításokat, hogy hogyan [hozzáférés felülvizsgálata](active-directory-azure-ad-controls-perform-access-review.md). 

Ha ez egy egyszeri tekintse át, majd a hozzáférési felülvizsgálati időszak felett van, vagy a rendszergazda a hozzáférési felülvizsgálat leállítása után kövesse a lépéseket a [hozzáférési felülvizsgálat befejezése](active-directory-azure-ad-controls-complete-access-review.md) megtekintéséhez és a alkalmazni az eredményeket.  

Ha ez egy felülvizsgálati sorozatot, majd lépjen **előzmények megtekintése** a hozzáférési felülvizsgálati sorozat lapon válassza ki a befejezett hozzáférési felülvizsgálat.  Közelgő felülvizsgálat alatt helyezkednek el **ütemezett felülvizsgálat**, ahol szerkesztheti a megadott időtartam, és hozzáadhat vagy távolítsa el az egyes értékelések részt vevő felülvizsgálóknak.

---
title: Hozzon létre egy csoport vagy a hozzáférést egy alkalmazáshoz, az Azure ad-vel rendelkező felhasználók tagjai egy áttekintése |} Microsoft Docs
description: Megtudhatja, hogyan hozzon létre egy áttekintése egy csoport vagy az alkalmazáshoz hozzáféréssel rendelkező felhasználók tagjai számára.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: compliance-reports
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 20f238a7d8a3882f2a126c900e04ecf7be613be5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2018
ms.locfileid: "37084977"
---
# <a name="create-an-access-review-of-group-members-or-application-access-with-azure-ad"></a>Hozzon létre egy áttekintése csoporttagok vagy alkalmazás-hozzáférés az Azure ad-vel

Hozzáférés hozzárendelések vált "elavult", ha a felhasználók többé nem kell hozzáférést. Elavult hozzáférés hozzárendelések társított kockázatok csökkentése érdekében a rendszergazdák segítségével Azure Active Directory (Azure AD) hozzon létre egy áttekintése csoport tagjainak vagy egy alkalmazás rendelt felhasználók. Ismétlődő hozzáférés értékelést létrehozása, akkor a funkciók. Ha rendszeresen tekintse át a felhasználókat, akik hozzáférést egy alkalmazáshoz vagy a csoport tagjai kell, megadhat ezen ellenőrzések gyakoriságát. Ezek a forgatókönyvek további információkért lásd: [felügyelheti a felhasználók hozzáférését](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md) és [vendég-hozzáférés kezelése](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md). 

## <a name="create-an-access-review"></a>Hozzáférési felülvizsgálat létrehozása

1. Globális rendszergazdaként vagy felhasználói fiók rendszergazdájához, navigáljon a [hozzáférés ellenőrzi, hogy lap](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), és válassza ki **programok**.

2. Válassza ki a program, amely tárolja a felülvizsgálati hozzáférés szeretne létrehozni. **Alapértelmezett Program** mindig jelen, vagy létrehozhat egy másik programot. Választhat, például, hogy minden megfelelőségi Initiative egy program vagy üzleti cél.

3. Válassza ki a programból **vezérlők**, majd válassza ki **Hozzáadás** vezérlő hozzáadása.

4. Neve a áttekintése. A felülvizsgálati szükség esetén adjon meg leírást. A nevet és leírást a felülvizsgáló jelennek meg.

5. Állítsa be a kezdő dátum. Alapértelmezés szerint egy áttekintése többször fordul, létrejön egy időben elindul, és ezzel véget ér, egy hónap. Módosíthatja a kezdő és záró dátumát, az elérhető legyen, ellenőrizze start legutóbbi és a jövőben azonban hány napig szeretné.

6. A hozzáférés felülvizsgálati ismétlődő, gyakoriságának módosítása egy alkalommal a heti, havi, negyedéves vagy évente, és használja, a csúszka vagy szöveges mezőben adja meg, hány napig minden át kell tekinteni a sorozat ismétlődő adatokat a véleményezők lesz. Például a maximális időtartamot tartozó állíthatja be a havi áttekintése: 27 nap értékelést átfedés elkerülése érdekében. 

7.  Az ismétlődő hozzáférés felülvizsgálati adatsorozat 3 módon fejezheti: folyamatosan elindításához értékelést határozatlan ideig, amíg egy adott dátumot, vagy egy megadott előfordulások száma befejezése után futtatja. Azt, egy másik felhasználói fiókot rendszergazda vagy egy másik globális rendszergazda leállításához az adatsorozat létrehozása után a dátum a beállítások módosítása, hogy az adott dátumon végződik.

8. Hozzáférés értékelést lehet egy csoport tagjai számára, vagy a felhasználók számára egy alkalmazás lettek társítva. További hatókörét megadhatja a hozzáférés csak tekintse át a tekintse át a Vendég számára tagok (vagy az alkalmazáshoz hozzárendelt), ahelyett, hogy minden olyan felhasználó, akik tagjai áttekintése vagy rendelkező felhasználók hozzáférni az alkalmazáshoz.

9. Válasszon egy vagy több személyeket, és tekintse át a hatókör összes felhasználóját. Vagy tekintse át a saját hozzáférésüket a tagot is kiválaszthatja. Ha az erőforrás egy csoport, kérje meg a csoportházirend-tulajdonosok áttekintéséhez. Is megkövetelheti, hogy felülvizsgáló OK adnia, amikor azok hagyja jóvá a hozzáférés.

10. Ha manuálisan alkalmazni az eredményeket, a felülvizsgálati befejezésekor, kattintson **Start**.  Ellenkező esetben a következő szakaszban konfigurálását ismerteti a felülvizsgálati automatikus alkalmazása.

### <a name="configuring-an-access-review-with-auto-apply"></a>Egy áttekintése auto-apply konfigurálása

1.  Bontsa ki a menü, befejezett beállításokat, majd engedélyezze automatikus alkalmazni eredmények erőforrás. 

2.  Azokban az esetekben, ahol felhasználók nem elvégezték a véleményező a felülvizsgálati időtartamon belül, akkor a áttekintése (Ha engedélyezve van), csatlakoztassa le a rendszer a javaslat a elutasítása/jóváhagyása is folyamatos hozzáférést biztosíthasson a felhasználó, a hozzáférésüket változatlanul hagyja, vagy távolítsa el a a hozzáférés. Ez hatással lesz a nem lett tekintse át a többi felhasználó által manuálisan – felhasználók esetén a végső felülvizsgáló döntési megtagadás, a felhasználó hozzáférése törlődni fog.

3.  Ahhoz, hogy az a lehetőség ajánlásokat kell felülvizsgálók nem válaszol, bontsa ki a speciális beállításokat, és engedélyezze a javaslatok megjelenítése.
 
4.  Végezetül kattintson **Start**.

Végrehajtási beállítások esetén a megadott beállítások szerint, automatikus-alkalmazni fogja a felülvizsgálati záró dátuma, vagy ha azt manuálisan állítsa le a felülvizsgálati után hajtható végre. A felülvizsgálat állapotának köztes állapota, például az alkalmazása állapotúról befejezve, és végül állapotba történt. Tekintse meg a letiltott felhasználók, ha vannak ilyenek, távolít el a csoport tagságát vagy alkalmazás hozzárendelés néhány perc múlva kell látnia.


## <a name="manage-the-access-review"></a>Kezelheti a áttekintése

Alapértelmezés szerint az Azure AD küld egy e-mailt a felülvizsgálók röviddel a felülvizsgálat indítása után. Ha úgy dönt, hogy ne legyenek az e-mailek küldése az Azure AD, ügyeljen arra, hogy tájékoztatja a felülvizsgálók váró egy áttekintése befejeződnek. Megjelenítheti őket a utasításokkal szolgál [tekintse át a hozzáférési](active-directory-azure-ad-controls-perform-access-review.md). Ha a felülvizsgálati tekintse át a saját hozzáférésüket a Vendégek, megjelenítéséhez a utasításokkal szolgál [tekintse át a saját hozzáférés](active-directory-azure-ad-controls-perform-access-review.md).

Ha a felülvizsgálók némelyike Vendégek, Vendégek értesítést kap e-mailben csak akkor, ha már korábban a meghívó elfogadásának.

Az access ellenőrzések sorozatának kezeléséhez keresse meg a áttekintése a **vezérlők**, és a rendszer jövőbeli keresése az ütemezett értékelést, és szerkesztése a záró dátum, illetve hozzáadása véleményezők megfelelően. 

A végrehajtás, a többi felhasználó végezze el az Azure AD-irányítópulton a áttekintette nyomon a **ellenőrzi, hogy hozzáférési** szakasz. Nincs hozzáférési jogosultsága, módosulnak, amíg a címtár [a felülvizsgálati befejeződött](active-directory-azure-ad-controls-complete-access-review.md).

## <a name="next-steps"></a>További lépések

Egy áttekintése indításakor az Azure AD nekik hozzáférést kérő e-mailt küld automatikusan felülvizsgálók. Ha a felhasználó nem kap egy e-mailt, elküldhetjük a utasításokkal szolgál [tekintse át a hozzáférési](active-directory-azure-ad-controls-perform-access-review.md). 

Ha ez egy egyszeri tekintse át, majd miután a hozzáférés felülvizsgálati időszak keresztül, vagy a rendszergazda leállítja a áttekintése, kövesse a lépéseket a [végrehajtani egy áttekintése](active-directory-azure-ad-controls-complete-access-review.md) megtekintéséhez és alkalmazásához az eredményeket.  

Ha ez tekintse át azokat, majd lépjen **tekintse át az előzmények** hozzáférés felülvizsgálati adatsorozat lapján jelölje be a befejezett áttekintése.  Közelgő felülvizsgálat alatt helyezkednek el **felülvizsgálati ütemezett**, ahol meg az időtartam, szerkesztése és hozzáadhat és eltávolíthat egyéni felülvizsgálati felülvizsgálók.

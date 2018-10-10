---
title: Az Azure AD alkalmazás jóváhagyásának ismertetése során lép |} A Microsoft Docs
description: Ismerje meg, további információt az Azure AD beleegyezik, hogy tekintse meg, hogyan használhatja, amikor a kezelése és az Azure AD-alkalmazások fejlesztéséhez használható kezelőfelülete
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2018
ms.author: celested
ms.reviewer: zawad
ms.openlocfilehash: e3e333594b32cff018f02cd1d3b32eaf44c11a65
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "48904322"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Az Azure AD alkalmazás jóváhagyásának ismertetése során lép fel.

Ismerje meg, további információt az Azure Active Directory (Azure AD-) alkalmazás hozzájárulás megadása a felhasználói élmény. Így intelligensen kezelheti az alkalmazásokat a szervezet számára, illetve a jóváhagyás a zökkenőmentes felhasználói élmény alkalmazások fejlesztéséhez.

## <a name="consent-and-permissions"></a>Jóváhagyás és engedélyek

Hozzájárulás az a folyamat egy felhasználó egy alkalmazást, hogy a felhasználók nevében védett erőforrások eléréséhez engedély. Egy rendszergazda vagy a felhasználó beleegyezését, hogy a vállalat vagy személy adatokhoz való hozzáférés is kéri.

Hozzájárulás a tényleges felhasználói élményét eltér attól függően, a felhasználó bérlőjéhez, a felhasználó hatókörén hatóság (vagy szerepkör) és típusát beállított [engedélyek](https://docs.microsoft.com/azure/active-directory/develop/active-directory-permissions) ügyfélalkalmazás által kért. Ez azt jelenti, hogy az alkalmazásfejlesztők és a bérlői rendszergazdák szabályozhatják, néhány a jóváhagyási felületen. A rendszergazdák rugalmasan és beállítási házirendeket egy bérlő vagy az alkalmazás szabályozhatja a bérlőben a jóváhagyási felületen letiltása. Az alkalmazásfejlesztők is szabályozzák, milyen típusú engedélyeket kérnek, és szeretné-e végigvezetik a felhasználókat a felhasználó hozzájárul a flow vagy a rendszergazdai jóváhagyási folyamatot.

- **Felhasználói jóváhagyási folyamatot** alkalmazásfejlesztő arra utasítja a felhasználók az engedélyezési végponton a célja, hogy csak az aktuális felhasználó rekord jóváhagyás esetén.
- **Rendszergazdai jóváhagyás folyamathoz** akkor, ha a alkalmazásfejlesztő arra utasítja a felhasználók számára a rendszergazdai hozzájárulás célja, hogy a teljes bérlő rekord jóváhagyási végpont. Annak érdekében, hogy a rendszergazdai jóváhagyási folyamat megfelelően működik, az alkalmazásfejlesztők az összes engedélyt kell tüntetniük a `RequiredResourceAccess` tulajdonság az alkalmazásjegyzékben. További információ: [alkalmazásjegyzék](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>A beleegyezést kérő építőelemei

A beleegyezést kérő felhasználók rendelkezzenek elegendő információt határozza meg, ha azokat megbízható-e a felhasználók nevében védett erőforrások eléréséhez az ügyfélalkalmazás célja. Építőelemeket megismerése segít a hozzájárulási ügyeljen biztosítása, további tájékoztatást a döntések és, segít a fejlesztőknek a jobb felhasználói élményt hozhat létre felhasználókat.

A következő ábra és táblázat a beleegyezést kérő építőelemeket információt biztosítanak.

![A beleegyezést kérő építőelemei](./media/application-consent-experience/consent_prompt.png)

| # | Összetevő | Cél |
| ----- | ----- | ----- |
| 1 | Felhasználóazonosító | Ez az azonosító képviseli a felhasználót, hogy az ügyfél alkalmazás nevében védett erőforrások eléréséhez. |
| 2 | Beosztás | A cím módosításainak alapján, hogy a felhasználók a felhasználó vagy rendszergazda hozzájárulását flow szolgáltatáson keresztül fog. A felhasználói jóváhagyási folyamatot a cím lesz "Engedélyeket kért" pedig a rendszergazdai jóváhagyás folyamatban a cím lesz rendelkeznek egy további sort "A szervezet elfogadás". |
| 3 | Alkalmazásembléma | Ez a rendszerkép segíthet a felhasználóknak egy vizuális köteg, hogy az alkalmazás az alkalmazás azokat kívánják elérni. Ez a rendszerkép az alkalmazásfejlesztők által biztosított, és a tulajdonjogát, a lemezkép nincs érvényesítve. |
| 4 | App neve | Ezt az értéket kell tájékoztatják a felhasználókat, hogy mely alkalmazás férhetnek hozzá az adataikhoz. Megjegyzés: ezt a nevet a fejlesztők által biztosított és az alkalmazás neve a tulajdonjogát nem ellenőrzi. |
| 5 | Közzétevő tartománya | Ezt az értéket kell biztosítania a felhasználók értékelhetik a megbízhatósága lehet egy tartomány. Ezt a tartományt a fejlesztők által biztosított, és a rendszer érvényesíti a közzétevő tartomány tulajdonjogát. |
| 6 | Engedélyek | Ez a lista tartalmazza az ügyfélalkalmazás által kért engedélyeket. Felhasználók mindig ki kell értékelni a típusú tudni, hogy milyen adatok eléréséhez a felhasználók nevében, ha elfogadja őket az ügyfélalkalmazás lesz jogosult a kért engedélyeket. Az alkalmazás fejlesztőjeként célszerű hozzáférés kéréséhez, a minimális jogosultságokkal rendelkező az engedélyeket. |
| 7 | Engedély leírása | Ez az érték az engedélyeket a szolgáltatás által biztosított. Szeretné az engedély leírása, akkor mellett az engedélyt a sávnyílra kell váltani. |
| 8 | Alkalmazás-feltételek | Ezeket a feltételeket az alkalmazás és az adatvédelmi nyilatkozat feltételeit hivatkozásokat tartalmaznak. A kiadó felelős szabályainak sbalování a a szolgáltatási feltételeket. Ezenkívül a kiadó felelős a módszert használja, és a felhasználói adatokat az adatvédelmi nyilatkozat a megosztás nyilvánosságra. Ha a közzétevő nem biztosítja ezeket az értékeket mutat, több-bérlős alkalmazásokhoz, félkövér figyelmeztetést a beleegyezést kérő üzenetet a lesz. |
| 9 | https://myapps.microsoft.com | Ez az a hivatkozás, amelyben a felhasználók áttekinthetik és adataik hozzáféréssel rendelkező-Microsoft alkalmazások eltávolításához. |

## <a name="common-consent-scenarios"></a>Jóváhagyás gyakori helyzetek

Az alábbiakban a hozzájárulási megtapasztalhatják, hogy egy felhasználó jelenhetnek meg a jóváhagyási gyakori helyzetek:

1. Egyéni felhasználók számára, amely vezeti őket a felhasználói alkalmazások elérése hozzájárul a folyamat során igénylő egy engedélycsoportot, amely a szolgáltató hatókörén belül van.
    
    1. Rendszergazdák akkor láthatnak, az további vezérlők a hagyományos beleegyezést kérő üzenetet, amely lehetővé teszi jóváhagyás nevében a teljes bérlőn. A vezérlő lesz alapértelmezés szerint megjelenő kapcsolja ki, így csak amikor rendszergazdák explicit módon ellenőrizze a box beleegyeznek kell megadni a nevében a teljes bérlőn. Mai a jelölőnégyzet csak akkor jelenik meg a globális rendszergazdai szerepkörhöz, felhőalapú felügyelet és az alkalmazás felügyeleti nem látják ezt a jelölőnégyzetet.

        ![A forgatókönyv 1a beleegyezést kérő üzenete](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. A felhasználók a hagyományos beleegyezést kérő üzenetet látnak.

        ![Jóváhagyás kérése 1b forgatókönyv](./media/application-consent-experience/consent_prompt_1b.png)

2. Egyéni felhasználók számára, amely kívül esik a hitelesítésszolgáltató hatókörébe tartozó legalább egy engedélyt igénylő alkalmazások elérése.
    1. A rendszergazdák, a fenti 1.i a ugyanazon üzenet jelenik meg.
    2. Felhasználók le lesz tiltva a hozzájárulás az alkalmazáshoz, és kérje meg a rendszergazda az alkalmazáshoz való hozzáféréshez használ. 
                
        ![Jóváhagyás kérése 1b forgatókönyv](./media/application-consent-experience/consent_prompt_2b.png)

3. Személyeket, keresse meg, vagy a rendszergazda a rendszer átirányítja a folyamat jóváhagyás.
    1. Rendszergazda felhasználók a rendszergazda beleegyezést kérő üzenetet jelenik meg. A cím és a megváltoztatja a a kérdés, a módosítások jelölje ki azt a tényt, hogy a válasszal megkapja az alkalmazás elérése a kért adatokat a teljes bérlő nevében engedély leírása.
        
        ![Jóváhagyás kérése 1b forgatókönyv](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. A nem rendszergazda felhasználók ugyanazon a képernyőn megjelenik a fenti 2. II.

## <a name="next-steps"></a>További lépések
- Részletes áttekintést [hogyan valósítja meg az Azure ad-ben hozzájárulási keretrendszer az hozzájárulási](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#overview-of-the-consent-framework).
- Ismerje meg részletesebben olvashat róluk, a [több-bérlős alkalmazás használatát a hozzájárulási keretrendszer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent) megvalósítása a "user" és "rendszergazda" engedélyt, támogató további speciális többrétegű alkalmazásminták.
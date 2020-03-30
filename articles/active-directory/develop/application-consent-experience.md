---
title: Az Azure AD alkalmazás hozzájárulási élménye
titleSuffix: Microsoft identity platform
description: További információ az Azure AD hozzájárulási élményéről, amelyből megtudhatja, hogyan használhatja azt az Azure AD-n futó alkalmazások kezelése és fejlesztése során
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c9b449b65a8f8def9dc28a668cd9ee3671124cb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484502"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Az Azure AD-alkalmazások hozzájárulási folyamatának ismertetése

További információ az Azure Active Directory (Azure AD) alkalmazás hozzájárulása felhasználói élményről. Így intelligensen kezelheti a szervezetéhez készült alkalmazásokat, és/vagy zökkenőmentesebb hozzájárulási felülettel fejleszthet alkalmazásokat.

## <a name="consent-and-permissions"></a>Hozzájárulás és engedélyek

A hozzájárulás az a folyamat, amelynek során a felhasználó engedélyt ad egy alkalmazásnak a védett erőforrásokhoz való hozzáférésre a nevükben. A rendszergazda vagy a felhasználó beleegyezését kérheti, hogy engedélyezze a hozzáférést a szervezet / egyéni adatok.

A hozzájárulás megadásának tényleges felhasználói élménye a felhasználó bérlőjéhez beállított házirendektől, a felhasználó jogosultsági körétől (vagy szerepkörétől) és az ügyfélalkalmazás által kért [engedélyek](https://docs.microsoft.com/azure/active-directory/azuread-dev/v1-permissions-consent) típusától függően változik. Ez azt jelenti, hogy az alkalmazásfejlesztők és a bérlői rendszergazdák némi ellenőrzést gyakorolnak a jóváhagyási élmény felett. A rendszergazdák rugalmasan beállíthatják és letilthatják a házirendeket egy bérlőn vagy alkalmazásban a bérlői hozzájárulási élmény szabályozásához. Az alkalmazásfejlesztők diktálhatják, hogy milyen típusú engedélyeket kérnek, és hogy szeretnék-e végigvezetni a felhasználókat a felhasználói hozzájárulási folyamaton vagy a rendszergazdai hozzájárulási folyamaton.

- **A felhasználói hozzájárulási folyamat** az, amikor egy alkalmazásfejlesztő az engedélyezési végpontra irányítja a felhasználókat azzal a szándékkal, hogy csak az aktuális felhasználó beleegyezését rögzítse.
- **Rendszergazdai hozzájárulási folyamat,** amikor egy alkalmazás fejlesztője irányítja a felhasználókat a rendszergazdai hozzájárulási végpont azzal a szándékkal, hogy rekord hozzájárulás a teljes bérlő. A rendszergazdai hozzájárulási folyamat megfelelő működésének biztosításához `RequiredResourceAccess` az alkalmazásfejlesztőknek fel kell sorolniuk az összes engedélyt az alkalmazásjegyzékben a tulajdonságban. További információ: [Application manifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest).

## <a name="building-blocks-of-the-consent-prompt"></a>A hozzájárulási gyors elemek

A hozzájárulási parancs célja annak biztosítása, hogy a felhasználók elegendő információval rendelkezzenek annak meghatározásához, hogy megbíznak-e abban, hogy az ügyfélalkalmazás hozzáférjen a védett erőforrásokhoz a nevükben. Az építőelemek megértése segít a beleegyezést adó felhasználóknak megalapozottabb döntéseket hozni, és segít a fejlesztőknek a jobb felhasználói élmény kialakításában.

Az alábbi ábra és táblázat a hozzájárulási parancs építőelemeiről nyújt tájékoztatást.

![A hozzájárulási gyors elemek](./media/application-consent-experience/consent_prompt.png)

| # | Összetevő | Cél |
| ----- | ----- | ----- |
| 1 | Felhasználói azonosító | Ez az azonosító azt a felhasználót jelöli, akitől az ügyfélalkalmazás a védett erőforrásokhoz való hozzáférést kéri. |
| 2 | Cím | A cím attól függően változik, hogy a felhasználók a felhasználó vagy a rendszergazda hozzájárulási folyamatán mennek-e keresztül. A felhasználói hozzájárulási folyamatban a cím "Kért engedélyek" lesz, míg a rendszergazdai hozzájárulási folyamatban a cím egy további "Elfogadás a szervezethez" sorból áll. |
| 3 | Alkalmazás emblémája | Ez a kép segít a felhasználóknak egy vizuális jelzést arról, hogy ez az alkalmazás az az alkalmazás, amelyet el akartak érni. Ezt a lemezképet az alkalmazásfejlesztők biztosítják, és a lemezkép tulajdonjoga nem érvényesítve. |
| 4 | App neve | Ennek az értéknek tájékoztatnia kell a felhasználókat arról, hogy melyik alkalmazás kér hozzáférést az adataikhoz. Vegye figyelembe, hogy ezt a nevet a fejlesztők adják meg, és az alkalmazásnév tulajdonjoga nem érvényesítve. |
| 5 | Közzétevő tartomány | Ennek az értéknek olyan tartományt kell biztosítania a felhasználóknak, amelyet megbízhatónak értékelhetnek. Ezt a tartományt a fejlesztők biztosítják, és a közzétevői tartomány tulajdonjogát érvényesíti. |
| 6 | Engedélyek | Ez a lista az ügyfélalkalmazás által kért engedélyeket tartalmazza. A felhasználóknak mindig ki kell értékelniük, hogy milyen típusú engedélyeket kérnek annak megértéséhez, hogy az ügyfélalkalmazás milyen adatokhoz férhet hozzá a nevükben, ha elfogadják. Alkalmazásfejlesztőként a legjobb, ha hozzáférést kér a legkevesebb jogosultsággal rendelkező engedélyekhez. |
| 7 | Engedély leírása | Ezt az értéket az engedélyeket felfedő szolgáltatás biztosítja. Az engedélyleírások megtekintéséhez az engedély mellett be kell váltania a sávot. |
| 8 | Alkalmazáskifejezések | Ezek a feltételek az alkalmazás szolgáltatási feltételeire és adatvédelmi nyilatkozatára mutató hivatkozásokat tartalmaznak. A kiadó felelős a szabályok nak a szolgáltatási feltételeikben való felvázolásáért. Ezenkívül a közzétevő felelős azért, hogy az adatvédelmi nyilatkozatában felfedje a felhasználói adatok felhasználásának és megosztásának módját. Ha a közzétevő nem biztosít hivatkozásokat ezekre az értékekre a több-bérlős alkalmazások, lesz egy félkövér figyelmeztetés a jóváhagyási parancs. |
| 9 | https://myapps.microsoft.com | Ez az a hivatkozás, ahol a felhasználók áttekinthetik és eltávolíthatják azokat a nem microsoftos alkalmazásokat, amelyek jelenleg hozzáférnek az adataikhoz. |

## <a name="common-consent-scenarios"></a>Közös hozzájárulási forgatókönyvek

A közös jóváhagyási forgatókönyvekben a felhasználó által látott hozzájárulási élmények a következők:

1. Olyan alkalmazáshoz hozzáférő személyek, amely a felhasználói hozzájárulási folyamathoz irányítja őket, miközben olyan engedélykészletet igényelnek, amely a hatáskörükbe tartozik.
    
    1. A rendszergazdák egy további vezérlőt fognak látni a hagyományos jóváhagyási parancs, amely lehetővé teszi számukra a teljes bérlő nevében beleegyezését. A vezérlő alapértelmezés szerint ki lesz kapcsolva, így csak akkor, ha a rendszergazdák kifejezetten bejelöli a jelölőnégyzetet, a teljes bérlő nevében adják meg a jóváhagyást. A mai nappal ez a jelölőnégyzet csak a Globális rendszergazdai szerepkörhöz jelenik meg, így a Felhőalapú rendszergazda és az alkalmazásadminisztrátor nem fogja látni ezt a jelölőnégyzetet.

        ![Hozzájárulási kérdés az 1a.](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. A felhasználók a hagyományos hozzájárulási kérést fogják látni.

        ![Hozzájárulási kérdés az 1b.](./media/application-consent-experience/consent_prompt_1b.png)

2. Olyan alkalmazáshoz hozzáférő személyek, amelyekhez legalább egy, a hatáskörükön kívül eső engedély szükséges.
    1. Adminok fogja látni ugyanazt a kérdést, mint 1.i fent látható.
    2. A felhasználók nem adhatnak engedélyt az alkalmazáshoz, és azt fogják kérni a rendszergazdától, hogy férhessen hozzá az alkalmazáshoz. 
                
        ![Hozzájárulási kérdés az 1b.](./media/application-consent-experience/consent_prompt_2b.png)

3. Azok a személyek, akik navigálnak, vagy a rendszergazdai hozzájárulási folyamathoz irányítják őket.
    1. A rendszergazdai felhasználók látni fogják a rendszergazdai hozzájárulási figyelmeztetést. A cím és az engedély leírása megváltozott ezen a kérdésen, a módosítások kiemelik azt a tényt, hogy a kérdés elfogadása hozzáférést biztosít az alkalmazásnak a kért adatokhoz a teljes bérlő nevében.
        
        ![Hozzájárulási kérdés az 1b.](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. A nem admin felhasználók ugyanazt a képernyőt fogják látni, mint a fenti 2.ii.

## <a name="next-steps"></a>További lépések
- Részletes áttekintést kaphat [arról, hogy az Azure AD-hozzájárulási keretrendszer hogyan valósítja meg a beleegyezést.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)
- További részletes, megtudhatja, [hogyan egy több-bérlős alkalmazás használhatja a jóváhagyási keretet](active-directory-devhowto-multi-tenant-overview.md) a "felhasználó" és a "rendszergazda" hozzájárulás megvalósításához, támogatva a fejlettebb többrétegű alkalmazásminták.
- További információ [az alkalmazás közzétevői tartományának konfigurálásáról.](howto-configure-publisher-domain.md)

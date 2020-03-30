---
title: Új alkalmazásregisztráció az Azure Portalon – képzési útmutató
description: Bemutatja az Azure Portal alkalmazásregisztrációs felületét
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154576"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Új alkalmazásregisztráció az Azure Portalon – képzési útmutató

Az azure-portálon számos fejlesztést talál az új [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) élményében. Ha ismeri az alkalmazásregisztrációk (örökölt) élményét az Azure Portalon, használja ezt a képzési útmutatót az új felület használatának megkezdéséhez.

Az Azure Active Directoryban az itt leírt új alkalmazásregisztrációs élmény általánosan elérhető. Az Azure Active Directory B2C (Azure AD B2C) ez a felület előzetes verzióban érhető el.

## <a name="key-changes"></a>Főbb változások

- Az alkalmazásregisztrációk nem korlátozódnak arra, hogy *webalkalmazás/API* vagy *natív* alkalmazás. Ugyanazt az alkalmazásregisztrációt használhatja az összes ilyen alkalmazáshoz a megfelelő átirányítási URI-k regisztrálásával.

- A korábbi élmény támogatott alkalmazások, amelyek csak szervezeti (Azure AD) fiókokkal jelentkeznek be. Az alkalmazások egybérlősként voltak regisztrálva. Az alkalmazások csak a szervezeti fiókokat támogatták abból a címtárból, amelyben az alkalmazás regisztrálva volt. Az alkalmazások több-bérlősek lehetnek, és minden szervezeti fiókot támogatnak. Az új felület lehetővé teszi olyan alkalmazások regisztrálását, amelyek mindkét lehetőséget támogatják, valamint egy harmadik lehetőséget is: az összes szervezeti fiókot és a személyes Microsoft-fiókokat.

- Az örökölt élmény csak akkor érhető el, ha egy szervezeti fiók használatával jelentkezett be az Azure Portalra. Az új felület, használhatja a személyes Microsoft-fiókok, amelyek nincsenek társítva a címtárhoz.

## <a name="list-of-applications"></a>Alkalmazások listája

Az új alkalmazáslista azokat az alkalmazásokat jeleníti meg, amelyeket az Azure Portalon az örökölt alkalmazásregisztrációk on keresztül regisztráltak. Ezek az alkalmazások az Azure AD-fiókok használatával jelentkeznek be. Az új alkalmazáslista az alkalmazásregisztrációs portálon keresztül regisztrált alkalmazásokat is megjeleníti. Ezek az alkalmazások az Azure AD és a személyes Microsoft-fiókok használatával jelentkeznek be.

>[!NOTE]
>Az alkalmazásregisztrációs portál elavult.

Az új alkalmazáslista nem rendelkezik **alkalmazástípus-oszloppal,** mivel egyetlen alkalmazásregisztráció több típusból is kiadható. A lista két további oszlopot tartalmaz: **Létrehozva** és **tanúsítványok & titkos kulcsok**. **A titkos kulcsok & tanúsítványok** az alkalmazásban regisztrált hitelesítő adatok állapotát jelenítik meg. Az állapotok közé tartozik **az Aktuális**, **hamarosan lejáró**és **lejárt**.

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

Az örökölt élményben egy olyan alkalmazás regisztrálásához, amelyet meg kellett adnia: **Név**, **Alkalmazástípus**és **Bejelentkezési URL/átirányítási URI**. A létrehozott alkalmazások csak egybérlős Azure-alkalmazások voltak. Csak abból a címtárból támogatták a szervezeti fiókokat, amelyben az alkalmazás regisztrálva volt.

Az új felületen meg kell adnia az alkalmazás **nevét,** és ki kell választania a **Támogatott fióktípusokat.** Tetszés szerint megadhat **átirányítási URI-t**is. Ha átirányítási URI-t ad meg, meg kell adnia, hogy az webes/nyilvános (mobil és asztali)-e. További információt a [Rövid útmutató: Alkalmazás regisztrálása a Microsoft identity platformmal című témakörben talál.](quickstart-register-app.md) Az Azure AD B2C-ről az [Alkalmazás regisztrálása az Azure Active Directory B2C-ben (Alkalmazás regisztrálása az Azure Active Directory B2C-ben) témakörben található.](../../active-directory-b2c/tutorial-register-applications.md)

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Az alkalmazásregisztrációs portál és az alkalmazásregisztrációk lap közötti különbségek

### <a name="the-legacy-properties-page"></a>Az örökölt tulajdonságok lap

Az örökölt élménynek volt egy **Tulajdonságok** lapja. **A tulajdonságok** a következő mezőkkel rendelkeztek:

- **Név**
- **Objektumazonosító**
- **Alkalmazásazonosító**
- **Alkalmazásazonosító URI**
- **Embléma**
- **Kezdőlap URL-címe**
- **Kijelentkezés URL-címe**
- **Szolgáltatási feltételek URL-címe**
- **Az adatvédelmi nyilatkozat URL-címe**
- **Alkalmazás típusa**
- **Több-bérlős**

Az új felület nem rendelkezik az adott oldallal. Itt találja az egyenértékű funkciókat:

- **A név**, **az embléma,** **a kezdőlap URL-címe,** **a szolgáltatási feltételek URL-címe**és az **adatvédelmi nyilatkozat URL-címe** most már szerepel az alkalmazás **márkajelzési** oldalán.
- **Az objektumazonosító** és **az ügyfélazonosító** az **Áttekintő** lapon található.
- Az örökölt környezetben a **több-bérlős** váltás által vezérelt funkciókat a **Hitelesítés** lapon **támogatott fióktípusok** váltották fel. További információt a [Rövid útmutató: Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)című témakörben talál.
- **A kijelentkezési URL most** már a **Hitelesítés** lapon található.
- **Az alkalmazástípus** már nem érvényes mező. Ehelyett irányítsa át az URI-kat, amelyek a **Hitelesítés** lapon találhatók, határozza meg, hogy mely alkalmazástípusok támogatottak.
- **Az Alkalmazásazonosító URI-t** most **antól alkalmazásazonosító URI-nak** hívják, és megtalálhatja **az API-k at elérhetővé**téve. Az örökölt felületen ezt a tulajdonságot a `https://{tenantdomain}/{appID}`következő formátumban regisztrálták automatikusan: például `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Az új felületen automatikusan `api://{appID}`generálódik, de explicit módon menteni kell. Az Azure AD B2C-bérlők, a `https://{tenantdomain}/{appID}` formátum továbbra is használatos.

### <a name="reply-urlsredirect-urls"></a>Válasz URL-ek/átirányítási URL-ek

Az örökölt élményben egy alkalmazásnak volt **válasz URL-címe.** Az új felületen a válasz URL-címei az alkalmazás **hitelesítési** oldalán találhatók. Most már **átirányítási URI-knak nevezzük őket.**

Az átirányítási URI-k formátuma megváltozott. Egy alkalmazástípushoz kell társíthatók, legyen az webes vagy nyilvános. Biztonsági okokból a `http://` helyettesítő karakterek és sémák nem támogatottak, kivéve a. *http://localhost*

### <a name="keyscertificates--secrets"></a>Kulcsok/tanúsítványok & titkos kulcsok

Az örökölt élményben egy alkalmazás **Keys lappal** rendelkezett. Az új felületen **tanúsítványok& titokra**lett átnevezve.

**A nyilvános kulcsokat** most **antól tanúsítványoknak nevezzük.** **A jelszavakat** **mostantól ügyféltitoknak nevezzük.**

### <a name="required-permissionsapi-permissions"></a>Szükséges engedélyek/API-engedélyek

Az örökölt élményben egy alkalmazás rendelkezik a **szükséges engedélyekkel rendelkező** lappal. Az új felületen **átnevezték API-engedélyekre.**

Ha kiválasztott egy API-t az örökölt élményben, a Microsoft API-k kis listájából választhat. A bérlőben is kereshet a szolgáltatásnévi tagok között. Az új felületen több lap közül választhat: **Microsoft API-k**, **a szervezet által használt API-k**vagy **saját API-k**közül. A keresési sáv az **API-k** a szervezet használ lap keresések a bérlőszolgáltatás-egyszerű szolgáltatás.

> [!NOTE]
> Ez a lap nem jelenik meg, ha az alkalmazás nincs bérlőhöz társítva. Az engedélyek kérésének módjáról további információt a [Rövid útmutató: Ügyfélalkalmazás konfigurálása webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md)című témakörben talál.

Az örökölt felületen a **Kért engedélyek** lap tetején található **Grant permissions** button. Az új felületen a **Grant consent** oldalon található egy Grant **admin consent** gomb az alkalmazás **API-engedélyeinek** szakaszán. Van még néhány különbség a gombok működésében.

Az örökölt élményben a logika a bejelentkezett felhasználótól és a kért engedélyektől függően változott. A logika a következő volt:

- Ha csak a felhasználó beleegyezését biztosító engedélyeket kérte, és a bejelentkezett felhasználó nem volt rendszergazda, a felhasználó engedélyt adhat a felhasználónak a kért engedélyekhez.
- Ha legalább egy rendszergazdai jóváhagyást igénylő engedélyt kértek, és a bejelentkezett felhasználó nem rendszergazda volt, a felhasználó hibaüzenetet kapott a hozzájárulás megkísérlésekénél.
- Ha a bejelentkezett felhasználó rendszergazda volt, rendszergazdai jóváhagyást kapott az összes kért engedélyhez.

Az új felületen csak egy rendszergazda adhat hozzájárulást. Ha egy rendszergazda a **Rendszergazdai hozzájárulás megadása**lehetőséget választja, az összes kért engedélyhez rendszergazdai hozzájárulás adható meg.

## <a name="deleting-an-app-registration"></a>Alkalmazásregisztráció törlése

Az örökölt környezetben csak egybérlős alkalmazások törölhetők. A törlés gomb le volt tiltva a több-bérlős alkalmazások esetében. Az új felületen bármilyen állapotban törölheti az alkalmazásokat, de meg kell erősítenie a műveletet. További információ: [Rövid útmutató: A Microsoft identity platformmal regisztrált alkalmazás eltávolítása](quickstart-remove-app.md)című témakörben talál.

## <a name="application-manifest"></a>Alkalmazásjegyzék

Az örökölt és az új tapasztalatok különböző verziókat használnak a JSON formátumához a jegyzékszerkesztőben. További információ: [Azure Active Directory app manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület

Az új felület a következő tulajdonságok felhasználói felületi vezérlőit adja hozzá:

- A **Hitelesítés** lap implicit`oauth2AllowImplicitFlow`támogatási **folyamattal** ( rendelkezik). Az örökölt élménytől eltérően **access-jogkivonatokat** vagy **azonosítójogkivonatokat, vagy mindkettőt engedélyezhet.**
- Az **API-k nak elérhető** lap tartalmazza az API (`oauth2Permissions`)`preAuthorizedApplications`és a Authorized client applications ( ( **által definiált hatóköröket.** **Authorized client applications** Ha többet szeretne tudni arról, hogy miként konfigurálhat egy alkalmazást webes API-ként, és hogyan teheti elérhetővé az engedélyeket/hatóköröket, olvassa [el a Rövid útmutató: Alkalmazás konfigurálása webes API-k felfedésére című témakörben.](quickstart-configure-app-expose-web-apis.md)
- A **Márkajelzés** lap a **Publisher tartományt**tartalmazza. A közzétevői tartomány az [alkalmazás hozzájárulási parancsában](application-consent-experience.md)jelenik meg a felhasználók számára. További információ: [Hogyan: Az alkalmazás közzétevői tartományának konfigurálása.](howto-configure-publisher-domain.md)

## <a name="limitations"></a>Korlátozások

Az új felület a következő korlátozásokkal rendelkezik:

- Az ügyféltitkok (alkalmazásjelszavak) formátuma eltér az örökölt élményformátumáttól, és megszakíthatja a CLI-t.
- A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazásjegyzéket használnia kell, kivéve, ha az Egybérlős Azure-beli és a több-bérlős Azure AD között vált.

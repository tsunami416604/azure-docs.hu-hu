---
title: Új alkalmazásregisztráció az Azure Portalon – képzési útmutató
description: Bevezeti az új Azure Portal alkalmazás regisztrációs élményét
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80154576"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Új alkalmazásregisztráció az Azure Portalon – képzési útmutató

Számos újdonságot talál a Azure Portal új [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felületén. Ha már ismeri a Azure Portal Alkalmazásregisztrációk (örökölt) felületét, az új felület használatának megkezdéséhez használja ezt a tanítási útmutatót.

Azure Active Directory az itt ismertetett új alkalmazás-regisztrációs élmény általánosan elérhető (GA). Azure Active Directory B2C (Azure AD B2C) Ez a felhasználói élmény előzetes verzióban érhető el.

## <a name="key-changes"></a>Legfontosabb változások

- Alkalmazásregisztrációk nem kizárólag *webalkalmazás/API* vagy *natív* alkalmazás lehet. A megfelelő átirányítási URI-k regisztrálásával ugyanazt az alkalmazás-regisztrációt használhatja az összes alkalmazáshoz.

- Az örökölt támogatott alkalmazások csak szervezeti (Azure AD-) fiókok használatával jelentkeznek be. Az alkalmazások egybérlős fájlként lettek regisztrálva. Az alkalmazások csak az alkalmazás által a címtárban regisztrált szervezeti fiókokat támogatják. Az alkalmazások úgy módosíthatók, hogy több-bérlős és támogassa az összes szervezeti fiókot. Az új felület lehetővé teszi olyan alkalmazások regisztrálását, amelyek támogatják mindkét lehetőséget, valamint egy harmadik lehetőséget: minden szervezeti fiókot és személyes Microsoft-fiókot is.

- A régi felhasználói felület csak akkor érhető el, ha szervezeti fiókkal jelentkezett be a Azure Portalba. Az új felülettel olyan személyes Microsoft-fiókokat is használhat, amelyek nem egy címtárhoz vannak társítva.

## <a name="list-of-applications"></a>Alkalmazások listája

Az új alkalmazás lista az Azure Portal korábbi alkalmazás-regisztrációi során regisztrált alkalmazásokat jeleníti meg. Ezek az alkalmazások az Azure AD-fiókok használatával jelentkeznek be. Az új alkalmazás listában az alkalmazás regisztrációs portálján regisztrált alkalmazások is megjelennek. Ezek az alkalmazások az Azure AD és a személyes Microsoft-fiókok használatával jelentkeznek be.

>[!NOTE]
>Az alkalmazás regisztrációs portálja elavult.

Az új alkalmazás-lista nem tartalmaz **Application Type** oszlopot, mert egyetlen alkalmazás regisztrálása többféle típus lehet. A listán két további oszlop található: **a** (z) és a **tanúsítványok & Secrets**. A **& Secrets tanúsítványok** az alkalmazásban regisztrált hitelesítő adatok állapotát jelenítik meg. Az állapotok közé tartozik az **aktuális**, **hamarosan lejár**és **lejárt**.

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

A régi felhasználói felületen a szükséges alkalmazások regisztrálásához a következőket kell megadnia: **név**, **alkalmazás típusa**, **bejelentkezési URL-cím/átirányítási URI**. A létrehozott alkalmazások az Azure AD-ben csak egyetlen bérlős alkalmazások voltak. Csak a címtárban az alkalmazásban regisztrált szervezeti fiókok támogatottak.

Az új felhasználói felületen meg kell adnia egy **nevet** az alkalmazásnak, és ki kell választania a **támogatott fióktípus**lehetőséget. Igény szerint **átirányítási URI**-t is megadhat. Ha átirányítási URI-t ad meg, meg kell adnia, hogy webes vagy nyilvános (mobil és asztali). További információ: gyors útmutató [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md). Azure AD B2C esetében lásd: [alkalmazás regisztrálása Azure Active Directory B2Cban](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Az alkalmazás regisztrációs portálja és a Alkalmazásregisztrációk lap közötti különbségek

### <a name="the-legacy-properties-page"></a>Az örökölt tulajdonságok lap

A régi felület **tulajdonságlapja** volt. A **Tulajdonságok** a következő mezőkkel rendelkeznek:

- **Név**
- **Objektumazonosító**
- **Alkalmazás azonosítója**
- **Alkalmazásazonosító URI**
- **Embléma**
- **Kezdőlap URL-címe**
- **Kijelentkezési URL-cím**
- **Szolgáltatási feltételek URL-címe**
- **Az adatvédelmi nyilatkozat URL-címe**
- **Alkalmazás típusa**
- **Több-bérlős**

Az új felület nem rendelkezik ezzel az oldallal. Itt találja a megfelelő funkciót:

- A **név**, az **embléma**, a **Kezdőlap URL**-címe, a **szolgáltatási feltételek URL**-címe és az **adatvédelmi nyilatkozat URL-címe** mostantól az alkalmazás **védjegyezési** oldalán található.
- Az **objektum-azonosító** és az **alkalmazás (ügyfél) azonosítója** az **Áttekintés** oldalon található.
- A **több-bérlős** váltás az örökölt felhasználói felület által vezérelt funkcióit a **hitelesítés** lapon **támogatott fióktípus** váltotta fel. További információ: gyors útmutató [: alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md).
- A **kijelentkezési URL-cím** mostantól a **hitelesítés** oldalon található.
- Az **alkalmazás típusa** már nem érvényes mező. Ehelyett átirányítási URI-k, amelyek a **hitelesítés** oldalon találhatók, meghatározhatja, hogy mely alkalmazások támogatottak.
- Az alkalmazás- **azonosító URI** -ja már **Application ID URI** -ként van meghívva, és megtalálhatja az API-k **elérhetővé**tétele céljából. A régi élményben ez a tulajdonság a következő formátumban lett automatikus regisztrálva:, `https://{tenantdomain}/{appID}`például: `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. Az új felhasználói élményben az automatikusan létrejön `api://{appID}`, de explicit módon el kell menteni. Azure AD B2C bérlők esetében a `https://{tenantdomain}/{appID}` formátum továbbra is használatban van.

### <a name="reply-urlsredirect-urls"></a>Válasz URL-címek/átirányítási URL-címek

A régi élményben az alkalmazásnak volt egy **Válasz URL-címe** . Az új felhasználói felületen a válasz URL-címei az alkalmazás **hitelesítési** oldalán találhatók. Ezek az úgynevezett **átirányítási URI**-k.

Az átirányítási URI-k formátuma megváltozott. Szükség van egy webes vagy nyilvános alkalmazás-típushoz való társításra. Biztonsági okokból a helyettesítő karakterek és `http://` sémák nem támogatottak, kivéve *http://localhost*a következőt:.

### <a name="keyscertificates--secrets"></a>Kulcsok/tanúsítványok & Secrets

A régi verzióban az alkalmazás **kulcsok** lapja. Az új felhasználói élményben átnevezték a **tanúsítványokat & titkokat**.

A **nyilvános kulcsokat** már **tanúsítványoknak**nevezzük. A **jelszavakat** a rendszer az **ügyfél titkos**kulcsaként említi.

### <a name="required-permissionsapi-permissions"></a>Szükséges engedélyek/API-engedélyek

A régi felhasználói felületen az alkalmazásnak megfelelő **engedélyekkel kell** rendelkeznie. Az új felhasználói felületen átnevezték az **API-engedélyeket**.

Ha az örökölt felületen kiválasztott egy API-t, a Microsoft API-k kisméretű listájából választhat. A bérlőn keresztül is kereshet az egyszerű szolgáltatások között. Az új felhasználói élményben több lapból is választhat: a **Microsoft API**-k, **a szervezet által használt API**-k vagy **az API**-k. A szervezet által használt **API** -k keresési sávján a szolgáltatás a bérlőn keresztüli egyszerű keresést végez.

> [!NOTE]
> Ez a lap nem jelenik meg, ha az alkalmazás nincs bérlőhöz társítva. Az engedélyek igénylésével kapcsolatos további információkért lásd: rövid útmutató [: ügyfélalkalmazás konfigurálása a webes API-k eléréséhez](quickstart-configure-app-access-web-apis.md).

A korábbi felhasználói élmény a **kért engedélyek** lap tetején található **engedélyezési engedélyek** gomb. Az új felhasználói felületen az engedély **jóváhagyása** lapon az alkalmazás **API-engedélyei** szakaszának engedély- **rendszergazdai jóváhagyása** gombja szerepel. Bizonyos különbségek vannak a gombok függvényének módjaival is.

A régi felhasználói élményben a logika a bejelentkezett felhasználótól és a kért engedélytől függően változhat. A logikája:

- Ha a felhasználó csak a felhasználói beleegyező jogosultságokat kéri, és a bejelentkezett felhasználó nem rendszergazda, a felhasználó a kért engedélyekhez felhasználói jóváhagyást biztosíthat.
- Ha legalább egy rendszergazdai jóváhagyást igénylő engedély kérése megtörtént, és a bejelentkezett felhasználó nem rendszergazda, a felhasználó hibaüzenetet kapott a jóváhagyás megadására tett kísérlet során.
- Ha a bejelentkezett felhasználó rendszergazdai jogosultsággal rendelkezik, a rendszer rendszergazdai beleegyezett az összes kért engedélyhez.

Az új felhasználói élményben csak a rendszergazda adhat meg jóváhagyást. Ha egy rendszergazda kiválasztja a **rendszergazdai jóváhagyást**, a rendszergazda beleegyezik az összes kért engedélybe.

## <a name="deleting-an-app-registration"></a>Alkalmazás regisztrációjának törlése

A régi élményben csak egyetlen bérlős alkalmazást törölhet. A törlés gomb a több-bérlős alkalmazások esetében le lett tiltva. Az új szolgáltatásban bármilyen állapotban törölheti az alkalmazásokat, de meg kell erősítenie a műveletet. További információ: gyors útmutató [: a Microsoft Identity platformmal regisztrált alkalmazás eltávolítása](quickstart-remove-app.md).

## <a name="application-manifest"></a>Alkalmazásjegyzék

Az örökölt és az új tapasztalatok a JSON formátumának különböző verzióit használják a jegyzékfájl-szerkesztőben. További információ: [Azure Active Directory app manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület

Az új kezelőfelület felhasználói felületi vezérlőket biztosít a következő tulajdonságokhoz:

- A **hitelesítés** lapon **implicit engedélyezési folyamat** (`oauth2AllowImplicitFlow`) szerepel. A régi élménytől eltérően engedélyezheti a **hozzáférési jogkivonatokat** vagy **azonosító jogkivonatokat**, vagy mindkettőt.
- Az **API közzététele** oldalon az`oauth2Permissions`API () és a **jóváhagyott ügyfélalkalmazások** (`preAuthorizedApplications`) **által definiált hatókörök** szerepelnek. Az alkalmazások webes API-hoz való konfigurálásával és az engedélyek/hatókörök beszerzésével kapcsolatos további információkért lásd [: gyors útmutató: alkalmazás konfigurálása webes API](quickstart-configure-app-expose-web-apis.md)-k megjelenítéséhez.
- A **védjegyezési** oldal tartalmazza a **közzétevő tartományt**. A közzétevő tartomány megjelenik a felhasználók számára az [alkalmazás jóváhagyására vonatkozó kérésben](application-consent-experience.md). További információkért lásd [: útmutató: alkalmazás közzétevő tartományának konfigurálása](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Korlátozások

Az új felhasználói élmény a következő korlátozásokkal jár:

- Az ügyfél-titkok formátuma (az alkalmazás jelszavai) eltér a régi élménytől, és a CLI-t is megszakíthatja.
- A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazás-jegyzékfájlt csak akkor kell használni, ha az Azure AD egybérlős és a több-bérlős közötti váltást végzi.

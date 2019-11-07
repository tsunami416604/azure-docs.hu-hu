---
title: Alkalmazásregisztrációk a Azure Portal képzési útmutatójában – Azure
description: A Microsoft Identity platform új alkalmazás-regisztrációs felületének bemutatása.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a539859fb4853467863f3fd2ab4144bcb789b9f5
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73662498"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Tanítási útmutató: Alkalmazásregisztrációk a Azure Portal

Számos javítást talál a Azure Portal új [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) felületén. Ha még jobban ismeri az örökölt élményt, használja ezt az oktatóanyagot az új felület használatának megkezdéséhez.

Azure Active Directory az itt ismertetett új alkalmazás-regisztrációs élmény általánosan elérhető (GA). Azure Active Directory B2C (Azure AD B2C) Ez a felhasználói élmény előzetes verzióban érhető el.

## <a name="key-changes"></a>Legfontosabb változások

- Alkalmazásregisztrációk nem kizárólag **webalkalmazás/API** vagy **natív** alkalmazás lehet. Ugyanezeket az alkalmazásokat használhatja a megfelelő átirányítási URI-k regisztrálásával.
- Az örökölt felhasználói élmény csak a szervezeti (Azure AD-) fiókokat bejelentkező, támogatott alkalmazásokat támogatja. Az alkalmazások egyszeri bérlőként lettek regisztrálva (csak a címtárban regisztrált szervezeti fiókok támogatása az alkalmazásban), és úgy módosíthatók, hogy több-bérlős (az összes szervezeti fiók támogatása). Az új felület lehetővé teszi olyan alkalmazások regisztrálását, amelyek támogatják mindkét lehetőséget, valamint egy harmadik lehetőséget: minden szervezeti fiókot és személyes Microsoft-fiókot is.
- A régi felhasználói felület csak akkor érhető el, ha szervezeti fiókkal jelentkezett be a Azure Portalba. Az új felülettel olyan személyes Microsoft-fiókokat is használhat, amelyek nem címtárhoz vannak társítva.

## <a name="list-of-applications"></a>Alkalmazások listája

- Az új alkalmazások listája az Azure Portal (az Azure AD-fiókokat bejelentkező alkalmazások), valamint az [alkalmazás-regisztrációs portálon](https://apps.dev.microsoft.com/) (a bejelentkezéshez használt alkalmazások) az örökölt alkalmazás regisztrációján keresztül regisztrált alkalmazásokat jeleníti meg. Azure AD-és személyes Microsoft-fiókok).
- Az új alkalmazások listájában nem szerepel az **alkalmazás típusa** oszlop (mivel egyetlen alkalmazás regisztrálása többféle típus lehet), és két további oszlop van: egy **létrehozott** oszlop és egy **tanúsítvány & titkok** oszlop, amely megjeleníti az állapotot (aktuális, az alkalmazásban regisztrált hitelesítő adatok hamarosan lejárnak vagy lejártnak minősülnek.

## <a name="new-app-registration"></a>Új alkalmazás regisztrálása

A régi felhasználói felületen a szükséges alkalmazások regisztrálásához a következőket kell megadnia: **név**, **alkalmazás típusa**, **bejelentkezési URL-cím/átirányítási URI**. A létrehozott alkalmazások az Azure AD csak egybérlős alkalmazások voltak, ami azt jelenti, hogy csak a címtárban regisztrált szervezeti fiókok támogatottak.

Az új felhasználói felületen meg kell adnia egy **nevet** az alkalmazásnak, és ki kell választania a **támogatott fióktípus**lehetőséget. Igény szerint **átirányítási URI**-t is megadhat. Ha átirányítási URI-t ad meg, meg kell adnia, hogy webes vagy nyilvános (mobil és asztali). Az alkalmazások új alkalmazás-regisztrációval való regisztrálásával kapcsolatos további információkért lásd: [alkalmazás regisztrálása a Microsoft Identity platformmal](quickstart-register-app.md). Azure AD B2C esetében lásd: [alkalmazás regisztrálása Azure Active Directory B2Cban](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>Az örökölt tulajdonságok lap

Az örökölt felület olyan **tulajdonságlapot** tartalmazott, amelyet az új felület nem tartalmaz. A **Properties (Tulajdonságok** ) panelen a következő mezők szerepelnek: **név**, **objektumazonosító**, **alkalmazás azonosítója**, alkalmazás- **azonosító URI**, **embléma**, **Kezdőlap URL**-címe, **kijelentkezési URL**-cím, **szolgáltatási feltételek URL-címe**, **adatvédelmi nyilatkozat URL-cím**, **alkalmazás típusa**és **több-bérlő.**

Itt találja a megfelelő funkciókat az új felhasználói élményben:

- A **név**, az **embléma**, a **Kezdőlap URL**-címe, a **szolgáltatási feltételek URL**-címe és az **adatvédelmi nyilatkozat URL-címe** mostantól az alkalmazás **védjegyezési** oldalán található.
- Az **objektum** -azonosító és az **alkalmazás (ügyfél) azonosítója** az **Áttekintés** oldalon található.
- A **több-bérlős** váltás az örökölt felhasználói felület által vezérelt funkcióit a **hitelesítés** lapon **támogatott fióktípus** váltotta fel. Ha többet szeretne megtudni arról, hogy a több-bérlős rendszer hogyan térképezi fel a támogatott fióktípus beállításait, tekintse meg [ezt](quickstart-modify-supported-accounts.md)a rövid útmutatót.
- A **kijelentkezési URL-cím** mostantól a **hitelesítés** oldalon található.
- Az **alkalmazás típusa** már nem érvényes mező. Ehelyett átirányítási URI-k (amelyek a **hitelesítés** oldalon találhatók) határozzák meg, hogy mely típusú alkalmazások támogatottak.
- Az alkalmazás- **azonosító URI** -ja már **Application ID URI** -ként van meghívva, amely az API-k **közzététele** panelen található. A régi felhasználói élményben ez a tulajdonság a következő formátumban lett automatikusan regisztrálva: `https://{tenantdomain}/{appID}` (például `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Az új felhasználói élményben automatikusan létrejön `api://{appID}`ként, de explicit módon el kell menteni. Azure AD B2C bérlők esetében a `https://{tenantdomain}/{appID}` formátum továbbra is használatban van.

## <a name="reply-urlsredirect-urls"></a>Válasz URL-címek/átirányítási URL-címek

A régi élményben az alkalmazásnak volt egy **Válasz URL-címe** . Az új felhasználói felületen a válasz URL-címei az alkalmazás **hitelesítési** szakaszán találhatók. Emellett **átirányítási URI**-ként is hivatkoznak rájuk. Emellett módosult az átirányítási URI-k formátuma is. Egy alkalmazás típusához (web vagy Public) kell társítani őket. Emellett biztonsági okokból a helyettesítő karakterek és a http://sémák nem támogatottak (http://localhost)kivételével.

## <a name="keyscertificates--secrets"></a>Kulcsok/tanúsítványok & Secrets

A régi verzióban az alkalmazás **kulcsok** lapja. Az új felhasználói élményben átnevezték a **tanúsítványokat & titkokat**. Emellett a **nyilvános kulcsokat** **tanúsítványoknak** és **jelszavaknak** nevezik, az **ügyfél titkos**kulcsaként.

## <a name="required-permissionsapi-permissions"></a>Szükséges engedélyek/API-engedélyek

- A régi felhasználói felületen az alkalmazásnak megfelelő **engedélyekkel kell** rendelkeznie. Az új felhasználói felületen átnevezték az **API-engedélyeket**.
- Ha az API-t az örökölt felületen választjuk ki, a Microsoft API-k kisméretű listájából választhat, vagy megkeresheti a bérlőben található egyszerű szolgáltatásokat. Az új felhasználói élményben több lapból is választhat: a **Microsoft API**-k, **a szervezet által használt API**-k vagy **az API**-k. A szervezet által használt **API** -k keresési sávján a szolgáltatás a bérlőn keresztüli egyszerű keresést végez.

   > [!NOTE]
   > Ez a lap nem jelenik meg, ha az alkalmazás nincs bérlőhöz társítva. Ha további információt szeretne arról, hogyan kérhet engedélyeket az új felhasználói felülettel, tekintse meg [ezt](quickstart-configure-app-access-web-apis.md)a rövid útmutatót.

- A korábbi felhasználói élmény a **kért engedélyek** lap tetején található **engedélyezési engedélyek** gomb. Az új **felhasználói felületen az** alkalmazás **API-engedélyei** szakaszának engedélyekkel kapcsolatos **engedélyezési szakasza** szerepel. Emellett a gombok függvényének módjában is vannak különbségek:
   - A régi felhasználói élményben a logika a bejelentkezett felhasználótól és a kért engedélytől függően változhat. A logikája:
      - Ha csak a felhasználó beleegyezik, és a bejelentkezett felhasználó nem rendszergazda, a felhasználó engedélyt kapott a kért engedélyekhez.
      - Ha legalább egy rendszergazdai jóváhagyást igénylő engedély kérése megtörtént, és a bejelentkezett felhasználó nem rendszergazda, a felhasználónak hiba történt a jóváhagyás megadására tett kísérlet során.
      - Ha a bejelentkezett felhasználó rendszergazdai jogosultsággal rendelkezik, a rendszer rendszergazdai beleegyezett az összes kért engedélyhez.
   - Az új felhasználói élményben csak a rendszergazda adhat meg jóváhagyást. Ha egy rendszergazda kiválasztja a **rendszergazdai jóváhagyás megadása** gombot, a rendszer rendszergazdai jóváhagyást kap az összes kért engedélyhez.

## <a name="deleting-an-app-registration"></a>Alkalmazás regisztrációjának törlése

A régi élményben az alkalmazásnak egyetlen bérlőt kellett törölni. A törlés gomb a több-bérlős alkalmazások esetében le lett tiltva. Az új felhasználói élményben az alkalmazások bármely állapotban törölhetők, de meg kell erősítenie a műveletet. Az alkalmazások regisztrációjának törlésével kapcsolatos további információkért tekintse meg [ezt a](quickstart-remove-app.md)rövid útmutatót.

## <a name="application-manifest"></a>Alkalmazásjegyzék

Az örökölt és az új tapasztalatok a JSON formátumának különböző verzióit használják a jegyzékfájl-szerkesztőben. További információ: [Application manifest](reference-app-manifest.md).

## <a name="new-ui"></a>Új felhasználói felület

A korábban csak a manifest Editor vagy az API használatával beállított tulajdonságok új felhasználói felülettel rendelkeznek, vagy nem létezett.

- Az **implicit engedélyezési folyamat** (oauth2AllowImplicitFlow) a **hitelesítés** oldalon található. A régi élménytől eltérően engedélyezheti a **hozzáférési jogkivonatokat** vagy **azonosító jogkivonatokat**, vagy mindkettőt.
- Az API (oauth2Permissions) és a **jóváhagyott ügyfélalkalmazások** (preAuthorizedApplications) **által definiált hatókörök** az API-k **közzététele** lapon konfigurálhatók. További információ az alkalmazások webes API-ként való konfigurálásáról és az engedélyek/hatókörök elérhetővé tétele: [ebben](quickstart-configure-app-expose-web-apis.md)a rövid útmutatóban.
- A **kiadói tartomány** (amely megjelenik az [alkalmazás jóváhagyását kérő](application-consent-experience.md)felhasználók számára) a **branding (védjegyezés** ) lapon található. A közzétevő tartomány konfigurálásával kapcsolatos további információkért tekintse meg [ezt a útmutató](howto-configure-publisher-domain.md)című témakört.

## <a name="limitations"></a>Korlátozások

Az új felhasználói élmény a következő korlátozásokkal jár:

- Az ügyfél-titkok formátuma (az alkalmazás jelszavai) eltér a régi élménytől, és a CLI-t is megszakíthatja.
- A támogatott fiókok értékének módosítása nem támogatott a felhasználói felületen. Az alkalmazás-jegyzékfájlt csak akkor kell használni, ha az Azure AD egybérlős és a több-bérlős közötti váltást végzi.

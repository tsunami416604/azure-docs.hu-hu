---
title: Az érvényesítési különbségek támogatott fióktípus alapján – Microsoft Identity platform | Azure
description: Az alkalmazás Microsoft Identity platformmal való regisztrálásakor megismerheti a különböző támogatott fióktípus különböző tulajdonságainak érvényesítési különbségeit.
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/21/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: e794e277f6731c7b6e57a4710eea437f65be0340
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336344"
---
# <a name="validation-differences-by-supported-account-types-signinaudience"></a>Érvényesítési eltérések támogatott fióktípus (signInAudience) alapján

Amikor a fejlesztők számára a Microsoft Identity platformmal regisztrál egy alkalmazást, a rendszer megkéri, hogy válassza ki az alkalmazás által támogatott fióktípus típusát. Az Application objektumban és a manifest-ben ez a tulajdonság `signInAudience` .

A lehetőségek a következők:

- *AzureADMyOrg*: csak a szervezeti könyvtárban lévő azon fiókok, amelyeken az alkalmazás regisztrálva van (egybérlős)
- *AzureADMultipleOrgs*: bármely szervezeti címtár fiókjai (több-bérlős)
- *AzureADandPersonalMicrosoftAccount*: fiókok bármely szervezeti címtárban (több-bérlős) és személyes Microsoft-fiókokban (például Skype, Xbox és Outlook.com)

A regisztrált alkalmazások esetében a támogatott fióktípus értékét az alkalmazás **hitelesítés** szakaszában találja. `signInAudience`A **jegyzékfájlban**található tulajdonság alatt is megtalálhatja.

Az ehhez a tulajdonsághoz kiválasztott érték hatással van az alkalmazás egyéb tulajdonságaira. Ennek eredményeképpen, ha módosítja ezt a tulajdonságot, akkor előfordulhat, hogy először módosítania kell a többi tulajdonságot.

Tekintse meg a következő táblázatot a különböző támogatott fióktípus különböző tulajdonságainak érvényesítési eltéréseit illetően.

| Tulajdonság | `AzureADMyOrg` | `AzureADMultipleOrgs` | `AzureADandPersonalMicrosoftAccount` és `PersonalMicrosoftAccount` |
|--------------|---------------|----------------|----------------|
| Alkalmazás-azonosító URI `identifierURIs` -ja ()  | Egyedinek kell lennie a bérlőben <br><br> a urn://-sémák támogatottak <br><br> A helyettesítő karakterek használata nem támogatott <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> Nincs korlát * a identifierURIs száma alapján  | Globálisan egyedinek kell lennie <br><br> a urn://-sémák támogatottak <br><br> A helyettesítő karakterek használata nem támogatott <br><br> A lekérdezési karakterláncok és töredékek támogatottak <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> Nincs korlát * a identifierURIs száma alapján | Globálisan egyedinek kell lennie <br><br> a urn://-sémák nem támogatottak <br><br> Helyettesítő karakterek, töredékek és lekérdezési karakterláncok nem támogatottak <br><br> Legfeljebb 120 karakter hosszú lehet <br><br> Legfeljebb 50 identifierURIs |
| Tanúsítványok ( `keyCredentials` ) | Szimmetrikus aláíró kulcs | Szimmetrikus aláíró kulcs | Titkosítási és aszimmetrikus aláírási kulcs | 
| Ügyfél-titkok ( `passwordCredentials` ) | Nincs korlát * | Nincs korlát * | Ha a liveSDK engedélyezve van: legfeljebb 2 ügyfél titka | 
| Átirányítási URI `replyURLs` -k () | További információért lásd: [átirányítási URI/válasz URL-címekre vonatkozó korlátozások és korlátozások](reply-url.md) . | | | 
| API-engedélyek ( `requiredResourceAccess` ) | Nincs korlát * | Nincs korlát * | Legfeljebb 50 erőforrás/alkalmazás és 30 engedély erőforrás alapján (például Microsoft Graph). 200-os teljes korlát (erőforrás x engedélyek). | 
| Az API által definiált hatókörök ( `oauth2Permissions` ) | A hatókör nevének maximális hossza 120 karakter <br><br> Nincs korlát * a definiált hatókörök számán | A hatókör nevének maximális hossza 120 karakter <br><br> Nincs korlát * a definiált hatókörök számán |  A hatókör nevének maximális hossza 40 karakter <br><br> Legfeljebb 100 hatókör definiálva | 
| Felhatalmazott ügyfélalkalmazások ( `preAuthorizedApplications` ) | Nincs korlát * | Nincs korlát * | Maximális 500 összesen <br><br> Legfeljebb 100 ügyfél-alkalmazás definiálva <br><br> Ügyfél által definiált maximális 30 hatókör | 
| appRoles | Támogatott <br> Nincs korlát * | Támogatott <br> Nincs korlát * | Nem támogatott | 
| Kijelentkezési URL-cím | http://localhost engedélyezett <br><br> Legfeljebb 255 karakter hosszú lehet | http://localhost engedélyezett <br><br> Legfeljebb 255 karakter hosszú lehet | <br><br> https://localhost engedélyezett, http://localhost sikertelen a MSA <br><br> Legfeljebb 255 karakter hosszú lehet <br><br> HTTP-séma használata nem engedélyezett <br><br> A helyettesítő karakterek használata nem támogatott | 

* Az alkalmazás-objektum összes gyűjteményi tulajdonságában a 1000-es elemek globális korlátja

## <a name="next-steps"></a>További lépések

- Tudnivalók az [alkalmazások regisztrálásáról](app-objects-and-service-principals.md)
- Az [alkalmazás jegyzékfájljának](reference-app-manifest.md) megismerése

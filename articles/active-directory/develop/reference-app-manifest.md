---
title: A Azure Active Directory alkalmazás jegyzékének ismertetése | Microsoft Docs
description: A Azure Active Directory alkalmazás jegyzékének részletes lefedettsége, amely egy Azure AD-bérlőben az alkalmazás identitás-konfigurációját jelöli, és a OAuth engedélyezésének, a hozzájárulási élménynek és egyéb megoldásoknak a megkönnyítése érdekében használatos.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: a665f2ffe3ac2d27fb4e4403922c72520dfb37e8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834878"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory alkalmazás jegyzékfájlja

Az alkalmazás jegyzékfájlja a Microsoft Identity platform alkalmazás-objektumának összes attribútumának definícióját tartalmazza. Az alkalmazás-objektum frissítésére szolgáló mechanizmusként is szolgál. Az alkalmazás entitásával és annak sémájával kapcsolatos további információkért tekintse meg a [Graph API alkalmazás-entitás dokumentációját](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity).

Az alkalmazások attribútumai a Azure Portalon vagy programozott módon konfigurálhatók [REST API](https://docs.microsoft.com/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) vagy a [PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#applications)használatával. Vannak azonban olyan helyzetek, amikor szerkesztenie kell az alkalmazás jegyzékfájlját az alkalmazás attribútumának konfigurálásához. Ezek a forgatókönyvek a következőket biztosítják:

* Ha az alkalmazást Azure AD több-bérlős és személyes Microsoft-fiókkal regisztrálta, a felhasználói felületen nem módosíthatja a támogatott Microsoft-fiókokat. Ehelyett az Application manifest Editort kell használnia a támogatott fióktípus módosításához.
* Ha meg kell határoznia az alkalmazás által támogatott engedélyeket és szerepköröket, módosítania kell az alkalmazás jegyzékfájlját.

## <a name="configure-the-app-manifest"></a>Az alkalmazás jegyzékfájljának konfigurálása

Az alkalmazás jegyzékfájljának konfigurálása:

1. Jelentkezzen be [](https://portal.azure.com)a Azure Portalba.
1. Válassza ki a **Azure Active Directory** szolgáltatást, majd válassza a **Alkalmazásregisztrációk**lehetőséget.
1. Válassza ki a konfigurálni kívánt alkalmazást.
1. Az alkalmazás **Áttekintés** lapján válassza az **Alkalmazásjegyzék** szakaszt. Megnyílik egy webalapú jegyzékfájl-szerkesztő, amely lehetővé teszi a jegyzékfájl szerkesztését a portálon belül. Ha szeretné, a **Letöltés** lehetőségre kattintva helyileg szerkesztheti a jegyzékfájlt, majd a **feltöltés** funkcióval újra alkalmazhatja azt az alkalmazásra.

## <a name="manifest-reference"></a>Jegyzékfájl-hivatkozás

> [!NOTE]
> Ha a **Leírás**után nem jelenik meg a **példa értéke** oszlop, maximalizálja a böngészőablakot, és görgessen/lapozzunk, amíg meg nem jelenik a **példa értéke** oszlop.

| Kulcs  | Érték típusa | Leírás  | Példaérték |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Üres Int32 | Megadja az erőforrás által várt hozzáférési jogkivonat verzióját. Ez megváltoztatja a végpont vagy az ügyfél által a hozzáférési jogkivonat igényléséhez használt JWT verzióját és formátumát.<br/><br/>A használt végpontot (v 1.0 vagy v 2.0) az ügyfél választja ki, és csak a id_tokens verzióját befolyásolja. Az erőforrásoknak explicit módon be `accesstokenAcceptedVersion` kell állítaniuk a hozzáférést a támogatott hozzáférési jogkivonat formátumának jelzéséhez.<br/><br/>A lehetséges értékei `accesstokenAcceptedVersion` : 1, 2 vagy NULL. Ha az érték null, ez az alapértelmezett érték 1, amely a v 1.0 végpontnak felel meg. <br/><br/>Ha `signInAudience` a`AzureADandPersonalMicrosoftAccount`értéke, akkor az értéknek kell lennie`2`  | `2` |
| `addIns` | Collection | Meghatározza azt az egyéni viselkedést, amelyet a felhasználó szolgáltatás használhat az alkalmazások adott környezetben való meghívásához. Előfordulhat például, hogy a fájlokat megjelenítő alkalmazások a "FileHandler" funkcióhoz a bővítmények tulajdonságot is megadhatják. Ez lehetővé teszi, hogy a szolgáltatások, például az Office 365 meghívja az alkalmazást egy olyan dokumentum kontextusában, amelyben a felhasználó dolgozik. | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Logikai | Megadja a tartalék alkalmazás típusát. Az Azure AD alapértelmezés szerint kikövetkezteti az alkalmazás típusát a replyUrlsWithType. Vannak olyan helyzetek, amikor az Azure AD nem tudja megállapítani az ügyfélalkalmazás típusát (pl. [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) folyamat, ahol http-kérelem URL-átirányítás nélkül történik). Ezekben az esetekben az Azure AD az alkalmazás típusát a tulajdonság értéke alapján fogja értelmezni. Ha az érték TRUE (igaz), a tartalék alkalmazás típusa nyilvános ügyfélként van beállítva, például egy mobileszközön futó telepített alkalmazás. Az alapértelmezett érték false (hamis), ami azt jelenti, hogy a tartalék alkalmazás típusa bizalmas ügyfél, például webalkalmazás. | `false` |
| `availableToOtherTenants` | Logikai | igaz, ha az alkalmazás más Bérlővel van megosztva; Ellenkező esetben hamis. <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `signInAudience`_ | |
| `appId` | Sztring | Az Azure AD által az alkalmazáshoz hozzárendelt alkalmazás egyedi azonosítóját adja meg. | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | Collection | Meghatározza az alkalmazás által deklarált szerepkörök gyűjteményét. Ezeket a szerepköröket felhasználókhoz, csoportokhoz vagy egyszerű szolgáltatásokhoz rendelheti hozzá. További példák és információk: alkalmazások [szerepköreinek hozzáadása az alkalmazásban és fogadása a](howto-add-app-roles-in-azure-ad-apps.md) jogkivonatban | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | Karakterlánc | Az alkalmazás megjelenítendő neve. <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `name`_ | `"MyRegisteredApp"` |
| `errorUrl` | Sztring | Támogatott. | |
| `groupMembershipClaims` | Karakterlánc | Az alkalmazás által várt felhasználói vagy OAuth 2,0 hozzáférési jogkivonatban kiadott jogcímetkonfigurálja.`groups` Az attribútum beállításához használja a következő érvényes karakterlánc-értékek egyikét:<br/><br/>- `"None"`<br/>- `"SecurityGroup"`(biztonsági csoportokhoz és Azure AD-szerepkörökhöz)<br/>- `"All"`(ez az összes olyan biztonsági csoportot, terjesztési csoportot és Azure AD-címtárbeli szerepkört megkapja, amely tagja a bejelentkezett felhasználónak. | `"SecurityGroup"` |
| `homepage` | Karakterlánc | Az alkalmazás kezdőlapjára mutató URL-cím. <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `signInUrl`_ | `"https://MyRegisteredApp"` |
| `objectId` | Sztring | Az alkalmazás egyedi azonosítója a címtárban. <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `id`_ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | Karakterlánc | A jogkivonatban az adott alkalmazás biztonsági jogkivonat szolgáltatása által visszaadott választható jogcímek.<br>Jelenleg a személyes fiókokat és az Azure AD-t támogató alkalmazások (az alkalmazás regisztrációs portálján keresztül regisztrálva) nem használhatják a választható jogcímeket. Az csak az Azure AD-ben a 2.0-s végponton regisztrált alkalmazások azonban megkapják a jegyzékfájlban kért választható jogcímeket is. További információ: [opcionális](active-directory-optional-claims.md)jogcímek. | `null` |
| `id` | Sztring | Az alkalmazás egyedi azonosítója a címtárban. Ez az azonosító nem az alkalmazás azonosítására szolgáló azonosító a protokoll tranzakciójában. Ez az objektum címtárbeli lekérdezésekben való hivatkozására szolgál. | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | A felhasználó által definiált URI-k, amelyek egyedileg azonosítanak egy webalkalmazást az Azure AD-bérlőn belül, vagy egy ellenőrzött egyéni tartományon belül, ha az alkalmazás több-bérlős. | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | Karakterlánc | Megadja az alkalmazás használati feltételeire és az adatvédelmi nyilatkozatra mutató hivatkozásokat. A használati feltételek és az adatvédelmi nyilatkozat a felhasználói beleegyező felületen keresztül történik. További információkért lásd [: How to: Adja meg a regisztrált Azure AD-alkalmazások](howto-add-terms-of-service-privacy-statement.md)használati feltételeit és adatvédelmi nyilatkozatát. | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | Collection | Az alkalmazáshoz rendelt hitelesítő adatokra, a karakterlánc-alapú közös titkokra és az X. 509 tanúsítványokra mutató hivatkozásokat tartalmazza. A rendszer ezeket a hitelesítő adatokat használja hozzáférési tokenek kérésekor (ha az alkalmazás ügyfélként működik, nem pedig erőforrásként). | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | A csomagban való beszerzéshez használatos, ha olyan megoldással rendelkezik, amely két részt tartalmaz: egy ügyfélalkalmazás és egy egyéni webes API-alkalmazás. Ha ezt az értéket adja meg az ügyfélalkalmazás appID, a felhasználónak csak egyszer kell megadnia az ügyfélalkalmazás számára. Az Azure AD tudomásul veszi, hogy az ügyfélnek való hozzájárulás implicit módon hozzájárul a webes API-hoz, és egyszerre automatikusan kiépíti az ügyfél és a webes API szolgáltatásait is. Az ügyfélnek és a webes API-alkalmazásnak is regisztrálva kell lennie ugyanabban a bérlőben. | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | Karakterlánc | Csak olvasható érték, amely arra a CDN URL-címre mutat, amelyet a portálon töltöttek fel. | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | Sztring | Az alkalmazásból kijelentkezni kívánt URL-cím. | `"https://MyRegisteredAppLogout"` |
| `name` | Sztring | Az alkalmazás megjelenítendő neve. | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Logikai | Azt adja meg, hogy a webalkalmazás igényelhet-e OAuth 2.0 implicit flow-hozzáférési jogkivonatokat. Az alapértelmezett érték a false. Ez a jelző a böngészőalapú alkalmazások, például a JavaScript egyoldalas alkalmazások esetében használatos. További információért írja be a `OAuth 2.0 implicit grant flow` tartalomjegyzékbe, és tekintse meg az implicit folyamattal kapcsolatos témaköröket. | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Logikai | Megadja, hogy a webalkalmazás igényelhet-e OAuth 2.0 implicit flow ID-tokeneket. Az alapértelmezett érték a false. Ez a jelző a böngészőalapú alkalmazások, például a JavaScript egyoldalas alkalmazások esetében használatos. | `false` |
| `oauth2Permissions` | Collection | Megadja a OAuth 2,0 jogosultsági hatókörök gyűjteményét, amelyeket a webes API-(erőforrás-) alkalmazás az ügyfélalkalmazások számára tesz elérhetővé. A jogosultsági hatókörök az ügyfélalkalmazások számára is megadhatók a hozzájárulás során. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Logikai | Azt határozza meg, hogy a OAuth 2,0 jogkivonat-kérelmek részeként az Azure AD engedélyezi-e a POST kéréseket, a kérések helyett. Az alapértelmezett érték a False (hamis), amely azt határozza meg, hogy csak a GET kérelmek engedélyezettek lesznek. | `false` |
| `parentalControlSettings` | Sztring | `countriesBlockedForMinors`meghatározza azokat az országokat, amelyekben az alkalmazás le van tiltva a kiskorúak számára.<br>`legalAgeGroupRule`meghatározza az alkalmazás felhasználóira vonatkozó jogi korhatár-szabályt. `Allow`Beállítható a `RequireConsentForPrivacyServices`következőre:,,, `BlockMinors`vagy. `RequireConsentForMinors` `RequireConsentForKids`  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | Collection | Tekintse meg `keyCredentials` a tulajdonság leírását. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | Collection | Felsorolja az alkalmazásokat és a kért engedélyeket az implicit engedélyhez. Megköveteli, hogy a rendszergazda beleegyezett az alkalmazásba. a preAuthorizedApplications nem szükséges, hogy a felhasználó beleegyezik a kért engedélyekkel. A preAuthorizedApplications felsorolt engedélyek nem igénylik a felhasználói hozzájárulásukat. Azonban a preAuthorizedApplications-ben nem szereplő további kért engedélyek felhasználói beleegyezett. | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Logikai | Azt határozza meg, hogy az alkalmazás nyilvános-e (például egy mobileszközön futó telepített alkalmazás). <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `allowPublicClient`_ | |
| `publisherDomain` | Karakterlánc | Az alkalmazás ellenőrzött közzétevő tartománya. Csak olvasható. | https://www.contoso.com |
| `replyUrls` | Karakterlánc-tömb | Ez a többértékű tulajdonság azon regisztrált redirect_uri-értékek listáját tartalmazza, amelyeket az Azure AD a célhelyként fogad el a tokenek visszaküldésekor. <br><br> _Megjegyzés: Ez csak Alkalmazásregisztrációk (örökölt) élményben érhető el. Lecserélte a Alkalmazásregisztrációk felhasználói felületén. [](https://go.microsoft.com/fwlink/?linkid=2083908) `replyUrlsWithType`_ | |
| `replyUrlsWithType` | Collection | Ez a többértékű tulajdonság azon regisztrált redirect_uri-értékek listáját tartalmazza, amelyeket az Azure AD a célhelyként fogad el a tokenek visszaküldésekor. Minden URI-értéknek tartalmaznia kell egy társított alkalmazás típusú értéket. A támogatott típusú értékek a `Web`következők `InstalledClient`:,. | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | Collection | A dinamikus beleegyezőség révén a rendszergazda beleegyezik a rendszergazdai jogosultságokkal, `requiredResourceAccess` és a felhasználók beleegyeznek a statikus beleegyezett. Ez azonban nem teszi elérhetővé a felhasználói beleegyező élményt az általános esethez.<br>`resourceAppId`annak az erőforrásnak az egyedi azonosítója, amelyhez az alkalmazásnak hozzá kell férnie. Ennek az értéknek meg kell egyeznie a célként megadott erőforrás-alkalmazásban deklarált appId.<br>`resourceAccess`a egy olyan tömb, amely felsorolja az alkalmazás által a megadott erőforrás által igényelt OAuth 2.0 engedély-hatóköröket és alkalmazás-szerepköröket. A megadott erőforrások `type`ésértékek értékét tartalmazza. `id` | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | Sztring | Az alkalmazás SAML-metaadatainak URL-címe. | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | Karakterlánc | Megadja az alkalmazás kezdőlapjának URL-címét. | `https://MyRegisteredApp` |
| `signInAudience` | Sztring | Megadja, hogy az aktuális alkalmazás milyen Microsoft-fiókokat támogat. Támogatott értékei a következők:<ul><li>**AzureADMyOrg** – Microsoft munkahelyi vagy iskolai fiókkal rendelkező felhasználók a saját szervezet Azure ad-bérlője (azaz egyetlen bérlő)</li><li>**AzureADMultipleOrgs** – Microsoft munkahelyi vagy iskolai fiókkal rendelkező felhasználók bármely szervezet Azure ad-bérlője (azaz több-bérlős)</li> <li>**AzureADandPersonalMicrosoftAccount** – felhasználók személyes Microsoft-fiók vagy munkahelyi vagy iskolai fiókkal bármely szervezet Azure ad-bérlőben</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | Egyéni karakterláncok, amelyek használatával kategorizálhatja és azonosíthatja az alkalmazást. | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>Gyakori problémák

### <a name="manifest-limits"></a>Jegyzékfájlok korlátai

Az alkalmazás-jegyzékfájl több attribútumot is tartalmaz, amelyeket gyűjteményekként nevezünk. például approles, a hitelesítő adatok, a knownClientApplications, a identifierUris, a rediretUris, a requiredResourceAccess és a oauth2Permissions. A teljes alkalmazás-jegyzékfájlban bármely alkalmazás esetében az összes gyűjtemény bejegyzéseinek teljes száma 1200. Ha már rendelkezik a 100-es átirányítási URI-k az alkalmazás jegyzékfájljában, akkor csak 1100 fennmaradó bejegyzéseket kell megadnia az összes többi, a jegyzékfájlt alkotó gyűjteményben való használathoz.

> [!NOTE]
> Ha több mint 1200 bejegyzést próbál felvenni az alkalmazás jegyzékfájljában, a következő hibaüzenet **jelenhet meg: "nem sikerült frissíteni az alkalmazást (xxxxxx). Hiba részletei: A jegyzékfájl mérete túllépte a korlátot. Csökkentse az értékek számát, majd próbálja megismételni a kérést. "**

### <a name="unsupported-attributes"></a>Nem támogatott attribútumok

Az alkalmazás jegyzékfájlja az alapul szolgáló alkalmazás modelljének sémáját jelöli az Azure AD-ben. Az alapul szolgáló séma fejlődése után a jegyzékfájl-szerkesztő frissülni fog, hogy az új sémát időről időre tükrözze. Ennek eredményeképpen előfordulhat, hogy az alkalmazás jegyzékfájljában látható új attribútumok is megjelenhetnek. Ritka esetekben előfordulhat, hogy szintaktikai vagy szemantikai változást észlel a meglévő attribútumokban, vagy előfordulhat, hogy a korábban létezett attribútumok már nem támogatottak. Láthatja például, hogy az új attribútumok megjelennek a [Alkalmazásregisztrációkban](https://go.microsoft.com/fwlink/?linkid=2083908) , amelyek más néven szerepelnek a Alkalmazásregisztrációk (örökölt) élményben.


| Alkalmazásregisztrációk (örökölt)| Alkalmazásregisztrációk           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Ezeknek az attribútumoknak a leírását a [jegyzékfájl referenciája](#manifest-reference) című szakaszban találja.

Amikor egy korábban letöltött jegyzékfájlt próbál feltölteni, az alábbi hibák valamelyikét láthatja. Ez valószínűleg azért van így, mert a jegyzékfájl-szerkesztő mostantól támogatja a séma újabb verzióját, amely nem egyezik meg azzal, amelyet fel szeretne tölteni.

- "**Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: Érvénytelen objektumazonosító: "nincs meghatározva". [].** "
- "**Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: Egy vagy több megadott tulajdonságérték érvénytelen. [].** "
- "**Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: Nem engedélyezett a availableToOtherTenants beállítása a frissítéshez ebben az API-verzióban. [].** "
- "**Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: Az "replyUrls" tulajdonság frissítései nem engedélyezettek ehhez az alkalmazáshoz. Ehelyett használja a "replyUrlsWithType" tulajdonságot. [].** "
- "**Nem sikerült frissíteni az xxxxxx-alkalmazást. Hiba részletei: A rendszer nem talált egy értéket a típus neve nélkül, és a várt típus nem érhető el. Ha a modell meg van adva, a hasznos adattartalomban szereplő minden értéknek olyan típusúnak kell lennie, amely adható meg a hasznos adattartalomban, explicit módon a hívó által, vagy implicit módon következtetett a fölérendelt értéktől. []** "

Ha az alábbi hibák valamelyikét látja, javasoljuk a következőket:

1. Szerkessze az attribútumokat egyenként a jegyzékfájl-szerkesztőben egy korábban letöltött jegyzékfájl feltöltése helyett. A [jegyzékfájlok hivatkozási](#manifest-reference) táblázatával megismerheti a régi és az új attribútumok szintaxisát és szemantikaát, így sikeresen szerkesztheti a kívánt attribútumokat. 
1. Ha a munkafolyamata megköveteli, hogy később is használhassa a jegyzékfájlokat a forrás-adattárban, javasoljuk, hogy az adattárban lévő mentett jegyzékfájlokat a **Alkalmazásregisztrációk** felhasználói felületén látható értékre alapozza.

## <a name="next-steps"></a>További lépések

* Az alkalmazások alkalmazás-és szolgáltatásnév-objektumai közötti kapcsolattal kapcsolatos további információkért lásd: [alkalmazás-és szolgáltatásnév-objektumok az Azure ad-ben](app-objects-and-service-principals.md).
* Tekintse meg a [Microsoft Identity platform fejlesztői szószedetét](developer-glossary.md) a Microsoft Identity platform számos fejlesztői koncepciójának meghatározásához.

A következő megjegyzések szakasz a tartalom pontosítását és formázását segítő visszajelzést nyújt.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/

---
title: Az Azure Active Directory alkalmazásjegyzék értelmezése |} A Microsoft Docs
description: Részletes lefedettségét az Azure Active Directory alkalmazásjegyzék, amely jelenti egy alkalmazás identitás konfigurálása az Azure AD-bérlő, és az OAuth hitelesítési, jóváhagyási felületen és egyéb megkönnyítésére szolgál.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/27/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja, justhu
ms.openlocfilehash: f336771da334ffd964ecd032654b8549b7a5e847
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2018
ms.locfileid: "43127020"
---
# <a name="azure-active-directory-app-manifest"></a>Az Azure Active Directory-manifest aplikace

Alkalmazások integrálása az Azure Active Directory (Azure AD) az Azure AD-bérlő regisztrálva kell lennie. Beállíthatja, hogy az alkalmazás a [az Azure portal](https://portal.azure.com) kiválasztásával **Azure Active Directory**, a konfigurálni kívánt alkalmazás kiválasztása, és kiválasztja az **Manifest**.

## <a name="manifest-reference"></a>Jegyzékfájl referencia

> [!NOTE]
> Ha nem látja a leírások, állítsa teljes méretre a böngészőablakot vagy görgessen/pöccintsen megtekintéséhez a leírásokat.

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]

| Kulcs  | Érték típusa | Példaérték | Leírás  |
|---------|---------|---------|---------|
| `appID` | Azonosító karakterlánc | `"601790de-b632-4f57-9523-ee7cb6ceba95"` | Itt adhatja meg az alkalmazás-alkalmazásokba az Azure AD által hozzárendelt egyedi azonosítója. |
| `appRoles` | A tömb típusa | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code> | Itt adhatja meg, hogy egy alkalmazás deklarálhat szerepkörök gyűjteménye. Ezeket a szerepköröket rendelhet felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokat. |
| `availableToOtherTenants`| logikai | `true` | Ha az értéke igaz, az alkalmazás érhető el a többi bérlőtől. Ha az értéke HAMIS, az alkalmazás csak a bérlő számára elérhető, regisztrálva van. További információ: [hogyan: a több-bérlős alkalmazás minta használatával az Azure AD-felhasználók bejelentkeztetése](howto-convert-app-to-be-multi-tenant.md). |
| `displayName` | sztring | `MyRegisteredApp` | Az alkalmazás megjelenített neve. |
| `errorURL` | sztring | `http://MyRegisteredAppError` | Hiba történt az alkalmazás URL-CÍMÉT. |
| `groupMembershipClaims` | sztring | `1` | Bitmaszk, amely beállítja a `groups` egy felhasználó vagy az OAuth 2.0 hozzáférési jogkivonatban, amelyeket az alkalmazás vár a kiállított jogcímet. A bitmaszk értékei a következők:<br>0: nincs<br>1: biztonsági csoportok és az Azure AD-szerepkörök<br>2: fenntartva<br>4: fenntartva<br>A bitmaszk beállítása a 7 minden a biztonsági csoportok, terjesztési csoportok és az Azure AD-címtárbeli szerepkörök, amelyek a bejelentkezett felhasználó tagja kap. |
| `optionalClaims` | sztring | `null` | A választható jogcímeket adja vissza a jogkivonat a biztonsági jogkivonat szolgáltatás az adott alkalmazás. További információ: [választható jogcímek](active-directory-optional-claims.md). |
| `acceptMappedClaims` | logikai | `true` | Ha ez az érték beállítása `true`, lehetővé teszi az alkalmazások a jogcímek társítása egyéni aláíró kulcs megadása nélkül. |
| `homepage` | sztring | `http://MyRegisteredApp` | Adja meg az URL-cím, az alkalmazás kezdőlapjára. |
| `informationalUrls` | sztring | <code>{<br>&nbsp;&nbsp;&nbsp;"privacy":"http://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"http://MyRegisteredApp/termsofservice"<br>}</code> | Adja meg a hivatkozásokat az alkalmazás és az adatvédelmi nyilatkozat feltételeit. A felhasználók számára a felhasználói jóváhagyási felületen keresztül illesztett és az adatvédelmi nyilatkozat feltételeit. További információ: [hogyan: regisztrált Azure AD-alkalmazások és az adatvédelmi nyilatkozat feltételeit bővíteni](howto-add-terms-of-service-privacy-statement.md). |
| `identifierUris` | Karakterlánc-tömbben | `http://MyRegistererdApp` | Felhasználó által definiált URI(s), amely egyedi azonosításához az Azure AD-bérlővel, vagy egyetlen ellenőrzött egyéni tartománynak egy webalkalmazás, ha az alkalmazás több-bérlős. |
| `keyCredentials` | A tömb típusa | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> | Hitelesítő alkalmazás által hozzárendelt, a karakterlánc-alapú közös titkos kulcsot és az X.509-tanúsítványokat mutató hivatkozásokat tartalmazza. Ezeket a hitelesítő adatokat használt hozzáférési jogkivonatokat kérésekor (ha az alkalmazás egy ügyfél hálózataként erőforrásként ahelyett hogy). |
| `knownClientApplications` | A tömb típusa | `[GUID]` | Jóváhagyás kötegelése használatos, ha van olyan megoldás, amely tartalmazza a két részből áll: egy ügyfélalkalmazást és a egy egyéni webes API-alkalmazás. Ha ezt az értéket ad meg az ügyfél-alkalmazás alkalmazásazonosítója, a felhasználó csak kell hozzájárulás megadása után az ügyfélalkalmazás. Az Azure AD tudni fogja, hogy hozzájárul ahhoz, hogy az ügyfél azt jelenti, hogy az implicit módon beleegyezik abba, hogy a webes API-t és az ügyfél és a webes API-k egyszerű szolgáltatásokat automatikusan kiépül egy időben. Az ügyfél és a webes API-alkalmazás ugyanabban a bérlőben regisztrálni kell. |
| `logoutUrl` | sztring | `http://MyRegisteredAppLogout` | Jelentkezzen ki az alkalmazásból az URL-címe. |
| `oauth2AllowImplicitFlow` | logikai | `false` | Itt adhatja meg, hogy ez a webalkalmazás kérhet OAuth2.0 implicit folyamat jogkivonatokat. Az alapértelmezett érték FALSE (hamis). Ez a jelző böngészőalapú alkalmazások, például a Javascript egyoldalas alkalmazások szolgál. |
| `oauth2AllowUrlPathMatching` | logikai | `false` | Megadja, hogy, az OAuth 2.0 jogkivonat-kérelmeket részeként az Azure AD engedélyezi az átirányítási URI az alkalmazás replyUrls elleni megfelelő elérési útját. Az alapértelmezett érték FALSE (hamis). |
| `oauth2Permissions` | A tömb típusa | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>]</code> | Adja meg az OAuth 2.0-engedélyhatókörök, amely a webes API (erőforrás) alkalmazás közzéteszi az ügyfélalkalmazások gyűjteménye. Ezek engedélyhatókörök ügyfélalkalmazások során hozzájárulási nyújtható. |
| `oauth2RequiredPostResponse` | logikai | `false` | Megadja, hogy, az OAuth 2.0 jogkivonat-kérelmeket részeként az Azure AD engedélyezi POST kéréseket, GET-kérések szemben. Az alapértelmezett érték FALSE (hamis), amely megadja, hogy csak a GET-kérések engedélyezett lesz. |
| `objectId` | Azonosító karakterlánc | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` | A címtárban lévő alkalmazás egyedi azonosítója. Ez az azonosító nem áll az alkalmazás bármely protokoll tranzakció azonosításához használt azonosító. A hivatkozik az objektumra a címtárlekérdezés használatos. |
| `parentalControlSettings` | sztring | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> | `countriesBlockedForMinors` Itt adhatja meg, amelyben az alkalmazás le van tiltva kiskorúak országok.<br>`legalAgeGroupRule` Adja meg a jogi korcsoport csoport szabály, amely az alkalmazás felhasználóinak vonatkozik. Állítható `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids`, vagy `BlockMinors`.  |
| `passwordCredentials` | A tömb típusa | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> | A leírást lásd a `keyCredentials` tulajdonság. |
| `publicClient` | logikai | `false` | Megadja, hogy az alkalmazás nyilvános ügyfél, például mobileszközön futó telepített alkalmazás. Az alapértelmezett értéke FALSE (hamis). |
| `replyUrls` | Karakterlánc-tömbben | `"http://localhost"` | Ez több érték a tulajdonság, amely elfogadja az Azure ad-ben regisztrált redirect_uri értékek listáját tartalmazza a célként jogkivonatok visszaadásakor. |
| `requiredResourceAccess` | A tömb típusa | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;]<br>&nbsp;}<br>] </code> | A dinamikus hozzájárulásával `requiredResourceAccess` meghajtók, a rendszergazdai jóváhagyási felületen és a felhasználói jóváhagyási felületen statikus hozzájárulási használó felhasználók számára. Azonban ez nem a meghajtó a felhasználói jóváhagyási felületen az Általános eset.<br>`resourceAppId` az egyedi azonosítója az erőforrást, az alkalmazás hozzáférésre van szüksége van. Ezt az értéket megadva a cél erőforrás-alkalmazás alkalmazásazonosítója egyenlőnek kell lennie.<br>`resourceAccess` van egy tömb, amely felsorolja a OAuth2.0-engedélyhatókörök, és az alkalmazás a megadott erőforrás igénylő alkalmazás-szerepkörök. Tartalmazza a `id` és `type` értékeket a megadott erőforrások. |
| `samlMetadataUrl` | sztring | `http://MyRegisteredAppSAMLMetadata` | Az alkalmazás SAML-metaadataira mutató URL-címe. |

## <a name="next-steps"></a>További lépések

* Az alkalmazás alkalmazás- és szolgáltatásnév objektumok közötti kapcsolatot a további információk: [alkalmazás és egyszerű szolgáltatási objektumok Azure AD-ben](app-objects-and-service-principals.md).
* Tekintse meg a [Azure AD fejlesztői szószedetét](developer-glossary.md) az alapvető Azure Active Directory (AD) fejlesztői fogalmak némelyike definíciói.

Használja a következő megjegyzéseket visszajelzést, amellyel pontosíthatja, és a tartalom formázása.

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


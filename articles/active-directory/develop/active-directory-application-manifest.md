---
title: Az Azure Active Directory alkalmazásjegyzékének megismerése |} A Microsoft Docs
description: Részletes lefedettségét az Azure Active Directory alkalmazásjegyzékének, amely jelenti egy alkalmazás identitás konfigurálása az Azure AD-bérlő, és az OAuth hitelesítési, jóváhagyási felületen és egyéb megkönnyítésére szolgál.
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
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, sureshja
ms.openlocfilehash: a0d302e740732c5bf76ba75486b75f6f73091940
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576457"
---
# <a name="azure-active-directory-application-manifest"></a>Az Azure Active Directory alkalmazásjegyzékének
Alkalmazások integrálása az Azure ad-ben az Azure AD-bérlő regisztrálva kell lennie. Ez az alkalmazás az alkalmazás-jegyzékfájl (alatt az Azure ad-ben panel) segítségével konfigurálható a [az Azure portal](https://portal.azure.com).

## <a name="manifest-reference"></a>Jegyzékfájl referencia

>[!div class="mx-tdBreakAll"]
>[!div class="mx-tdCol2BreakAll"]
|Kulcs  |Érték típusa |Példaérték  |Leírás  |
|---------|---------|---------|---------|
|appID     |  Azonosító karakterlánc       |""|  Az alkalmazás-alkalmazásokba az Azure AD által hozzárendelt egyedi azonosítója.|
|appRoles     |    A tömb típusa     |<code>[{<br>&emsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&emsp;],<br>&emsp;"description":"Read-only access to device information",<br>&emsp;"displayName":"Read Only",<br>&emsp;"id":guid,<br>&emsp;"isEnabled":true,<br>&emsp;"value":"ReadOnly"<br>}]</code>|Egy alkalmazás deklarálhat szerepkörök gyűjteménye. Ezeket a szerepköröket rendelhet felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokat.|
|availableToOtherTenants|logikai|`true`|Ha az értéke igaz, az alkalmazás elérhető-e más bérlők számára. Ha az értéke HAMIS, az alkalmazás csak a bérlő számára elérhető, regisztrálva van. További információkért lásd: [Azure Active Directory (AD) felhasználók bejelentkeztetése több-bérlős alkalmazásminta használatával hogyan](howto-convert-app-to-be-multi-tenant.md). |
|displayName     |sztring         |`MyRegisteredApp`         |Az alkalmazás megjelenített neve. |
|errorURL     |sztring         |`http://MyRegisteredAppError`         |Hiba történt az alkalmazások URL-CÍMÉT. |
|groupMembershipClaims     |    sztring     |    `1`     |   Egy bitmaszk, amely a kiállított "groups" jogcímet konfigurálja a felhasználói vagy OAuth 2.0 hozzáférési jogkivonatban, amelyeket az alkalmazás vár. A bitmaszk értékei: 0: nincs, 1: biztonsági csoportok és az Azure AD-szerepkörök, 2: fenntartva, és a 4: foglalt. A bitmaszk beállítása a 7 minden a biztonsági csoportok, terjesztési csoportok és az Azure AD-címtárbeli szerepkörök, amelyek a bejelentkezett felhasználó tagja kap. |
|optionalClaims     |  sztring       |     `null`    |    A [választható jogcímek](active-directory-optional-claims.md) adja vissza a jogkivonat a biztonsági jogkivonat szolgáltatás az adott alkalmazás. |
|acceptMappedClaims    |      logikai   | `true`        |    Ha ez az érték értéke true, lehetővé teszi az alkalmazás jogcímtársítások egyéni aláíró kulcs megadása nélkül.|
|Kezdőlap     |  sztring       |`http://MyRegistererdApp`         |    Az alkalmazás kezdőlapjára URL-címe. |
|identifierUris     |  Karakterlánc-tömbben       | `http://MyRegistererdApp`        |   Felhasználó által definiált URI(s), amelyek egyértelműen azonosítják a webalkalmazást az Azure AD-bérlővel, vagy egyetlen ellenőrzött egyéni tartománynak belül, több-bérlős alkalmazás esetén. |
|keyCredentials     |   A tömb típusa      | <code>[{<br>&nbsp;"customKeyIdentifier":null,<br>"endDate":"2018-09-13T00:00:00Z",<br>"keyId":"\<guid>",<br>"startDate":"2017-09-12T00:00:00Z",<br>"type":"AsymmetricX509Cert",<br>"usage":"Verify",<br>"value":null<br>}]</code>      |   Ez a tulajdonság tárolja a hitelesítő alkalmazás által hozzárendelt, a karakterlánc-alapú közös titkos kulcsot és az X.509-tanúsítványokat mutató hivatkozásokat. Ezeket a hitelesítő adatokat használt hozzáférési jogkivonatokat kérésekor (ha az alkalmazás egy ügyfél hálózataként erőforrásként ahelyett hogy). |
|knownClientApplications     |     A tömb típusa    |    [guid]     |     Az érték jóváhagyás kötegelése szolgál, ha van olyan megoldás, amely tartalmazza a két részből áll, egy ügyfélalkalmazás és a egy egyéni webes API-alkalmazás. Az alkalmazásazonosító alapján az ügyfélalkalmazás ezt az értéket ad meg, ha a felhasználó csak kell hozzájárulás megadása után az ügyfélalkalmazásnak. Az Azure AD tudni fogja, hogy hozzájárul ahhoz, hogy az ügyfél azt jelenti, hogy az implicit módon beleegyezik abba, hogy a webes API-t és az ügyfél és a webes API-k egyszerű szolgáltatásokat automatikusan kiépül egy időben. Az ügyfél és a webes API-alkalmazás is regisztrálni kell ugyanabban a bérlőben.|
|logoutUrl     |   sztring      |     `http://MyRegisteredAppLogout`    |   Alkalmazás kijelentkezési URL-címe. |
|oauth2AllowImplicitFlow     |   logikai      |  `false`       |       Itt adhatja meg, hogy ez a webalkalmazás kérhet OAuth2.0 implicit folyamat jogkivonatok. Az alapértelmezett érték FALSE (hamis). Ez a jelző böngészőalapú alkalmazások, például a Javascript egyoldalas alkalmazások szolgál. |
|oauth2AllowUrlPathMatching     |   logikai      |  `false`       |   Megadja, hogy, az OAuth 2.0 jogkivonat-kérelmeket részeként az Azure AD engedélyezi az átirányítási URI az alkalmazás replyUrls elleni megfelelő elérési útját. Az alapértelmezett érték FALSE (hamis). |
|oauth2Permissions     | A tömb típusa         |      <code>[{<br>"adminConsentDescription":"Allow the application to access resources on behalf of the signed-in user.",<br>"adminConsentDisplayName":"Access resource1",<br>"id":"\<guid>",<br>"isEnabled":true,<br>"type":"User",<br>"userConsentDescription":"Allow the application to access resource1 on your behalf.",<br>"userConsentDisplayName":"Access resources",<br>"value":"user_impersonation"<br>}]  </code> |  Az OAuth 2.0-engedélyhatókörök, amely a webes API (erőforrás) alkalmazás közzéteszi az ügyfélalkalmazások gyűjteménye. Ezek engedélyhatókörök hozzájárulási során adható az ügyfélalkalmazások számára. |
|oauth2RequiredPostResponse     | logikai        |    `false`     |      Megadja, hogy, az OAuth 2.0 jogkivonat-kérelmeket részeként az Azure AD engedélyezi POST kéréseket, GET-kérések szemben. Az alapértelmezett érték FALSE (hamis), amely megadja, hogy csak a GET-kérések engedélyezett lesz. 
|objectId     | Azonosító karakterlánc        |     ""    |    A címtárban az alkalmazás egyedi azonosítója. Ez az azonosító nem áll az alkalmazás bármely protokoll tranzakció azonosításához használt azonosító. Felhasználói feladata a a címtárlekérdezés lévő objektumra hivatkozik.|
|passwordCredentials     | A tömb típusa        |   <code>[{<br>"customKeyIdentifier":null,<br>"endDate":"2018-10-19T17:59:59.6521653Z",<br>"keyId":"\<guid>",<br>"startDate":"2016-10-19T17:59:59.6521653Z",<br>"value":null<br>}]  </code>    |    Tekintse meg a keyCredentials tulajdonság leírása. |
|publicClient     |  logikai       |      `false`   | Megadja, hogy egy alkalmazás nyilvános ügyfél (például egy mobileszközön futó telepített alkalmazás). Az alapértelmezett érték a false (hamis). |
|supportsConvergence     |  logikai       |   `false`      | Ez a tulajdonság nem szerkeszthetők. Fogadja el az alapértelmezett értéket. |
|replyUrls     |  Karakterlánc-tömbben       |   `http://localhost`     |  A többértékű tulajdonság, amely elfogadja az Azure ad-ben regisztrált redirect_uri értékek listáját tartalmazza a célként jogkivonatok visszaadásakor. |
|requiredResourceAccess     |     A tömb típusa    |    <code>[{<br>"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>"resourceAccess":[{<br>&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;}]<br>}] </code>    |   Adja meg az erőforrásokat, az alkalmazás elérését és az OAuth-engedélyhatókörök, és ezeket az erőforrásokat az egyes szükséges alkalmazás-szerepkörök használatához. Ez előtti konfiguráció szükséges erőforrás-hozzáférés meghajtók, a jóváhagyási felületen.|
|resourceAppId     |    Azonosító karakterlánc     |  ""      |   Egyedi azonosítója, amely szükséges az alkalmazás számára a hozzáférést az erőforráshoz. Ezt az értéket megadva a cél erőforrás-alkalmazás alkalmazásazonosítója egyenlőnek kell lennie. |
|resourceAccess     |  A tömb típusa       | Lásd a példa requiredResourceAccess tulajdonság értéke. |   OAuth2.0-engedélyhatókörök, és az alkalmazás a megadott erőforrás (azonosító, és írja be értékeket a megadott erőforrások tartalmazza) igénylő alkalmazás-szerepkörök listája        |
|samlMetadataUrl    |sztring| `http://MyRegisteredAppSAMLMetadata` |Az alkalmazás SAML-metaadataira mutató URL-cím.| 

## <a name="next-steps"></a>További lépések
* Egy alkalmazás alkalmazás- és szolgáltatásnév objektumok közötti kapcsolatot további információkért lásd: [alkalmazás és egyszerű szolgáltatási objektumok Azure AD-ben][AAD-APP-OBJECTS].
* Tekintse meg a [Azure AD fejlesztői szószedetét] [ AAD-DEVELOPER-GLOSSARY] az alapvető Azure Active Directory (AD) fejlesztői fogalmak némelyike definíciói.

Használja a következő megjegyzéseket visszajelzést, amellyel pontosíthatja, és a tartalom formázása.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]: active-directory-dev-glossary.md
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


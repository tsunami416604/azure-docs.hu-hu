---
title: Azure API Management sablon erőforrásai | Microsoft Docs
description: Ismerje meg az Azure API Management fejlesztői portál sablonjaiban használható erőforrások típusait.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79249502"
---
# <a name="azure-api-management-template-resources"></a>Azure API Management-sablon erőforrásai
Az Azure API Management a következő típusú erőforrásokat biztosítja a fejlesztői portál sablonjaiban való használathoz.  
  
-   [Karakterlánc-erőforrások](#strings)  
  
-   [Karakterjel-erőforrások](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="strings"></a>Karakterlánc-erőforrások  
 A API Management a fejlesztői portálon használható karakterlánc-erőforrások átfogó készletét nyújtja. Ezek az erőforrások a API Management által támogatott összes nyelvre honosítva vannak. Az alapértelmezett sablonok ezeket az erőforrásokat használják a fejlesztői portálon megjelenített fejlécek, címkék és bármely állandó sztringek esetében. Ha karakterlánc-erőforrást szeretne használni a sablonokban, adja meg az erőforrás-karakterlánc előtagját, amelyet a karakterlánc neve követ, az alábbi példában látható módon.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 A következő példa a Terméklista sablonból származik, és megjeleníti az oldal tetején található **termékeket** .  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
A következő honosítási lehetőségek támogatottak:

| Területi beállítás    | Nyelv               |
|-----------|------------------------|
| „hu”      | Angol              |
| CS      | Cseh              |
| de      | Deutsch              |
| es      | Spanyol              |
| fr      | Francia             |
| hu      | Magyar               |
| Ez      | Italiano             |
| "ja-JP"   | 日本語                |
| Ko      | "한국어"                |
| nl      | Nederlands           |
| pl      | Polski               |
| "pt-br"   | "Portugál (Brasil)"   |
| "PT-PT"   | "Portugál (Portugália)" |
| ru      | "Русский"              |
| Sv      | "Svenska"              |
| TR      | Türkçe               |
| "zh-Hans" | "kínai (egyszerűsített)"           |
| "zh-Hant" | "kínai (繁體)"           |

 Tekintse át a következő táblázatokat a fejlesztői portál sablonjaiban használható karakterlánc-erőforrásokhoz. A tábla nevét használja a tábla karakterlánc-erőforrásainak előtagjaként.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentáció](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleApis|API-k|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsDetailsTitle|Alkalmazás előnézete|  
|WebApplicationsRequirementsHeader|Követelmények|  
|WebApplicationsScreenshotAlt|Képernyőkép|  
|WebApplicationsScreenshotsHeader|Képernyőképek|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Biztosan el kívánja távolítani az alkalmazást?|  
|WebDevelopersAppNotPublished|Nincs közzétéve|  
|WebDevelopersAppNotSubmitted|Nincs elküldve|  
|WebDevelopersAppTableCategoryHeader|Kategória|  
|WebDevelopersAppTableNameHeader|Név|  
|WebDevelopersAppTableStateHeader|State|  
|WebDevelopersEditLink|Szerkesztés|  
|WebDevelopersRegisterAppLink|Alkalmazás regisztrálása|  
|WebDevelopersRemoveLink|Eltávolítás|  
|WebDevelopersSubmitLink|Küldés|  
|WebDevelopersYourApplicationsHeader|Alkalmazásai|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsHeader|Alkalmazások|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Név|Szöveg|  
|----------|----------|  
|NoItemsToDisplay|Nem található eredmény.|  
|GeneralExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes hiba vagy egy hiba. Próbálkozzon újra.|  
|GeneralJsonExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes hiba vagy egy hiba. Töltse be újra a lapot, és próbálkozzon újra.|  
|ConfirmationMessageUnsavedChanges|Néhány nem mentett módosítás. Biztos, hogy megszakítja és elveti a módosításokat?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|A HTTP-kérelem törzse túl nagy.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelCancel|Mégse|  
|ButtonLabelSave|Mentés|  
|GeneralExceptionMessage|Valami nem megfelelő. Ez lehet egy ideiglenes hiba vagy egy hiba. Próbálkozzon újra.|  
|NoItemsToDisplay|Nincsenek megjeleníthető elemek.|  
|PagerButtonLabelFirst|első|  
|PagerButtonLabelLast|Legutóbbi|  
|PagerButtonLabelNext|Tovább|  
|PagerButtonLabelPrevious|Előző|  
|PagerLabelPageNOfM|{1} oldal {0}|  
|PasswordTooShort|A jelszó túl rövid|  
|EmailAsPassword|E-mail-cím használata jelszóként|  
|PasswordSameAsUserName|A jelszó nem tartalmazhatja a felhasználónevét|  
|PasswordTwoCharacterClasses|Különböző karakteres osztályok használata|  
|PasswordTooManyRepetitions|Túl sok ismétlődés|  
|PasswordSequenceFound|A jelszó sorozatot tartalmaz|  
|PagerLabelPageSize|Oldalméret|  
|CurtainLabelLoading|Betöltés...|  
|TablePlaceholderNothingToDisplay|A kiválasztott időszak és a hatókör nem rendelkezik a megadott értékkel.|  
|ButtonLabelClose|Bezárás|  
  
###  <a name="Documentation"></a>Dokumentáció  
  
|Név|Szöveg|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Érvénytelen fejléc: "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Érvénytelen kérelem URL-címe|  
|TextboxLabelAccessToken|Hozzáférési jogkivonat *|  
|DropdownOptionPrimaryKeyFormat|Elsődleges –{0}|  
|DropdownOptionSecondaryKeyFormat|Másodlagos –{0}|  
|WebDocumentationSubscriptionKeyText|Az előfizetési kulcs|  
|WebDocumentationTemplatesAddHeaders|Kötelező HTTP-fejlécek hozzáadása|  
|WebDocumentationTemplatesBasicAuthSample|Alapszintű engedélyezési minta|  
|WebDocumentationTemplatesCurlForBasicAuth|alapszintű engedélyezési használat:--felhasználó {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Adja meg az elérésiút-paraméterek ({...}) értékeit, az előfizetési kulcsot és a lekérdezési paraméterek értékeit.|  
|WebDocumentationTemplatesDeveloperKey|Adja meg az előfizetési kulcsot|  
|WebDocumentationTemplatesJavaApache|Ez a példa az Apache HTTP-ügyfelet használja a HTTP-összetevőkből (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Szükség szerint határozza meg a választható paraméterek értékeit|  
|WebDocumentationTemplatesPhpPackage|Ez a példa a HTTP_Request2 csomagot használja. (További információ: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Határozza meg az elérésiút-paraméterek ({...}) értékeit, és szükség esetén kérje a törzset.|  
|WebDocumentationTemplatesRequestBody|Kérelem törzsének meghatározása|  
|WebDocumentationTemplatesRequiredParams|A következő kötelező paraméterek értékeinek megadása|  
|WebDocumentationTemplatesValuesForPath|Az elérésiút-paraméterek értékeinek megadása (lásd: {...})|  
|OAuth2AuthorizationEndpointDescription|Az engedélyezési végpont az erőforrás-tulajdonossal való interakcióra és egy engedélyezési engedély beszerzésére szolgál.|  
|OAuth2AuthorizationEndpointName|Engedélyezési végpont|  
|OAuth2TokenEndpointDescription|A jogkivonat-végpontot az ügyfél a hozzáférési jogkivonat beszerzéséhez használja az engedélyezési vagy frissítési jogkivonat megadásával.|  
|OAuth2TokenEndpointName|Jogkivonat-végpont|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél kezdeményezi a folyamatot úgy, hogy az erőforrás-tulajdonos felhasználói ügynökét az engedélyezési végpontra irányítja.  Az ügyfél tartalmazza az ügyfél-azonosítót, a kért hatókört, a helyi állapotot, valamint egy átirányítási URI-t, amelyhez az engedélyezési kiszolgáló a hozzáférés engedélyezése (vagy elutasítása) után visszaküldi a felhasználói ügynököt.     </p\> < p\> az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosát (a felhasználói ügynökön keresztül), és megállapítja, hogy az erőforrás tulajdonosa engedélyezi vagy megtagadja az ügyfél hozzáférési kérelmét.     </p\> < p\> feltételezve, hogy az erőforrás tulajdonosa hozzáférést biztosít, az engedélyezési kiszolgáló a korábban megadott átirányítási URI-val (a kérelemben vagy az ügyfél-regisztráció során) átirányítja a felhasználót az ügyfélnek.  Az átirányítási URI tartalmaz egy engedélyezési kódot és az ügyfél által korábban megadott helyi állapotot.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\>, ha a felhasználó megtagadja a hozzáférési kérést, ha a kérelem érvénytelen, az ügyfél a következő paraméterekkel bővült az átirányítás: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Engedélyezési kérelem|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazásnak el kell küldenie a felhasználót az engedélyezési végpontnak a OAuth folyamat elindításához.          Az engedélyezési végponton a felhasználó hitelesíti és engedélyezi vagy megtagadja az alkalmazáshoz való hozzáférést.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> feltételezve, hogy az erőforrás tulajdonosa hozzáférést biztosít, az engedélyezési kiszolgáló a korábban megadott átirányítási URI-val (a kérelemben vagy az ügyfél-regisztráció során) átirányítja a felhasználói ügynököt az ügyfélnek.  Az átirányítási URI tartalmaz egy engedélyezési kódot és az ügyfél által korábban megadott helyi állapotot. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> az ügyfél hozzáférési jogkivonatot kér az engedélyezési kiszolgáló jogkivonat-végpontján az előző lépésben fogadott engedélyezési kóddal együtt.  A kérelem végrehajtásakor az ügyfél hitelesíti magát az engedélyezési kiszolgálóval.  Az ügyfél tartalmazza a hitelesítési kód beszerzéséhez használt átirányítási URI-t. </p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, érvényesíti az engedélyezési kódot, és biztosítja, hogy a fogadott átirányítási URI megfelel az ügyfél átirányításához használt URI-nak (C).  Ha érvényes, az engedélyezési kiszolgáló visszaválaszol egy hozzáférési jogkivonattal, és opcionálisan egy frissítési tokent is. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> ha a kérelem ügyfél-hitelesítése sikertelen vagy érvénytelen, az engedélyezési kiszolgáló a HTTP 400 (hibás kérelem) állapotkódot válaszol (kivéve, ha másként van megadva), és az alábbi paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél egy kérést küld a jogkivonat-végpontnak a következő paraméterek küldésével az "application/x-www-Form-urlencoded" formátumban az UTF-8 karakterkódolásával a HTTP-kérelem entitás-törzsében. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> az engedélyezési kiszolgáló kiadja a hozzáférési jogkivonatot és opcionális frissítési jogkivonatot, és létrehozza a választ úgy, hogy hozzáadja a következő paramétereket a HTTP-válasz entitás-törzséhez egy 200 (OK) állapotkód használatával. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> az ügyfél hitelesíti magát az engedélyezési kiszolgálóval, és hozzáférési jogkivonatot kér a jogkivonat-végponttól. </p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, és ha érvényes, a hozzáférési tokent bocsát ki. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> ha a kérelem sikertelen ügyfél-hitelesítése vagy érvénytelen, az engedélyezési kiszolgáló a HTTP 400 (hibás kérelem) állapotkódot válaszol (kivéve, ha másként van megadva), és az alábbi paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél a jogkivonat-végpontra irányuló kérést küld a következő paraméterek hozzáadásával az "application/x-www-Form-urlencoded" formátumban az UTF-8 karakterkódolásával a HTTP-kérelem entitás-törzsében. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> ha a hozzáférési jogkivonat kérelme érvényes és engedélyezett, az engedélyezési kiszolgáló kiadja a hozzáférési jogkivonatot és opcionális frissítési jogkivonatot, és létrehozza a választ úgy, hogy hozzáadja a következő paramétereket a HTTP-válasz entitás-törzséhez egy 200 (OK) állapotkóddal. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél kezdeményezi a folyamatot úgy, hogy az erőforrás-tulajdonos felhasználói ügynökét az engedélyezési végpontra irányítja.  Az ügyfél tartalmazza az ügyfél-azonosítót, a kért hatókört, a helyi állapotot, valamint egy átirányítási URI-t, amelyhez az engedélyezési kiszolgáló a hozzáférés engedélyezése (vagy elutasítása) után visszaküldi a felhasználói ügynököt. </p\> < p\> az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosát (a felhasználói ügynökön keresztül), és megállapítja, hogy az erőforrás tulajdonosa engedélyezi vagy megtagadja az ügyfél hozzáférési kérelmét. </p\> < p\> feltételezve, hogy az erőforrás-tulajdonos hozzáférést biztosít, az engedélyezési kiszolgáló a korábban megadott átirányítási URI használatával átirányítja a felhasználói ügynököt az ügyfélnek.  Az átirányítási URI tartalmazza a hozzáférési tokent az URI-töredékben. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\>, ha az erőforrás tulajdonosa megtagadja a hozzáférési kérelmet, vagy ha a kérés nem hiányzik vagy érvénytelen átirányítási URI-t tartalmaz, az engedélyezési kiszolgáló értesíti az ügyfelet az átirányítási URI töredék összetevőjéhez az "application/x-www-Form-urlencoded" formátum használatával. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazásnak el kell küldenie a felhasználót az engedélyezési végpontnak a OAuth folyamat elindításához.      Az engedélyezési végponton a felhasználó hitelesíti és engedélyezi vagy megtagadja az alkalmazáshoz való hozzáférést. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\>, ha az erőforrás tulajdonosa engedélyezi a hozzáférési kérést, az engedélyezési kiszolgáló kiadja a hozzáférési jogkivonatot, és továbbítja azt az ügyfélnek az "application/x-www-Form-urlencoded" formátum használatával a következő paraméterek hozzáadásával az átirányítási URI töredék összetevőjéhez. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Az engedélyezési kód folyamata olyan ügyfelek számára van optimalizálva, amelyek képesek a hitelesítő adataik titkosságának megőrzésére (például a PHP, a Java, a Python, a Ruby, a ASP.NET stb. használatával megvalósított webkiszolgálói alkalmazások).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Engedélyezési kód engedélyezése|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Az ügyfél-hitelesítő adatok folyamata olyan esetekben megfelelő, amikor az ügyfél (az alkalmazás) hozzáférést kér a védett erőforrásokhoz a vezérlőn belül. Az ügyfél erőforrás-tulajdonosnak minősül, így nincs szükség végfelhasználói beavatkozásra.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Ügyfél hitelesítő adatainak megadása|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Az implicit folyamat olyan ügyfelek számára van optimalizálva, amelyek nem képesek a hitelesítő adatok titkosságának fenntartására, hogy egy adott átirányítási URI-t működtessenek. Ezeket az ügyfeleket általában egy parancsfájlban, például a JavaScript használatával implementálják a böngészőben.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit támogatás|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Az erőforrás-tulajdonos jelszavának hitelesítő adatai folyamata olyan esetekben megfelelő, amikor az erőforrás tulajdonosa megbízhatósági kapcsolatban áll az ügyféllel (az alkalmazással), például az eszköz operációs rendszerével vagy egy magas jogosultsági szintű alkalmazással. Ez a folyamat olyan ügyfelek számára megfelelő, akik képesek az erőforrás-tulajdonos hitelesítő adatainak beszerzésére (a felhasználónevet és a jelszót általában interaktív formában használják).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Erőforrás-tulajdonos jelszavas hitelesítő adatok megadása|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> az erőforrás tulajdonosa megadja az ügyfelet a felhasználónevével és jelszavával. </p\> < p\> az ügyfél egy hozzáférési jogkivonatot kér az engedélyezési kiszolgáló jogkivonat-végpontján azáltal, hogy tartalmazza az erőforrás tulajdonosától kapott hitelesítő adatokat.  A kérelem végrehajtásakor az ügyfél hitelesíti magát az engedélyezési kiszolgálóval. </p\> < p\> az engedélyezési kiszolgáló hitelesíti az ügyfelet, és érvényesíti az erőforrás-tulajdonos hitelesítő adatait, és ha érvényes, kiadja a hozzáférési jogkivonatot. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> ha a kérelem sikertelen ügyfél-hitelesítése vagy érvénytelen, az engedélyezési kiszolgáló a HTTP 400 (hibás kérelem) állapotkódot válaszol (kivéve, ha másként van megadva), és az alábbi paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél a jogkivonat-végpontra irányuló kérést küld a következő paraméterek hozzáadásával az "application/x-www-Form-urlencoded" formátumban az UTF-8 karakterkódolásával a HTTP-kérelem entitás-törzsében. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> ha a hozzáférési jogkivonat kérelme érvényes és engedélyezett, az engedélyezési kiszolgáló kiadja a hozzáférési jogkivonatot és opcionális frissítési jogkivonatot, és létrehozza a választ úgy, hogy hozzáadja a következő paramétereket a HTTP-válasz entitás-törzséhez egy 200 (OK) állapotkóddal. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Hozzáférési jogkivonat kérése|  
|OAuth2Step_AuthorizationRequest_Name|Engedélyezési kérelem|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiállított hozzáférési jogkivonat.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiállított hozzáférési jogkivonat.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Szükséges. Az engedélyezési kiszolgáló által kiállított hozzáférési jogkivonat.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiállított hozzáférési jogkivonat.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Szükséges. Ügyfél-azonosító.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|KÖTELEZŐ, ha az ügyfél nem hitelesíti magát az engedélyezési kiszolgálóval.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Szükséges. Az ügyfél-azonosító.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Szükséges. Az engedélyezési kiszolgáló által generált engedélyezési kód.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Szükséges. Az engedélyezési kiszolgálótól kapott engedélyezési kód.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Választható. Az ember által olvasható ASCII-szöveg további információkat biztosít.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Választható. Az ember által olvasható ASCII-szöveg további információkat biztosít.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Választható. Az ember által olvasható ASCII-szöveg további információkat biztosít.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Választható. Az ember által olvasható ASCII-szöveg további információkat biztosít.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Választható. Az ember által olvasható ASCII-szöveg további információkat biztosít.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Választható. A hibával kapcsolatos információkat tartalmazó, ember által olvasható weblapot azonosító URI.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Választható. A hibával kapcsolatos információkat tartalmazó, ember által olvasható weblapot azonosító URI.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Választható. A hibával kapcsolatos információkat tartalmazó, ember által olvasható weblapot azonosító URI.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Választható. A hibával kapcsolatos információkat tartalmazó, ember által olvasható weblapot azonosító URI.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Választható. A hibával kapcsolatos információkat tartalmazó, ember által olvasható weblapot azonosító URI.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Szükséges. Egyetlen ASCII-hibakód a következők közül: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Szükséges. Egyetlen ASCII-hibakód a következők közül: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII-hibakód a következők közül: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Szükséges. Az értéket "authorization_code" értékre kell beállítani.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Szükséges. Az értéket "client_credentials" értékre kell beállítani.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az értéket a "password" értékre kell beállítani.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az erőforrás tulajdonosának jelszava.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Választható. Az átirányítási végpont URI azonosítójának abszolút URI-nak kell lennie.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|KÖTELEZŐ, ha a "redirect_uri" paraméter szerepel az engedélyezési kérelemben, és az értékeknek azonosnak kell lenniük.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Választható. Az átirányítási végpont URI azonosítójának abszolút URI-nak kell lennie.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Választható. A frissítési token, amely új hozzáférési tokenek beszerzésére használható.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Választható. A frissítési token, amely új hozzáférési tokenek beszerzésére használható.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Választható. A frissítési token, amely új hozzáférési tokenek beszerzésére használható.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Szükséges. Az értéket "code" értékre kell beállítani.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Szükséges. Az értéket "token" értékre kell beállítani.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél által kért hatókörrel; Ellenkező esetben kötelező.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél által kért hatókörrel; Ellenkező esetben kötelező.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|Nem kötelező, ha megegyezik az ügyfél által kért hatókörrel; Ellenkező esetben kötelező.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Nem kötelező, ha megegyezik az ügyfél által kért hatókörrel; Ellenkező esetben kötelező.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|KÖTELEZŐ, ha az "State" paraméter szerepel az ügyfél-engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Ajánlott. Az ügyfél által a kérelem és a visszahívás közötti állapot fenntartására használt átlátszatlan érték.  Az engedélyezési kiszolgáló ezt az értéket tartalmazza, amikor a felhasználó-ügynök visszairányítja az ügyfelet.  A paramétert a helyek közötti kérelmek hamisításának megelőzésére kell használni.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|KÖTELEZŐ, ha az "State" paraméter szerepel az ügyfél-engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|KÖTELEZŐ, ha az "State" paraméter szerepel az ügyfél-engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Ajánlott. Az ügyfél által a kérelem és a visszahívás közötti állapot fenntartására használt átlátszatlan érték.  Az engedélyezési kiszolgáló ezt az értéket tartalmazza, amikor a felhasználó-ügynök visszairányítja az ügyfelet.  A paramétert a helyek közötti kérelmek hamisításának megelőzésére kell használni.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|KÖTELEZŐ, ha az "State" paraméter szerepel az ügyfél-engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Szükséges. A kiállított jogkivonat típusa.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Szükséges. A kiállított jogkivonat típusa.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Szükséges. A kiállított jogkivonat típusa.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Szükséges. A kiállított jogkivonat típusa.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az erőforrás-tulajdonos felhasználóneve.|  
|OAuth2UnsupportedTokenType|A (z) "{0}" jogkivonat-típus nem támogatott.|  
|OAuth2InvalidState|Érvénytelen válasz az engedélyezési kiszolgálótól|  
|OAuth2GrantType_AuthorizationCode|Engedélyezési kód|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Ügyfél-hitelesítő adatok|  
|OAuth2GrantType_ResourceOwnerPassword|Erőforrás-tulajdonos jelszava|  
|WebDocumentation302Code|302 található|  
|WebDocumentation400Code|400 (hibás kérés)|  
|OAuth2SendingMethod_AuthHeader|Engedélyezési fejléc|  
|OAuth2SendingMethod_QueryParam|Lekérdezési paraméter|  
|OAuth2AuthorizationServerGeneralException|Hiba történt a hozzáférés engedélyezésekor {0}|  
|OAuth2AuthorizationServerCommunicationException|Nem sikerült HTTP-kapcsolatokat létesíteni az engedélyezési kiszolgálóval, vagy váratlanul lezárult.|  
|WebDocumentationOAuth2GeneralErrorMessage|Váratlan hiba történt.|  
|AuthorizationServerCommunicationException|Az engedélyezési kiszolgáló kommunikációs kivétel történt. Forduljon a rendszergazdához.|  
|TextblockSubscriptionKeyHeaderDescription|Az ehhez az API-hoz hozzáférést biztosító előfizetési kulcs. A < egy href = '/Developer '\>profilt </a\>található.|  
|TextblockOAuthHeaderDescription|OAuth 2,0 < i\>{0}</i\>által beszerzett hozzáférési jogkivonat. Támogatott engedélyezési típusok: < i\>{1}</i\>.|  
|TextblockContentTypeHeaderDescription|Az API-nak eljuttatott törzs adathordozó-típusa.|  
|ErrorMessageApiNotAccessible|A hívni próbált API jelenleg nem érhető el. Vegye fel a kapcsolatot az API-közzétevővel < href = "/Issues"\>itt </a\>.|  
|ErrorMessageApiTimedout|A hívni próbált API a szokásosnál hosszabb időt vesz igénybe. Vegye fel a kapcsolatot az API-közzétevővel < href = "/Issues"\>itt </a\>.|  
|BadRequestParameterExpected|a rendszer "{0}" paramétert várt: "|  
|TooltipTextDoubleClickToSelectAll|Kattintson duplán az összes kiválasztásához.|  
|TooltipTextHideRevealSecret|Megjelenítés/elrejtés|  
|ButtonLinkOpenConsole|Kipróbálom|  
|SectionHeadingRequestBody|A kérés törzse|  
|SectionHeadingRequestParameters|Kérelmek paramétereinek megadása|  
|SectionHeadingRequestUrl|Kérés URL-címe|  
|SectionHeadingResponse|Válasz|  
|SectionHeadingRequestHeaders|Kérelemfejlécek|  
|FormLabelSubtextOptional|választható|  
|SectionHeadingCodeSamples|Kódminták|  
|TextblockOpenidConnectHeaderDescription|Az < i\>{0}</i\>ból beszerzett OpenID Connect ID-jogkivonat. Támogatott engedélyezési típusok: < i\>{1}</i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelBack|vissza|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|Küldjön nekünk e-mailt|  
|PageTitleError|Sajnos hiba történt a kért lap kiszolgálásakor|  
|TextblockPotentialCauseIntermittentIssue|Ez lehet egy időszakos adathozzáférési probléma, amely már elment.|  
|TextblockPotentialCauseOldLink|Előfordulhat, hogy a hivatkozás, amelyre kattintott, elavult, és nem a megfelelő helyre mutat.|  
|TextblockPotentialCauseTechnicalProblem|Lehetséges, hogy technikai probléma van a végén.|  
|TextblockPotentialSolutionRefresh|Próbálja meg frissíteni a lapot.|  
|TextblockPotentialSolutionStartOver|Kezdje a {0}.|  
|TextblockPotentialSolutionTryAgain|Lépjen {0}, és próbálja meg újra végrehajtani a műveletet.|  
|TextReportProblem|{0} leírja, hogy mi volt a baj, és a lehető leghamarabb megvizsgáljuk.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Valószínűleg csak egy ideiglenes probléma, néhány dolog, amit kipróbálhat|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebIssuesIndexTitle|Problémák|  
|WebIssuesNoActiveSubscriptions|Nincsenek aktív előfizetések. A probléma bejelentéséhez elő kell fizetnie egy termékre.|  
|WebIssuesNotSignin|Nincs bejelentkezve. A probléma bejelentéséhez vagy a Megjegyzés közzétételéhez {0}.|  
|WebIssuesReportIssueButton|Probléma jelentése|  
|WebIssuesSignIn|bejelentkezés|  
|WebIssuesStatusReportedBy|Állapot: {0} &#124; által jelentett {1}|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|Küldjön nekünk e-mailt|  
|PageTitleNotFound|Sajnos nem található a keresett oldal|  
|TextblockPotentialCauseMisspelledUrl|Előfordulhat, hogy hibásan írta be az URL-címet, ha beírta a-ben.|  
|TextblockPotentialCauseOldLink|Előfordulhat, hogy a hivatkozás, amelyre kattintott, elavult, és nem a megfelelő helyre mutat.|  
|TextblockPotentialSolutionRetype|Próbálja meg újból beírni az URL-címet.|  
|TextblockPotentialSolutionStartOver|Kezdje a {0}.|  
|TextReportProblem|{0} leírja, hogy mi volt a baj, és a lehető leghamarabb megvizsgáljuk.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Lehetséges megoldás|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebProductsAgreement|{0} termékre való feliratkozással Elfogadom a `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Használati feltételek|  
|WebProductsSubscribeButton|Előfizetés|  
|WebProductsUsageLimitsHeader|Használati korlátok|  
|WebProductsYouAreNotSubscribed|Erre a termékre előfizetett.|  
|WebProductsYouRequestedSubscription|A termékre való előfizetést kért.|  
|ErrorYouNeedToAgreeWithLegalTerms|A folytatáshoz el kell fogadnia a használati feltételeket.|  
|ButtonLabelAddSubscription|Előfizetés hozzáadása|  
|LinkLabelChangeSubscriptionName|módosítása|  
|ButtonLabelConfirm|Megerősítés|  
|TextblockMultipleSubscriptionsCount|{0} előfizetése van a termékre:|  
|TextblockSingleSubscriptionsCount|{0} előfizetése van a termékre:|  
|TextblockSingleApisCount|Ez a termék {0} API-t tartalmaz:|  
|TextblockMultipleApisCount|Ez a termék {0} API-kat tartalmaz:|  
|TextblockHeaderSubscribe|Előfizetés a termékre|  
|TextblockSubscriptionDescription|A rendszer új előfizetést hoz létre a következő módon:|  
|TextblockSubscriptionLimitReached|Elérte az előfizetések korlátját.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleProducts|Termékek|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Név|Szöveg|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|A bejelentkezést a rendszergazdák jelenleg letiltották.|  
|TextboxExternalIdentitiesSigninInvitation|Másik lehetőségként jelentkezzen be|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Bejelentkezés a rel:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Név|Szöveg|  
|----------|----------|  
|PrincipalNotFound|A rendszerbiztonsági tag nem található, vagy az aláírás érvénytelen.|  
|ErrorSsoAuthenticationFailed|Az SSO-hitelesítés nem sikerült|  
|ErrorSsoAuthenticationFailedDetailed|Érvénytelen lexikális elem van megadva, vagy az aláírás nem ellenőrizhető.|  
|ErrorSsoTokenInvalid|Az SSO-jogkivonat érvénytelen|  
|ValidationErrorSpecificEmailAlreadyExists|A (z) "{0}" e-mail már regisztrálva van|  
|ValidationErrorSpecificEmailInvalid|A (z){0}e-mail-cím érvénytelen|  
|ValidationErrorPasswordInvalid|A jelszó érvénytelen. Javítsa ki a hibákat, és próbálkozzon újra.|  
|PropertyTooShort|túl rövid {0}|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Érvénytelen e-mail cím.|  
|ValidationMessageNewPasswordConfirmationRequired|Új jelszó megerősítése|  
|ValidationErrorPasswordConfirmationRequired|A Jelszó megerősítése üres|  
|WebAuthenticationEmailChangeNotice|A megerősítő e-mailek módosításának módja a {0}. Az új e-mail-cím megerősítéséhez kövesse az itt található utasításokat. Ha az e-mail nem érkezik meg a Beérkezett üzenetek mappájába a következő néhány percben, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationEmailChangeNoticeHeader|Az e-mail-módosítási kérelme sikeresen feldolgozva|  
|WebAuthenticationEmailChangeNoticeTitle|E-mail-módosítás kérelmezve|  
|WebAuthenticationEmailHasBeenRevertedNotice|Már létezik e-mail-cím. A kérelem visszavonásra került|  
|ValidationErrorEmailAlreadyExists|Az e-mail már létezik|  
|ValidationErrorEmailInvalid|Érvénytelen e-mail cím|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|E-mail-cím megadása kötelező.|  
|WebAuthenticationErrorNoticeHeader|Hiba|  
|WebAuthenticationFieldLengthErrorMessage|a {0}nak maximális hosszúságú kell lennie {1}|  
|TextboxLabelEmailFirstName|Keresztnév|  
|ValidationErrorFirstNameRequired|A keresztnév megadása kötelező.|  
|ValidationErrorFirstNameInvalid|Érvénytelen keresztnév|  
|NoticeInvalidInvitationToken|Vegye figyelembe, hogy a megerősítő hivatkozások csak 48 óráig érvényesek. Ha továbbra is ezen az időkereten belül van, ellenőrizze, hogy helyes-e a hivatkozás. Ha a hivatkozás lejárt, akkor ismételje meg a megerősíteni kívánt műveletet.|  
|NoticeHeaderInvalidInvitationToken|Érvénytelen meghívó token|  
|NoticeTitleInvalidInvitationToken|Megerősítési hiba|  
|WebAuthenticationLastNameInvalidErrorMessage|Érvénytelen vezetéknév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|ValidationErrorLastNameRequired|A vezetéknév megadása kötelező.|  
|WebAuthenticationLinkExpiredNotice|A jóváhagyásra küldött hivatkozás lejárt. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A jelszó-visszaállítási hivatkozás érvénytelen vagy lejárt.|  
|WebAuthenticationLinkExpiredNoticeTitle|Hivatkozás elküldve|  
|WebAuthenticationNewPasswordLabel|Új jelszó|  
|ValidationMessageNewPasswordRequired|Új jelszó szükséges.|  
|TextboxLabelNotificationsSenderEmail|Értesítések feladójának e-mail címe|  
|TextboxLabelOrganizationName|Szervezet neve|  
|WebAuthenticationOrganizationRequiredErrorMessage|A szervezet neve üres|  
|WebAuthenticationPasswordChangedNotice|A jelszó frissítése sikerült|  
|WebAuthenticationPasswordChangedNoticeTitle|Jelszó frissítve|  
|WebAuthenticationPasswordCompareErrorMessage|A jelszavak nem egyeznek|  
|WebAuthenticationPasswordConfirmLabel|Jelszó megerősítése|  
|ValidationErrorPasswordInvalidDetailed|A jelszó túl gyenge.|  
|WebAuthenticationPasswordLabel|Jelszó|  
|ValidationErrorPasswordRequired|A jelszó megadása kötelező.|  
|WebAuthenticationPasswordResetSendNotice|Jelszó megerősítő e-mail-címének módosítása {0}. A jelszó-módosítási folyamat folytatásához kövesse az e-mailben található utasításokat.|  
|WebAuthenticationPasswordResetSendNoticeHeader|A jelszó-visszaállítási kérelem feldolgozása sikeresen megtörtént|  
|WebAuthenticationPasswordResetSendNoticeTitle|Új jelszó kérése|  
|WebAuthenticationRequestNotFoundNotice|A kérelem nem található|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Az értesítések feladójának e-mail-címe üres|  
|WebAuthenticationSigninPasswordLabel|Kérjük, erősítse meg a módosítást jelszó megadásával|  
|WebAuthenticationSignupConfirmNotice|A regisztráció megerősítő e-mail-címe a {0}. < br/\> kérjük, kövesse az e-mailben található utasításokat a fiók aktiválásához. < br/\>, ha az e-mail nem érkezik meg a Beérkezett üzenetek mappájába a következő néhány percen belül, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationSignupConfirmNoticeHeader|A fiók létrehozása sikerült|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|A regisztrációs visszaigazoló e-mailt újra elküldtük|  
|WebAuthenticationSignupConfirmNoticeTitle|Fiók létrehozva|  
|WebAuthenticationTokenRequiredErrorMessage|A jogkivonat üres|  
|WebAuthenticationUserAlreadyRegisteredNotice|Úgy tűnik, hogy az e-mail-címmel rendelkező felhasználó már regisztrálva van a rendszeren. Ha elfelejtette a jelszavát, próbálja meg visszaállítani, vagy forduljon a támogatási csapathoz.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|A felhasználó már regisztrálva van|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Már regisztrálva van|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelChangeAccountInfo|Fiókadatok módosítása|  
|ButtonLabelCloseAccount|Fiók lezárása|  
|WebAuthenticationInvalidCaptchaErrorMessage|A beírt szöveg nem egyezik meg a képen látható szöveggel. Kérjük, próbálkozzon újból.|  
|ValidationErrorCredentialsInvalid|Az e-mail vagy a jelszó érvénytelen. Javítsa ki a hibákat, és próbálkozzon újra.|  
|WebAuthenticationRequestIsNotValid|A kérelem érvénytelen.|  
|WebAuthenticationUserIsNotConfirm|A bejelentkezés megkísérlése előtt erősítse meg a regisztrációt.|  
|WebAuthenticationInvalidEmailFormated|Az e-mail érvénytelen: {0}|  
|WebAuthenticationUserNotFound|A felhasználó nem található|  
|WebAuthenticationTenantNotRegistered|A fiókja olyan Azure Active Directory bérlőhöz tartozik, amely nem jogosult a portál elérésére.|  
|WebAuthenticationAuthenticationFailed|A hitelesítés sikertelen volt.|  
|WebAuthenticationGooglePlusNotEnabled|A hitelesítés sikertelen volt. Ha Ön engedélyezte az alkalmazást, forduljon a rendszergazdához, és győződjön meg arról, hogy a Google-hitelesítés megfelelően van konfigurálva.|  
|ValidationErrorAllowedTenantIsRequired|Engedélyezett bérlő megadása kötelező|  
|ValidationErrorTenantIsNotValid|A (z){0}Azure Active Directory bérlő érvénytelen.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Jelentkezzen be {0}-fiókjával|  
|WebAuthenticationUserLimitNotice|A szolgáltatás elérte az engedélyezett felhasználók maximális számát. Kérjük, `<a href="mailto:{0}"\>contact the administrator</a\>` a szolgáltatás frissítésére, és engedélyezze újra a felhasználói regisztrációt.|  
|WebAuthenticationUserLimitNoticeHeader|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserLimitNoticeTitle|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserRegistrationDisabledNotice|A rendszergazda letiltotta a felhasználók regisztrációját. Jelentkezzen be külső identitás-szolgáltatóval.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Felhasználói regisztráció letiltva|  
|WebAuthenticationSignupPendingConfirmationNotice|Ahhoz, hogy elvégezze a fiók létrehozását, ellenőrizni kell az e-mail-címét. Küldtünk egy e-mailt a {0}. A fiók aktiválásához kövesse az e-mailben szereplő utasításokat. Ha az e-mail-cím nem érkezik meg a következő néhány percen belül, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Nem megerősített fiókot találtunk az e-mail cím {0}. A fiók létrehozásának befejezéséhez ellenőrizni kell az e-mail címét. Küldtünk egy e-mailt a {0}. A fiók aktiválásához kövesse az e-mailben szereplő utasításokat. Ha az e-mail-cím nem érkezik meg a következő néhány percen belül, ellenőrizze a levélszemét mappáját|  
|WebAuthenticationSignupConfirmationAlmostDone|Majdnem kész|  
|WebAuthenticationSignupConfirmationEmailSent|Küldtünk egy e-mailt a {0}. A fiók aktiválásához kövesse az e-mailben szereplő utasításokat. Ha az e-mail-cím nem érkezik meg a következő néhány percen belül, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationEmailSentNotificationMessage|Az e-mail sikeresen elküldve {0}|  
|WebAuthenticationNoAadTenantConfigured|Nincs Azure Active Directory bérlő konfigurálva a szolgáltatáshoz.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Elfogadom a `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Kérjük, tekintse át `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Használati feltételek|  
|ValidationMessageConsentNotAccepted|A folytatáshoz el kell fogadnia a használati feltételeket.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebAuthenticationForgotPassword|Elfelejtette jelszavát?|  
|WebAuthenticationIfAdministrator|Ha Ön rendszergazda, `<a href="{0}"\>here</a\>`kell bejelentkeznie.|  
|WebAuthenticationNotAMember|Még nem tag? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Megjegyzés a számítógépen|  
|WebAuthenticationSigininWithPassword|Bejelentkezés felhasználónévvel és jelszóval|  
|WebAuthenticationSigninTitle|Bejelentkezés|  
|WebAuthenticationSignUpNow|Regisztráljon most|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleSignup|Regisztráció|  
|WebAuthenticationAlreadyAMember|Már van tag?|  
|WebAuthenticationCreateNewAccount|Új API Management fiók létrehozása|  
|WebAuthenticationSigninNow|Bejelentkezés most|  
|ButtonLabelSignup|Regisztráció|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|SubscriptionCancelConfirmation|Biztosan meg kívánja szakítani ezt az előfizetést?|  
|SubscriptionRenewConfirmation|Biztosan meg kívánja újítani ezt az előfizetést?|  
|WebDevelopersManageSubscriptions|Előfizetések kezelése|  
|WebDevelopersPrimaryKey|Elsődleges kulcs|  
|WebDevelopersRegenerateLink|Újragenerálás|  
|WebDevelopersSecondaryKey|Másodlagos kulcs|  
|ButtonLabelShowKey|Megjelenítés|  
|ButtonLabelRenewSubscription|Frissítés|  
|WebDevelopersSubscriptionRequested|Kérelem {0}|  
|WebDevelopersSubscriptionRequestedState|Kérve|  
|WebDevelopersSubscriptionTableNameHeader|Név|  
|WebDevelopersSubscriptionTableStateHeader|State|  
|WebDevelopersUsageStatisticsLink|Elemzési jelentések|  
|WebDevelopersYourSubscriptions|Előfizetések|  
|SubscriptionPropertyLabelRequestedDate|Igénylés bekapcsolva|  
|SubscriptionPropertyLabelStartedDate|Elindítva|  
|PageTitleRenameSubscription|Előfizetés átnevezése|  
|SubscriptionPropertyLabelName|Előfizetés neve|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Név|Szöveg|  
|----------|----------|  
|SectionHeadingCloseAccount|Szeretné lezárta a fiókját?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Elrejtése|  
|ButtonLabelRegenerateKey|Újragenerálás|  
|InformationMessageKeyWasRegenerated|Biztosan újra szeretné állítani ezt a kulcsot?|  
|ButtonLabelShowKey|Megjelenítés|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profil frissítése|  
|PageTitleUpdateProfile|Fiókadatok frissítése|  
  
###  <a name="UserProfile"></a>UserProfile  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Fiókadatok módosítása|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelCloseAccount|Fiók lezárása|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Keresztnév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|TextboxLabelNotificationsSenderEmail|Értesítések feladójának e-mail címe|  
|TextboxLabelOrganizationName|Szervezet neve|  
|SubscriptionStateActive|Active|  
|SubscriptionStateCancelled|Megszakítva|  
|SubscriptionStateExpired|Elévült|  
|SubscriptionStateRejected|Elutasítva|  
|SubscriptionStateRequested|Kérve|  
|SubscriptionStateSuspended|Felfüggesztve|  
|DefaultSubscriptionNameTemplate|{0} (alapértelmezett)|  
|SubscriptionNameTemplate|Fejlesztői hozzáférés #{0}|  
|TextboxLabelSubscriptionName|Előfizetés neve|  
|ValidationMessageSubscriptionNameRequired|Az előfizetés neve nem lehet üres.|  
|ApiManagementUserLimitReached|A szolgáltatás elérte az engedélyezett felhasználók maximális számát. Frissítsen magasabb díjszabási szintre.|  
  
##  <a name="glyphs"></a>Karakterjel-erőforrások  
 API Management fejlesztői portál sablonjai a Glyphicons származó karakterjeleket használhatják a [bootstrapből](https://getbootstrap.com/components/#glyphicons). Ez a karakterjel-készlet több mint 250 karakterjelet tartalmaz a [Glyphicon](https://glyphicons.com/) félszerzetek készletből származó betűkészlet formátumban. Ha a készletből karakterjelet szeretne használni, használja a következő szintaxist.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 A karakterjelek teljes listájáért lásd: [Glyphicons from bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>További lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).

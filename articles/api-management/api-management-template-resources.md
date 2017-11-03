---
title: "Azure API Management-sablon erőforrások |} Microsoft Docs"
description: "További tudnivalók a típusú erőforrás fejlesztői portál sablonok az Azure API Management használható."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 212e7ea7bb2ffea63c7ba210195df0da38aa8f0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-resources"></a>Azure API Management-sablon erőforrások
Az Azure API Management az alábbi típusú erőforrások számára biztosít a fejlesztők a portál sablonok.  
  
-   [Karakterlánc-erőforrások](#strings)  
  
-   [A betűkép-erőforrások](#glyphs)  
  
##  <a name="strings"></a>Karakterlánc-erőforrások  
 Az API Management széles választékát erőforrásait a fejlesztői portálra számára biztosít. Ezek az erőforrások vannak honosítva API Management által támogatott nyelveket. Sablonok alapértelmezett készletét erőforrásokat használó oldalfejlécben, címkéket és állandó karakterláncokkal, amelyek megjelennek a fejlesztői portálján. A sablonok karakterlánc erőforrás használatához adja meg a erőforrás-karakterlánc előtagot, karakterlánc neve követ, a következő példában látható módon.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 A következő példa a termék lista sablonból, és megjeleníti **termékek** az oldal tetején.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
 Tekintse át az alábbi táblázatok a fejlesztői portál sablonok használható erőforrásait. A táblázat nevét használják az előtag a tábla erőforrásait.  
  
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
  
-   [Felhasználói profil](#UserProfile)  
  
###  <a name="ApisStrings"></a>ApisStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleApis|API-k|  
  
###  <a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsDetailsTitle|Alkalmazás megtekintése|  
|WebApplicationsRequirementsHeader|Követelmények|  
|WebApplicationsScreenshotAlt|Képernyőfelvétel|  
|WebApplicationsScreenshotsHeader|Képernyőfelvételek|  
  
###  <a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Biztosan el kívánja távolítani alkalmazást?|  
|WebDevelopersAppNotPublished|Nincs közzétéve|  
|WebDevelopersAppNotSubminted|Nem elküldve|  
|WebDevelopersAppTableCategoryHeader|Kategória|  
|WebDevelopersAppTableNameHeader|Név|  
|WebDevelopersAppTableStateHeader|Állapot|  
|WebDevelopersEditLink|Szerkesztés|  
|WebDevelopersRegisterAppLink|Alkalmazás regisztrálása|  
|WebDevelopersRemoveLink|Eltávolítás|  
|WebDevelopersSubmitLink|Küldés|  
|WebDevelopersYourApplicationsHeader|Az alkalmazások|  
  
###  <a name="AppStrings"></a>AppStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsHeader|Alkalmazások|  
  
###  <a name="CommonResources"></a>CommonResources  
  
|Név|Szöveg|  
|----------|----------|  
|NoItemsToDisplay|Nincs eredmény.|  
|GeneralExceptionMessage|Hiba található nem megfelelő. Ideiglenes működési hiba vagy egy hiba lehet. Kérjük próbálkozzon újra.|  
|GeneralJsonExceptionMessage|Hiba található nem megfelelő. Ideiglenes működési hiba vagy egy hiba lehet. Kérjük frissítse az oldalt, és próbálkozzon újra.|  
|ConfirmationMessageUnsavedChanges|Egyes nem mentett módosítások vannak. Biztosan megszakítja, és elveti a módosításokat?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|HTTP kérelem törzse túl nagy.|  
  
###  <a name="CommonStrings"></a>CommonStrings  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelCancel|Mégse|  
|ButtonLabelSave|Mentés|  
|GeneralExceptionMessage|Hiba található nem megfelelő. Ideiglenes működési hiba vagy egy hiba lehet. Kérjük próbálkozzon újra.|  
|NoItemsToDisplay|Nincsenek megjeleníthető elemek.|  
|PagerButtonLabelFirst|Első|  
|PagerButtonLabelLast|utolsó|  
|PagerButtonLabelNext|Következő lépés|  
|PagerButtonLabelPrevious|Előző|  
|PagerLabelPageNOfM|{0} lap {1}|  
|PasswordTooShort|A jelszó nem túl rövid|  
|EmailAsPassword|Ne használja az e-maileket a jelszót|  
|PasswordSameAsUserName|A jelszó nem tartalmazhatja a felhasználónevet|  
|PasswordTwoCharacterClasses|Használjon különböző karakterkészletnek osztályok|  
|PasswordTooManyRepetitions|Túl sok ismétlődését|  
|PasswordSequenceFound|A jelszó karaktersorozatokat tartalmaz|  
|PagerLabelPageSize|Oldalméret|  
|CurtainLabelLoading|Betöltése...|  
|TablePlaceholderNothingToDisplay|A kijelölt időszak és a hatókör nincs elérhető adat|  
|ButtonLabelClose|Bezárás|  
  
###  <a name="Documentation"></a>Dokumentáció  
  
|Név|Szöveg|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Érvénytelen fejléc "{0}"|  
|WebDocumentationInvalidRequestErrorMessage|Érvénytelen a kérelem URL-címe|  
|TextboxLabelAccessToken|Hozzáférési jogkivonat *|  
|DropdownOptionPrimaryKeyFormat|Elsődleges – {0}|  
|DropdownOptionSecondaryKeyFormat|Másodlagos-{0}|  
|WebDocumentationSubscriptionKeyText|Az Előfizetés kulcs|  
|WebDocumentationTemplatesAddHeaders|Kötelező HTTP-fejlécek hozzáadása|  
|WebDocumentationTemplatesBasicAuthSample|Alapszintű engedélyezési minta|  
|WebDocumentationTemplatesCurlForBasicAuth|Alapszintű engedélyezési használatra:--{username} felhasználó: {jelszó}|  
|WebDocumentationTemplatesCurlValuesForPath|Elérési út paraméterek ({...} útvonalként megjelenő), az Előfizetés kulcs és a lekérdezési paraméterek értékeit értékek megadása|  
|WebDocumentationTemplatesDeveloperKey|Adja meg az Előfizetés kulcs|  
|WebDocumentationTemplatesJavaApache|Ez a minta használja az Apache HTTP-ügyfél a HTTP-összetevők (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Adja meg a nem kötelező paraméter értékét, igény szerint|  
|WebDocumentationTemplatesPhpPackage|Ez a minta a HTTP_Request2 csomagot fogja használni. (További információ: http://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Adja meg az elérési út paraméterek ({...} útvonalként megjelenő) értékét, és a kérelem törzsében, szükség esetén|  
|WebDocumentationTemplatesRequestBody|Adja meg a kérelem törzsében|  
|WebDocumentationTemplatesRequiredParams|Adja meg a következő kötelező paraméterek értékét|  
|WebDocumentationTemplatesValuesForPath|Adja meg az elérési út paraméterek ({...} útvonalként megjelenő) értékét|  
|OAuth2AuthorizationEndpointDescription|Az engedélyezési végpont az erőforrás tulajdonosa kezelésének és az beszerzése egy hitelesítésengedélyezési szolgál.|  
|OAuth2AuthorizationEndpointName|engedélyezési végpont|  
|OAuth2TokenEndpointDescription|A jogkivonat végpontjához szerezze be a hozzáférési tokent az hitelesítésengedélyezési segítségével, vagy frissítse a jogkivonatot az ügyfél használják.|  
|OAuth2TokenEndpointName|token-végpont|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél kezdeményezi a folyamat során irányítja az erőforrás tulajdonosa felhasználói ügynök az engedélyezési végpont.  Az ügyfél tartalmazza az ügyfél-azonosítókra, megadott hatókörben, helyi, és amelyek a hitelesítési kiszolgáló küldeni fog a felhasználói ügynök vissza, ha hozzáférést (engedélyezett vagy tiltott) átirányítási URI.     < /p\> < p\> a hitelesítési kiszolgáló hitelesíti az erőforrás tulajdonosa (a felhasználói ügynök) keresztül, és megállapítja, hogy az erőforrás tulajdonosa engedélyezi vagy megtagadja az ügyfél-hozzáférési kérelem.     < /p\> < p\> feltéve, hogy az erőforrás tulajdonosa ad hozzáférést, a hitelesítési kiszolgáló átirányítja a felhasználói ügynök átirányítással megadott URI-Azonosítót az ügyfél korábbi (a kérésben vagy ügyfél registrati során a).  Az átirányítási URI Azonosítót tartalmazza az engedélyezési kód és bármely helyi az ügyfél által korábban meghatározott.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|< p\> a felhasználó megtagadja a hozzáférési kérelem a, ha a kérés nem érvényes, ha az ügyfél értesítést kap az átirányítási hozzáadni a következő paraméterekkel: < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Hitelesítési kérelem|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazás kell küldenie a felhasználó az engedélyezési végpont ahhoz, hogy az OAuth-folyamat elindítása.          Az engedélyezési végpont a felhasználó hitelesíti, és majd engedélyezi, vagy megtagadja a hozzáférést az alkalmazásba.     < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|< p\> feltéve, hogy az erőforrás tulajdonosa ad hozzáférést, engedélyezési kiszolgáló átirányítja a felhasználói ügynök átirányítással megadott URI-Azonosítót az ügyfél korábbi (ügyfélregisztráció során vagy a kérelem).  Az átirányítási URI Azonosítót tartalmazza az engedélyezési kód és bármely helyi az ügyfél által korábban meghatározott. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|< p\> az ügyfél olyan hozzáférési jogkivonatot kéri le a hitelesítési kiszolgáló "s jogkivonat végpontjához az előző lépésben kapott engedélyezési kódot-ot.  Amikor a kérést, a hitelesítési kiszolgáló hitelesíti az ügyfelet.  Az ügyfél az beszerzése az engedélyezési kód az ellenőrzéshez használt URI átirányítás tartalmazza. < /p\> < p\> a engedélyezési kiszolgáló hitelesíti az ügyfelet, az engedélyezési kódot, és biztosítja, hogy az átirányítási URI kapott megegyezik-e a átirányítja az ügyfelet (C) lépésben használt URI-azonosító.  Érvényes, ha a hitelesítési kiszolgáló válaszol, vissza olyan hozzáférési jogkivonatot, és opcionálisan egy frissítési jogkivonat. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|< p\> ügyfél-hitelesítési kérelem sikertelen volt, vagy nem érvényes, ha a hitelesítési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkóddal (kivéve, ha másként), és a válasz a következő paramétereket tartalmaz. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél egy kérést küld a token-végpont a következő paraméterek az "application/x-www-form-urlencoded" formátum használata egy forráskarakter-kódolás az UTF-8, a HTTP-kérelmek entitástörzsében küldésével. < /p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|< p\> a hitelesítési kiszolgáló kibocsát egy hozzáférési jogkivonat és opcionális frissítési jogkivonat, és hoz létre a válasz a következő paramétereket ad a 200 (OK) állapotkód: a HTTP-válasz törzsében. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|< p\> az ügyfél hitelesítse magát a hitelesítési kiszolgáló és egy hozzáférési jogkivonatot kér a jogkivonat végpontjához. < /p\> < p\> a hitelesítési kiszolgáló hitelesíti az ügyfelet, és érvényes, ha egy jogkivonatot állít ki. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Ha a kérelem nem sikerült az ügyfél-hitelesítéshez, vagy érvénytelen a hitelesítési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkóddal (kivéve, ha másként), és a válasz a következő paramétereket tartalmaz. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél egy kérést küld a token-végpont a következő paraméterek az "application/x-www-form-urlencoded" formátum használata egy forráskarakter-kódolás az UTF-8, a HTTP-kérelmek entitástörzsében hozzáadásával. < /p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Ha a hozzáférési token kérelem érvényes és engedélyezett, a hitelesítési kiszolgáló kibocsát egy hozzáférési jogkivonat és opcionális frissítési jogkivonat és hoz létre a válasz a következő paramétereket ad a 200-as HTTP-válasz törzsében  Állapotkód (OK). < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|< p\> az ügyfél kezdeményezi a folyamat során irányítja az erőforrás tulajdonosa "s felhasználói ügynök az engedélyezési végpont.  Az ügyfél tartalmazza az ügyfél-azonosítókra, megadott hatókörben, helyi, és amelyek a hitelesítési kiszolgáló küldeni fog a felhasználói ügynök vissza, ha hozzáférést (engedélyezett vagy tiltott) átirányítási URI. < /p\> < p\> a hitelesítési kiszolgáló hitelesíti az erőforrás tulajdonosa (a felhasználói ügynök) keresztül, és megállapítja, hogy az erőforrás tulajdonosa engedélyezi vagy megtagadja az ügyfél "s hozzáférési kérelem. < /p\> < p\> feltéve, hogy az erőforrás tulajdonosa ad hozzáférést, a hitelesítési kiszolgáló átirányítja a felhasználói ügynök az ügyfélnek az átirányítási URI korábban megadott használatával.  Az átirányítási URI Azonosítót a hozzáférési jogkivonat szerepel az URI-töredéket. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|< p\> Ha az erőforrás tulajdonosa letiltja a hozzáférési kérelem, vagy ha a kérelem sikertelen lesz, és ennek oka egy hiányzó vagy érvénytelen átirányítási URI Azonosítót, a hitelesítési kiszolgáló az ügyfél tájékoztatja a következő paramétereket ad a fragment összetevőt o f az átirányítási URI Azonosítót, az "application/x-www-form-urlencoded" formátumban. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|< p\> az ügyfélalkalmazás kell küldenie a felhasználó az engedélyezési végpont ahhoz, hogy az OAuth-folyamat elindítása.      Az engedélyezési végpont a felhasználó hitelesíti, és majd engedélyezi, vagy megtagadja a hozzáférést az alkalmazásba. < /p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|< p\> , ha az erőforrás tulajdonosa biztosít a hozzáférési kérelem, a hitelesítési kiszolgáló egy jogkivonatot állít ki, és továbbítja az ügyfél a következő paramétereket ad a fragment összetevőt az átirányítási URI Azonosítót, az "application/x-www használatával, -űrlap - urlencoded"formátumban. < /p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Hitelesítésikód-folyamata a hitelesítő adataikat (például a webkiszolgáló alkalmazások megvalósították a PHP, Java, Python, Ruby, ASP.NET, stb.) adatainak bizalmas alkalmas ügyfelek van optimalizálva.|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Kód hitelesítésengedélyezési|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Ügyfél-hitelesítő adatok folyamata az azokban az esetekben, ahol az ügyfél (az alkalmazás) kér az ellenőrzése alatt a védett erőforrások elérésére alkalmas. Az ügyfél erőforrás tulajdonosa, minősül, ezért nem végfelhasználói beavatkozásra szükség.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Ügyfél-hitelesítő adatok megadása|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit engedélyezési folyamat nem alkalmas a hitelesítő adataikat, amelyről ismert, hogy egy adott átirányítási URI Azonosítót fog adatainak bizalmas ügyfelek van optimalizálva. Ezek az ügyfelek általában valósíthatók meg egy böngészőt, például a JavaScriptek programozási nyelv használatával.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit támogatás|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Erőforrás tulajdonosa jelszó hitelesítő adatok folyamata megfelelő azokban az esetekben, ahol az erőforrás tulajdonosa megbízhatósági kapcsolatban áll az ügyfél (az alkalmazás), például az eszköz operációs rendszere vagy egy magas szintű jogosultsággal rendelkező alkalmazás. Ez a folyamat alkalmas képes megszerezni az erőforrás tulajdonos hitelesítő adatait (felhasználónévvel és jelszóval, általában az interaktív űrlap használatával).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Erőforrás tulajdonosa jelszó hitelesítő adatok megadása|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|< p\> az erőforrás tulajdonosa ellátja az ügyfelet a felhasználónevet és jelszót. < /p\> < p\> az ügyfél olyan hozzáférési jogkivonatot kéri le a hitelesítési kiszolgáló "s jogkivonat végpontjához az erőforrás tulajdonosa kapott hitelesítő adatok-ot.  Amikor a kérést, a hitelesítési kiszolgáló hitelesíti az ügyfelet. < /p\> < p\> a hitelesítési kiszolgáló hitelesíti az ügyfelet, és ellenőrzi az erőforrás tulajdonos hitelesítő adatait, és érvényes, ha egy jogkivonatot állít ki. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|< p\> Ha a kérelem nem sikerült az ügyfél-hitelesítéshez, vagy érvénytelen a hitelesítési kiszolgáló válaszol a HTTP 400 (hibás kérés) állapotkóddal (kivéve, ha másként), és a válasz a következő paramétereket tartalmaz. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|< p\> az ügyfél egy kérést küld a token-végpont a következő paraméterek az "application/x-www-form-urlencoded" formátum használata egy forráskarakter-kódolás az UTF-8, a HTTP-kérelmek entitástörzsében hozzáadásával. < /p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|< p\> Ha a hozzáférési token kérelem érvényes és engedélyezett, a hitelesítési kiszolgáló kibocsát egy hozzáférési jogkivonat és opcionális frissítési jogkivonat és hoz létre a válasz a következő paramétereket ad a 20 a HTTP-válasz törzsében 0 (OK) állapotkódot. < /p\>|  
|OAuth2Step_AccessTokenRequest_Name|Hozzáférési jogkivonat kérelem|  
|OAuth2Step_AuthorizationRequest_Name|Hitelesítési kérelem|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|SZÜKSÉGES. A hitelesítési kiszolgáló által kiállított jogkivonat.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|SZÜKSÉGES. A hitelesítési kiszolgáló által kiállított jogkivonat.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES. A hitelesítési kiszolgáló által kiállított jogkivonat.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|SZÜKSÉGES. A hitelesítési kiszolgáló által kiállított jogkivonat.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|SZÜKSÉGES. Ügyfél-azonosító.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES, ha az ügyfél nem hitelesíti a engedélyezési kiszolgálóval.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|SZÜKSÉGES. Az ügyfél-azonosító.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|SZÜKSÉGES. A hitelesítési kiszolgáló által létrehozott engedélyezési kódot.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES. Az engedélyezési kiszolgálótól kapott engedélyezési kódot.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Emberek számára olvasható ASCII szöveges kezeléséről további információt.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Egy URI-emberek számára olvasható weblapon azonosítja a hibával kapcsolatos információkat.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|NEM KÖTELEZŐ. Egy URI-emberek számára olvasható weblapon azonosítja a hibával kapcsolatos információkat.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Egy URI-emberek számára olvasható weblapon azonosítja a hibával kapcsolatos információkat.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|NEM KÖTELEZŐ. Egy URI-emberek számára olvasható weblapon azonosítja a hibával kapcsolatos információkat.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|NEM KÖTELEZŐ. Egy URI-emberek számára olvasható weblapon azonosítja a hibával kapcsolatos információkat.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|SZÜKSÉGES. A következő egyetlen ASCII hiba kódja: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|SZÜKSÉGES. A következő egyetlen ASCII hiba kódja: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|SZÜKSÉGES. A következő egyetlen ASCII hiba kódja: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|SZÜKSÉGES. A következő egyetlen ASCII hiba kódja: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|SZÜKSÉGES. A következő egyetlen ASCII hiba kódja: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|AJÁNLOTT. A másodpercben és a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|AJÁNLOTT. A másodpercben és a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|AJÁNLOTT. A másodpercben és a hozzáférési jogkivonat.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|AJÁNLOTT. A másodpercben és a hozzáférési jogkivonat.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES. "Authorization_code" értéket kell beállítani.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|SZÜKSÉGES. "Client_credentials" értéket kell beállítani.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. Érték a "password" értékre kell állítani.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. Az erőforrás-tulajdonosi jelszó.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|NEM KÖTELEZŐ. Az átirányítás végpont URI abszolút URI Azonosítónak kell lennie.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES, ha a "redirect_uri" paraméter található az engedélyezési kérelmeket, és értékeik azonosnak kell lennie.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|NEM KÖTELEZŐ. Az átirányítás végpont URI abszolút URI Azonosítónak kell lennie.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonatot, amely segítségével új hozzáférési tokenek beszerzése érdekében.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonatot, amely segítségével új hozzáférési tokenek beszerzése érdekében.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NEM KÖTELEZŐ. A frissítési jogkivonatot, amely segítségével új hozzáférési tokenek beszerzése érdekében.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|SZÜKSÉGES. "Kód" értéket kell beállítani.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|SZÜKSÉGES. Értéket kell megadni a "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|NEM KÖTELEZŐ. A hozzáférési kérelem hatókörét.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|Nem kötelező, ha azonos; az ügyfél által kért hatóköre Ellenkező esetben szükséges.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|NEM KÖTELEZŐ. A hozzáférési kérelem hatókörét.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|Nem kötelező, ha azonos; az ügyfél által kért hatóköre Ellenkező esetben szükséges.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|NEM KÖTELEZŐ. A hozzáférési kérelem hatókörét.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|Nem kötelező, ha azonos; az ügyfél által kért hatóköre Ellenkező esetben szükséges.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|NEM KÖTELEZŐ. A hozzáférési kérelem hatókörét.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Nem kötelező, ha azonos; az ügyfél által kért hatóköre Ellenkező esetben szükséges.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|SZÜKSÉGES, ha a "állapot" paraméter szerepelt az ügyfél engedélyezési kérelmeket.  A pontos értéket kapott az ügyféltől.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|AJÁNLOTT. Egy nem átlátszó értéket, az ügyfél között a kérelem és a visszahívás-állapot karbantartásához.  A hitelesítési kiszolgáló például ezt az értéket, ha az ügyfél a felhasználói ügynök átirányítása.  A paraméter webhelyközi kérések hamisításának megakadályozása használható.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|SZÜKSÉGES, ha a "állapot" paraméter szerepelt az ügyfél engedélyezési kérelmeket.  A pontos értéket kapott az ügyféltől.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|SZÜKSÉGES, ha a "állapot" paraméter szerepelt az ügyfél engedélyezési kérelmeket.  A pontos értéket kapott az ügyféltől.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|AJÁNLOTT. Egy nem átlátszó értéket, az ügyfél között a kérelem és a visszahívás-állapot karbantartásához.  A hitelesítési kiszolgáló például ezt az értéket, ha az ügyfél a felhasználói ügynök átirányítása.  A paraméter webhelyközi kérések hamisításának megakadályozása használható.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES, ha a "állapot" paraméter szerepelt az ügyfél engedélyezési kérelmeket.  A pontos értéket kapott az ügyféltől.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|SZÜKSÉGES. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|SZÜKSÉGES. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|SZÜKSÉGES. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|SZÜKSÉGES. A kibocsátott jogkivonat típusa.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|SZÜKSÉGES. Az erőforrás tulajdonosa felhasználónév.|  
|OAuth2UnsupportedTokenType|A jogkivonat típusa "{0}" nincs supporetd.|  
|OAuth2InvalidState|Érvénytelen válasz engedélyezési kiszolgálótól|  
|OAuth2GrantType_AuthorizationCode|Engedélyezési kód|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Ügyfél hitelesítő adatait|  
|OAuth2GrantType_ResourceOwnerPassword|Erőforrás-tulajdonosi jelszó|  
|WebDocumentation302Code|302 található|  
|WebDocumentation400Code|400 (hibás kérés)|  
|OAuth2SendingMethod_AuthHeader|Engedélyezési fejléc|  
|OAuth2SendingMethod_QueryParam|Lekérdezési paraméter|  
|OAuth2AuthorizationServerGeneralException|Hiba történt az {0} hozzáférésének engedélyezése|  
|OAuth2AuthorizationServerCommunicationException|Engedélyezési kiszolgáló HTTP-kapcsolat nem hozható létre, vagy váratlanul megszűnt.|  
|WebDocumentationOAuth2GeneralErrorMessage|Váratlan hiba történt.|  
|AuthorizationServerCommunicationException|Engedélyezési kiszolgálói kommunikáció kivétel történt. Lépjen kapcsolatba a rendszergazdával.|  
|TextblockSubscriptionKeyHeaderDescription|Előfizetés-kulcsot, amely hozzáférést biztosít az API. Található a < a href = "/ fejlesztői"\>profil < /a\>.|  
|TextblockOAuthHeaderDescription|OAuth 2.0 hozzáférési jogkivonatot kapott < i\>{0} < /i\>. Támogatott grant típusok: < i\>{1} < /i\>.|  
|TextblockContentTypeHeaderDescription|A szervezet az API-nak küldött adathordozó-típusát.|  
|ErrorMessageApiNotAccessible|Az API-hívás kívánt jelenleg nem érhető el. Lépjen kapcsolatba az API-közzétevő < a href = "/ problémák"\>itt < /a\>.|  
|ErrorMessageApiTimedout|A hívni kívánt API válasz visszaszerezheti szokásosnál tovább tart. Lépjen kapcsolatba az API-közzétevő < a href = "/ problémák"\>itt < /a\>.|  
|BadRequestParameterExpected|"a"{0}"paramétert várt."|  
|TooltipTextDoubleClickToSelectAll|Kattintson duplán az összes kijelöléséhez.|  
|TooltipTextHideRevealSecret|Megjelenítése/elrejtése|  
|ButtonLinkOpenConsole|Kipróbálom|  
|SectionHeadingRequestBody|Kérés törzsében|  
|SectionHeadingRequestParameters|A kérelemben szereplő paraméterek|  
|SectionHeadingRequestUrl|Kérelem URL-címe|  
|SectionHeadingResponse|Válasz|  
|SectionHeadingRequestHeaders|Kérelem fejlécei|  
|FormLabelSubtextOptional|Nem kötelező|  
|SectionHeadingCodeSamples|Kódminták|  
|TextblockOpenidConnectHeaderDescription|OpenID Connect azonosító jogkivonatot kapott < i\>{0} < /i\>. Támogatott grant típusok: < i\>{1} < /i\>.|  
  
###  <a name="ErrorPageStrings"></a>ErrorPageStrings  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelBack|biztonsági|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|e-mail küldése|  
|PageTitleError|Sajnos probléma merült fel a kért lap szolgáltató|  
|TextblockPotentialCauseIntermittentIssue|Ennek az lehet, amely már nem látható időszakos adatok hozzáférési problémát.|  
|TextblockPotentialCauseOldLink|A hivatkozásra kattintott, a régi és történhet mutat a megfelelő helyre vonatkozóan.|  
|TextblockPotentialCauseTechnicalProblem|Az end műszaki probléma lehet.|  
|TextblockPotentialSolutionRefresh|Frissítse a lapot.|  
|TextblockPotentialSolutionStartOver|Kezdje újra a folyamatot a következőhöz: {0}.|  
|TextblockPotentialSolutionTryAgain|Nyissa meg {0}, és próbálja meg újra végrehajtani a műveletet.|  
|TextReportProblem|a Microsoft és a hibával leíró {0} áttekintjük, hogy azt, amint azt is.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Valószínűleg csak egy ideiglenes probléma, néhány dolog, amit próbálja|  
  
###  <a name="IssuesStrings"></a>IssuesStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebIssuesIndexTitle|Hibák|  
|WebIssuesNoActiveSubscriptions|Nincs aktív előfizetéssel rendelkezik. Elő kell fizetnie probléma termék.|  
|WebIssuesNotSignin|Nincs bejelentkezve. Kérjük, {0} probléma bejelentése, vagy írjon egy megjegyzést.|  
|WebIssuesReportIssueButton|A jelentés probléma|  
|WebIssuesSignIn|bejelentkezés|  
|WebIssuesStatusReportedBy|Állapot: {0} &#124; {1} által jelentett|  
  
###  <a name="NotFoundStrings"></a>NotFoundStrings  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelHomePage|Kezdőlap|  
|LinkLabelSendUsEmail|e-mail küldése|  
|PageTitleNotFound|Sajnos nem található a keresett lap|  
|TextblockPotentialCauseMisspelledUrl|Valószínűleg elírta az URL-CÍMÉT írta be a Ha.|  
|TextblockPotentialCauseOldLink|A hivatkozásra kattintott, a régi és történhet mutat a megfelelő helyre vonatkozóan.|  
|TextblockPotentialSolutionRetype|Próbálja meg, hogy beírja az URL-címet.|  
|TextblockPotentialSolutionStartOver|Kezdje újra a folyamatot a következőhöz: {0}.|  
|TextReportProblem|a Microsoft és a hibával leíró {0} áttekintjük, hogy azt, amint azt is.|  
|TitlePotentialCause|Lehetséges ok|  
|TitlePotentialSolution|Lehetséges megoldás|  
  
###  <a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebProductsAgreement|{0}. a termék feliratkozással elfogadom a `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`.|  
|WebProductsLegalTermsLink|Használati feltételek|  
|WebProductsSubscribeButton|Feliratkozás|  
|WebProductsUsageLimitsHeader|Használati korlátok|  
|WebProductsYouAreNotSubscribed|A termék előfizetett.|  
|WebProductsYouRequestedSubscription|A kért előfizetés a terméket.|  
|ErrorYouNeedtoAgreeWithLegalTerms|A folytatás előtt el kell fogadnia a használati feltételeket.|  
|ButtonLabelAddSubscription|Előfizetés hozzáadása|  
|LinkLabelChangeSubscriptionName|módosítás|  
|ButtonLabelConfirm|Erősítse meg|  
|TextblockMultipleSubscriptionsCount|A termék {0} előfizetések közül választhat:|  
|TextblockSingleSubscriptionsCount|A termék {0}-előfizetéssel rendelkezik:|  
|TextblockSingleApisCount|Ez a termék {0} API tartalmaz:|  
|TextblockMultipleApisCount|Ez a termék {0} API-kat tartalmaz:|  
|TextblockHeaderSubscribe|Fizessen elő a termék|  
|TextblockSubscriptionDescription|Egy új előfizetés jön létre az alábbiak szerint:|  
|TextblockSubscriptionLimitReached|Előfizetési határértékét.|  
  
###  <a name="ProductsStrings"></a>ProductsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleProducts|Termékek|  
  
###  <a name="ProviderInfoStrings"></a>ProviderInfoStrings  
  
|Név|Szöveg|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|A bejelentkezés le van tiltva a rendszergazdák által pillanatnyilag.|  
|TextboxExternalIdentitiesSigninInvitation|Alternatív megoldásként jelentkezzen be|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Jelentkezzen be:|  
  
###  <a name="SigninResources"></a>SigninResources  
  
|Név|Szöveg|  
|----------|----------|  
|PrincipalNotFound|Rendszerbiztonsági tag nem található vagy érvénytelen aláírása|  
|ErrorSsoAuthenticationFailed|Egyszeri bejelentkezési hitelesítés sikertelen volt|  
|ErrorSsoAuthenticationFailedDetailed|Érvénytelen a megadott jogkivonat vagy aláírás nem ellenőrizhető.|  
|ErrorSsoTokenInvalid|Egyszeri bejelentkezési token érvénytelen|  
|ValidationErrorSpecificEmailAlreadyExists|E-mailek "{0}" már regisztrálva van|  
|ValidationErrorSpecificEmailInvalid|E-mailek "{0}" érvénytelen.|  
|ValidationErrorPasswordInvalid|Jelszó érvénytelen. Javítsa ki a hibákat, és próbálkozzon újra.|  
|PropertyTooShort|{0} túl rövid|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Érvénytelen e-mail címet.|  
|ValidationMessageNewPasswordConfirmationRequired|Új jelszó megerősítése|  
|ValidationErrorPasswordConfirmationRequired|Ellenőrizze, hogy a jelszava üres|  
|WebAuthenticationEmailChangeNotice|Változás visszaigazoló e-mailben van folyamatban: {0}. Kérjük, kövesse az új e-mail cím megerősítése szereplő utasításokat. Ha az e-mailt nem érkezik a Beérkezett fájlok mappa a következő néhány perc múlva, ellenőrizze a Levélszemét mappát.|  
|WebAuthenticationEmailChangeNoticeHeader|Az e-mailek változáskérés feldolgozása sikeresen megtörtént|  
|WebAuthenticationEmailChangeNoticeTitle|E-mailt kért módosítás|  
|WebAuthenticationEmailHasBeenRevertedNotice|Már létezik, e-mailt. Kérelem vissza lett állítva.|  
|ValidationErrorEmailAlreadyExists|E-mailek már létezik.|  
|ValidationErrorEmailInvalid|Érvénytelen e-mail cím|  
|TextboxLabelEmail|E-mail cím|  
|ValidationErrorEmailRequired|E-mailek szükség.|  
|WebAuthenticationErrorNoticeHeader|Hiba|  
|WebAuthenticationFieldLengthErrorMessage|{0} {1} legfeljebb ilyen hosszúnak kell lennie.|  
|TextboxLabelEmailFirstName|Utónév|  
|ValidationErrorFirstNameRequired|Keresztnév megadása szükséges.|  
|ValidationErrorFirstNameInvalid|Érvénytelen Utónév|  
|NoticeInvalidInvitationToken|Vegye figyelembe, hogy megerősítési hivatkozások érvényesek csak 48 órán keresztül. Ha továbbra is a időkereten belül, győződjön meg arról, hogy a hivatkozás megfelelő. Ha lejárt a hivatkozásra, majd ismételje meg a művelet megerősítéséhez kívánt.|  
|NoticeHeaderInvalidInvitationToken|Érvénytelen a meghívást jogkivonat|  
|NoticeTitleInvalidInvitationToken|Jóváhagyás hiba|  
|WebAuthenticationLastNameInvalidErrorMessage|Utolsó neve érvénytelen|  
|TextboxLabelEmailLastName|Vezetéknév|  
|ValidationErrorLastNameRequired|Vezetéknév megadása szükséges.|  
|WebAuthenticationLinkExpiredNotice|Lejárt a megerősítési hivatkozást kap. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A jelszó-visszaállítási hivatkozásra értéke érvénytelen vagy lejárt.|  
|WebAuthenticationLinkExpiredNoticeTitle|Küldött hivatkozások|  
|WebAuthenticationNewPasswordLabel|Új jelszó|  
|ValidationMessageNewPasswordRequired|Meg kell adni az új jelszót.|  
|TextboxLabelNotificationsSenderEmail|Feladó e-mail értesítések|  
|TextboxLabelOrganizationName|Szervezet neve|  
|WebAuthenticationOrganizationRequiredErrorMessage|Szervezet neve nincs megadva|  
|WebAuthenticationPasswordChangedNotice|A jelszó frissítése sikeresen befejeződött.|  
|WebAuthenticationPasswordChangedNoticeTitle|Jelszó frissítése|  
|WebAuthenticationPasswordCompareErrorMessage|Jelszavak nem egyeznek|  
|WebAuthenticationPasswordConfirmLabel|Jelszó megerősítése|  
|ValidationErrorPasswordInvalidDetailed|A jelszava túl egyszerű.|  
|WebAuthenticationPasswordLabel|Jelszó|  
|ValidationErrorPasswordRequired|Meg kell adni a jelszót.|  
|WebAuthenticationPasswordResetSendNotice|Módosítsa a jelszó megerősítését kérő e-mailek van folyamatban: {0}. Kövesse a belül az e-mailt a jelszómódosítási folyamat folytatásához.|  
|WebAuthenticationPasswordResetSendNoticeHeader|A jelszó alaphelyzetbe állítása kérés feldolgozása sikeresen megtörtént|  
|WebAuthenticationPasswordResetSendNoticeTitle|Jelszó alaphelyzetbe állítása irányuló kérés.|  
|WebAuthenticationRequestNotFoundNotice|Nem található|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Feladó e-mail értesítések értéke üres|  
|WebAuthenticationSigninPasswordLabel|Erősítse meg a módosítást a jelszó megadásával|  
|WebAuthenticationSignupConfirmNotice|Regisztrációs visszaigazoló e-mailben hamarosan megérkezik: {0}. < br /\> adjon kövesse az utasításokat az e-mailt belül aktiválja a fiókjához. < br /\> Ha az e-mailt nem érkezik a Beérkezett üzenetek a következő néhány percen belül, ellenőrizze a levélszemét e-mailek mappát.|  
|WebAuthenticationSignupConfirmNoticeHeader|A fiók sikeresen létrejött.|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Regisztrációs visszaigazoló e-mailben küldött újra|  
|WebAuthenticationSignupConfirmNoticeTitle|Fiók létrehozása|  
|WebAuthenticationTokenRequiredErrorMessage|A token üres|  
|WebAuthenticationUserAlreadyRegisteredNotice|Úgy tűnik, hogy ezzel az e-mail a felhasználó már regisztrálva van a rendszerben. Ha elfelejtette a jelszavát, próbálja állítsa vissza, vagy forduljon a terméktámogatási csapathoz.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|A felhasználó már regisztrálva van|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Már regisztrálva van|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelChangeAccountInfo|Fiók adatainak módosítása|  
|ButtonLabelCloseAccount|Zárja be a fiók|  
|WebAuthenticationInvalidCaptchaErrorMessage|A megadott szöveg nem felel meg a képen látható szöveg. Kérjük, próbálkozzon újból.|  
|ValidationErrorCredentialsInvalid|E-mailek és a jelszó érvénytelen. Javítsa ki a hibákat, és próbálkozzon újra.|  
|WebAuthenticationRequestIsNotValid|Kérelem érvénytelen.|  
|WebAuthenticationUserIsNotConfirm|Ellenőrizze, hogy a regisztráció előtt bejelentkezni.|  
|WebAuthenticationInvalidEmailFormated|Érvénytelen e-mail: {0}|  
|WebAuthenticationUserNotFound|A felhasználó nem található|  
|WebAuthenticationTenantNotRegistered|Egy Azure Active Directory-bérlőt, amelyhez a portál elérésére nem jogosult a fiókja tagja.|  
|WebAuthenticationAuthenticationFailed|Nem sikerült a hitelesítés.|  
|WebAuthenticationGooglePlusNotEnabled|Nem sikerült a hitelesítés. Ha Ön jogosult az alkalmazás ezután adjon kérje a rendszergazdát, hogy győződjön meg arról, hogy a Google hitelesítési megfelelően van konfigurálva.|  
|ValidationErrorAllowedTenantIsRequired|Engedélyezett bérlői szükség|  
|ValidationErrorTenantIsNotValid|Az Azure Active Directory-bérlőt "{0}" érvénytelen.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Jelentkezzen be a {0} fiókjával|  
|WebAuthenticationUserLimitNotice|Ez a szolgáltatás elérte a megengedett felhasználók maximális száma. Adjon `<a href="mailto:{0}"\>contact the administrator</a\>` frissítse a szolgáltatást, és engedélyezze újra a felhasználói regisztráció.|  
|WebAuthenticationUserLimitNoticeHeader|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserLimitNoticeTitle|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserRegistrationDisabledNotice|Felhasználók regisztrálása a rendszergazda letiltotta. Kérjük, jelentkezzen be a külső identitásszolgáltatótól.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Felhasználói regisztráció letiltva|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Felhasználói regisztráció letiltva|  
|WebAuthenticationSignupPendingConfirmationNotice|Azt is fiókja létrehozásának befejezéséhez igazolnia kell e-mail címének ellenőrzése. Küldtünk egy e-mailt {0}. Kövesse a belül az e-mailt a fiók aktiválásához. Ha az e-mail cím a következő néhány percen belül nem érkeznek, ellenőrizze a Levélszemét mappát.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Nem megerősített fiók e-mail címét a(z) {0} észleltünk. A fiókja létrehozásának befejezéséhez igazolnia kell az e-mail cím. Küldtünk egy e-mailt {0}. Kövesse a belül az e-mailt a fiók aktiválásához. Ha az e-mail cím a következő néhány percen belül nem érkeznek, ellenőrizze a Levélszemét mappa|  
|WebAuthenticationSignupConfirmationAlmostDone|Majdnem kész|  
|WebAuthenticationSignupConfirmationEmailSent|Küldtünk egy e-mailt {0}. Kövesse a belül az e-mailt a fiók aktiválásához. Ha az e-mail cím a következő néhány percen belül nem érkeznek, ellenőrizze a Levélszemét mappát.|  
|WebAuthenticationEmailSentNotificationMessage|Az e-mail sikeresen elküldve {0}.|  
|WebAuthenticationNoAadTenantConfigured|Nincs konfigurálva a szolgáltatáshoz Azure Active Directory-bérlő.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Elfogadom a `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Tekintse át`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Használati feltételek|  
|ValidationMessageConsentNotAccepted|A folytatás előtt el kell fogadnia a használati feltételeket.|  
  
###  <a name="SigninStrings"></a>SigninStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebAuthenticationForgotPassword|Elfelejtette a jelszavát?|  
|WebAuthenticationIfAdministrator|Ha rendszergazdaként jelentkezett be kell jelentkeznie a `<a href="{0}"\>here</a\>`.|  
|WebAuthenticationNotAMember|Nem tagja még? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Emlékezzen rám ezen a számítógépen|  
|WebAuthenticationSigininWithPassword|Jelentkezzen be a felhasználónevét és jelszavát|  
|WebAuthenticationSigninTitle|Bejelentkezés|  
|WebAuthenticationSignUpNow|Regisztráljon most|  
  
###  <a name="SignupStrings"></a>SignupStrings  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleSignup|Regisztráció|  
|WebAuthenticationAlreadyAMember|Egy tag már?|  
|WebAuthenticationCreateNewAccount|Új API Management-fiók létrehozása|  
|WebAuthenticationSigninNow|Jelentkezzen be|  
|ButtonLabelSignup|Regisztráció|  
  
###  <a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|SubscriptionCancelConfirmation|Biztos, hogy szeretné-e az előfizetés megszüntetése?|  
|SubscriptionRenewConfirmation|Biztos, hogy szeretné-e az előfizetés meghosszabbításához?|  
|WebDevelopersManageSubscriptions|Előfizetések kezelése|  
|WebDevelopersPrimaryKey|Elsődleges kulcs|  
|WebDevelopersRegenerateLink|Újragenerálás|  
|WebDevelopersSecondaryKey|Másodlagos kulcsát.|  
|ButtonLabelShowKey|Megjelenítés|  
|ButtonLabelRenewSubscription|Frissítés|  
|WebDevelopersSubscriptionReqested|A kért {0}|  
|WebDevelopersSubscriptionRequestedState|A kért|  
|WebDevelopersSubscriptionTableNameHeader|Név|  
|WebDevelopersSubscriptionTableStateHeader|Állapot|  
|WebDevelopersUsageStatisticsLink|Elemzési jelentések|  
|WebDevelopersYourSubscriptions|Az előfizetések|  
|SubscriptionPropertyLabelRequestedDate|A kért|  
|SubscriptionPropertyLabelStartedDate|A következőn:|  
|PageTitleRenameSubscription|Nevezze át az előfizetést|  
|SubscriptionPropertyLabelName|Előfizetés neve|  
  
###  <a name="SubscriptionStrings"></a>SubscriptionStrings  
  
|Név|Szöveg|  
|----------|----------|  
|SectionHeadingCloseAccount|Szeretnék lezárta a fiókját?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Elrejtése|  
|ButtonLabelRegenerateKey|Újragenerálás|  
|InformationMessageKeyWasRegenerated|Biztos, hogy szeretné-e ez a kulcs újragenerálása?|  
|ButtonLabelShowKey|Megjelenítés|  
  
###  <a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profil frissítése|  
|PageTitleUpdateProfile|Fiókadatok frissítése|  
  
###  <a name="UserProfile"></a>Felhasználói profil  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Fiók adatainak módosítása|  
|ButtonLabelChangePassword|Jelszó módosítása|  
|ButtonLabelCloseAccount|Zárja be a fiók|  
|TextboxLabelEmail|E-mail cím|  
|TextboxLabelEmailFirstName|Utónév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|TextboxLabelNotificationsSenderEmail|Feladó e-mail értesítések|  
|TextboxLabelOrganizationName|Szervezet neve|  
|SubscriptionStateActive|Aktív|  
|SubscriptionStateCancelled|Megszakítva|  
|SubscriptionStateExpired|Lejárt|  
|SubscriptionStateRejected|Elutasított|  
|SubscriptionStateRequested|A kért|  
|SubscriptionStateSuspended|Felfüggesztve|  
|DefaultSubscriptionNameTemplate|{0} (alapértelmezett)|  
|SubscriptionNameTemplate|Fejlesztői hozzáférést #{0}|  
|TextboxLabelSubscriptionName|Előfizetés neve|  
|ValidationMessageSubscriptionNameRequired|Előfizetés neve nem lehet üres.|  
|ApiManagementUserLimitReached|Ez a szolgáltatás elérte a megengedett felhasználók maximális száma. Frissítse a magasabb szintű tarifacsomagban használható.|  
  
##  <a name="glyphs"></a>A betűkép-erőforrások  
 Az API Management fejlesztői portál sablonok használhatja a glyphs elem a [a rendszerindítási Glyphicons](http://getbootstrap.com/components/#glyphicons). A glyphs elem tartalmaz több mint 250 glyphs elem a betűtípus-formátumban a [Glyphicon](http://glyphicons.com/) Halflings beállítása. A készletből egy betűkép használatához a következő szintaxissal.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 A glyphs elem teljes listájáért lásd: [a rendszerindítási Glyphicons](http://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Következő lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).

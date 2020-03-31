---
title: Azure API Management sablon erőforrások | Microsoft dokumentumok
description: Ismerje meg, hogy milyen típusú erőforrások érhetők el az Azure API Management fejlesztői portálsablonjaiban való használatra.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249502"
---
# <a name="azure-api-management-template-resources"></a>Az Azure API Management sablon erőforrásai
Az Azure API Management a következő típusú erőforrásokat biztosítja a fejlesztői portál sablonjaiban való használatra.  
  
-   [Karakterlánc-erőforrások](#strings)  
  
-   [A jeljel erőforrásai](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a>Karakterlánc-erőforrások  
 Az API Management a fejlesztői portálon használható karakterlánc-erőforrások átfogó készletét biztosítja. Ezek az erőforrások az API Management által támogatott összes nyelvre vannak honosítva. Az alapértelmezett sablonkészlet ezeket az erőforrásokat használja a fejlesztői portálon megjelenő oldalfejlécekhez, címkékhez és állandó karakterláncokhoz. Ha karakterlánc-erőforrást szeretne használni a sablonokban, adja meg az erőforráskarakterlánc-előtagot, majd a karakterlánc nevét, ahogy az a következő példában látható.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 A következő példa a Terméklista sablonból származik, és a lap tetején jeleníti meg a **Termékek** elemet.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
A következő honosítási beállítások támogatottak:

| Területi beállítás    | Nyelv               |
|-----------|------------------------|
| „hu”      | "Angol"              |
| "cs"      | "Čeština"              |
| "de"      | "Deutsch"              |
| "es"      | "Español"              |
| "fr"      | "Français"             |
| "hu"      | "Magyar"               |
| "ez"      | "Olasz"             |
| "ja-JP"   | "啦ンン"                |
| "ko"      | "啦ンン"                |
| "nl"      | "Nederlands"           |
| "pl"      | "Polski"               |
| "pt-br"   | "Português (Brasil)"   |
| "pt-pt"   | "Português (Portugália)" |
| "ru"      | "Русский"              |
| "sv"      | "Svenska"              |
| "tr"      | "Türkçe"               |
| "Zh-hans" | "A"啦ンン-ek"           |
| "Zh-hant" | "啦ンンン)"           |

 Tekintse meg az alábbi táblázatokat a fejlesztői portál sablonjaiban használható karakterlánc-erőforrásokat. A tábla nevét használja a tábla karakterlánc-erőforrásainak előtagjaként.  
  
-   [ApisStrings (ApisStrings)](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [Közös erőforrások](#CommonResources)  
  
-   [CommonStrings (Közös karakterláncok)](#CommonStrings)  
  
-   [Dokumentáció](#Documentation)  
  
-   [ErrorPageStrings (Hibalapkarakterláncok)](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings (Nem Található karakterláncok)](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings (TermékekHúrok)](#ProductsStrings)  
  
-   [ProviderInfoStrings (Szolgáltatóinfokarakterláncok)](#ProviderInfoStrings)  
  
-   [Bejelentkezési erőforrások](#SigninResources)  
  
-   [Bejelentkezési karakterláncok](#SigninStrings)  
  
-   [SignupStrings (Regisztrációs karakterláncok)](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [Előfizetési karakterláncok](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a>ApisStrings (ApisStrings)  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleApis|API-k|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a>AppDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsDetailsTitle cím|Alkalmazás előnézete|  
|WebApplicationsRequirementsfejléc|Követelmények|  
|WebApplicationsKépernyőTalt|Képernyőfelvétel|  
|WebApplicationsScreenshotsFejléc|Képernyőképek|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a>ApplicationListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Biztosan eltávolítja az alkalmazást?|  
|WebDevelopersAppNot Published|Nem tették közzé|  
|WebDevelopersAppNotSubmitted|Nincs elküldve|  
|WebDevelopersAppTableCategoryfejléc|Kategória|  
|WebDevelopersAppTableNamefejléc|Név|  
|WebDevelopersAppTableStatefejléc|Állapot|  
|WebdevelopersEditLink|Szerkesztés|  
|WebDevelopersRegisterAppLink|Alkalmazás regisztrálása|  
|WebDevelopersRemoveLink|Eltávolítás|  
|WebDevelopersSubmitLink|Küldés|  
|WebdevelopersYourApplicationsfejléc|Az alkalmazások|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a>AppStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebApplicationsfejléc|Alkalmazások|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a>Közös erőforrások  
  
|Név|Szöveg|  
|----------|----------|  
|NoItemsToDisplay|Nincs találat.|  
|GeneralExceptionMessage|Valami nincs rendben. Lehet, hogy egy átmeneti hiba, vagy egy hiba. Kérem, próbálja újra.|  
|GeneralJsonExceptionMessage|Valami nincs rendben. Lehet, hogy egy átmeneti hiba, vagy egy hiba. Töltse be újra az oldalt, majd próbálkozzon újra.|  
|ConfirmationMessageUnsavedChanges (ConfirmationMessageUnsavedChanges)|Van néhány nem mentett módosítás. Biztosan megszakítja és elveti a módosításokat?|  
|AzureActiveDirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Http Request Body túl nagy.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a>CommonStrings (Közös karakterláncok)  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelMégs|Mégse|  
|ButtonLabelSave|Mentés|  
|GeneralExceptionMessage|Valami nincs rendben. Lehet, hogy egy átmeneti hiba, vagy egy hiba. Kérem, próbálja újra.|  
|NoItemsToDisplay|Nincsenek megjelenendő elemek.|  
|PagerButtonLabelFirst|Első|  
|PagerButtonLabelLast|Utolsó|  
|PagerButtonLabelNext|Következő lépés|  
|PagerButtonLabel Előző|Előző|  
|PageNOfM page|{0}./{1}. oldal|  
|PasswordTooShort|A jelszó túl rövid|  
|EmailAsPassword|Ne használja az e-mailt jelszóként|  
|Passwordsameasusername|A jelszó nem tartalmazza a felhasználónevét|  
|PasswordTwoCharacterClasses|Különböző karakterosztályok használata|  
|PasswordTooManyIsmétlések|Túl sok ismétlés|  
|PasswordSequenceFound|A jelszó szekvenciákat tartalmaz|  
|PagerLabelPageSize (PagerLabelPageSize)|Oldalméret|  
|CurtainLabelLoading|Betöltés...|  
|TablePlaceholderNothingToDisplay|Nincsenek adatok a kiválasztott időszakra és hatókörre vonatkozóan|  
|ButtonLabelClose|Bezárás|  
  
###  <a name="documentation"></a><a name="Documentation"></a>Dokumentáció  
  
|Név|Szöveg|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Érvénytelen fejléc{0}'|  
|WebDocumentationInvalidRequestErrorMessage|Érvénytelen kérelem URL-címe|  
|TextboxLabelAccessToken|Hozzáférési jogkivonat *|  
|DropdownOptionPrimaryKeyFormat|Elsődleges-{0}|  
|DropdownOptionSecondaryKeyFormat|Másodlagos-{0}|  
|WebDocumentationSubscriptionKeyText|Az előfizetési kulcs|  
|WebDocumentationTemplatesAddHeaders|Kötelező HTTP-fejlécek hozzáadása|  
|WebDocumentationTemplatesBasicAuthSample|Alapszintű engedélyezési minta|  
|WebDocumentationTemplatesCurlForBasicAuth|alaplicenc-használat: --user {username}:{password}|  
|WebDocumentationTemplatesCurlValuesForPath|Adja meg az elérési út paramétereinek értékeit (a következő ként jelenik meg: {...}), az előfizetési kulcs és a lekérdezési paraméterek értékei|  
|WebDocumentationTemplatesDeveloperKey|Az előfizetési kulcs megadása|  
|WebDocumentationTemplatesJavaApache|Ez a minta az Apache HTTP-klienst használja a HTTP-összetevőkből (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|Szükség szerint adja meg a választható paraméterek értékeit|  
|WebDocumentationTemplatesPhpPackage csomag|Ez a minta a HTTP_Request2 csomagot használja. (további információkért:https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|Adja meg az elérési út paramétereinek értékeit (a következő ként jelenik meg: {...}), és szükség esetén kérje a törzset.|  
|WebDocumentationTemplatesRequestBody|Kérelem törzsének megadása|  
|WebDocumentationTemplatesRequiredParams|Adja meg a következő szükséges paraméterek értékeit|  
|WebDocumentationTemplatesValuesForPath|Elérési út paramétereinek megadása ({...})Specify values for path parameters (shown as {...})|  
|OAuth2AuthorizationEndpointDescription|Az engedélyezési végpont az erőforrás tulajdonosával való interakcióra és az engedélyezési támogatás megszerzésére szolgál.|  
|OAuth2AuthorizationEndpointName|Engedélyezési végpont|  
|OAuth2TokenEndpointDescription|A jogkivonat-végpontot az ügyfél egy hozzáférési jogkivonat megszerzéséhez használja az engedélyezési támogatás vagy a frissítési jogkivonat bemutatásával.|  
|OAuth2TokenEndpointName|Token végpontja|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<\> p Az ügyfél úgy indítja el a folyamatot, hogy az erőforrás tulajdonosának felhasználói ügynökét az engedélyezési végpontra irányítja.  Az ügyfél tartalmazza az ügyfélazonosítót, a kért hatókört, a helyi állapotot és egy átirányítási URI-t, amelyre az engedélyezési kiszolgáló visszaküldi a felhasználói ügynököt, amint a hozzáférés meglett (vagy megtagadták).     </p\> <\> p Az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosát (a felhasználói ügynökön keresztül), és megállapítja, hogy az erőforrás tulajdonosa megadja vagy elutasítja az ügyfél hozzáférési kérelmét.     </p\> <\> p Feltételezve, hogy az erőforrás-tulajdonos hozzáférést ad, az engedélyezési kiszolgáló visszairányítja a felhasználói ügynököt az ügyfélhez a korábban megadott átirányítási URI használatával (a kérelemben vagy az ügyfél regisztrációja során).  Az átirányítási URI tartalmaz egy engedélyezési kódot és az ügyfél által korábban biztosított bármely helyi állapotot.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Ha a felhasználó megtagadja a hozzáférési kérelmet, ha a kérelem érvénytelen, az ügyfél tájékoztatást kap az átirányításhoz hozzáadott következő paraméterek használatával: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Engedélyezési kérelem|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<\> p Az ügyfélalkalmazásnak el kell küldenie a felhasználót az engedélyezési végpontra az OAuth folyamat elindításához.          Az engedélyezési végponton a felhasználó hitelesíti magát, majd engedélyezi vagy megtagadja az alkalmazáshoz való hozzáférést.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<\> p A hozzáférést az erőforrás-tulajdonos adja, az engedélyezési kiszolgáló visszairányítja a felhasználói ügynököt az ügyfélhez a korábban megadott átirányítási URI használatával (a kérelemben vagy az ügyfél regisztrációja során).  Az átirányítási URI tartalmaz egy engedélyezési kódot és az ügyfél által korábban biztosított bármely helyi állapotot. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<\> p Az ügyfél hozzáférési jogkivonatot kér az engedélyezési kiszolgáló tokenvégpontjától az előző lépésben kapott engedélyezési kód dal.  A kérelem benyújtásakor az ügyfél hitelesíti magát az engedélyezési kiszolgálóval.  Az ügyfél tartalmazza az engedélyezési kód ellenőrzéshez használt átirányítási URI-t. </p\> <\> p Az engedélyezési kiszolgáló hitelesíti az ügyfelet, ellenőrzi az engedélyezési kódot, és biztosítja, hogy a kapott átirányítás megegyezik az ügyfél (C) lépésben történő átirányításához használt URI-val.  Ha érvényes, az engedélyezési kiszolgáló egy hozzáférési jogkivonattal és szükség esetén egy frissítési jogkivonattal válaszol vissza. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<\> p Ha a kérelemügyfél hitelesítése sikertelen vagy érvénytelen, az engedélyezési kiszolgáló HTTP 400 (Rossz kérés) állapotkóddal válaszol (hanincs másképp) és a következő paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<\> p Az ügyfél a következő paraméterek et küldi el a token végpontjára az "application/x-www-form-urlencoded" formátumban, a HTTP-kérelem entitástörzsében az UTF-8 karakterkódolásával. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<\> p Az engedélyezési kiszolgáló egy hozzáférési jogkivonatot és egy választható frissítési jogkivonatot ad ki, és a választ úgy építi ki, hogy a következő paramétereket adja hozzá a HTTP-válasz entitástörzséhez egy 200 (OK) állapotkóddal. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<\> p Az ügyfél hitelesíti magát az engedélyezési kiszolgálóval, és hozzáférési jogkivonatot kér a token végpontjától. </p\> <\> p Az engedélyezési kiszolgáló hitelesíti az ügyfelet, és ha érvényes, kiad egy hozzáférési jogkivonatot. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Ha a kérelem sikertelen volt az ügyfél hitelesítése vagy érvénytelen, az engedélyezési kiszolgáló HTTP 400 (Rossz kérés) állapotkóddal válaszol (hacsak másként nincs megadva), és a következő paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p Az ügyfél a következő paraméterek hozzáadásával kéri a token végpontját az "application/x-www-form-urlencoded" formátumban, a HTTP-kérelem entitástörzsében az UTF-8 karakterkódolásával. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Ha a hozzáférési jogkivonat-kérelem érvényes és engedélyezett, az engedélyezési kiszolgáló hozzáférési jogkivonatot és választható frissítési jogkivonatot ad ki, és a választ úgy építi ki, hogy a következő paramétereket adja hozzá a HTTP-válasz entitástörzséhez egy 200 (OK) állapotkóddal. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<\> p Az ügyfél úgy indítja el a folyamatot, hogy az erőforrás tulajdonosának felhasználói ügynökét az engedélyezési végpontra irányítja.  Az ügyfél tartalmazza az ügyfélazonosítót, a kért hatókört, a helyi állapotot és egy átirányítási URI-t, amelyre az engedélyezési kiszolgáló visszaküldi a felhasználói ügynököt, amint a hozzáférés meglett (vagy megtagadták). </p\> <\> p Az engedélyezési kiszolgáló hitelesíti az erőforrás tulajdonosát (a felhasználói ügynökön keresztül), és megállapítja, hogy az erőforrás tulajdonosa megadja vagy elutasítja az ügyfél hozzáférési kérelmét. </p\> <\> p A felhasználó tól az erőforrás-tulajdonos hozzáférést biztosít, az engedélyezési kiszolgáló a korábban megadott átirányítási URI használatával visszairányítja a felhasználói ügynököt az ügyfélhez.  Az átirányítási URI tartalmazza a hozzáférési jogkivonatot az URI-töredékben. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<\> p Ha az erőforrás tulajdonosa elutasítja a hozzáférési kérelmet, vagy ha a kérelem nem hiányzó vagy érvénytelen átirányítási URI miatt sikertelen, az engedélyezési kiszolgáló úgy tájékoztatja az ügyfelet, hogy az átirányítási URI töredékösszetevőjéhez az "application/x-www-form-urlencoded" formátumban adja hozzá az ügyfelet. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<\> p Az ügyfélalkalmazásnak el kell küldenie a felhasználót az engedélyezési végpontra az OAuth folyamat elindításához.      Az engedélyezési végponton a felhasználó hitelesíti magát, majd engedélyezi vagy megtagadja az alkalmazáshoz való hozzáférést. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<\> p Ha az erőforrás-tulajdonos megadja a hozzáférési kérelmet, az engedélyezési kiszolgáló kiad egy hozzáférési jogkivonatot, és átadja azt az ügyfélnek az átirányítási URI töredékösszetevőjéhez az "application/x-www-form-urlencoded" formátumban. </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Az engedélyezési kódáramlás olyan ügyfelek számára van optimalizálva, akik képesek megtartani hitelesítő adataik titkosságát (pl. PHP, Java, Python, Ruby, ASP.NET stb.) implementált webkiszolgáló-alkalmazások.|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Engedélyezési kód megadása|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Az ügyfél hitelesítő adatok áramlása megfelelő azokban az esetekben, amikor az ügyfél (az alkalmazás) hozzáférést kér az ellenőrzése alatt álló védett erőforrásokhoz. Az ügyfél erőforrás-tulajdonosnak minősül, ezért nincs szükség végfelhasználói beavatkozásra.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Ügyfélhitelesítő adatok megadása|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Implicit folyamat van optimalizálva az ügyfelek nem képesek fenntartani a bizalmas adatait ismert, hogy egy adott átirányítási URI-t működtetni. Ezek az ügyfelek általában egy böngészőben, parancsfájlnyelv, például a JavaScript használatával kerülnek megvalósításra.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Implicit támogatás|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Az erőforrás-tulajdonos ipassword-hitelesítő adatok folyamata megfelelő azokban az esetekben, amikor az erőforrás tulajdonosa bizalmi kapcsolatban áll az ügyféllel (az alkalmazás), például az eszköz operációs rendszerrel vagy egy magas szintű jogosultsággal rendelkező alkalmazással. Ez a folyamat olyan ügyfelek számára alkalmas, akik képesek megszerezni az erőforrás tulajdonosának hitelesítő adatait (felhasználónév és jelszó, általában egy interaktív űrlap használatával).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Erőforrás-tulajdonosi jelszó hitelesítő adatok megadása|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<\> p Az erőforrás tulajdonosa megadja az ügyfélnek a felhasználónevét és jelszavát. </p\> <\> p Az ügyfél az erőforrás-tulajdonostól kapott hitelesítő adatok megadásával hozzáférési jogkivonatot kér az engedélyezési kiszolgáló tokenvégpontjától.  A kérelem benyújtásakor az ügyfél hitelesíti magát az engedélyezési kiszolgálóval. </p\> <\> p Az engedélyezési kiszolgáló hitelesíti az ügyfelet, ellenőrzi az erőforrás tulajdonosának hitelesítő adatait, és ha ez érvényes, kiad egy hozzáférési jogkivonatot. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<\> p Ha a kérelem sikertelen volt az ügyfél hitelesítése vagy érvénytelen, az engedélyezési kiszolgáló HTTP 400 (Rossz kérés) állapotkóddal válaszol (hacsak másként nincs megadva), és a következő paramétereket tartalmazza a válaszhoz. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<\> p Az ügyfél a következő paraméterek hozzáadásával kéri a token végpontját az "application/x-www-form-urlencoded" formátumban, a HTTP-kérelem entitástörzsében az UTF-8 karakterkódolásával. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<\> p Ha a hozzáférési jogkivonat-kérelem érvényes és engedélyezett, az engedélyezési kiszolgáló hozzáférési jogkivonatot és választható frissítési jogkivonatot ad ki, és a választ úgy építi ki, hogy a következő paramétereket adja hozzá a HTTP-válasz entitástörzséhez egy 200 (OK) állapotkóddal. </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Hozzáférési jogkivonat-kérelem|  
|OAuth2Step_AuthorizationRequest_Name|Engedélyezési kérelem|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiadott hozzáférési jogkivonat.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiadott hozzáférési jogkivonat.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Szükséges. Az engedélyezési kiszolgáló által kiadott hozzáférési jogkivonat.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Szükséges. Az engedélyezési kiszolgáló által kiadott hozzáférési jogkivonat.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Szükséges. Ügyfélazonosító.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES, ha az ügyfél nem hitelesíti az engedélyezési kiszolgálót.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Szükséges. Az ügyfélazonosító.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Szükséges. Az engedélyezési kiszolgáló által létrehozott engedélyezési kód.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Szükséges. Az engedélyezési kiszolgálótól kapott engedélyezési kód.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Választható. Emberre olvasható ASCII szöveg, amely további információkat tartalmaz.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Választható. Emberre olvasható ASCII szöveg, amely további információkat tartalmaz.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Választható. Emberre olvasható ASCII szöveg, amely további információkat tartalmaz.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Választható. Emberre olvasható ASCII szöveg, amely további információkat tartalmaz.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Választható. Emberre olvasható ASCII szöveg, amely további információkat tartalmaz.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Választható. Egy ember által olvasható weboldalt azonosító URI, amely a hibával kapcsolatos információkat tartalmaz.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Választható. Egy ember által olvasható weboldalt azonosító URI, amely a hibával kapcsolatos információkat tartalmaz.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Választható. Egy ember által olvasható weboldalt azonosító URI, amely a hibával kapcsolatos információkat tartalmaz.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Választható. Egy ember által olvasható weboldalt azonosító URI, amely a hibával kapcsolatos információkat tartalmaz.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Választható. Egy ember által olvasható weboldalt azonosító URI, amely a hibával kapcsolatos információkat tartalmaz.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Szükséges. Egyetlen ASCII hibakód a következőből: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII hibakód a következő ből: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII hibakód a következő ből: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Szükséges. Egyetlen ASCII hibakód a következőből: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Szükséges. Egyetlen ASCII hibakód a következő ből: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Ajánlott. A hozzáférési jogkivonat élettartama másodpercben.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Szükséges. Az értéket "authorization_code" értékre kell állítani.|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Szükséges. Az értéket "client_credentials" értékre kell állítani.|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az értéket "jelszó" értékre kell állítani.|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az erőforrás-tulajdonosi jelszó.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Választható. Az átirányítási végpont URI-nak abszolút URI-nak kell lennie.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|SZÜKSÉGES, ha az engedélyezési kérelem tartalmazza a "redirect_uri" paramétert, és értéküknek azonosnak kell lennie.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Választható. Az átirányítási végpont URI-nak abszolút URI-nak kell lennie.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Választható. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Választható. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Választható. A frissítési jogkivonat, amely új hozzáférési jogkivonatok beszerzésére használható.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Szükséges. Az értéket "kód" értékre kell állítani.|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Szükséges. Az értéket "token" értékre kell állítani.|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|NEM KÖTELEZŐ, ha megegyezik az ügyfél által kért hatókörrel; ellenkező esetben kötelező.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|NEM KÖTELEZŐ, ha megegyezik az ügyfél által kért hatókörrel; ellenkező esetben kötelező.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|NEM KÖTELEZŐ, ha megegyezik az ügyfél által kért hatókörrel; ellenkező esetben kötelező.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Választható. A hozzáférési kérelem hatóköre.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|NEM KÖTELEZŐ, ha megegyezik az ügyfél által kért hatókörrel; ellenkező esetben kötelező.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|KÖTELEZŐ, ha az "állapot" paraméter szerepel az ügyfél engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Ajánlott. Az ügyfél által a kérelem és a visszahívás közötti állapot fenntartásához használt átlátszatlan érték.  Az engedélyezési kiszolgáló ezt az értéket tartalmazza, amikor a felhasználói ügynököt visszairányítja az ügyfélre.  A paramétert a helyek közötti kérelmek hamisításának megelőzésére kell használni.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|KÖTELEZŐ, ha az "állapot" paraméter szerepel az ügyfél engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|KÖTELEZŐ, ha az "állapot" paraméter szerepel az ügyfél engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Ajánlott. Az ügyfél által a kérelem és a visszahívás közötti állapot fenntartásához használt átlátszatlan érték.  Az engedélyezési kiszolgáló ezt az értéket tartalmazza, amikor a felhasználói ügynököt visszairányítja az ügyfélre.  A paramétert a helyek közötti kérelmek hamisításának megelőzésére kell használni.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|KÖTELEZŐ, ha az "állapot" paraméter szerepel az ügyfél engedélyezési kérelemben.  Az ügyféltől kapott pontos érték.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Szükséges. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Szükséges. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Szükséges. A kibocsátott jogkivonat típusa.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Szükséges. A kibocsátott jogkivonat típusa.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Szükséges. Az erőforrás tulajdonosának felhasználóneve.|  
|OAuth2UnsupportedTokenType|A '{0}' token típus nem támogatott.|  
|OAuth2InvalidÁllam|Érvénytelen válasz az engedélyezési kiszolgálótól|  
|OAuth2GrantType_AuthorizationCode|Engedélyezési kód|  
|OAuth2GrantType_Implicit|Implicit|  
|OAuth2GrantType_ClientCredentials|Ügyfél-hitelesítő adatok|  
|OAuth2GrantType_ResourceOwnerPassword|Erőforrás-tulajdonosi jelszó|  
|WebDocumentation302Kód|302 talált|  
|WebDocumentation400Kód|400 (Rossz kérés)|  
|OAuth2SendingMethod_AuthHeader|Engedélyezési fejléc|  
|OAuth2SendingMethod_QueryParam|Lekérdezési paraméter|  
|OAuth2AuthorizationServerGeneralException|Hiba történt a hozzáférés engedélyezése közben{0}|  
|OAuth2AuthorizationServerCommunicationException|Nem sikerült HTTP-kapcsolatot létesíteni az engedélyezési kiszolgálóval, vagy váratlanul lezárták.|  
|WebDocumentationOAuth2Általános hibaüzenet|Váratlan hiba történt.|  
|AuthorizationServerCommunicationException|Az engedélyezési kiszolgáló kommunikációs kivétele megtörtént. Forduljon a rendszergazdához.|  
|TextblockSubscriptionKeyHeaderDescription|Előfizetési kulcs, amely hozzáférést biztosít ehhez az API-hoz. A <egy href='/developer'\>Profile\></a található.|  
|TextblockOAuthHeaderDescription|OAuth 2.0 hozzáférési jogkivonat\> {0} <i\></i . Támogatott támogatási típusok:\> {1} <\>i</i .|  
|TextblockContentTypeHeaderDescription|Az API-nak küldött törzs médiatípusa.|  
|ErrorMessageapiNem érhető el|A hívni kívánt API jelenleg nem érhető el. Kérjük, lépjen kapcsolatba az API kiadójával <a href="/issues"\>itt</a.\>|  
|ErrorMessageApiTimedout|A hívni kívánt API a szokásosnál tovább tart, hogy visszakapja a választ. Kérjük, lépjen kapcsolatba az API kiadójával <a href="/issues"\>itt</a.\>|  
|BadRequestParameterExpected|"'{0}' paraméter t várt"|  
|TooltipTextDoubleClickToSelectAll|Kattintson duplán az összes kijelöléséhez.|  
|TooltipTextHideRevealSecret|Megjelenítés/elrejtés|  
|ButtonLinkOpenConsole|Kipróbálom|  
|SectionHeadingRequestBody|A kérés törzse|  
|SectionHeadingRequestParameters|Kérelem paraméterei|  
|SectionHeadingRequestUrl|Kérés URL-címe|  
|SectionHeadingResponse|Válasz|  
|SectionHeadingRequestHeaders|Kérésfejlécek|  
|FormLabelSubtextNem kötelező|választható|  
|SectionHeadingCodeSamples|Kódminták|  
|TextblockOpenidConnectHeaderDescription|A <i</i.OpenID Connect ID tokenje <i\> {0}</i.\> Támogatott támogatási típusok:\> {1} <\>i</i .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a>ErrorPageStrings (Hibalapkarakterláncok)  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelBack|Vissza|  
|LinkLabelHomePage|kezdőlap|  
|LinkLabelSendUsEmail|Küldjön nekünk egy e-mailt|  
|PageTitleError (PageTitleError)|Sajnáljuk, probléma merült fel a kért oldal kiszolgálása koron|  
|TextblockPotentialCauseIntermittentIssue|Ez lehet egy időszakos adatelérési probléma, amely már elment.|  
|TextblockPotentialCauseOldLink|A link, amelyre rákattintott, lehet, hogy régi, és már nem a megfelelő helyre mutat.|  
|TextblockPotentialCauseTechnicalProbléma|Lehet, hogy technikai probléma van a mi oldalunkon.|  
|TextblockPotentialSolutionRefresh|Próbálja meg frissíteni az oldalt.|  
|TextblockPotentialSolutionStartOver|Kezdjük elölről a mi {0}.|  
|TextblockPotentialSolutionTryAgain|Menj, {0} és próbálja meg a műveletet végzett újra.|  
|TextReportProblem (Szövegjelentés – probléma|{0}leírja, mi volt a baj, és mi nézd meg, amint tudunk.|  
|CímPotentialCause|Lehetséges ok|  
|CímPotentialSolution|Ez talán csak egy átmeneti kérdés, néhány dolog, hogy megpróbálja|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a>IssuesStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebIssuesIndexTitle|Hibák|  
|WebIssuesNoActiveSubscriptions|Nincs aktív előfizetése. A probléma bejelentéséhez elő kell fizetnie egy termékre.|  
|WebIssuesNotSignin|Nincs bejelentkezve. Kérjük, {0} jelentse na a problémát, vagy tegyen közzé megjegyzést.|  
|WebIssuesReportIssueButton|Jelentés kiadása|  
|WebissuesSignIn|bejelentkezés|  
|WebIssuesStatusReportedby|Állapot: {0} &#124; által jelentett{1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a>NotFoundStrings (Nem Található karakterláncok)  
  
|Név|Szöveg|  
|----------|----------|  
|LinkLabelHomePage|kezdőlap|  
|LinkLabelSendUsEmail|küldjön nekünk egy e-mailt|  
|PageTitleNem található|Sajnáljuk, nem találjuk a keresett oldalt|  
|TextblockPotentialCauseMisspelledUrl|Lehet, hogy elgépelte az URL-címet, ha beírta.|  
|TextblockPotentialCauseOldLink|A link, amelyre rákattintott, lehet, hogy régi, és már nem a megfelelő helyre mutat.|  
|TextblockPotentialSolutionRetype|Próbálja meg újrabeírni az URL-címet.|  
|TextblockPotentialSolutionStartOver|Kezdjük elölről a mi {0}.|  
|TextReportProblem (Szövegjelentés – probléma|{0}leírja, mi volt a baj, és mi nézd meg, amint tudunk.|  
|CímPotentialCause|Lehetséges ok|  
|CímPotentialSolution|Lehetséges megoldás|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a>ProductDetailsStrings  
  
|Név|Szöveg|  
|----------|----------|  
|WebProductsAgreement|A Termékre {0} való feliratkozással elfogadom `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>`a .|  
|WebtermékekJogi- és szerződési feltételek|Használati feltételek|  
|WebProductsSubscribeButton|Feliratkozás|  
|WebProductsUsageLimitsfejléc|Használati korlátok|  
|WebProductsYouAreNotSubscribed|Feliratkozott erre a termékre.|  
|WebtermékekYouRequestedSubscription|Előfizetést kért erre a termékre.|  
|ErrorYouneedagreewithLegalterms|A folytatáshoz el kell fogadnia a Használati feltételeket.|  
|ButtonLabelAddSubscription|Előfizetés hozzáadása|  
|LinkLabelChangeSubscriptionName|Módosítása|  
|ButtonLabelMegerősítés|Jóváhagyás|  
|TextblockMultipleSubscriptionsCount|A {0} termékre előfizetése van:|  
|TextblockSingleSubscriptionsCount|A {0} termékre előfizetése van:|  
|TextblockSingleApisCount|Ez a {0} termék API-t tartalmaz:|  
|TextblockMultipleApisCount|Ez a {0} termék API-kat tartalmaz:|  
|TextblockHeaderSubscribe|Feliratkozás a termékre|  
|TextblockSubscriptionDescription|Az új előfizetés a következőképpen jön létre:|  
|TextblockSubscriptionLimitReached|Az előfizetések korlátját elértük.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a>ProductsStrings (TermékekHúrok)  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleProducts|Termékek|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a>ProviderInfoStrings (Szolgáltatóinfokarakterláncok)  
  
|Név|Szöveg|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|A bejelentkezést jelenleg a rendszergazdák letiltják.|  
|TextboxExternalIdentitiesSigninInvitation|Másik lehetőségként jelentkezzen be|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Bejelentkezés a következővel:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a>Bejelentkezési erőforrások  
  
|Név|Szöveg|  
|----------|----------|  
|Igazgató Nem alapítva|A rendszer nem található a tag, vagy az aláírás érvénytelen|  
|HibaSsoAuthenticationfailed|Az SSO-hitelesítés nem sikerült|  
|HibaSsoAuthenticationFailedDetailed|Érvénytelen jogkivonat vagy aláírás nem ellenőrizhető.|  
|ErrorSsoTokenInvalid|Az Egyszeri bejelentkezés tokenje érvénytelen|  
|ValidationErrorSpecificEmailMár létezik|E-mail '{0}' már regisztrálva|  
|ValidationErrorSpecificEmailInvalid|{0}Az '' e-mail érvénytelen|  
|ValidationErrorPasswordÉrvénytelen|A jelszó érvénytelen. Javítsa ki a hibákat, majd próbálkozzon újra.|  
|IngatlanTooShort|{0}túl rövid|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Érvénytelen e-mail cím.|  
|ValidationMessageNewPasswordConfirmationRequired|Új jelszó megerősítése|  
|ValidationErrorPasswordConfirmationRequired|A jelszó üresszavának ellenőrzése|  
|WebAuthenticationEmailChangeNotice|A visszaigazoló e-mail {0}módosítása úton van a felé. Kérjük, kövesse az utasításokat, hogy erősítse meg az új e-mail címét. Ha az e-mail nem érkezik meg a postaládájába a következő néhány percben, kérjük, ellenőrizze a levélszemét mappát.|  
|WebAuthenticationEmailChangeNoticefejléc|Az e-mail módosítási kérelmet sikeresen feldolgozták|  
|WebAuthenticationEmailChangeNoticeTitle cím|E-mail módosítása kért|  
|WebAuthenticationEmailHasRevertedNotice|Ön e-mail már létezik. A kérelem visszalett állítva|  
|ValidationErrorEmailMár létezik|Az e-mail már létezik|  
|ValidationErrorEmailInvalid|Érvénytelen e-mail cím|  
|TextboxLabelEmail e-mail|E-mail|  
|ValidationErrorEmailRequired|Az e-mail-cím megadása kötelező.|  
|WebAuthenticationErrorNoticefejléc|Hiba|  
|WebAuthenticationFieldLengtherrorÜzenet|{0}maximális hosszúságúnak kell lennie{1}|  
|TextboxLabelEmailFirstName|Utónév|  
|ValidationErrorFirstNameRequired|A keresztnév kötelező.|  
|ValidationErrorFirstNameInvalid|Érvénytelen utónév|  
|NoticeInvalidInvitationToken|Felhívjuk figyelmét, hogy a visszaigazoló linkek csak 48 óráig érvényesek. Ha még mindig ezen időkereten belül, kérjük, győződjön meg róla, hogy a link helyes. Ha a hivatkozás lejárt, akkor ismételje meg a megerősítést.|  
|NoticeHeaderInvalidInvitationToken|Érvénytelen meghívási jogkivonat|  
|NoticeTitleInvalidInvitationToken|Megerősítési hiba|  
|WebAuthenticationLastnameInvalidErrorMessage|Érvénytelen vezetéknév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|Érvényesítési hibaLastnameRequired|A vezetéknév kötelező.|  
|WebAuthenticationLinkExpiredNotice|Az Önnek küldött megerősítő hivatkozás lejárt. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|A jelszó-visszaállítási hivatkozás érvénytelen vagy lejárt.|  
|WebAuthenticationLinkExpiredNoticeTitle cím|Elküldött hivatkozás|  
|WebAuthenticationNewPasswordLabel|Új jelszó|  
|ValidationMessageNewPasswordSzükséges|Új jelszó szükséges.|  
|TextboxLabelNotificationsSenderEmail|Értesítések feladójának e-mail címe|  
|TextboxLabelOrganizationName|Szervezet neve|  
|WebAuthenticationOrganizationRequirederrorMessage|A szervezet neve üres|  
|WebAuthenticationPasswordChangedNotice|A jelszó frissítése sikeresen megtörtént|  
|WebAuthenticationPasswordChangedNoticeTitle|Jelszó frissítve|  
|WebAuthenticationPasswordCompareErrorMessage|A jelszavak nem egyeznek|  
|WebAuthenticationPasswordConfirmLabel|Jelszó megerősítése|  
|ValidationErrorPasswordInvalidDetailed|A jelszó túl gyenge.|  
|WebAuthenticationPasswordLabel|Jelszó|  
|ValidationErrorPasswordSzükséges|Jelszó szükséges.|  
|WebAuthenticationPasswordResetSendNotice|A jelszó-visszaigazoló e-mail módosítása úton van a. {0} Kérjük, kövesse az e-mailben található utasításokat a jelszómódosítási folyamat folytatásához.|  
|WebAuthenticationPasswordResetSendNoticefejléc|A jelszó-visszaállítási kérelem feldolgozása sikeresen megtörtént|  
|WebAuthenticationPasswordResetSendNoticeTitle cím|Jelszó-visszaállítás kérése|  
|WebAuthenticationRequestNotFoundNotice|A kérelem nem található|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Az értesítések feladója e-mail üres|  
|WebAuthenticationSigninPasswordLabel|Erősítse meg a módosítást jelszó megadásával|  
|WebAuthenticationSignupConfirmNotice|Regisztrációs visszaigazoló e-mail {0}úton van, hogy .<br /\> Kérjük,\> kövesse az utasításokat az e-mailben, hogy aktiválja a account.<br / Ha az e-mail nem érkezik meg a postaládájába a következő néhány percben, kérjük, ellenőrizze a levélszemét mappát.|  
|WebAuthenticationSignupConfirmNoticefejléc|A fiók létrehozása sikeresen megtörtént|  
|WebAuthenticationSignupConfirmNoticeRepeatheader|A regisztrációt visszaigazoló e-mailt ismét elküldtük|  
|WebAuthenticationSignupConfirmNoticeTitle|Fiók létrehozva|  
|WebAuthenticationTokenRequirederrorMessage|A token üres|  
|WebAuthenticationuserAlreadyRegisteredNotice|Úgy tűnik, a felhasználó ezt az e-mailt már regisztrált a rendszerben. Ha elfelejtette jelszavát, próbálja meg visszaállítani, vagy lépjen kapcsolatba ügyfélszolgálatunkkal.|  
|WebAuthenticationuserAlreadyRegisteredNoticefejléc|A felhasználó már regisztrálva van|  
|WebAuthenticationuserAlreadyRegisteredNoticeTitle|Már regisztrálva van|  
|ButtonLabelChangePassword|Change password|  
|ButtonLabelChangeAccountInfo|Fiókadatok módosítása|  
|ButtonLabelCloseAccount|Fiók bezárása|  
|WebAuthenticationInvalidCaptchaErrorMessage|A beírt szöveg nem egyezik a képen lévő szöveggel. Próbálkozzon újra.|  
|ValidationErrorCredentialsInvalid|Az e-mail cím vagy a jelszó érvénytelen. Javítsa ki a hibákat, majd próbálkozzon újra.|  
|WebAuthenticationRequestIsNotValid|A kérelem érvénytelen|  
|WebAuthenticationuserIsNotConfirm|A bejelentkezés előtt erősítse meg a regisztrációt.|  
|WebAuthenticationInvalidEmailformázva|Az e-mail érvénytelen:{0}|  
|WebAuthenticationuserNotFound|A felhasználó nem található|  
|WebAuthenticationTenantNotRegistered|A fiók egy Azure Active Directory-bérlőhöz tartozik, amely nem jogosult a portál elérésére.|  
|WebAuthenticationAuthenticationsikertelen|A hitelesítés nem sikerült.|  
|WebAuthenticationGooglePlusNotEnabled|A hitelesítés nem sikerült. Ha engedélyezte az alkalmazást, kérjük, forduljon az admin, hogy megbizonyosodjon arról, hogy a Google-hitelesítés megfelelően van konfigurálva.|  
|ValiderrorallowedTenantIsRequired|Engedélyezett bérlő szükséges|  
|ValidationerrorTenantIsNot|Az Azure Active{0}Directory-bérlő ' ' érvénytelen.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginASzolgáltató használatával|Bejelentkezés a {0} fiókkal|  
|WebAuthenticationUserLimitNotice|Ez a szolgáltatás elérte az engedélyezett felhasználók maximális számát. Frissítse `<a href="mailto:{0}"\>contact the administrator</a\>` a szolgáltatást, és engedélyezze újra a felhasználói regisztrációt.|  
|WebAuthenticationUserLimitNoticefejléc|A felhasználói regisztráció le tiltva|  
|WebAuthenticationUserLimitNoticeTitle cím|A felhasználói regisztráció le tiltva|  
|WebAuthenticationUserRegistrationDisabledNotice|A rendszergazda letiltotta a felhasználók regisztrációját. Kérjük, jelentkezzen be a külső identitás szolgáltató.|  
|WebAuthenticationUserRegistrationDisabledNoticefejléc|A felhasználói regisztráció le tiltva|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|A felhasználói regisztráció le tiltva|  
|WebAuthenticationSignupPendingConfirmationNotice|Mielőtt befejeznénk a fiók létrehozását, ellenőriznünk kell az ön e-mail címét. Küldtünk egy e-mailt {0}a. Kérjük, kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg néhány percen belül, kérjük, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Találtunk egy meg nem erősített {0}fiókot az e-mail címhez. A fiók létrehozásához ellenőriznünk kell az e-mail címét. Küldtünk egy e-mailt {0}a. Kérjük, kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg néhány percen belül, kérjük, ellenőrizze a levélszemét mappáját|  
|WebAuthenticationSignupConfirmationAlmostDone|Majdnem kész|  
|WebAuthenticationSignupConfirmationEmailSent|Küldtünk egy e-mailt {0}a. Kérjük, kövesse az e-mailben található utasításokat a fiók aktiválásához. Ha az e-mail nem érkezik meg néhány percen belül, kérjük, ellenőrizze a levélszemét mappáját.|  
|WebAuthenticationEmailSentNotificationMessage|Az e-mail küldése sikeresen befejeződött{0}|  
|WebAuthenticationNoAadTenantConfigured|Nincs konfigurálva a szolgáltatáshoz konfigurált Azure Active Directory-bérlő.|  
|CheckboxLabelUserRegistrationTermsConsentRequiredRequired|Egyetértek azzal, hogy a `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>`.|  
|TextblockUserRegistrationTermsProvided|Kérjük, olvassa el`<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|Párbeszédcímkifejezésekhasználat|Használati feltételek|  
|ValidationMessageConsentNotAccepted|A folytatáshoz el kell fogadnia a Használati feltételeket.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a>Bejelentkezési karakterláncok  
  
|Név|Szöveg|  
|----------|----------|  
|WebAuthenticationForgotPassword|Elfelejtette a jelszavát?|  
|WebAuthenticationIfAdministrator|Ha Ön rendszergazda, be `<a href="{0}"\>here</a\>`kell jelentkeznie.|  
|WebAuthenticationNotAMember|Még nem tag? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationNe|Emlékezz rám ezen a számítógépen|  
|WebAuthenticationSiginininWithPassword|Bejelentkezés felhasználónévvel és jelszóval|  
|WebAuthenticationSigninTitle|Bejelentkezés|  
|WebAuthenticationSignUpNow|Regisztráljon most|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a>SignupStrings (Regisztrációs karakterláncok)  
  
|Név|Szöveg|  
|----------|----------|  
|PageTitleSignup (PageTitleSignup)|Regisztráció|  
|WebAuthenticationAlreadyAMember|Már tag?|  
|WebAuthenticationCreateNewAccount|Új API Management-fiók létrehozása|  
|WebAuthenticationSigninNow|Bejelentkezés most|  
|ButtonLabelSignup|Regisztráció|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a>SubscriptionListStrings  
  
|Név|Szöveg|  
|----------|----------|  
|Előfizetés-visszavonás megerősítése|Biztosan lemondja ezt az előfizetést?|  
|Előfizetésmegújítottamegerősítése|Biztosan megszeretné újítani ezt az előfizetést?|  
|WebDevelopersManageSubscriptions|Előfizetések kezelése|  
|WebDevelopersPrimaryKey|Elsődleges kulcs|  
|WebDevelopersRegenerateLink|Újragenerálás|  
|WebDevelopersSecondaryKey|Másodlagos kulcs|  
|ButtonLabelShowKey|Megjelenítés|  
|ButtonLabelRenewElőfizetés|Frissítés|  
|WebDevelopersSubscriptionRequested|Kérés:{0}|  
|WebDevelopersSubscriptionRequestedState|Kérelmezve|  
|WebDevelopersSubscriptionTableNamefejléc|Név|  
|WebDevelopersSubscriptionTableStatefejléc|Állapot|  
|WebDevelopersUsageStatisticsLink|Analytics-jelentések|  
|WebDevelopersYourSubscriptions|Az Ön előfizetései|  
|SubscriptionPropertyLabelRequestedDate|Kérés:|  
|SubscriptionPropertyLabelStarteddate|Kezdés:|  
|PageTitleRenameElőfizetés|Előfizetés átnevezése|  
|SubscriptionPropertyLabelName|Előfizetés neve|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a>Előfizetési karakterláncok  
  
|Név|Szöveg|  
|----------|----------|  
|SectionHeadingCloseAccount|Szeretné lezárni a fiókját?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Elrejtés|  
|ButtonLabelRegenerateKey|Újragenerálás|  
|InformationMessageKeyRegenerated|Biztosan újragenerálja ezt a kulcsot?|  
|ButtonLabelShowKey|Megjelenítés|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a>UpdateProfileStrings  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelUpdateProfile|Profil frissítése|  
|PageTitleUpdateProfile|Fiókadatok frissítése|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a>Userprofile  
  
|Név|Szöveg|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Fiókadatok módosítása|  
|ButtonLabelChangePassword|Change password|  
|ButtonLabelCloseAccount|Fiók bezárása|  
|TextboxLabelEmail e-mail|E-mail|  
|TextboxLabelEmailFirstName|Utónév|  
|TextboxLabelEmailLastName|Vezetéknév|  
|TextboxLabelNotificationsSenderEmail|Értesítések feladójának e-mail címe|  
|TextboxLabelOrganizationName|Szervezet neve|  
|SubscriptionStateActive|Aktív|  
|SubscriptionStateCancelled|Lemondva|  
|ElőfizetésLejárt|Lejárt|  
|SubscriptionStateElutasított|Elutasítva|  
|SubscriptionStateRequested|Kérelmezve|  
|SubscriptionStateSuspended|Felfüggesztve|  
|DefaultSubscriptionNameTemplate|{0}(alapértelmezett)|  
|Előfizetésnametemplate|Fejlesztői hozzáférés #{0}|  
|TextboxLabelSubscriptionName|Előfizetés neve|  
|ValidationMessageSubscriptionNameRequired|Az előfizetés neve nem lehet üres.|  
|ApiManagementUserLimitReached|Ez a szolgáltatás elérte az engedélyezett felhasználók maximális számát. Frissítsen magasabb tarifacsomagra.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a>A jeljel erőforrásai  
 Az API Management fejlesztői portálsablonjai a [Bootstrap jelekből származó](https://getbootstrap.com/components/#glyphicons)jeleket használhatják. Ez a jelképkészlet több mint 250 jelcset tartalmaz betűformátumban a [Glyphicon](https://glyphicons.com/) Halflings készletből. Ha ebből a készletből szeretne jelmondatot használni, használja az alábbi szintaxist.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 A jelek teljes listáját a [Bootstrap jelek között](https://getbootstrap.com/components/#glyphicons)található.

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.

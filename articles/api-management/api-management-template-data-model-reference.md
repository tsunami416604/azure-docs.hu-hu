---
title: Az Azure API Management-sablon data modellhez tartozó referencia |} Microsoft Docs
description: További tudnivalók az adatok modellek használata a fejlesztői portál sablonok az Azure API Management for közös elemek entitás és típus felelősséget.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 0f27b6b529c2591e37d48e3386190077fc8efc32
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablon modell hivatkozás
Ez a témakör ismerteti az adatok modellek használata a fejlesztői portál sablonok az Azure API Management for közös elemek entitás és típus felelősséget.  
  
 A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [API-összefoglalót](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Melléklet](#Attachment)  
-   [kódminta](#Sample)  
-   [Megjegyzés](#Comment)  
-   [Szűrés](#Filtering)  
-   [Fejléc](#Header)  
-   [HTTP-kérelem](#HTTPRequest)  
-   [HTTP-válasz](#HTTPResponse)  
-   [A probléma](#Issue)  
-   [Művelet](#Operation)  
-   [Művelet menü](#Menu)  
-   [A művelet menüpont](#MenuItem)  
-   [Lapozófájl](#Paging)  
-   [A paraméter](#Parameter)  
-   [A termék](#Product)  
-   [Szolgáltató](#Provider)  
-   [Megjelenítésre](#Representation)  
-   [Előfizetés](#Subscription)  
-   [Előfizetés összefoglaló](#SubscriptionSummary)  
-   [Felhasználói fiók adatainak](#UserAccountInfo)  
-   [Felhasználói bejelentkezés](#UseSignIn)  
-   [A felhasználói regisztráció](#UserSignUp)  
  
##  <a name="API"></a> API  
 A `API` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|karakterlánc|Erőforrás-azonosítója. Az API-t az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|név|karakterlánc|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|karakterlánc|Az API leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|serviceUrl|karakterlánc|Ez az API végrehajtási háttérszolgáltatást abszolút URL-címe|  
|elérési út|karakterlánc|Egyedi azonosítása, ez az API és az összes az API Management szolgáltatáspéldány belül az erőforrás elérési utak relatív URL-címe. Az API végpont alap URL-cím szolgáltatás példány létrehozásakor megadott kialakításához egy nyilvános URL-címet a API-lesz csatolva.|  
|protokollok|a tömb szám|Ismerteti a protokollok a műveletek az API-ban is elindítható. Két érték engedélyezett `1 - http` és `2 - https`, vagy mindkettőt.|  
|authenticationSettings|[Engedélyezési kiszolgáló hitelesítési beállítások](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Ez az API szerepel hitelesítési beállítások gyűjteménye.|  
|subscriptionKeyParameterNames|objektum|Nem kötelező tulajdonság az előfizetés kulcsot tartalmazó lekérdezés és/vagy a fejléc paraméterek egyéni nevének megadására használható. Ha ez a tulajdonság jelen, a két alábbi tulajdonságok közül legalább egyet tartalmazhat.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a> API-összefoglalót  
 A `API summary` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|karakterlánc|Erőforrás-azonosítója. Az API-t az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|név|karakterlánc|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|karakterlánc|Az API leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
  
##  <a name="Application"></a> Alkalmazás  
 A `application` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|Az alkalmazás egyedi azonosítója.|  
|Beosztás|karakterlánc|Az alkalmazás címe.|  
|Leírás|karakterlánc|Az alkalmazás leírását.|  
|URL-cím|URI|Az alkalmazás URI.|  
|Verzió|karakterlánc|Fájlverzió-információkat az alkalmazás.|  
|Követelmények|karakterlánc|Az alkalmazás leírását.|  
|Állapot|szám|Az alkalmazás aktuális állapota.<br /><br /> -0 - regisztrálva<br /><br /> -1 - elküldése megtörtént<br /><br /> -Közzétett 2-<br /><br /> -3 - elutasítva<br /><br /> – 4 – közzé nem tett|  
|RegistrationDate|DateTime|A dátum és idő, az alkalmazás lett regisztrálva.|  
|CategoryId|szám|A kategória az alkalmazás (pénzügyi, szórakozás stb.)|  
|DeveloperId|karakterlánc|A fejlesztő, a kérelmet egyedi azonosítója.|  
|Mellékletek|A gyűjtemény [melléklet](#Attachment) entitásokat.|Az alkalmazás, például a képernyőképek vagy ikonok mellékleteit.|  
|Ikon|[Melléklet](#Attachment)|Az ikonra az alkalmazásához.|  
  
##  <a name="Attachment"></a> Melléklet  
 A `attachment` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|UniqueId|karakterlánc|A melléklet egyedi azonosítója.|  
|URL-cím|karakterlánc|Az erőforrás URL-CÍMÉT.|  
|Típus|karakterlánc|A melléklet típusa.|  
|ContentType|karakterlánc|A melléklet adathordozó-típusát.|  
  
##  <a name="Sample"></a> kódminta  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|cím|karakterlánc|A művelet neve.|  
|részlet|karakterlánc|Ez a tulajdonság elavult, és nem használható.|  
|Ecset|karakterlánc|Mely kód szintaxisa a kódminta megjelenítésekor használt sablon színátmenetekhez. Két érték engedélyezett `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, és `csharp`.|  
|sablon|karakterlánc|A kód a minta sablon neve.|  
|törzs|karakterlánc|A kód a minta része a kódrészletet helyőrzője.|  
|method|karakterlánc|A művelet HTTP-metódus.|  
|séma|karakterlánc|A művelet kérelem használni kívánt protokollt.|  
|elérési út|karakterlánc|A művelet elérési útját.|  
|lekérdezés|karakterlánc|Lekérdezési karakterlánc például a megadott paraméterekkel.|  
|gazdagép|karakterlánc|Az API-hoz, amely tartalmazza ezt a műveletet az API Management szolgáltatás átjáró URL-CÍMÉT.|  
|fejlécek|A gyűjtemény [fejléc](#Header) entitásokat.|Fejlécek ehhez a művelethez.|  
|paraméterek|A gyűjtemény [paraméter](#Parameter) entitásokat.|Ehhez a művelethez megadott paraméterek.|  
  
##  <a name="Comment"></a> Megjegyzés  
 A `API` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|szám|A Megjegyzés azonosítója.|  
|CommentText|karakterlánc|A szervezet a megjegyzés. HTML is tartalmazhat.|  
|DeveloperCompany|karakterlánc|A fejlesztői vállalat neve.|  
|PostedOn|DateTime|A dátum és idő, a megjegyzés megtalálható.|  
  
##  <a name="Issue"></a> A probléma  
 A `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|A probléma egyedi azonosítója.|  
|ApiID|karakterlánc|Az API-t, amelynek a probléma történt az azonosítója.|  
|Beosztás|karakterlánc|A probléma címét.|  
|Leírás|karakterlánc|A probléma leírása.|  
|SubscriptionDeveloperName|karakterlánc|Utónév a fejlesztő, amely a hibát jelzett.|  
|IssueState|karakterlánc|A probléma aktuális állapotát. A lehetséges értékek a következők: Proposed, Opened, lezárva.|  
|ReportedOn|DateTime|A dátum és idő, a probléma történt a következő.|  
|Megjegyzések|A gyűjtemény [Megjegyzés](#Comment) entitásokat.|A probléma a megjegyzéseit.|  
|Mellékletek|A gyűjtemény [melléklet](api-management-template-data-model-reference.md#Attachment) entitásokat.|A probléma a mellékleteket.|  
|Szolgáltatások|A gyűjtemény [API](#API) entitásokat.|Az API-k előfizetett a felhasználót, hogy a probléma bejegyezve.|  
  
##  <a name="Filtering"></a> Szűrés  
 A `filtering` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Mintázat|karakterlánc|Az aktuális keresési kifejezés; vagy `null` nincs keresési kifejezés esetén.|  
|Helyőrző|karakterlánc|A szöveget a keresőmezőbe nincs megadott keresési kifejezés esetén.|  
  
##  <a name="Header"></a> Fejléc  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Leírás|Típus|  
|--------------|-----------------|----------|  
|név|karakterlánc|A paraméter neve.|  
|leírás|karakterlánc|A paraméter leírását.|  
|érték|karakterlánc|Fejléc értéke.|  
|A TypeName értéke|karakterlánc|Állomásfejléc-érték adattípusa.|  
|beállítások|karakterlánc|Beállítások.|  
|Szükséges|logikai|Hogy a fejléc szükség.|  
|readOnly|logikai|Hogy a fejléc csak olvasható.|  
  
##  <a name="HTTPRequest"></a> HTTP-kérelem  
 Ez a szakasz ismerteti a `request` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|leírás|karakterlánc|A művelet kérelem leírása.|  
|fejlécek|a tömb [fejléc](#Header) entitásokat.|Kérelem fejlécei.|  
|paraméterek|a tömb [paraméter](#Parameter)|A művelet a kérelemben szereplő paraméterek gyűjteménye.|  
|felelősséget|a tömb [ábrázolása](#Representation)|Művelet kérelem felelősséget gyűjteménye.|  
  
##  <a name="HTTPResponse"></a> HTTP-válasz  
 Ez a szakasz ismerteti a `response` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|állapotkód|Pozitív egész szám|A művelet válasz állapotkódja.|  
|leírás|karakterlánc|A művelet válasz leírása.|  
|felelősséget|a tömb [ábrázolása](#Representation)|Művelet válasz felelősséget gyűjteménye.|  
  
##  <a name="Operation"></a> művelet  
 A `operation` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|karakterlánc|Erőforrás-azonosítója. A művelet a jelenlegi API-kezelés szolgáltatás példány belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{aid}/operations/{id}` ahol `{aid}` API azonosító és `{id}` művelet azonosítója. Ez a tulajdonság csak olvasható.|  
|név|karakterlánc|A művelet neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|karakterlánc|A művelet leírását. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|séma|karakterlánc|Ismerteti a protokollok a műveletek az API-ban is elindítható. Két érték engedélyezett `http`, `https`, vagy mindkettőt `http` és `https`.|  
|uriTemplate|karakterlánc|Relatív URL-cím sablon azonosító az ehhez a művelethez a cél erőforráson. Paraméterek is tartalmazhat. Példa: `customers/{cid}/orders/{oid}/?date={date}`|  
|gazdagép|karakterlánc|Az API Management átjáró URL-címet a API-t futtató.|  
|httpMethod|karakterlánc|Művelet HTTP-metódus.|  
|Kérelem|[HTTP-kérelem](#HTTPRequest)|Kérelem részleteit tartalmazó entitás.|  
|válaszok|a tömb [HTTP-válasz](#HTTPResponse)|Művelet tömbje [HTTP-válasz](#HTTPResponse) entitásokat.|  
  
##  <a name="Menu"></a> Művelet menü  
 A `operation menu` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|apiId|karakterlánc|A jelenlegi API azonosítója.|  
|CurrentOperationId|karakterlánc|Az aktuális művelet azonosítója.|  
|Műveletek|karakterlánc|A menüben írja be.|  
|MenuItems|A gyűjtemény [művelet menüpont](#MenuItem) entitásokat.|A jelenlegi API-műveleteket.|  
  
##  <a name="MenuItem"></a> A művelet menüpont  
 A `operation menu item` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|A művelet azonosítója.|  
|Beosztás|karakterlánc|A művelet leírását.|  
|HttpMethod|karakterlánc|A művelet Http-metódus.|  
  
##  <a name="Paging"></a> Lapozófájl  
 A `paging` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Oldal|szám|Az aktuális oldalszám.|  
|PageSize együttes értéke|szám|Az eredmények maximális száma egy oldalon megjelenő.|  
|TotalItemCount|szám|A megjelenítendő elemek száma.|  
|ShowAll|logikai|Hason összes annak az eredménye egy oldalon kell-e.|  
|PageCount|szám|Az eredmények oldalak száma.|  
  
##  <a name="Parameter"></a> A paraméter  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Leírás|Típus|  
|--------------|-----------------|----------|  
|név|karakterlánc|A paraméter neve.|  
|leírás|karakterlánc|A paraméter leírását.|  
|érték|karakterlánc|A paraméter értékét.|  
|beállítások|A karakterlánc tömbje|Lekérdezési paraméter értéke a definiált értékekkel.|  
|Szükséges|logikai|Meghatározza, hogy paraméter szükség-e vagy sem.|  
|típusa|szám|Hogy a paraméter egy elérési utat (1), vagy egy lekérdezési karakterlánc paraméter (2).|  
|A TypeName értéke|karakterlánc|Paraméter típusa.|  
  
##  <a name="Product"></a> A termék  
 A `product` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|Erőforrás-azonosítója. A termék az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója. Ez a tulajdonság csak olvasható.|  
|Beosztás|karakterlánc|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Leírás|karakterlánc|A termék leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|Fogalmak|karakterlánc|A termék használati feltételeket. Próbál előfizetni a termék fejlesztők jelenik meg a rendszer, és a rendszer elfogadja ezeket a feltételeket, azok az előfizetés-műveletnek a végrehajtásához szükséges.|  
|ProductState|szám|Meghatározza, hogy a termék van közzétéve, vagy nem. Közzétett termékeket a fejlesztői portálján a fejlesztők által felderíthető. A nem közzétett termékek csak a rendszergazdák számára láthatók.<br /><br /> A termék állapota megengedett értékei:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|logikai|Meghatározza, hogy egy felhasználó egy időben a terméket több előfizetéssel is rendelkezhet.|  
|MultipleSubscriptionsCount|szám|A termék-előfizetések maximális száma a felhasználó szerepelhetnek egyszerre.|  
  
##  <a name="Provider"></a> Szolgáltató  
 A `provider` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Tulajdonságok|karakterlánc szótár|A hitelesítésszolgáltató tulajdonságai.|  
|AuthenticationType|karakterlánc|A szolgáltató típusát. (Az azure Active Directory, bejelentkezés Facebook, Google-fiók, a Microsoft Account, Twitter).|  
|Felirat|karakterlánc|Megjeleníti a szolgáltató nevét.|  
  
##  <a name="Representation"></a> Megjelenítésre  
 Ez a szakasz ismerteti a `representation`.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|contentType|karakterlánc|Meghatározza a regisztrált vagy egyéni content-type a ábrázolását, például `application/xml`.|  
|minta|karakterlánc|Példa a megjelenítésre.|  
  
##  <a name="Subscription"></a> Előfizetés  
 A `subscription` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|Erőforrás-azonosítója. Az előfizetés az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|Termékazonosító|karakterlánc|A termék erőforrás-azonosítója az előfizetett termék. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója.|  
|ProductTitle|karakterlánc|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|ProductDescription|karakterlánc|A termék leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|ProductDetailsUrl|karakterlánc|Relatív URL-címe a termék részletei.|  
|state|karakterlánc|Az előfizetés állapotát. Lehetséges állapota van:<br /><br /> - `0 - suspended` – az előfizetés le van tiltva, és az előfizető nem hívható meg a termék bármely API-k.<br /><br /> - `1 - active` – az előfizetés nem aktív.<br /><br /> - `2 - expired` – az előfizetés elérte a lejárat és inaktiváltuk.<br /><br /> - `3 - submitted` – az előfizetési kérelem a fejlesztő történt, de még nem jóváhagyták vagy elutasították.<br /><br /> - `4 - rejected` – az előfizetési kérelem rendszergazda meg lett tagadva.<br /><br /> - `5 - cancelled` – az előfizetés a fejlesztői vagy a rendszergazda által megszakítva.|  
|displayName|karakterlánc|Az előfizetéshez tartozó megjelenített név.|  
|CreatedDate|Dátum és idő|Az előfizetés létrehozásának dátuma, az ISO 8601 formátum: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|logikai|Hogy az előfizetés lehet megszakítani az aktuális felhasználó.|  
|IsAwaitingApproval|logikai|Az előfizetés e jóváhagyására vár.|  
|A StartDate|Dátum és idő|A kezdő dátum, az előfizetés, az ISO 8601 formátum: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Dátum és idő|ISO 8601 formátumban, az előfizetés lejárati dátuma: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Dátum és idő|Az előfizetés, ISO 8601 formátumban értesítési dátuma: `2014-06-24T16:25:00Z`.|  
|primaryKey|karakterlánc|Az előfizetés elsődleges kulcs. Hossza legfeljebb 256 karakter lehet.|  
|secondaryKey|karakterlánc|A másodlagos előfizetés kulcs. Hossza legfeljebb 256 karakter lehet.|  
|CanBeRenewed|logikai|Hogy az előfizetés lehet megújítani az aktuális felhasználó.|  
|HasExpired|logikai|Hogy az előfizetése lejárt.|  
|IsRejected|logikai|Hogy az előfizetés kérelem visszautasítva.|  
|CancelUrl|karakterlánc|A relatív URL-címe az előfizetés megszüntetése.|  
|RenewUrl|karakterlánc|Az előfizetés megújításához relatív URL-címe|  
  
##  <a name="SubscriptionSummary"></a> Előfizetés összefoglaló  
 A `subscription summary` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|karakterlánc|Erőforrás-azonosítója. Az előfizetés az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|displayName|karakterlánc|Az előfizetés megjelenített neve|  
  
##  <a name="UserAccountInfo"></a> Felhasználói fiók adatainak  
 A `user account info` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Utónév|karakterlánc|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Vezetéknév|karakterlánc|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|E-mail|karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Jelszó|karakterlánc|Felhasználói fiók jelszavát.|  
|NameIdentifier|karakterlánc|A fiókazonosító, ugyanaz, mint a felhasználói e-mailben.|  
|ProviderName|karakterlánc|Hitelesítési szolgáltató neve.|  
|IsBasicAccount|logikai|Értéke TRUE, ha ez a fiók regisztrálták az e-mailek és a jelszó; hamis, ha a fiók regisztrálták szolgáltató használatával.|  
  
##  <a name="UseSignIn"></a> Felhasználói bejelentkezés  
 A `user sign in` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|E-mail|karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Jelszó|karakterlánc|Felhasználói fiók jelszavát.|  
|ReturnUrl|karakterlánc|Ha a felhasználó kattintott a lap URL-jelentkezzen be.|  
|RememberMe|logikai|E menteni a jelenlegi felhasználó adatait.|  
|RegistrationEnabled|logikai|Regisztrációs engedélyezve van-e.|  
|DelegationEnabled|logikai|Delegált bejelentkezés engedélyezve van-e.|  
|DelegationUrl|karakterlánc|A delegált bejelentkezési URL-címe, ha engedélyezve van.|  
|SsoSignUpUrl|karakterlánc|Az egyszeri bejelentkezési URL-CÍMÉT a felhasználó számára, ha van ilyen.|  
|AuxServiceUrl|karakterlánc|Ha a jelenlegi felhasználó rendszergazda, ez az Azure-portálon a szolgáltatáspéldány mutató hivatkozást.|  
|Szolgáltatók|A gyűjtemény [szolgáltató](#Provider) entitások|A hitelesítésszolgáltatókat ehhez a felhasználóhoz.|  
|UserRegistrationTerms|karakterlánc|Azok a feltételek, amelyek a felhasználói bejelentkezés előtt kell fogadnia.|  
|UserRegistrationTermsEnabled|logikai|E kifejezések engedélyezettek.|  
  
##  <a name="UserSignUp"></a> Felhasználói bejelentkezési  
 A `user sign up` entitás tulajdonságai a következők:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|PasswordConfirm|logikai|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|Jelszó|karakterlánc|Felhasználói fiók jelszavát.|  
|PasswordVerdictLevel|szám|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|UserRegistrationTerms|karakterlánc|Azok a feltételek, amelyek a felhasználói bejelentkezés előtt kell fogadnia.|  
|UserRegistrationTermsOptions|szám|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|ConsentAccepted|logikai|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|E-mail|karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Utónév|karakterlánc|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Vezetéknév|karakterlánc|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Felhasználói adatok|karakterlánc|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up) vezérlő.|  
|NameIdentifier|karakterlánc|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|ProviderName|karakterlánc|Hitelesítési szolgáltató neve.|

## <a name="next-steps"></a>További lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).

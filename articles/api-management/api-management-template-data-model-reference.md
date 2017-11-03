---
title: "Az Azure API Management-sablon data modellhez tartozó referencia |} Microsoft Docs"
description: "További tudnivalók az adatok modellek használata a fejlesztői portál sablonok az Azure API Management for közös elemek entitás és típus felelősséget."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 72936a4d38f809934ddea74e5ae4a6029450a97c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablon modell hivatkozás
Ez a témakör ismerteti az adatok modellek használata a fejlesztői portál sablonok az Azure API Management for közös elemek entitás és típus felelősséget.  
  
 A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
-   [API](#API)  
-   [API-összefoglalót](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Melléklet](#Attachment)  
-   [Kódminta](#Sample)  
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
-   [Felhasználói bejelentkezési](#UserSignUp)  
  
##  <a name="API"></a>API  
 A `API` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|Karakterlánc|Erőforrás-azonosítója. Az API-t az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|név|Karakterlánc|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|Karakterlánc|Az API leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|serviceUrl|Karakterlánc|Ez az API végrehajtási háttérszolgáltatást abszolút URL-címe|  
|Elérési út|Karakterlánc|Egyedi azonosítása, ez az API és az összes az API Management szolgáltatáspéldány belül az erőforrás elérési utak relatív URL-címe. Az API végpont alap URL-cím szolgáltatás példány létrehozásakor megadott kialakításához egy nyilvános URL-címet a API-lesz csatolva.|  
|protokollok|a tömb szám|Ismerteti a protokollok a műveletek az API-ban is elindítható. Két érték engedélyezett `1 - http` és `2 - https`, vagy mindkettőt.|  
|authenticationSettings|[Engedélyezési kiszolgáló hitelesítési beállítások](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Ez az API szerepel hitelesítési beállítások gyűjteménye.|  
|subscriptionKeyParameterNames|Objektum|Nem kötelező tulajdonság az előfizetés kulcsot tartalmazó lekérdezés és/vagy a fejléc paraméterek egyéni nevének megadására használható. Ha ez a tulajdonság van meghatározva, akkor tartalmaznia kell a két alábbi tulajdonságok közül legalább egyet.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>API-összefoglalót  
 A `API summary` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|Karakterlánc|Erőforrás-azonosítója. Az API-t az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{id}` ahol `{id}` API azonosítója. Ez a tulajdonság csak olvasható.|  
|név|Karakterlánc|Az API neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|Karakterlánc|Az API leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
  
##  <a name="Application"></a>Alkalmazás  
 A `application` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|Az alkalmazás egyedi azonosítója.|  
|Cím|Karakterlánc|Az alkalmazás címe.|  
|Leírás|Karakterlánc|Az alkalmazás leírását.|  
|URL-cím|URI|Az alkalmazás URI.|  
|Verzió|Karakterlánc|Fájlverzió-információkat az alkalmazás.|  
|Követelmények|Karakterlánc|Az alkalmazás leírását.|  
|Állapot|Szám|Az alkalmazás aktuális állapota.<br /><br /> -0 - regisztrálva<br /><br /> -1 - elküldése megtörtént<br /><br /> -Közzétett 2-<br /><br /> -3 - elutasítva<br /><br /> – 4 – közzé nem tett|  
|RegistrationDate|Dátum és idő|A dátum és idő, az alkalmazás lett regisztrálva.|  
|CategoryId|Szám|A kategória az alkalmazás (pénzügyi, szórakozás stb.)|  
|DeveloperId|Karakterlánc|A fejlesztő, a kérelmet egyedi azonosítója.|  
|Mellékletek|A gyűjtemény [melléklet](#Attachment) entitásokat.|Az alkalmazás, például a képernyőképek vagy ikonok mellékleteit.|  
|Ikon|[Melléklet](#Attachment)|Az ikonra az alkalmazásához.|  
  
##  <a name="Attachment"></a>Melléklet  
 A `attachment` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Egyedi azonosító|Karakterlánc|A melléklet egyedi azonosítója.|  
|URL-cím|Karakterlánc|Az erőforrás URL-CÍMÉT.|  
|Típus|Karakterlánc|A melléklet típusa.|  
|A ContentType|Karakterlánc|A melléklet adathordozó-típusát.|  
  
##  <a name="Sample"></a>Kódminta  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Cím|Karakterlánc|A művelet neve.|  
|részlet|Karakterlánc|Ez a tulajdonság elavult, és nem használható.|  
|Ecset|Karakterlánc|Mely kód szintaxisa a kódminta megjelenítésekor használt sablon színátmenetekhez. Két érték engedélyezett `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`, és `csharp`.|  
|sablon|Karakterlánc|A kód a minta sablon neve.|  
|Törzs|Karakterlánc|A kód a minta része a kódrészletet helyőrzője.|  
|Módszer|Karakterlánc|A művelet HTTP-metódus.|  
|séma|Karakterlánc|A művelet kérelem használni kívánt protokollt.|  
|Elérési út|Karakterlánc|A művelet elérési útját.|  
|lekérdezés|Karakterlánc|Lekérdezési karakterlánc például a megadott paraméterekkel.|  
|állomás|Karakterlánc|Az API-hoz, amely tartalmazza ezt a műveletet az API Management szolgáltatás átjáró URL-CÍMÉT.|  
|Fejlécek|A gyűjtemény [fejléc](#Header) entitásokat.|Fejlécek ehhez a művelethez.|  
|paraméterek|A gyűjtemény [paraméter](#Parameter) entitásokat.|Ehhez a művelethez megadott paraméterek.|  
  
##  <a name="Comment"></a>Megjegyzés  
 A `API` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Szám|A Megjegyzés azonosítója.|  
|CommentText|Karakterlánc|A szervezet a megjegyzés. HTML is tartalmazhat.|  
|DeveloperCompany|Karakterlánc|A fejlesztői vállalat neve.|  
|PostedOn|Dátum és idő|A dátum és idő, a megjegyzés megtalálható.|  
  
##  <a name="Issue"></a>A probléma  
 A `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|A probléma egyedi azonosítója.|  
|ApiID|Karakterlánc|Az API-t, amelynek a probléma történt az azonosítója.|  
|Cím|Karakterlánc|A probléma címét.|  
|Leírás|Karakterlánc|A probléma leírása.|  
|SubscriptionDeveloperName|Karakterlánc|Utónév a fejlesztő, amely a hibát jelzett.|  
|IssueState|Karakterlánc|A probléma aktuális állapotát. A lehetséges értékek a következők: Proposed, Opened, lezárva.|  
|ReportedOn|Dátum és idő|A dátum és idő, a probléma történt a következő.|  
|Megjegyzések|A gyűjtemény [Megjegyzés](#Comment) entitásokat.|A probléma a megjegyzéseit.|  
|Mellékletek|A gyűjtemény [melléklet](api-management-template-data-model-reference.md#Attachment) entitásokat.|A probléma a mellékleteket.|  
|Szolgáltatások|A gyűjtemény [API](#API) entitásokat.|Az API-k előfizetett a felhasználót, hogy a probléma bejegyezve.|  
  
##  <a name="Filtering"></a>Szűrés  
 A `filtering` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Minta|Karakterlánc|Az aktuális keresési kifejezés; vagy `null` nincs keresési kifejezés esetén.|  
|Helyőrző|Karakterlánc|A szöveget a keresőmezőbe nincs megadott keresési kifejezés esetén.|  
  
##  <a name="Header"></a>Fejléc  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Leírás|Típus|  
|--------------|-----------------|----------|  
|név|Karakterlánc|A paraméter neve.|  
|leírás|Karakterlánc|A paraméter leírását.|  
|érték|Karakterlánc|Fejléc értéke.|  
|A TypeName értéke|Karakterlánc|Állomásfejléc-érték adattípusa.|  
|beállítások|Karakterlánc|Beállítások.|  
|Szükséges|Logikai érték|Hogy a fejléc szükség.|  
|csak olvasható|Logikai érték|Hogy a fejléc csak olvasható.|  
  
##  <a name="HTTPRequest"></a>HTTP-kérelem  
 Ez a szakasz ismerteti a `request` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|leírás|Karakterlánc|A művelet kérelem leírása.|  
|Fejlécek|a tömb [fejléc](#Header) entitásokat.|Kérelem fejlécei.|  
|paraméterek|a tömb [paraméter](#Parameter)|A művelet a kérelemben szereplő paraméterek gyűjteménye.|  
|felelősséget|a tömb [ábrázolása](#Representation)|Művelet kérelem felelősséget gyűjteménye.|  
  
##  <a name="HTTPResponse"></a>HTTP-válasz  
 Ez a szakasz ismerteti a `response` ábrázolását.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|statusCode|Pozitív egész szám|A művelet válasz állapotkódja.|  
|leírás|Karakterlánc|A művelet válasz leírása.|  
|felelősséget|a tömb [ábrázolása](#Representation)|Művelet válasz felelősséget gyűjteménye.|  
  
##  <a name="Operation"></a>Művelet  
 A `operation` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|id|Karakterlánc|Erőforrás-azonosítója. A művelet a jelenlegi API-kezelés szolgáltatás példány belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `apis/{aid}/operations/{id}` ahol `{aid}` API azonosító és `{id}` művelet azonosítója. Ez a tulajdonság csak olvasható.|  
|név|Karakterlánc|A művelet neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|leírás|Karakterlánc|A művelet leírását. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|séma|Karakterlánc|Ismerteti a protokollok a műveletek az API-ban is elindítható. Két érték engedélyezett `http`, `https`, vagy mindkettőt `http` és `https`.|  
|uriTemplate|Karakterlánc|Relatív URL-cím sablon azonosító az ehhez a művelethez a cél erőforráson. Paraméterek is tartalmazhat. Példa:`customers/{cid}/orders/{oid}/?date={date}`|  
|állomás|Karakterlánc|Az API Management átjáró URL-címet a API-t futtató.|  
|HttpMethod|Karakterlánc|Művelet HTTP-metódus.|  
|Kérelem|[HTTP-kérelem](#HTTPRequest)|Kérelem részleteit tartalmazó entitás.|  
|válaszok|a tömb [HTTP-válasz](#HTTPResponse)|Művelet tömbje [HTTP-válasz](#HTTPResponse) entitásokat.|  
  
##  <a name="Menu"></a>Művelet menü  
 A `operation menu` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|apiId|Karakterlánc|A jelenlegi API azonosítója.|  
|CurrentOperationId|Karakterlánc|Az aktuális művelet azonosítója.|  
|Műveletek|Karakterlánc|A menüben írja be.|  
|MenuItems|A gyűjtemény [művelet menüpont](#MenuItem) entitásokat.|A jelenlegi API-műveleteket.|  
  
##  <a name="MenuItem"></a>A művelet menüpont  
 A `operation menu item` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|A művelet azonosítója.|  
|Cím|Karakterlánc|A művelet leírását.|  
|HttpMethod|Karakterlánc|A művelet Http-metódus.|  
  
##  <a name="Paging"></a>Lapozófájl  
 A `paging` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Oldal|Szám|Az aktuális oldalszám.|  
|PageSize együttes értéke|Szám|Az eredmények maximális száma egy oldalon megjelenő.|  
|TotalItemCount|Szám|A megjelenítendő elemek száma.|  
|ShowAll|Logikai érték|Hason összes annak az eredménye egy oldalon kell-e.|  
|PageCount|Szám|Az eredmények oldalak száma.|  
  
##  <a name="Parameter"></a>A paraméter  
 Ez a szakasz ismerteti a `parameter` ábrázolását.  
  
|Tulajdonság|Leírás|Típus|  
|--------------|-----------------|----------|  
|név|Karakterlánc|A paraméter neve.|  
|leírás|Karakterlánc|A paraméter leírását.|  
|érték|Karakterlánc|A paraméter értékét.|  
|beállítások|A karakterlánc tömbje|Lekérdezési paraméter értéke a definiált értékekkel.|  
|Szükséges|Logikai érték|Meghatározza, hogy paraméter szükség-e vagy sem.|  
|típusa|Szám|Hogy a paraméter egy elérési utat (1), vagy egy lekérdezési karakterlánc paraméter (2).|  
|A TypeName értéke|Karakterlánc|Paraméter típusa.|  
  
##  <a name="Product"></a>A termék  
 A `product` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|Erőforrás-azonosítója. A termék az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója. Ez a tulajdonság csak olvasható.|  
|Cím|Karakterlánc|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Leírás|Karakterlánc|A termék leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|Fogalmak|Karakterlánc|A termék használati feltételeket. Próbál előfizetni a termék fejlesztők jelenik meg a rendszer, és a rendszer elfogadja ezeket a feltételeket, azok az előfizetés-műveletnek a végrehajtásához szükséges.|  
|ProductState|Szám|Meghatározza, hogy a termék van közzétéve, vagy nem. Közzétett termékeket a fejlesztői portálján a fejlesztők által felderíthető. A nem közzétett termékek csak a rendszergazdák számára láthatók.<br /><br /> A termék állapota megengedett értékei:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|AllowMultipleSubscriptions|Logikai érték|Meghatározza, hogy egy felhasználó egy időben a terméket több előfizetéssel is rendelkezhet.|  
|MultipleSubscriptionsCount|Szám|Az aktuális felhasználó a termék előfizetések száma.|  
  
##  <a name="Provider"></a>Szolgáltató  
 A `provider` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Tulajdonságok|karakterlánc szótár|A hitelesítésszolgáltató tulajdonságai.|  
|AuthenticationType|Karakterlánc|A szolgáltató típusát. (Az azure Active Directory, bejelentkezés Facebook, Google-fiók, a Microsoft Account, Twitter).|  
|Felirat|Karakterlánc|Megjeleníti a szolgáltató nevét.|  
  
##  <a name="Representation"></a>Megjelenítésre  
 Ez a szakasz ismerteti a `representation`.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|A ContentType|Karakterlánc|Adja meg a regisztrált vagy egyéni content-type a ábrázolását, pl. `application/xml`.|  
|minta|Karakterlánc|Példa a megjelenítésre.|  
  
##  <a name="Subscription"></a>Előfizetés  
 A `subscription` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|Erőforrás-azonosítója. Az előfizetés az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|Termékazonosító|Karakterlánc|A termék erőforrás-azonosítója az előfizetett termék. Az érték formátuma érvényes relatív URL- `products/{pid}` ahol `{pid}` termék azonosítója.|  
|ProductTitle|Karakterlánc|A termék nevét. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|ProductDescription|Karakterlánc|A termék leírása. Nem lehet üres. HTML-címkék formázás is tartalmazhat. Hossza legfeljebb 1000 karakter lehet.|  
|ProductDetailsUrl|Karakterlánc|Relatív URL-címe a termék részletei.|  
|state|Karakterlánc|Az előfizetés állapotát. Lehetséges állapota van:<br /><br /> - `0 - suspended`– az előfizetés le van tiltva, és az előfizető nem hívható meg a termék bármely API-k.<br /><br /> - `1 - active`– az előfizetés nem aktív.<br /><br /> - `2 - expired`– az előfizetés elérte a lejárat és inaktiváltuk.<br /><br /> - `3 - submitted`– az előfizetési kérelem a fejlesztő történt, de még nem jóváhagyták vagy elutasították.<br /><br /> - `4 - rejected`– az előfizetési kérelem rendszergazda meg lett tagadva.<br /><br /> - `5 - cancelled`– az előfizetés a fejlesztői vagy a rendszergazda megszakította.|  
|displayName|Karakterlánc|Az előfizetéshez tartozó megjelenített név.|  
|CreatedDate|Dátum és idő|Az előfizetés létrehozásának dátuma, az ISO 8601 formátum: `2014-06-24T16:25:00Z`.|  
|CanBeCancelled|Logikai érték|Hogy az előfizetés törölni lehessen az aktuális felhasználó.|  
|IsAwaitingApproval|Logikai érték|Az előfizetés e jóváhagyására vár.|  
|A StartDate|Dátum és idő|A kezdő dátum, az előfizetés, az ISO 8601 formátum: `2014-06-24T16:25:00Z`.|  
|ExpirationDate|Dátum és idő|ISO 8601 formátumban, az előfizetés lejárati dátuma: `2014-06-24T16:25:00Z`.|  
|NotificationDate|Dátum és idő|Az előfizetés, ISO 8601 formátumban értesítési dátuma: `2014-06-24T16:25:00Z`.|  
|primaryKey|Karakterlánc|Az előfizetés elsődleges kulcs. Hossza legfeljebb 256 karakter lehet.|  
|másodlagos kulcs|Karakterlánc|A másodlagos előfizetés kulcs. Hossza legfeljebb 256 karakter lehet.|  
|CanBeRenewed|Logikai érték|Hogy az előfizetés lehet megújítani az aktuális felhasználó.|  
|HasExpired|Logikai érték|Hogy az előfizetése lejárt.|  
|IsRejected|Logikai érték|Hogy az előfizetés kérelem visszautasítva.|  
|CancelUrl|Karakterlánc|A relatív URL-címe az előfizetés megszüntetése.|  
|RenewUrl|Karakterlánc|Az előfizetés megújításához relatív URL-címe|  
  
##  <a name="SubscriptionSummary"></a>Előfizetés összefoglaló  
 A `subscription summary` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Azonosító|Karakterlánc|Erőforrás-azonosítója. Az előfizetés az API Management aktuális szolgáltatáspéldányt belül egyedi azonosítására szolgál. Az érték formátuma érvényes relatív URL- `subscriptions/{sid}` ahol `{sid}` egy előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|displayName|Karakterlánc|Az előfizetés megjelenített neve|  
  
##  <a name="UserAccountInfo"></a>Felhasználói fiók adatainak  
 A `user account info` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|Utónév|Karakterlánc|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Vezetéknév|Karakterlánc|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|E-mail cím|Karakterlánc|E-mail cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Jelszó|Karakterlánc|Felhasználói fiók jelszavát.|  
|NameIdentifier|Karakterlánc|A fiókazonosító, ugyanaz, mint a felhasználói e-mailben.|  
|ProviderName|Karakterlánc|Hitelesítési szolgáltató neve.|  
|IsBasicAccount|Logikai érték|Értéke TRUE, ha ez a fiók regisztrálták az e-mailek és a jelszó; hamis, ha a fiók regisztrálták szolgáltató használatával.|  
  
##  <a name="UseSignIn"></a>Felhasználói bejelentkezés  
 A `user sign in` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|E-mail cím|Karakterlánc|E-mail cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Jelszó|Karakterlánc|Felhasználói fiók jelszavát.|  
|ReturnUrl|Karakterlánc|Ha a felhasználó kattintott a lap URL-jelentkezzen be.|  
|RememberMe|Logikai érték|E menteni a jelenlegi felhasználó adatait.|  
|RegistrationEnabled|Logikai érték|Regisztrációs engedélyezve van-e.|  
|DelegationEnabled|Logikai érték|Delegált bejelentkezés engedélyezve van-e.|  
|DelegationUrl|Karakterlánc|A delegált bejelentkezési URL-címe, ha engedélyezve van.|  
|SsoSignUpUrl|Karakterlánc|Az egyszeri bejelentkezési URL-CÍMÉT a felhasználó számára, ha van ilyen.|  
|AuxServiceUrl|Karakterlánc|Ha az aktuális felhasználó a rendszergazda, a szolgáltatáspéldány, a klasszikus Azure-portálon mutató hivatkozás.|  
|Szolgáltatók|A gyűjtemény [szolgáltató](#Provider) entitások|A hitelesítésszolgáltatókat ehhez a felhasználóhoz.|  
|UserRegistrationTerms|Karakterlánc|Azok a feltételek, amelyek a felhasználói bejelentkezés előtt kell fogadnia.|  
|UserRegistrationTermsEnabled|Logikai érték|E kifejezések engedélyezettek.|  
  
##  <a name="UserSignUp"></a>Felhasználói bejelentkezési  
 A `user sign up` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|PasswordConfirm|Logikai érték|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|Jelszó|Karakterlánc|Felhasználói fiók jelszavát.|  
|PasswordVerdictLevel|Szám|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|UserRegistrationTerms|Karakterlánc|Azok a feltételek, amelyek a felhasználói bejelentkezés előtt kell fogadnia.|  
|UserRegistrationTermsOptions|Szám|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|ConsentAccepted|Logikai érték|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|E-mail cím|Karakterlánc|E-mail cím. Nem lehet üres, és a szolgáltatáspéldány belül egyedieknek kell lenniük. Maximális hossza 254 karakter.|  
|Utónév|Karakterlánc|Utónév. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Vezetéknév|Karakterlánc|Utolsó neve. Nem lehet üres. Hossza legfeljebb 100 karakter lehet.|  
|Felhasználói adatok|Karakterlánc|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up) vezérlő.|  
|NameIdentifier|Karakterlánc|Által használt érték a [előfizetési](api-management-page-controls.md#sign-up)előfizetési vezérlő.|  
|ProviderName|Karakterlánc|Hitelesítési szolgáltató neve.|

## <a name="next-steps"></a>Következő lépések
A sablonok használatának kapcsolatos további információkért lásd: [hogyan szabhatja testre a sablonok segítségével az API Management fejlesztői portálján](api-management-developer-portal-templates.md).

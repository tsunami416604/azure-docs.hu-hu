---
title: Az Azure API Management sablon adatmodell-referenciája | Microsoft Docs
description: Az Azure API Management fejlesztői portál sablonjaihoz tartozó adatmodellekben használt általános elemekhez tartozó entitások és típusok ismertetésének ismertetése.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: b0ad7e15-9519-4517-bb73-32e593ed6380
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/05/2017
ms.author: apimpm
ms.openlocfilehash: 323b3effb4c4a63d03ab7ea5251e0d59271d9dcd
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072133"
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablon adatmodell-referenciája
Ez a témakör az Azure API Management fejlesztői portál sablonjainak adatmodelljeiben használt általános elemekhez tartozó entitásokat és típusokat ismerteti.  
  
 További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

A fejlesztői portál nem érhető el a felhasználási szinten.

## <a name="reference"></a>Hivatkozás

-   [API](#API)  
-   [API-összefoglalás](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Mellékletet](#Attachment)  
-   [Kódminta](#Sample)  
-   [Megjegyzés](#Comment)  
-   [Szűrés](#Filtering)  
-   [Fejléc](#Header)  
-   [HTTP-kérelem](#HTTPRequest)  
-   [HTTP-válasz](#HTTPResponse)  
-   [Kérdés](#Issue)  
-   [Művelet](#Operation)  
-   [Művelet menü](#Menu)  
-   [Művelet menüpont](#MenuItem)  
-   [Lapozófájl](#Paging)  
-   [A paraméter](#Parameter)  
-   [Termék](#Product)  
-   [Szolgáltató](#Provider)  
-   [Képviselet](#Representation)  
-   [Előfizetés](#Subscription)  
-   [Előfizetés összefoglalása](#SubscriptionSummary)  
-   [Felhasználói fiók adatai](#UserAccountInfo)  
-   [Felhasználói bejelentkezés](#UseSignIn)  
-   [Felhasználói regisztráció](#UserSignUp)  
  
##  <a name="API"></a> API  
 Az `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|Karakterlánc|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management Service-példányon belül. Az érték egy érvényes relatív URL- `apis/{id}` cím, ahol `{id}` az egy API-azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|Karakterlánc|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|Karakterlánc|Az API leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`serviceUrl`|Karakterlánc|Az API-t megvalósító háttér-szolgáltatás abszolút URL-címe.|  
|`path`|Karakterlánc|A relatív URL-cím egyedi módon azonosítja ezt az API-t és annak összes erőforrás-elérési útját az API Management Service-példányon belül. A rendszer hozzáfűzi a szolgáltatás példányának létrehozása során megadott API-végponti alap URL-címhez, hogy az API számára egy nyilvános URL-címet formáljon.|  
|`protocols`|szám tömbje|Leírja, hogy mely protokollok hívhatók meg az API műveleteiben. Az `1 - http` engedélyezett értékek: `2 - https`és, vagy mindkettő.|  
|`authenticationSettings`|[Engedélyezési kiszolgáló hitelesítési beállításai](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Az API-ban található hitelesítési beállítások gyűjteménye.|  
|`subscriptionKeyParameterNames`|object|Nem kötelező tulajdonság, amely az előfizetési kulcsot tartalmazó lekérdezési és/vagy fejléc-paraméterek egyéni nevének megadására használható. Ha ez a tulajdonság jelen van, a következő két tulajdonság közül legalább egyet tartalmaznia kell:<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>API-összefoglalás  
 Az `API summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|Karakterlánc|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management Service-példányon belül. Az érték egy érvényes relatív URL- `apis/{id}` cím, ahol `{id}` az egy API-azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|Karakterlánc|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|Karakterlánc|Az API leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
  
##  <a name="Application"></a>Alkalmazás  
 Az `application` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|Az alkalmazás egyedi azonosítója.|  
|`Title`|Karakterlánc|Az alkalmazás címe.|  
|`Description`|Karakterlánc|Az alkalmazás leírása.|  
|`Url`|URI|Az alkalmazás URI-ja.|  
|`Version`|Karakterlánc|Az alkalmazás verziószáma.|  
|`Requirements`|Karakterlánc|Az alkalmazás követelményeinek leírása.|  
|`State`|szám|Az alkalmazás jelenlegi állapota.<br /><br /> -0 – regisztrált<br /><br /> -1 – elküldve<br /><br /> -2 – közzétett<br /><br /> -3 – elutasítva<br /><br /> -4 – közzé nem tett|  
|`RegistrationDate`|DateTime|Az alkalmazás regisztrálásának dátuma és időpontja.|  
|`CategoryId`|szám|Az alkalmazás kategóriája (pénzügy, szórakozás stb.)|  
|`DeveloperId`|Karakterlánc|Az alkalmazást elküldő fejlesztő egyedi azonosítója.|  
|`Attachments`|[Melléklet](#Attachment) entitások gyűjteménye.|Az alkalmazáshoz tartozó mellékletek, például képernyőképek vagy ikonok.|  
|`Icon`|[Mellékletet](#Attachment)|Az alkalmazás ikonja.|  
  
##  <a name="Attachment"></a>Mellékletet  
 Az `attachment` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`UniqueId`|Karakterlánc|A melléklet egyedi azonosítója.|  
|`Url`|Karakterlánc|Az erőforrás URL-címe.|  
|`Type`|Karakterlánc|A melléklet típusa.|  
|`ContentType`|Karakterlánc|A melléklet adathordozó-típusa.|  
  
##  <a name="Sample"></a>Mintakód  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`title`|Karakterlánc|A művelet neve.|  
|`snippet`|Karakterlánc|Ez a tulajdonság elavult, és nem használható.|  
|`brush`|Karakterlánc|A kód mintájának megjelenítésekor használandó kód szintaxisa. Az engedélyezett értékek `plain` `php` :,`java`,,,,, és .`csharp` `xml` `objc` `python` `ruby`|  
|`template`|Karakterlánc|A kód sablonjának neve.|  
|`body`|Karakterlánc|A kódrészlet kód minta részének helyőrzője.|  
|`method`|Karakterlánc|A művelet HTTP-metódusa.|  
|`scheme`|Karakterlánc|A műveleti kérelemhez használandó protokoll.|  
|`path`|Karakterlánc|A művelet elérési útja.|  
|`query`|Karakterlánc|Példa lekérdezési karakterláncra a megadott paraméterekkel.|  
|`host`|Karakterlánc|A műveletet tartalmazó API API Management szolgáltatás-átjárójának URL-címe.|  
|`headers`|[Fejléc](#Header) entitások gyűjteménye|A művelethez tartozó fejlécek.|  
|`parameters`|A [paraméter](#Parameter) entitások gyűjteménye.|A művelethez definiált paraméterek.|  
  
##  <a name="Comment"></a>Megjegyzés  
 Az `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|szám|A Megjegyzés azonosítója.|  
|`CommentText`|Karakterlánc|A Megjegyzés törzse. Tartalmazhat HTML-kódot.|  
|`DeveloperCompany`|Karakterlánc|A fejlesztő vállalatának neve.|  
|`PostedOn`|DateTime|A Megjegyzés közzétételének dátuma és időpontja.|  
  
##  <a name="Issue"></a>Kérdés  
 Az `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|A probléma egyedi azonosítója.|  
|`ApiID`|Karakterlánc|Annak az API-azonosítónak a azonosítója, amelyre a problémát jelentették.|  
|`Title`|Karakterlánc|A probléma címe.|  
|`Description`|Karakterlánc|A probléma leírása.|  
|`SubscriptionDeveloperName`|Karakterlánc|A problémát jelentett fejlesztő vezetékneve.|  
|`IssueState`|Karakterlánc|A probléma jelenlegi állapota. A lehetséges értékek javasoltak, megnyitva, lezárva.|  
|`ReportedOn`|DateTime|A probléma jelentésének dátuma és időpontja.|  
|`Comments`|[Megjegyzés](#Comment) entitások gyűjteménye|Megjegyzések a probléma megoldásához.|  
|`Attachments`|[Melléklet](api-management-template-data-model-reference.md#Attachment) entitások gyűjteménye.|A probléma mellékleteit.|  
|`Services`|[API](#API) -entitások gyűjteménye.|A problémát benyújtó felhasználó által előfizetett API-k.|  
  
##  <a name="Filtering"></a>Szűrés  
 Az `filtering` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Pattern`|Karakterlánc|Az aktuális keresési kifejezés; vagy `null` ha nincs keresési kifejezés.|  
|`Placeholder`|Karakterlánc|A keresési mezőben megjelenítendő szöveg, ha nincs megadva keresési kifejezés.|  
  
##  <a name="Header"></a>Fejléc  
 Ez a szakasz a `parameter` megjelenítést ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|Karakterlánc|Paraméter neve|  
|`description`|Karakterlánc|Paraméter leírása|  
|`value`|Karakterlánc|Fejléc értéke.|  
|`typeName`|Karakterlánc|A fejléc értékének adattípusa|  
|`options`|Karakterlánc|Beállítások.|  
|`required`|boolean|Azt jelzi, hogy szükség van-e a fejlécre.|  
|`readOnly`|boolean|Azt jelzi, hogy a fejléc írásvédett-e.|  
  
##  <a name="HTTPRequest"></a>HTTP-kérelem  
 Ez a szakasz a `request` megjelenítést ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`description`|Karakterlánc|Művelet kérésének leírása.|  
|`headers`|[fejléc](#Header) entitások tömbje|Kérések fejlécei|  
|`parameters`|[paraméter](#Parameter) tömbje|A műveleti kérelem paramétereinek gyűjteménye.|  
|`representations`|Ábrázolási [](#Representation) tömb|A műveleti kérések gyűjteménye.|  
  
##  <a name="HTTPResponse"></a>HTTP-válasz  
 Ez a szakasz a `response` megjelenítést ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`statusCode`|pozitív egész szám|Művelet válaszának állapota kód.|  
|`description`|Karakterlánc|Művelet válaszának leírása.|  
|`representations`|Ábrázolási [](#Representation) tömb|A művelet-visszajelzések gyűjteménye.|  
  
##  <a name="Operation"></a>Művelet  
 Az `operation` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|Karakterlánc|Erőforrás-azonosító. Egyedi módon azonosítja a műveletet az aktuális API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL- `apis/{aid}/operations/{id}` cím, ahol `{aid}` az egy API-azonosító, és `{id}` egy műveleti azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|Karakterlánc|A művelet neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|Karakterlánc|A művelet leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`scheme`|Karakterlánc|Leírja, hogy mely protokollok hívhatók meg az API műveleteiben. Az engedélyezett értékek `http`a `https`következők:, `http` , `https`vagy mindkettő és.|  
|`uriTemplate`|Karakterlánc|A művelethez tartozó cél erőforrást azonosító relatív URL-sablon. Tartalmazhat paramétereket. Például: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|Karakterlánc|Az API-t üzemeltető API Management átjáró URL-címe.|  
|`httpMethod`|Karakterlánc|Művelet HTTP-metódusa.|  
|`request`|[HTTP-kérelem](#HTTPRequest)|A kérelem részleteit tartalmazó entitás.|  
|`responses`|HTTP- [Válasz](#HTTPResponse) tömbje|Műveleti http- [Válasz](#HTTPResponse) entitások tömbje.|  
  
##  <a name="Menu"></a>Művelet menü  
 Az `operation menu` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`ApiId`|Karakterlánc|Az aktuális API azonosítója.|  
|`CurrentOperationId`|Karakterlánc|Az aktuális művelet azonosítója.|  
|`Action`|Karakterlánc|A menü típusa|  
|`MenuItems`|[Művelet menüelem](#MenuItem) -entitások gyűjteménye|Az aktuális API műveletei.|  
  
##  <a name="MenuItem"></a>Művelet menüpont  
 Az `operation menu item` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|A művelet azonosítója.|  
|`Title`|Karakterlánc|A művelet leírása.|  
|`HttpMethod`|Karakterlánc|A művelet http-metódusa.|  
  
##  <a name="Paging"></a>Lapozófájl  
 Az `paging` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Page`|szám|Az aktuális oldalszám.|  
|`PageSize`|szám|Az egyetlen oldalon megjelenítendő eredmények maximális száma.|  
|`TotalItemCount`|szám|A megjelenítendő elemek száma.|  
|`ShowAll`|boolean|Azt jelzi, hogy az összes eredmény megjelenik-e egyetlen oldalon.|  
|`PageCount`|szám|Az eredmények oldalszáma.|  
  
##  <a name="Parameter"></a>Paraméter  
 Ez a szakasz a `parameter` megjelenítést ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|Karakterlánc|Paraméter neve|  
|`description`|Karakterlánc|Paraméter leírása|  
|`value`|Karakterlánc|Paraméter értéke.|  
|`options`|sztringek tömbje|A lekérdezési paraméterek értékeihez megadott értékek.|  
|`required`|boolean|Megadja, hogy kötelező-e a paraméter.|  
|`kind`|szám|Azt határozza meg, hogy ez a paraméter egy elérésiút-paraméter (1) vagy egy querystring-paraméter (2)-e.|  
|`typeName`|Karakterlánc|Paraméter típusa|  
  
##  <a name="Product"></a>Termék  
 Az `product` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|Erőforrás-azonosító. Egyedileg azonosítja a terméket a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím, `products/{pid}` ahol `{pid}` a a termék azonosítója. Ez a tulajdonság csak olvasható.|  
|`Title`|Karakterlánc|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Description`|Karakterlánc|A termék leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`Terms`|Karakterlánc|A termék használati feltételei. A termékre előfizetett előfizetéseket a fejlesztőknek be kell mutatniuk, és el kell fogadniuk ezeket a feltételeket ahhoz, hogy el tudja végezni az előfizetés folyamatát.|  
|`ProductState`|szám|Megadja, hogy a termék közzé van-e téve. A közzétett termékek a fejlesztői portál fejlesztői számára is felderíthetők. A nem közzétett termékek csak a rendszergazdák számára láthatók.<br /><br /> A termék állapotának engedélyezett értékei a következők:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|boolean|Azt határozza meg, hogy egy felhasználó egyszerre több előfizetéssel is rendelkezhet a termékhez.|  
|`MultipleSubscriptionsCount`|szám|A termékhez tartozó előfizetések maximális száma a felhasználó számára engedélyezett.|  
  
##  <a name="Provider"></a>Szolgáltató  
 Az `provider` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Properties`|karakterlánc-szótár|A hitelesítési szolgáltató tulajdonságai.|  
|`AuthenticationType`|Karakterlánc|A szolgáltató típusa (Azure Active Directory, Facebook-Bejelentkezés, Google-fiók, Microsoft-fiók, Twitter).|  
|`Caption`|Karakterlánc|A szolgáltató megjelenített neve.|  
  
##  <a name="Representation"></a>Képviselet  
 Ez a szakasz a `representation`következőt ismerteti:.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`contentType`|Karakterlánc|Itt adható meg a jelen ábrázoláshoz tartozó regisztrált vagy egyéni tartalomtípus, például `application/xml`:.|  
|`sample`|Karakterlánc|Példa a képviseletre.|  
  
##  <a name="Subscription"></a>Előfizetés  
 Az `subscription` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím, `subscriptions/{sid}` amelynek `{sid}` a formátuma az előfizetés azonosítója. Ez a tulajdonság csak olvasható.|  
|`ProductId`|Karakterlánc|Az előfizetett termék termék-erőforrás-azonosítója. Az érték egy érvényes relatív URL-cím, `products/{pid}` ahol `{pid}` a a termék azonosítója.|  
|`ProductTitle`|Karakterlánc|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`ProductDescription`|Karakterlánc|A termék leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`ProductDetailsUrl`|Karakterlánc|A termék részleteinek relatív URL-címe.|  
|`state`|Karakterlánc|Az előfizetés állapota. A lehetséges állapotok a következők:<br /><br /> - `0 - suspended`– az előfizetés le van tiltva, és az előfizető nem hívhat meg semmilyen API-t a termékről.<br /><br /> - `1 - active`– az előfizetés aktív.<br /><br /> - `2 - expired`– az előfizetés elérte a lejárati dátumot, és inaktiválva lett.<br /><br /> - `3 - submitted`– az előfizetési kérést a fejlesztő hozta létre, de még nem hagyta jóvá vagy nem utasította el.<br /><br /> - `4 - rejected`– az előfizetési kérelmet a rendszergazda megtagadta.<br /><br /> - `5 - cancelled`– a fejlesztő vagy a rendszergazda megszakította az előfizetést.|  
|`DisplayName`|Karakterlánc|Az előfizetés megjelenített neve.|  
|`CreatedDate`|Dátum és idő|Az előfizetés létrehozásának dátuma, ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|boolean|Azt határozza meg, hogy az előfizetést az aktuális felhasználó megszakíthatja-e.|  
|`IsAwaitingApproval`|boolean|Az előfizetés jóváhagyásra vár-e.|  
|`StartDate`|Dátum és idő|Az előfizetés kezdő dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Dátum és idő|Az előfizetés lejárati dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Dátum és idő|Az előfizetés értesítési dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|Karakterlánc|Az elsődleges előfizetési kulcs. A maximális hossz 256 karakter.|  
|`secondaryKey`|Karakterlánc|A másodlagos előfizetési kulcs. A maximális hossz 256 karakter.|  
|`CanBeRenewed`|boolean|Azt határozza meg, hogy az aktuális felhasználó megújíthatja-e az előfizetést.|  
|`HasExpired`|boolean|Az előfizetés lejárt-e.|  
|`IsRejected`|boolean|Azt jelzi, hogy az előfizetési kérelem megtagadva.|  
|`CancelUrl`|Karakterlánc|Az előfizetés megszakítására szolgáló relatív URL-cím.|  
|`RenewUrl`|Karakterlánc|Az előfizetés megújítására szolgáló relatív URL-cím.|  
  
##  <a name="SubscriptionSummary"></a>Előfizetés összefoglalása  
 Az `subscription summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|Karakterlánc|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím, `subscriptions/{sid}` amelynek `{sid}` a formátuma az előfizetés azonosítója. Ez a tulajdonság csak olvasható.|  
|`DisplayName`|Karakterlánc|Az előfizetés megjelenített neve|  
  
##  <a name="UserAccountInfo"></a>Felhasználói fiók adatai  
 Az `user account info` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`FirstName`|Karakterlánc|Keresztnév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|Karakterlánc|Vezetéknév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Email`|Karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`Password`|Karakterlánc|Felhasználói fiók jelszava.|  
|`NameIdentifier`|Karakterlánc|A felhasználói e-mail-cím megegyezik a fiók azonosítójával.|  
|`ProviderName`|Karakterlánc|Hitelesítési szolgáltató neve.|  
|`IsBasicAccount`|boolean|Igaz, ha a fiók e-mailben és jelszóval van regisztrálva; hamis, ha a fiókot szolgáltató használatával regisztrálták.|  
  
##  <a name="UseSignIn"></a>Felhasználói bejelentkezés  
 Az `user sign in` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Email`|Karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`Password`|Karakterlánc|Felhasználói fiók jelszava.|  
|`ReturnUrl`|Karakterlánc|Annak az oldalnak az URL-címe, ahol a felhasználó a bejelentkezés elemre kattintott.|  
|`RememberMe`|boolean|Azt határozza meg, hogy az aktuális felhasználó adatait szeretné-e menteni.|  
|`RegistrationEnabled`|boolean|Engedélyezve van-e a regisztráció.|  
|`DelegationEnabled`|boolean|Engedélyezve van-e a delegált bejelentkezés.|  
|`DelegationUrl`|Karakterlánc|A delegált bejelentkezési URL-cím, ha engedélyezve van.|  
|`SsoSignUpUrl`|Karakterlánc|A felhasználó egyszeri bejelentkezési URL-címe, ha van ilyen.|  
|`AuxServiceUrl`|Karakterlánc|Ha az aktuális felhasználó rendszergazda, akkor ez a Azure Portal található szolgáltatási példányra mutató hivatkozás.|  
|`Providers`|Szolgáltatói [](#Provider) entitások gyűjteménye|A felhasználó hitelesítési szolgáltatói.|  
|`UserRegistrationTerms`|Karakterlánc|Feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsEnabled`|boolean|A feltételek engedélyezve vannak-e.|  
  
##  <a name="UserSignUp"></a>Felhasználó regisztrálása  
 Az `user sign up` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|boolean|A regisztrálási bejelentkezési [](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`Password`|Karakterlánc|Felhasználói fiók jelszava.|  
|`PasswordVerdictLevel`|szám|A regisztrálási bejelentkezési [](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`UserRegistrationTerms`|Karakterlánc|Feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsOptions`|szám|A regisztrálási bejelentkezési [](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`ConsentAccepted`|boolean|A regisztrálási bejelentkezési [](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`Email`|Karakterlánc|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`FirstName`|Karakterlánc|Keresztnév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|Karakterlánc|Vezetéknév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`UserData`|Karakterlánc|A [regisztrációs](api-management-page-controls.md#sign-up) vezérlő által használt érték.|  
|`NameIdentifier`|Karakterlánc|A regisztrálási bejelentkezési [](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`ProviderName`|Karakterlánc|Hitelesítési szolgáltató neve.|

## <a name="next-steps"></a>További lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).

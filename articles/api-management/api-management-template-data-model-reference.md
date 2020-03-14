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
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 5625ff7e4fc51b9b6b894698719247902a480f44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243938"
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablon adatmodell-referenciája
Ez a témakör az Azure API Management fejlesztői portál sablonjainak adatmodelljeiben használt általános elemekhez tartozó entitásokat és típusokat ismerteti.  
  
 További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referencia

-   [API](#API)  
-   [API-összefoglalás](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Mellékletet](#Attachment)  
-   [Mintakód](#Sample)  
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
-   [Paraméter](#Parameter)  
-   [Termék](#Product)  
-   [Szolgáltató](#Provider)  
-   [Képviselet](#Representation)  
-   [Előfizetés](#Subscription)  
-   [Előfizetés összefoglalása](#SubscriptionSummary)  
-   [Felhasználói fiók adatai](#UserAccountInfo)  
-   [Felhasználói bejelentkezés](#UseSignIn)  
-   [Felhasználói regisztráció](#UserSignUp)  
  
##  <a name="API"></a>API  
 A `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management Service-példányon belül. Az érték egy érvényes relatív URL-cím `apis/{id}` formátumban, ahol a `{id}` API-azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|sztring|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|Az API leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`serviceUrl`|sztring|Az API-t megvalósító háttér-szolgáltatás abszolút URL-címe.|  
|`path`|sztring|A relatív URL-cím egyedi módon azonosítja ezt az API-t és annak összes erőforrás-elérési útját az API Management Service-példányon belül. A rendszer hozzáfűzi a szolgáltatás példányának létrehozása során megadott API-végponti alap URL-címhez, hogy az API számára egy nyilvános URL-címet formáljon.|  
|`protocols`|szám tömbje|Leírja, hogy mely protokollok hívhatók meg az API műveleteiben. Az engedélyezett értékek `1 - http` és `2 - https`, vagy mindkettő.|  
|`authenticationSettings`|[Engedélyezési kiszolgáló hitelesítési beállításai](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Az API-ban található hitelesítési beállítások gyűjteménye.|  
|`subscriptionKeyParameterNames`|objektum|Nem kötelező tulajdonság, amely az előfizetési kulcsot tartalmazó lekérdezési és/vagy fejléc-paraméterek egyéni nevének megadására használható. Ha ez a tulajdonság jelen van, a következő két tulajdonság közül legalább egyet tartalmaznia kell:<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="APISummary"></a>API-összefoglalás  
 A `API summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management Service-példányon belül. Az érték egy érvényes relatív URL-cím `apis/{id}` formátumban, ahol a `{id}` API-azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|sztring|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|Az API leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
  
##  <a name="Application"></a>Alkalmazás  
 A `application` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Az alkalmazás egyedi azonosítója.|  
|`Title`|sztring|Az alkalmazás címe.|  
|`Description`|sztring|Az alkalmazás leírása.|  
|`Url`|URI|Az alkalmazás URI-ja.|  
|`Version`|sztring|Az alkalmazás verziószáma.|  
|`Requirements`|sztring|Az alkalmazás követelményeinek leírása.|  
|`State`|szám|Az alkalmazás jelenlegi állapota.<br /><br /> -0 – regisztrált<br /><br /> -1 – elküldve<br /><br /> -2 – közzétett<br /><br /> -3 – elutasítva<br /><br /> -4 – közzé nem tett|  
|`RegistrationDate`|DateTime|Az alkalmazás regisztrálásának dátuma és időpontja.|  
|`CategoryId`|szám|Az alkalmazás kategóriája (pénzügy, szórakozás stb.)|  
|`DeveloperId`|sztring|Az alkalmazást elküldő fejlesztő egyedi azonosítója.|  
|`Attachments`|[Melléklet](#Attachment) entitások gyűjteménye.|Az alkalmazáshoz tartozó mellékletek, például képernyőképek vagy ikonok.|  
|`Icon`|[Mellékletet](#Attachment)|Az alkalmazás ikonja.|  
  
##  <a name="Attachment"></a>Mellékletet  
 A `attachment` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`UniqueId`|sztring|A melléklet egyedi azonosítója.|  
|`Url`|sztring|Az erőforrás URL-címe.|  
|`Type`|sztring|A melléklet típusa.|  
|`ContentType`|sztring|A melléklet adathordozó-típusa.|  
  
##  <a name="Sample"></a>Mintakód  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`title`|sztring|A művelet neve.|  
|`snippet`|sztring|Ez a tulajdonság elavult, és nem használható.|  
|`brush`|sztring|A kód mintájának megjelenítésekor használandó kód szintaxisa. Az engedélyezett értékek: `plain`, `php`, `java`, `xml`, `objc`, `python`, `ruby`és `csharp`.|  
|`template`|sztring|A kód sablonjának neve.|  
|`body`|sztring|A kódrészlet kód minta részének helyőrzője.|  
|`method`|sztring|A művelet HTTP-metódusa.|  
|`scheme`|sztring|A műveleti kérelemhez használandó protokoll.|  
|`path`|sztring|A művelet elérési útja.|  
|`query`|sztring|Példa lekérdezési karakterláncra a megadott paraméterekkel.|  
|`host`|sztring|A műveletet tartalmazó API API Management szolgáltatás-átjárójának URL-címe.|  
|`headers`|[Fejléc](#Header) entitások gyűjteménye|A művelethez tartozó fejlécek.|  
|`parameters`|A [paraméter](#Parameter) entitások gyűjteménye.|A művelethez definiált paraméterek.|  
  
##  <a name="Comment"></a>Megjegyzés  
 A `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|szám|A Megjegyzés azonosítója.|  
|`CommentText`|sztring|A Megjegyzés törzse. Tartalmazhat HTML-kódot.|  
|`DeveloperCompany`|sztring|A fejlesztő vállalatának neve.|  
|`PostedOn`|DateTime|A Megjegyzés közzétételének dátuma és időpontja.|  
  
##  <a name="Issue"></a>Kérdés  
 A `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|A probléma egyedi azonosítója.|  
|`ApiID`|sztring|Annak az API-azonosítónak a azonosítója, amelyre a problémát jelentették.|  
|`Title`|sztring|A probléma címe.|  
|`Description`|sztring|A probléma leírása.|  
|`SubscriptionDeveloperName`|sztring|A problémát jelentett fejlesztő vezetékneve.|  
|`IssueState`|sztring|A probléma jelenlegi állapota. A lehetséges értékek javasoltak, megnyitva, lezárva.|  
|`ReportedOn`|DateTime|A probléma jelentésének dátuma és időpontja.|  
|`Comments`|[Megjegyzés](#Comment) entitások gyűjteménye|Megjegyzések a probléma megoldásához.|  
|`Attachments`|[Melléklet](api-management-template-data-model-reference.md#Attachment) entitások gyűjteménye.|A probléma mellékleteit.|  
|`Services`|[API](#API) -entitások gyűjteménye.|A problémát benyújtó felhasználó által előfizetett API-k.|  
  
##  <a name="Filtering"></a>Szűrés  
 A `filtering` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Pattern`|sztring|Az aktuális keresési kifejezés; vagy `null`, ha nincs keresési kifejezés.|  
|`Placeholder`|sztring|A keresési mezőben megjelenítendő szöveg, ha nincs megadva keresési kifejezés.|  
  
##  <a name="Header"></a>Fejléc  
 Ez a szakasz a `parameter` ábrázolást ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|sztring|Paraméter neve|  
|`description`|sztring|Paraméter leírása|  
|`value`|sztring|Fejléc értéke.|  
|`typeName`|sztring|A fejléc értékének adattípusa|  
|`options`|sztring|Beállítások.|  
|`required`|logikai|Azt jelzi, hogy szükség van-e a fejlécre.|  
|`readOnly`|logikai|Azt jelzi, hogy a fejléc írásvédett-e.|  
  
##  <a name="HTTPRequest"></a>HTTP-kérelem  
 Ez a szakasz a `request` ábrázolást ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`description`|sztring|Művelet kérésének leírása.|  
|`headers`|[fejléc](#Header) entitások tömbje|Kérések fejlécei|  
|`parameters`|[paraméter](#Parameter) tömbje|A műveleti kérelem paramétereinek gyűjteménye.|  
|`representations`|[ábrázolási](#Representation) tömb|A műveleti kérések gyűjteménye.|  
  
##  <a name="HTTPResponse"></a>HTTP-válasz  
 Ez a szakasz a `response` ábrázolást ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`statusCode`|Pozitív egész szám|Művelet válaszának állapota kód.|  
|`description`|sztring|Művelet válaszának leírása.|  
|`representations`|[ábrázolási](#Representation) tömb|A művelet-visszajelzések gyűjteménye.|  
  
##  <a name="Operation"></a>Művelet  
 A `operation` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedi módon azonosítja a műveletet az aktuális API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím `apis/{aid}/operations/{id}` formátumban, ahol a `{aid}` API-azonosító, a `{id}` pedig egy műveleti azonosító. Ez a tulajdonság csak olvasható.|  
|`name`|sztring|A művelet neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|A művelet leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`scheme`|sztring|Leírja, hogy mely protokollok hívhatók meg az API műveleteiben. Az engedélyezett értékek: `http`, `https`, vagy mindkettő `http` és `https`.|  
|`uriTemplate`|sztring|A művelethez tartozó cél erőforrást azonosító relatív URL-sablon. Tartalmazhat paramétereket. Például: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|sztring|Az API-t üzemeltető API Management átjáró URL-címe.|  
|`httpMethod`|sztring|Művelet HTTP-metódusa.|  
|`request`|[HTTP-kérelem](#HTTPRequest)|A kérelem részleteit tartalmazó entitás.|  
|`responses`|HTTP- [Válasz](#HTTPResponse) tömbje|Műveleti http- [Válasz](#HTTPResponse) entitások tömbje.|  
  
##  <a name="Menu"></a>Művelet menü  
 A `operation menu` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`ApiId`|sztring|Az aktuális API azonosítója.|  
|`CurrentOperationId`|sztring|Az aktuális művelet azonosítója.|  
|`Action`|sztring|A menü típusa|  
|`MenuItems`|[Művelet menüelem](#MenuItem) -entitások gyűjteménye|Az aktuális API műveletei.|  
  
##  <a name="MenuItem"></a>Művelet menüpont  
 A `operation menu item` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|A művelet azonosítója.|  
|`Title`|sztring|A művelet leírása.|  
|`HttpMethod`|sztring|A művelet http-metódusa.|  
  
##  <a name="Paging"></a>Lapozófájl  
 A `paging` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Page`|szám|Az aktuális oldalszám.|  
|`PageSize`|szám|Az egyetlen oldalon megjelenítendő eredmények maximális száma.|  
|`TotalItemCount`|szám|A megjelenítendő elemek száma.|  
|`ShowAll`|logikai|Azt jelzi, hogy az összes eredmény megjelenik-e egyetlen oldalon.|  
|`PageCount`|szám|Az eredmények oldalszáma.|  
  
##  <a name="Parameter"></a>Paraméter  
 Ez a szakasz a `parameter` ábrázolást ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|sztring|Paraméter neve|  
|`description`|sztring|Paraméter leírása|  
|`value`|sztring|Paraméter értéke.|  
|`options`|sztringek tömbje|A lekérdezési paraméterek értékeihez megadott értékek.|  
|`required`|logikai|Megadja, hogy kötelező-e a paraméter.|  
|`kind`|szám|Azt határozza meg, hogy ez a paraméter egy elérésiút-paraméter (1) vagy egy querystring-paraméter (2)-e.|  
|`typeName`|sztring|Paraméter típusa|  
  
##  <a name="Product"></a>Termék  
 A `product` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja a terméket a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím `products/{pid}` formátumban, ahol `{pid}` a termék azonosítója. Ez a tulajdonság csak olvasható.|  
|`Title`|sztring|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Description`|sztring|A termék leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`Terms`|sztring|A termék használati feltételei. A termékre előfizetett előfizetéseket a fejlesztőknek be kell mutatniuk, és el kell fogadniuk ezeket a feltételeket ahhoz, hogy el tudja végezni az előfizetés folyamatát.|  
|`ProductState`|szám|Megadja, hogy a termék közzé van-e téve. A közzétett termékek a fejlesztői portál fejlesztői számára is felderíthetők. A nem közzétett termékek csak a rendszergazdák számára láthatók.<br /><br /> A termék állapotának engedélyezett értékei a következők:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|logikai|Azt határozza meg, hogy egy felhasználó egyszerre több előfizetéssel is rendelkezhet a termékhez.|  
|`MultipleSubscriptionsCount`|szám|A termékhez tartozó előfizetések maximális száma a felhasználó számára engedélyezett.|  
  
##  <a name="Provider"></a>Szolgáltató  
 A `provider` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Properties`|karakterlánc-szótár|A hitelesítési szolgáltató tulajdonságai.|  
|`AuthenticationType`|sztring|A szolgáltató típusa (Azure Active Directory, Facebook-Bejelentkezés, Google-fiók, Microsoft-fiók, Twitter).|  
|`Caption`|sztring|A szolgáltató megjelenített neve.|  
  
##  <a name="Representation"></a>Képviselet  
 Ez a szakasz egy `representation`ismertet.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`contentType`|sztring|Itt adható meg a jelen ábrázoláshoz tartozó regisztrált vagy egyéni tartalomtípus, például `application/xml`.|  
|`sample`|sztring|Példa a képviseletre.|  
  
##  <a name="Subscription"></a>Előfizetés  
 A `subscription` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím `subscriptions/{sid}` formátumban, amelyben `{sid}` előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|`ProductId`|sztring|Az előfizetett termék termék-erőforrás-azonosítója. Az érték egy érvényes relatív URL-cím `products/{pid}` formátumban, ahol `{pid}` a termék azonosítója.|  
|`ProductTitle`|sztring|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`ProductDescription`|sztring|A termék leírása. Nem lehet üres. Tartalmazhat HTML-formázási címkéket. A maximális hossz 1000 karakter.|  
|`ProductDetailsUrl`|sztring|A termék részleteinek relatív URL-címe.|  
|`state`|sztring|Az előfizetés állapota. A lehetséges állapotok a következők:<br /><br /> - `0 - suspended` – az előfizetés le van tiltva, és az előfizető nem hívhat meg semmilyen API-t a termékről.<br /><br /> - `1 - active` – az előfizetés aktív.<br /><br /> - `2 - expired` – az előfizetés elérte a lejárati dátumot, és inaktiválva lett.<br /><br /> - `3 - submitted` – az előfizetési kérést a fejlesztő hozta létre, de még nem hagyta jóvá vagy nem utasította el.<br /><br /> - `4 - rejected` – az előfizetési kérelmet a rendszergazda megtagadta.<br /><br /> - `5 - cancelled` – az előfizetést a fejlesztő vagy a rendszergazda megszakította.|  
|`DisplayName`|sztring|Az előfizetés megjelenített neve.|  
|`CreatedDate`|Dátum és idő|Az előfizetés létrehozásának dátuma, ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|logikai|Azt határozza meg, hogy az előfizetést az aktuális felhasználó megszakíthatja-e.|  
|`IsAwaitingApproval`|logikai|Az előfizetés jóváhagyásra vár-e.|  
|`StartDate`|Dátum és idő|Az előfizetés kezdő dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`ExpirationDate`|Dátum és idő|Az előfizetés lejárati dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Dátum és idő|Az előfizetés értesítési dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|sztring|Az elsődleges előfizetési kulcs. A maximális hossz 256 karakter.|  
|`secondaryKey`|sztring|A másodlagos előfizetési kulcs. A maximális hossz 256 karakter.|  
|`CanBeRenewed`|logikai|Azt határozza meg, hogy az aktuális felhasználó megújíthatja-e az előfizetést.|  
|`HasExpired`|logikai|Az előfizetés lejárt-e.|  
|`IsRejected`|logikai|Azt jelzi, hogy az előfizetési kérelem megtagadva.|  
|`CancelUrl`|sztring|Az előfizetés megszakítására szolgáló relatív URL-cím.|  
|`RenewUrl`|sztring|Az előfizetés megújítására szolgáló relatív URL-cím.|  
  
##  <a name="SubscriptionSummary"></a>Előfizetés összefoglalása  
 A `subscription summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést a jelenlegi API Management szolgáltatási példányon belül. Az érték egy érvényes relatív URL-cím `subscriptions/{sid}` formátumban, amelyben `{sid}` előfizetés-azonosító. Ez a tulajdonság csak olvasható.|  
|`DisplayName`|sztring|Az előfizetés megjelenített neve|  
  
##  <a name="UserAccountInfo"></a>Felhasználói fiók adatai  
 A `user account info` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`FirstName`|sztring|Keresztnév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|sztring|Vezetéknév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`NameIdentifier`|sztring|A felhasználói e-mail-cím megegyezik a fiók azonosítójával.|  
|`ProviderName`|sztring|Hitelesítési szolgáltató neve.|  
|`IsBasicAccount`|logikai|Igaz, ha a fiók e-mailben és jelszóval van regisztrálva; hamis, ha a fiókot szolgáltató használatával regisztrálták.|  
  
##  <a name="UseSignIn"></a>Felhasználói bejelentkezés  
 A `user sign in` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`ReturnUrl`|sztring|Annak az oldalnak az URL-címe, ahol a felhasználó a bejelentkezés elemre kattintott.|  
|`RememberMe`|logikai|Azt határozza meg, hogy az aktuális felhasználó adatait szeretné-e menteni.|  
|`RegistrationEnabled`|logikai|Engedélyezve van-e a regisztráció.|  
|`DelegationEnabled`|logikai|Engedélyezve van-e a delegált bejelentkezés.|  
|`DelegationUrl`|sztring|A delegált bejelentkezési URL-cím, ha engedélyezve van.|  
|`SsoSignUpUrl`|sztring|A felhasználó egyszeri bejelentkezési URL-címe, ha van ilyen.|  
|`AuxServiceUrl`|sztring|Ha az aktuális felhasználó rendszergazda, akkor ez a Azure Portal található szolgáltatási példányra mutató hivatkozás.|  
|`Providers`|[Szolgáltatói](#Provider) entitások gyűjteménye|A felhasználó hitelesítési szolgáltatói.|  
|`UserRegistrationTerms`|sztring|Feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsEnabled`|logikai|A feltételek engedélyezve vannak-e.|  
  
##  <a name="UserSignUp"></a>Felhasználó regisztrálása  
 A `user sign up` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|logikai|A [regisztrálási](api-management-page-controls.md#sign-up)bejelentkezési vezérlő által használt érték.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`PasswordVerdictLevel`|szám|A [regisztrálási](api-management-page-controls.md#sign-up)bejelentkezési vezérlő által használt érték.|  
|`UserRegistrationTerms`|sztring|Feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsOptions`|szám|A [regisztrálási](api-management-page-controls.md#sign-up)bejelentkezési vezérlő által használt érték.|  
|`ConsentAccepted`|logikai|A [regisztrálási](api-management-page-controls.md#sign-up)bejelentkezési vezérlő által használt érték.|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és a szolgáltatási példányon belül egyedinek kell lennie. A maximális hossz 254 karakter.|  
|`FirstName`|sztring|Keresztnév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|sztring|Vezetéknév. Nem lehet üres. A maximális hossz 100 karakter.|  
|`UserData`|sztring|A [regisztrációs](api-management-page-controls.md#sign-up) vezérlő által használt érték.|  
|`NameIdentifier`|sztring|A [regisztrálási](api-management-page-controls.md#sign-up)bejelentkezési vezérlő által használt érték.|  
|`ProviderName`|sztring|Hitelesítési szolgáltató neve.|

## <a name="next-steps"></a>Következő lépések
További információ a sablonok használatáról: [API Management fejlesztői portál testreszabása sablonok használatával](api-management-developer-portal-templates.md).

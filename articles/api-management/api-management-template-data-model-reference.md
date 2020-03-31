---
title: Azure API Management sablonadatmodell-hivatkozás | Microsoft dokumentumok
description: Ismerje meg az entitás és a típus ábrázolások a használt általános elemek a fejlesztői portál sablonok az Azure API Management az adatmodellek.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243938"
---
# <a name="azure-api-management-template-data-model-reference"></a>Az Azure API Management sablonadatmodell-referencia
Ez a témakör ismerteti az entitás és a típus ábrázolások az azure API-kezelés fejlesztői portálsablonjainak adatmodelljeiben használt általános elemek.  
  
 A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)témakörben talál.  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="reference"></a>Referencia

-   [Api](#API)  
-   [API összefoglaló](#APISummary)  
-   [Alkalmazás](#Application)  
-   [Melléklet](#Attachment)  
-   [Kódminta](#Sample)  
-   [Megjegyzés](#Comment)  
-   [Szűrés](#Filtering)  
-   [Fejléc](#Header)  
-   [HTTP-kérés](#HTTPRequest)  
-   [HTTP-válasz](#HTTPResponse)  
-   [Probléma](#Issue)  
-   [Művelet](#Operation)  
-   [Művelet menü](#Menu)  
-   [Művelet menüpont](#MenuItem)  
-   [Lapozás](#Paging)  
-   [Paraméter](#Parameter)  
-   [Termék](#Product)  
-   [Szolgáltató](#Provider)  
-   [Képviselet](#Representation)  
-   [Előfizetés](#Subscription)  
-   [Előfizetés összegzése](#SubscriptionSummary)  
-   [Felhasználói fiók adatai](#UserAccountInfo)  
-   [Felhasználói bejelentkezés](#UseSignIn)  
-   [Felhasználói regisztráció](#UserSignUp)  
  
##  <a name="api"></a><a name="API"></a>Api  
 Az `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a `apis/{id}` formátumban, ahol `{id}` egy API-azonosító. Ez a tulajdonság írásvédett.|  
|`name`|sztring|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|Az API leírása. Nem lehet üres. Tartalmazhat HTML formázási címkéket. A maximális hossz 1000 karakter.|  
|`serviceUrl`|sztring|Az API-t megvalósító háttérszolgáltatás abszolút URL-címe.|  
|`path`|sztring|Relatív URL-t egyedileg azonosítja ezt az API-t és annak összes erőforrás-útvonalak az API Management szolgáltatáspéldányon belül. Hozzáfűzi az API-végpont alap URL-cím a szolgáltatáspéldány létrehozása során megadott nyilvános URL-címet ehhez az API-hoz.|  
|`protocols`|tömb szám|Leírja, hogy az API-ban szereplő műveletek mely protokollok alapján hívhatók meg. Az engedélyezett `1 - http` `2 - https`értékek és a , vagy mindkettő.|  
|`authenticationSettings`|[Engedélyezési kiszolgáló hitelesítési beállításai](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-contract-reference#AuthenticationSettings)|Az API-ban található hitelesítési beállítások gyűjteménye.|  
|`subscriptionKeyParameterNames`|objektum|Választható tulajdonság, amely az előfizetési kulcsot tartalmazó lekérdezési és/vagy fejlécparaméterek egyéni nevének megadására használható. Ha ez a tulajdonság jelen van, a következő tulajdonságok közül legalább egyet tartalmaznia kell.<br /><br /> `{   "subscriptionKeyParameterNames":   {     "query": “customQueryParameterName",     "header": “customHeaderParameterName"   } }`|  
  
##  <a name="api-summary"></a><a name="APISummary"></a>API összegzése  
 Az `API summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az API-t az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a `apis/{id}` formátumban, ahol `{id}` egy API-azonosító. Ez a tulajdonság írásvédett.|  
|`name`|sztring|Az API neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|Az API leírása. Nem lehet üres. Tartalmazhat HTML formázási címkéket. A maximális hossz 1000 karakter.|  
  
##  <a name="application"></a><a name="Application"></a>Alkalmazás  
 Az `application` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Az alkalmazás egyedi azonosítója.|  
|`Title`|sztring|A kérelem címe.|  
|`Description`|sztring|A kérelem leírása.|  
|`Url`|URI|Az alkalmazás URI-ja.|  
|`Version`|sztring|Az alkalmazás verzióadatai.|  
|`Requirements`|sztring|A kérelemre vonatkozó követelmények leírása.|  
|`State`|szám|Az alkalmazás aktuális állapota.<br /><br /> - 0 - Regisztrált<br /><br /> - 1 - Benyújtott<br /><br /> - 2 - Megjelent<br /><br /> - 3 - Elutasítva<br /><br /> - 4 - Kiadatlan|  
|`RegistrationDate`|DateTime|A kérelem regisztrálásának dátuma és időpontja.|  
|`CategoryId`|szám|Az alkalmazás kategóriája (Pénzügy, szórakoztatás stb.)|  
|`DeveloperId`|sztring|A jelentkezést beküldő fejlesztő egyedi azonosítója.|  
|`Attachments`|[Mellékletentitások](#Attachment) gyűjteménye.|Az alkalmazás hoz való mellékletek, például képernyőképek vagy ikonok.|  
|`Icon`|[Melléklet](#Attachment)|Az ikon az alkalmazáshoz.|  
  
##  <a name="attachment"></a><a name="Attachment"></a>Mellékletet  
 Az `attachment` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`UniqueId`|sztring|A melléklet egyedi azonosítója.|  
|`Url`|sztring|Az erőforrás URL-címe.|  
|`Type`|sztring|A melléklet típusa.|  
|`ContentType`|sztring|A melléklet adathordozó-típusa.|  
  
##  <a name="code-sample"></a><a name="Sample"></a>Kódminta  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`title`|sztring|A művelet neve.|  
|`snippet`|sztring|Ez a tulajdonság elavult, és nem használható.|  
|`brush`|sztring|Melyik kódszintaxisszín-sablont kell használni a kódminta megjelenítésekor. Az engedélyezett `plain` `php`értékek `java` `xml`a `objc` `python`következők: , , , , , `ruby`, és `csharp`.|  
|`template`|sztring|A kódmintasablon neve.|  
|`body`|sztring|A kódrészlet kódmintarészének helyőrzője.|  
|`method`|sztring|A művelet HTTP-metódusa.|  
|`scheme`|sztring|A műveleti kérelemhez használandó protokoll.|  
|`path`|sztring|A művelet elérési útja.|  
|`query`|sztring|Lekérdezési karakterlánc példa definiált paraméterekkel.|  
|`host`|sztring|A műveletet tartalmazó API-szolgáltatás átjárójának URL-címe.|  
|`headers`|[Fejlécentitások](#Header) gyűjteménye.|A művelet fejlécei.|  
|`parameters`|[Paraméterentitások](#Parameter) gyűjteménye.|A művelethez definiált paraméterek.|  
  
##  <a name="comment"></a><a name="Comment"></a>Megjegyzést  
 Az `API` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|szám|A megjegyzés azonosítója.|  
|`CommentText`|sztring|A testület a megjegyzés. Tartalmazhat HTML-t.|  
|`DeveloperCompany`|sztring|A fejlesztő cégneve.|  
|`PostedOn`|DateTime|A megjegyzés közzétételének dátuma és időpontja.|  
  
##  <a name="issue"></a><a name="Issue"></a>Kérdés  
 Az `issue` entitás a következő tulajdonságokkal rendelkezik.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|A probléma egyedi azonosítója.|  
|`ApiID`|sztring|Annak az API-nak az azonosítója, amelyről a problémát jelentették.|  
|`Title`|sztring|A probléma címe.|  
|`Description`|sztring|A probléma leírása.|  
|`SubscriptionDeveloperName`|sztring|A problémát okozó fejlesztő utóneve.|  
|`IssueState`|sztring|A probléma aktuális állapota. A lehetséges értékek a következők: Javasolt, Megnyitott, Lezárt.|  
|`ReportedOn`|DateTime|A probléma jelentésének dátuma és időpontja.|  
|`Comments`|[Megjegyzés](#Comment) entitások gyűjteménye.|Megjegyzések ezzel a kérdéssel kapcsolatban.|  
|`Attachments`|[Mellékletentitások](api-management-template-data-model-reference.md#Attachment) gyűjteménye.|A probléma esetleges mellékletei.|  
|`Services`|[API-entitások](#API) gyűjteménye.|A problémát benyújtó felhasználó által előfizetett API-k.|  
  
##  <a name="filtering"></a><a name="Filtering"></a>Szűrés  
 Az `filtering` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Pattern`|sztring|Az aktuális keresési kifejezés; vagy `null` ha nincs keresési kifejezés.|  
|`Placeholder`|sztring|A keresőmezőben megjelenítendő szöveg, ha nincs megadva keresési kifejezés.|  
  
##  <a name="header"></a><a name="Header"></a>Fejléc  
 Ez a szakasz `parameter` a reprezentációt ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|sztring|A paraméter neve.|  
|`description`|sztring|A paraméter leírása.|  
|`value`|sztring|Fejléc értéke.|  
|`typeName`|sztring|A fejlécérték adattípusa.|  
|`options`|sztring|Lehetőségek.|  
|`required`|logikai|Azt jelzi, hogy szükség van-e a fejlécre.|  
|`readOnly`|logikai|Azt jelzi, hogy a fejléc írásvédett-e.|  
  
##  <a name="http-request"></a><a name="HTTPRequest"></a>HTTP-kérelem  
 Ez a szakasz `request` a reprezentációt ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`description`|sztring|Műveleti kérelem leírása.|  
|`headers`|[fejlécentitások](#Header) tömbje.|Fejlécek kérése.|  
|`parameters`|[paraméter](#Parameter) tömbje|A műveletkérelem paramétereinek gyűjtése.|  
|`representations`|[ábrázolástömbje](#Representation)|A műveleti kérelmek ábrázolásainak gyűjteménye.|  
  
##  <a name="http-response"></a><a name="HTTPResponse"></a>HTTP-válasz  
 Ez a szakasz `response` a reprezentációt ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`statusCode`|pozitív egész szám|Műveletválasz állapotkódja.|  
|`description`|sztring|A művelet válaszának leírása.|  
|`representations`|[ábrázolástömbje](#Representation)|A műveletválasz-ábrázolások gyűjteménye.|  
  
##  <a name="operation"></a><a name="Operation"></a>Művelet  
 Az `operation` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja a műveletet az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a `apis/{aid}/operations/{id}` formátumban, hogy hol `{aid}` van egy API-azonosító, és `{id}` egy műveletazonosító. Ez a tulajdonság írásvédett.|  
|`name`|sztring|A művelet neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`description`|sztring|A művelet leírása. Nem lehet üres. Tartalmazhat HTML formázási címkéket. A maximális hossz 1000 karakter.|  
|`scheme`|sztring|Leírja, hogy az API-ban szereplő műveletek mely protokollok alapján hívhatók meg. Az engedélyezett `http` `https`értékek a `http` `https`, vagy mindkettő és a.|  
|`uriTemplate`|sztring|A művelet célerőforrását azonosító relatív URL-sablon. Paraméterek is tartalmazhatnak. Például: `customers/{cid}/orders/{oid}/?date={date}`|  
|`host`|sztring|Az API-t tartalmazó API-felügyeleti átjáró URL-címe.|  
|`httpMethod`|sztring|Http-művelet.|  
|`request`|[HTTP-kérés](#HTTPRequest)|A kérelem részleteit tartalmazó entitás.|  
|`responses`|[HTTP-válasz tömbje](#HTTPResponse)|[A http-válasz](#HTTPResponse) entitások művelettömbje.|  
  
##  <a name="operation-menu"></a><a name="Menu"></a>Művelet menü  
 Az `operation menu` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`ApiId`|sztring|Az aktuális API azonosítója.|  
|`CurrentOperationId`|sztring|Az aktuális művelet azonosítója.|  
|`Action`|sztring|A menü típusa.|  
|`MenuItems`|A [Művelet menüelem-entitások](#MenuItem) gyűjteménye.|Az aktuális API műveletei.|  
  
##  <a name="operation-menu-item"></a><a name="MenuItem"></a>Művelet menüpont  
 Az `operation menu item` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|A művelet azonosítója.|  
|`Title`|sztring|A művelet leírása.|  
|`HttpMethod`|sztring|A művelet http-módszere.|  
  
##  <a name="paging"></a><a name="Paging"></a>Személyhívó  
 Az `paging` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Page`|szám|Az aktuális oldalszám.|  
|`PageSize`|szám|Az egy oldalon megjelenítendő maximális eredmények.|  
|`TotalItemCount`|szám|A megjelenítéshez szükséges elemek száma.|  
|`ShowAll`|logikai|Attól, hogy az összes eredményt egyetlen oldalra tolja.|  
|`PageCount`|szám|Az eredmények oldalainak száma.|  
  
##  <a name="parameter"></a><a name="Parameter"></a>Paraméter  
 Ez a szakasz `parameter` a reprezentációt ismerteti.  
  
|Tulajdonság|Típus|Leírás|  
|--------------|-----------------|----------|  
|`name`|sztring|A paraméter neve.|  
|`description`|sztring|A paraméter leírása.|  
|`value`|sztring|Paraméter értéke.|  
|`options`|sztringek tömbje|Lekérdezési paraméterértékekhez definiált értékek.|  
|`required`|logikai|Itt adható meg, hogy a paraméter kötelező-e vagy sem.|  
|`kind`|szám|Azt jelzi, hogy ez a paraméter elérési út (1) vagy querystring paraméter (2).|  
|`typeName`|sztring|Paraméter típusa.|  
  
##  <a name="product"></a><a name="Product"></a>Termék  
 Az `product` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja a terméket az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a formátumban, `products/{pid}` hogy hol `{pid}` van a termékazonosító. Ez a tulajdonság írásvédett.|  
|`Title`|sztring|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Description`|sztring|A termék leírása. Nem lehet üres. Tartalmazhat HTML formázási címkéket. A maximális hossz 1000 karakter.|  
|`Terms`|sztring|A termék használati feltételei. A termékre feliratkozni próbáló fejlesztők megjelennek, és el kell fogadniuk ezeket a feltételeket, mielőtt befejeznék az előfizetési folyamatot.|  
|`ProductState`|szám|Itt adható meg, hogy a termék közzé legyen-e téve vagy sem. A közzétett termékeket a fejlesztők a fejlesztői portálon fedezhetik fel. A nem közzétett termékek csak a rendszergazdák számára láthatók.<br /><br /> A termékállapot megengedett értékei a következők:<br /><br /> - `0 - Not Published`<br /><br /> - `1 - Published`<br /><br /> - `2 - Deleted`|  
|`AllowMultipleSubscriptions`|logikai|Itt adható meg, hogy egy felhasználó egyszerre több előfizetéssel is rendelkezhet-e a termékre.|  
|`MultipleSubscriptionsCount`|szám|A felhasználó által egy időben engedélyezett a termékre szóló előfizetések maximális száma.|  
  
##  <a name="provider"></a><a name="Provider"></a>Szolgáltató  
 Az `provider` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Properties`|karakterlánc szótár|A hitelesítésszolgáltató tulajdonságai.|  
|`AuthenticationType`|sztring|A szolgáltató típusa. (Azure Active Directory, Facebook bejelentkezés, Google-fiók, Microsoft-fiók, Twitter).|  
|`Caption`|sztring|A szolgáltató megjelenítendő neve.|  
  
##  <a name="representation"></a><a name="Representation"></a>Képviselet  
 Ez a szakasz `representation`a .  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`contentType`|sztring|A ábrázolás regisztrált vagy egyéni tartalomtípusát adja `application/xml`meg, például .|  
|`sample`|sztring|Egy példa a képviseletre.|  
  
##  <a name="subscription"></a><a name="Subscription"></a>Előfizetés  
 Az `subscription` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a `subscriptions/{sid}` formátumban, ahol `{sid}` az előfizetés-azonosító. Ez a tulajdonság írásvédett.|  
|`ProductId`|sztring|A előfizetett termék termékerőforrás-azonosítója. Az érték egy érvényes relatív URL-cím abban a formátumban, `products/{pid}` hogy hol `{pid}` van a termékazonosító.|  
|`ProductTitle`|sztring|A termék neve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`ProductDescription`|sztring|A termék leírása. Nem lehet üres. Tartalmazhat HTML formázási címkéket. A maximális hossz 1000 karakter.|  
|`ProductDetailsUrl`|sztring|A termék részleteinek relatív URL-címe.|  
|`state`|sztring|Az előfizetés állapota. A lehetséges állapotok a következők:<br /><br /> - `0 - suspended`– az előfizetés le van tiltva, és az előfizető nem hívhatja meg a termék API-jait.<br /><br /> - `1 - active`– az előfizetés aktív.<br /><br /> - `2 - expired`– az előfizetés elérte a lejárati dátumot, és inaktiválták.<br /><br /> - `3 - submitted`– az előfizetési kérelmet a fejlesztő nyújtotta be, de még nem hagyták jóvá vagy utasították el.<br /><br /> - `4 - rejected`– az előfizetési kérelmet a rendszergazda megtagadta.<br /><br /> - `5 - cancelled`– az előfizetést a fejlesztő vagy a rendszergazda lemondta.|  
|`DisplayName`|sztring|Az előfizetés megjelenítendő neve.|  
|`CreatedDate`|Datetime|Az előfizetés létrehozásának dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`CanBeCancelled`|logikai|Azt jelzi, hogy az aktuális felhasználó lemondhatja-e az előfizetést.|  
|`IsAwaitingApproval`|logikai|Azt jelzi, hogy az előfizetés jóváhagyásra vár-e.|  
|`StartDate`|Datetime|Az előfizetés kezdő dátuma ISO 8601 `2014-06-24T16:25:00Z`formátumban: .|  
|`ExpirationDate`|Datetime|Az előfizetés lejárati dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`NotificationDate`|Datetime|Az előfizetés értesítési dátuma ISO 8601 formátumban: `2014-06-24T16:25:00Z`.|  
|`primaryKey`|sztring|Az elsődleges előfizetési kulcs. A maximális hossz 256 karakter.|  
|`secondaryKey`|sztring|A másodlagos előfizetési kulcs. A maximális hossz 256 karakter.|  
|`CanBeRenewed`|logikai|Azt jelzi, hogy az aktuális felhasználó megújíthatja-e az előfizetést.|  
|`HasExpired`|logikai|Azt jelzi, hogy az előfizetés lejárt-e.|  
|`IsRejected`|logikai|Azt jelzi, hogy az előfizetési kérelem megtagadva lett-e.|  
|`CancelUrl`|sztring|Az előfizetés lemondásához szükséges relatív URL.The relative URL to cancel the subscription.|  
|`RenewUrl`|sztring|Az előfizetés megújításához szükséges relatív URL.The relative URL to renew the subscription.|  
  
##  <a name="subscription-summary"></a><a name="SubscriptionSummary"></a>Előfizetés összegzése  
 Az `subscription summary` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Id`|sztring|Erőforrás-azonosító. Egyedileg azonosítja az előfizetést az aktuális API Management szolgáltatáspéldányon belül. Az érték egy érvényes relatív URL-cím abban a `subscriptions/{sid}` formátumban, ahol `{sid}` az előfizetés-azonosító. Ez a tulajdonság írásvédett.|  
|`DisplayName`|sztring|Az előfizetés megjelenítendő neve|  
  
##  <a name="user-account-info"></a><a name="UserAccountInfo"></a>Felhasználói fiók adatai  
 Az `user account info` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`FirstName`|sztring|A keresztneve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|sztring|A vezetékneve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és egyedinek kell lennie a szolgáltatáspéldányon belül. A maximális hossz 254 karakter.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`NameIdentifier`|sztring|Fiókazonosító, ugyanaz, mint a felhasználói e-mail.|  
|`ProviderName`|sztring|Hitelesítésszolgáltató neve.|  
|`IsBasicAccount`|logikai|Igaz, ha ezt a fiókot e-mail ben és jelszóval regisztrálták; hamis, ha a fiókot szolgáltató val regisztrálták.|  
  
##  <a name="user-sign-in"></a><a name="UseSignIn"></a>Felhasználói bejelentkezés  
 Az `user sign in` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és egyedinek kell lennie a szolgáltatáspéldányon belül. A maximális hossz 254 karakter.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`ReturnUrl`|sztring|Annak a lapnak az URL-címe, amelybe a felhasználó a bejelentkezésre kattintott.|  
|`RememberMe`|logikai|Azt jelzi, hogy menti-e az aktuális felhasználó adatait.|  
|`RegistrationEnabled`|logikai|Azt jelzi, hogy engedélyezve van-e a regisztráció.|  
|`DelegationEnabled`|logikai|Azt jelzi, hogy engedélyezve van-e a delegált bejelentkezés.|  
|`DelegationUrl`|sztring|A delegált bejelentkezési url-cím, ha engedélyezve van.|  
|`SsoSignUpUrl`|sztring|A felhasználó egyetlen bejelentkezési URL-címe, ha van ilyen.|  
|`AuxServiceUrl`|sztring|Ha az aktuális felhasználó rendszergazda, ez egy hivatkozás az Azure Portalon található szolgáltatáspéldányra.|  
|`Providers`|[Szolgáltatóentitások](#Provider) gyűjteménye|A felhasználó hitelesítésszolgáltatói.|  
|`UserRegistrationTerms`|sztring|Olyan feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsEnabled`|logikai|Azt jelzi, hogy a feltételek engedélyezve vannak-e.|  
  
##  <a name="user-sign-up"></a><a name="UserSignUp"></a>Felhasználói regisztráció  
 Az `user sign up` entitás a következő tulajdonságokkal rendelkezik:  
  
|Tulajdonság|Típus|Leírás|  
|--------------|----------|-----------------|  
|`PasswordConfirm`|logikai|A [regisztrációs](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`Password`|sztring|Felhasználói fiók jelszava.|  
|`PasswordVerdictLevel`|szám|A [regisztrációs](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`UserRegistrationTerms`|sztring|Olyan feltételek, amelyeket a felhasználónak el kell fogadnia a bejelentkezés előtt.|  
|`UserRegistrationTermsOptions`|szám|A [regisztrációs](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`ConsentAccepted`|logikai|A [regisztrációs](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`Email`|sztring|E-mail-cím. Nem lehet üres, és egyedinek kell lennie a szolgáltatáspéldányon belül. A maximális hossz 254 karakter.|  
|`FirstName`|sztring|A keresztneve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`LastName`|sztring|A vezetékneve. Nem lehet üres. A maximális hossz 100 karakter.|  
|`UserData`|sztring|A [regisztrációs](api-management-page-controls.md#sign-up) vezérlő által használt érték.|  
|`NameIdentifier`|sztring|A [regisztrációs](api-management-page-controls.md#sign-up)vezérlő által használt érték.|  
|`ProviderName`|sztring|Hitelesítésszolgáltató neve.|

## <a name="next-steps"></a>További lépések
A sablonok használatáról további információt [az API Management fejlesztői portál testreszabása sablonok használatával című](api-management-developer-portal-templates.md)témakörben talál.

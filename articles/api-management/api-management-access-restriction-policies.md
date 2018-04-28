---
title: A szoftverkorlátozó házirendek az Azure API Management hozzáférés |} Microsoft Docs
description: További tudnivalók a hozzáférés szoftverkorlátozó házirendek az Azure API Management használható.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 5fbb4f8a15ee7ee8b6cecbe76391e2b2a7e4be1b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="api-management-access-restriction-policies"></a>Az API Management hozzáférés szoftverkorlátozó házirendek
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AccessRestrictionPolicies"></a> Hozzáférés a szoftverkorlátozó házirendek  
  
-   [Ellenőrizze a HTTP-fejléc](api-management-access-restriction-policies.md#CheckHTTPHeader) -érvénybe lépteti a létezését és/vagy a HTTP-fejléc értékét.  
-   [Előfizetési határértéket hívás arányt a](api-management-access-restriction-policies.md#LimitCallRate) -megakadályozza, hogy API-használati napra hívás arány / előfizetés alapon korlátozásával.  
-   [Korlát hívás arányt a kulcs](#LimitCallRateByKey) -megakadályozza, hogy API-használati napra hívás arány / kulcs alapon korlátozásával.  
-   [A hívó IP-címek korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) -szűrők (engedélyezi vagy megtagadja) hívást bizonyos IP-címeket és/vagy címtartományokat.  
-   [Set memóriahasználati kvóta előfizetéssel](api-management-access-restriction-policies.md#SetUsageQuota) -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy előfizetés alapon érvényesítését.  
-   [Set memóriahasználati kvóta kulcs által](#SetUsageQuotaByKey) -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy kulcs alapon érvényesítését.  
-   [Ellenőrizze a JWT](api-management-access-restriction-policies.md#ValidateJWT) -érvénybe lépteti a létezését és a jwt-t vagy a megadott HTTP-fejléc, vagy a megadott lekérdezési paraméter kinyert érvényességét.  
  
##  <a name="CheckHTTPHeader"></a> Ellenőrizze a HTTP-fejléc  
 Használja a `check-header` házirend kényszerítéséhez, hogy egy kérelem rendelkezik-e a megadott HTTP-fejléc. Meg nem kötelezően ellenőrizze, hogy a fejléc vannak-e egy adott érték vagy az engedélyezett értéktartományon tartomány ellenőrzése. Ha az ellenőrzés sikertelen, a HTTP kód és a hiba állapotüzenetet a szabályzat által megadott értéket, és a házirend kérelem feldolgozása leáll.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="True">  
    <value>Value1</value>  
    <value>Value2</value>  
</check-header>  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">  
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>  
</check-header>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|ellenőrzés-fejléc|A gyökérelem.|Igen|  
|érték|Engedélyezett HTTP-fejléc értéke. Ha több érték elem meg van adva, a jelölőnégyzet akkor tekinthető sikeres, ha az értékek közül bármelyik esetben.|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|failed-check-error-message|Ha a fejléc nem létezik vagy érvénytelen értéket adja vissza a HTTP-válasz törzsében hibaüzenetet. Ez az üzenet rendelkeznie kell a megfelelő escape-karaktersorozatot különleges karaktereket.|Igen|–|  
|nem sikerült – jelölőnégyzet-HTTP-kód|HTTP-állapotkód vissza, ha a fejléc nem létezik, vagy érvénytelen értékkel rendelkezik.|Igen|–|  
|fejléc-neve|Ellenőrizze, hogy a HTTP-fejléc nevét.|Igen|–|  
|esetben figyelmen kívül hagyása|Állítható igaz vagy hamis. Ha eset igaz értékre állítva a rendszer figyelmen kívül hagyja, ha a fejléc értékének a rendszer összehasonlítja a készlet az elfogadható értéktartományon.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő, kimenő  
  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
##  <a name="LimitCallRate"></a> Előfizetés hívás arányt a korlát  
 A `rate-limit` házirend megakadályozza, hogy egy előfizetés alapon API használati igényeiben jelentkező, ha egy megadott számára egy megadott időszak hívás sebessége korlátozza. Ez a házirend kiváltásakor a hívó kap egy `429 Too Many Requests` válasz állapotkódja.  
  
> [!IMPORTANT]
>  Ezzel a házirend-házirend dokumentumonként csak egyszer használható.  
>   
>  [Házirend-kifejezések](api-management-policy-expressions.md) nem használható a házirend attribútumokat a házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<rate-limit calls="number" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</rate-limit>  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit calls="20" renewal-period="90" />  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|korlát beállítása|A gyökérelem.|Igen|  
|api-t|Vegyen fel legalább egy hívás sebessége korlátozza az API-k a terméken belüli ezen elemek. Termék és API hívása sebesség egymástól függetlenül korlátokat alkalmazza. API lehet hivatkozni vagy keresztül `name` vagy `id`. Ha mindkét biztosított, `id` fogja használni és `name` figyelmen kívül hagyja.|Nem|  
|művelet|Vegyen fel legalább egy műveleten belül az API-k hívása sebessége korlátozza az ezen elemek. Termék API és művelet hívása sebesség korlátok egymástól függetlenül alkalmazza. Művelet lehet hivatkozni vagy keresztül `name` vagy `id`. Ha mindkét biztosított, `id` fogja használni és `name` figyelmen kívül hagyja.|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|név|Az API-t, amelyre alkalmazni a sávszélesség-korlátjának neve.|Igen|–|  
|hívás|A megadott időintervallumon engedélyezett hívások maximális száma a `renewal-period`.|Igen|–|  
|megújítási időszak|Az adott időszakban másodpercben, amely után a kvóta visszaállítja.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** termék  
  
##  <a name="LimitCallRateByKey"></a> Korlát hívás arányt a kulcs  
 A `rate-limit-by-key` házirend miatt egy kulcs alapon API használati igényeiben jelentkező, ha egy megadott számára egy megadott időszak hívás sebessége korlátozza. A kulcs egy tetszőleges karakterlánc értéke lehet, és általában valósul meg a házirend-kifejezés használatával. Választható növekmény feltétel adhatók meg, hogy mely kérelmek kell számolni, a határérték felé számolnak. Ez a házirend kiváltásakor a hívó kap egy `429 Too Many Requests` válasz állapotkódja.  
  
 További tudnivalók és példák ezt a házirendet, lásd: [speciális kérelmet az Azure API Management-szabályozás](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Ezzel a házirend-házirend dokumentumonként csak egyszer használható.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<rate-limit-by-key calls="number"  
                   renewal-period="seconds"   
                   increment-condition="condition"   
                   counter-key="key value" />  
  
```  
  
### <a name="example"></a>Példa  
 A következő példában a sávszélesség-korlátjának a hívó IP-cím szerinti kulccsal definiált.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <rate-limit-by-key  calls="10"  
              renewal-period="60"  
              increment-condition="@(context.Response.StatusCode == 200)"  
              counter-key="@(context.Request.IpAddress)"/>  
    </inbound>  
    <outbound>  
        <base />          
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|korlát beállítása|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|hívás|A megadott időintervallumon engedélyezett hívások maximális száma a `renewal-period`.|Igen|–|  
|másik kulcs|A sebesség korlát házirend használandó kulcs.|Igen|–|  
|increment-condition|Adja meg, ha a kérelem kell számolni, felé a kvóta logikai kifejezés (`true`).|Nem|–|  
|megújítási időszak|Az adott időszakban másodpercben, amely után a kvóta visszaállítja.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
##  <a name="RestrictCallerIPs"></a> A hívó IP-címek korlátozása  
 A `ip-filter` házirend szűrők (engedélyezi vagy megtagadja) hívásait bizonyos IP-címeket és/vagy címtartományokat.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<ip-filter action="allow | forbid">  
    <address>address</address>  
    <address-range from="address" to="address" />  
</ip-filter>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|ip-filter|A gyökérelem.|Igen|  
|Cím|Adja meg a szűrni kívánt egyetlen IP-címet.|Legalább egy `address` vagy `address-range` elemet kell megadni.|  
|címtartományt, az "address" = "address" =|Szűrni kívánt az IP-címet ad meg.|Legalább egy `address` vagy `address-range` elemet kell megadni.|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|címtartományt, az "address" = "address" =|Engedélyezi vagy megtagadja a hozzáférést egy adott IP-címeket.|Szükséges, ha a `address-range` elem szolgál.|–|  
|IP-szűrési művelet = "engedélyezése &#124; megtiltják"|Megadja, hogy hívások engedélyezni kell, vagy nem az a megadott IP-címek és tartományok.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
##  <a name="SetUsageQuota"></a> Set memóriahasználati kvóta-előfizetéssel  
 A `quota` házirend érvénybe lépteti a megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvóta, egy előfizetés alapon.  
  
> [!IMPORTANT]
>  Ezzel a házirend-házirend dokumentumonként csak egyszer használható.  
>   
>  [Házirend-kifejezések](api-management-policy-expressions.md) nem használható a házirend attribútumokat a házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">  
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />  
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />  
    </api>  
</quota>  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|kvóta|A gyökérelem.|Igen|  
|api-t|Adjon hozzá egy vagy több ezeket az elemeket a terméken belüli hívás API-k kvóta bevezetése. A termék és API-hívás kvóták egymástól függetlenül vonatkozik. API lehet hivatkozni vagy keresztül `name` vagy `id`. Ha mindkét biztosított, `id` fogja használni és `name` figyelmen kívül hagyja.|Nem|  
|művelet|Vegyen fel legalább egy hívás kvóta műveleten belül az API-k bevezetése ezeket az elemeket. Termék API és művelet hívása kvóták egymástól függetlenül érvényesek. Művelet lehet hivatkozni vagy keresztül `name` vagy `id`. Ha mindkét biztosított, `id` fogja használni és `name` figyelmen kívül hagyja.|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|név|Az API-t vagy a művelet, amelynek a kvóta vonatkozik neve.|Igen|–|  
|Sávszélesség|A megadott időintervallumon engedélyezett kilobájt maximális száma a `renewal-period`.|Vagy `calls`, `bandwidth`, vagy együttesen kell megadni.|–|  
|hívás|A megadott időintervallumon engedélyezett hívások maximális száma a `renewal-period`.|Vagy `calls`, `bandwidth`, vagy együttesen kell megadni.|–|  
|megújítási időszak|Az adott időszakban másodpercben, amely után a kvóta visszaállítja.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
-   **Házirend hatókörök:** termék  
  
##  <a name="SetUsageQuotaByKey"></a> Set memóriahasználati kvóta gombot  
 A `quota-by-key` házirend érvénybe lépteti a megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvóta, egy kulcs alapon. A kulcs egy tetszőleges karakterlánc értéke lehet, és általában valósul meg a házirend-kifejezés használatával. Választható növekmény feltétel adhatók meg, hogy mely kérelmek kell számolni, a kvóta felé.  
  
 További tudnivalók és példák ezt a házirendet, lásd: [speciális kérelmet az Azure API Management-szabályozás](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).  
  
> [!IMPORTANT]
>  Ezzel a házirend-házirend dokumentumonként csak egyszer használható.  
>   
>  [Házirend-kifejezések](api-management-policy-expressions.md) nem használható a házirend attribútumokat a házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<quota-by-key calls="number"   
              bandwidth="kilobytes"   
              renewal-period="seconds"  
              increment-condition="condition"   
              counter-key="key value" />  
  
```  
  
### <a name="example"></a>Példa  
 A következő példában a kvóta a hívó IP-cím szerinti kulccsal definiált.  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"  
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"  
                      counter-key="@(context.Request.IpAddress)" />  
    </inbound>  
    <outbound>  
        <base />  
    </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|kvóta|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|Sávszélesség|A megadott időintervallumon engedélyezett kilobájt maximális száma a `renewal-period`.|Vagy `calls`, `bandwidth`, vagy együttesen kell megadni.|–|  
|hívás|A megadott időintervallumon engedélyezett hívások maximális száma a `renewal-period`.|Vagy `calls`, `bandwidth`, vagy együttesen kell megadni.|–|  
|másik kulcs|A kvóta házirend használandó kulcs.|Igen|–|  
|increment-condition|Adja meg, ha a kérelem kell számolni, felé a kvóta logikai kifejezés (`true`)|Nem|–|  
|megújítási időszak|Az adott időszakban másodpercben, amely után a kvóta visszaállítja.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
##  <a name="ValidateJWT"></a> JWT ellenőrzése  
 A `validate-jwt` házirend érvénybe lépteti a meglétét, és a jwt-t érvényességét kinyert vagy egy meghatározott HTTP-fejléc vagy a megadott lekérdezési paraméter.  
  
> [!IMPORTANT]
>  A `validate-jwt` házirend megköveteli, hogy a `exp` regisztrált jogcím megtalálható a JWT jogkivonat, kivéve, ha `require-expiration-time` attribútum van megadva, és beállítása `false`.  
> A `validate-jwt` házirend HS256 és RS256 aláírási algoritmust támogat. HS256 a kulcsot meg kell adni a base64-kódolású képernyőn a szabályzaton belüli beágyazott. A kulcs RS256 keresztül nyitott azonosító konfigurációs végpontok biztosításához rendelkezik.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<validate-jwt   
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"   
    failed-validation-httpcode="http status code to return on failure"   
    failed-validation-error-message="error message to return on failure"   
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"   
    clock-skew="allowed clock skew in seconds">  
  <issuer-signing-keys>  
    <key>base64 encoded signing key</key>  
    <!-- if there are multiple keys, then add additional key elements -->  
  </issuer-signing-keys>  
  <audiences>  
    <audience>audience string</audience>  
    <!-- if there are multiple possible audiences, then add additional audience elements -->  
  </audiences>  
  <issuers>  
    <issuer>issuer string</issuer>  
    <!-- if there are multiple possible issuers, then add additional issuer elements -->  
  </issuers>  
  <required-claims>  
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>  
      <!-- if there is more than one allowed values, then add additional value elements -->  
    </claim>  
    <!-- if there are multiple possible allowed values, then add additional value elements -->  
  </required-claims>  
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />  
  <zumo-master-key id="key identifier">key value</zumo-master-key>  
</validate-jwt>  
  
```  
  
### <a name="examples"></a>Példák  
  
#### <a name="azure-mobile-services-token-validation"></a>Az Azure Mobile Services jogkivonatok érvényesség-ellenőrzése  
  
```xml  
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">  
    <issuers>  
        <issuer>urn:microsoft:windows-azure:zumo</issuer>  
    </issuers>  
    <audiences>  
        <audience>Facebook</audience>  
    </audiences>  
    <issuer-signing-keys>  
        <zumo-master-key id="0">insert key here</zumo-master-key>  
    </issuer-signing-keys>  
</validate-jwt>  
```  
  
#### <a name="azure-active-directory-token-validation"></a>Az Azure Active Directory jogkivonatok érvényesség-ellenőrzése  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />  
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  

  
#### <a name="azure-active-directory-b2c-token-validation"></a>Az Azure Active Directory B2C jogkivonatok érvényesség-ellenőrzése  
  
```xml  
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">  
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>  
        <claim name="id" match="all">  
            <value>insert claim here</value>  
        </claim>  
    </required-claims>  
</validate-jwt>  
```  
  
#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Műveletek a jogcímek jogkivonat-alapú hozzáférés hitelesítése  
 A példa bemutatja, hogyan használható a [érvényesítése JWT](api-management-access-restriction-policies.md#ValidateJWT) házirend előre a műveletek hozzáférés hitelesítése a token jogcímei alapján. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és előretekerés 13:50. Gyors továbbítsa 15:00, a házirendek a Helyicsoportházirend-szerkesztő konfigurált megjelenítéséhez, majd a művelet hívása a developer portálról, és a szükséges engedélyezési jogkivonat anélkül bemutatója 18:50.  
  
```xml  
<!-- Copy the following snippet into the inbound section at the api (or higher) level to pre-authorize access to operations based on token claims -->  
<set-variable name="signingKey" value="insert signing key here" />  
<choose>  
  <when condition="@(context.Request.Method.Equals("patch",StringComparison.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="edit">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <when condition="@(new [] {"post", "put"}.Contains(context.Request.Method,StringComparer.OrdinalIgnoreCase))">  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
      <required-claims>  
        <claim name="create">  
          <value>true</value>  
        </claim>  
      </required-claims>  
    </validate-jwt>  
  </when>  
  <otherwise>  
    <validate-jwt header-name="Authorization">  
      <issuer-signing-keys>  
        <key>@((string)context.Variables["signingKey"])</key>  
      </issuer-signing-keys>  
    </validate-jwt>  
  </otherwise>  
</choose>  
```  
  
### <a name="elements"></a>Elemek  
  
|Elem|Leírás|Szükséges|  
|-------------|-----------------|--------------|  
|jwt ellenőrzése|A gyökérelem.|Igen|  
|célcsoportok|Lehet, hogy a jogkivonat jelenlegi elfogadható célközönség jogcímeket listáját tartalmazza. Ha több célközönség értékek találhatók, akkor minden egyes érték próbálkozik amíg újra nem indítják összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg valamelyik nem jár sikerrel. Legalább egy célközönség meg kell adni.|Nem|  
|Kiállítói aláírási kulcsok|Aláírt jogkivonatokat érvényesítéséhez használt Base64-kódolású biztonsági kulcsok listáját. Ha több biztonsági kulcsok szerepelnek, akkor minden kulcs próbálkozik amíg újra nem indítják összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg valamelyik nem jár sikerrel (hasznos token kulcsváltás). Fő elemekből kell egy nem kötelező `id` az egyeztetéshez használt attribútum `kid` jogcímek.|Nem|  
|kibocsátók|A jogkivonat kiállító elfogadható résztvevők listájának. Ha több kibocsátó értékek találhatók, akkor minden egyes érték próbálkozik amíg újra nem indítják összes kimerültek (ebben az esetben az érvényesítés sikertelen), vagy amíg valamelyik nem jár sikerrel.|Nem|  
|openid-konfiguráció|Az elem, amelyből aláíró kulcsok és a kiállító érhető el megfelelő megnyitott azonosító konfigurációs végpont megadására használt.|Nem|  
|szükséges jogcímeket|A jogcímek kellene lennie ahhoz, hogy a nem érvényes jogkivonat megtalálható listáját tartalmazza. Ha a `match` attribútum van beállítva `all` minden jogcím értékét a házirend a jogkivonat az érvényesítés sikeres jelen kell lennie. Ha a `match` attribútum van beállítva `any` legalább egy jogcímet a jogkivonat az érvényesítés sikeres jelen kell lennie.|Nem|  
|zumo főkulcs|Az Azure Mobile Services által kiállított jogkivonatokat főkulcs|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
|Name (Név)|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|óraeltérés|TimeSpan érték. Adja meg a várt közötti maximális eltérést a jogkivonat kiállítójának rendszerideje és az API Management-példány használatával.|Nem|0 másodperc|  
|failed-validation-error-message|A HTTP-válasz törzsében adja vissza, ha a jwt-t nem felel meg az érvényesítési hibaüzenet. Ez az üzenet rendelkeznie kell a megfelelő escape-karaktersorozatot különleges karaktereket.|Nem|Alapértelmezett hibaüzenetet függ az érvényesítési hibát, például "JWT nem található."|  
|nem sikerült – érvényesítési-HTTP-kód|HTTP-állapotkód vissza, ha a jwt-t nem teljesíti az ellenőrző.|Nem|401|  
|fejléc-neve|A HTTP-fejlécnek a tokent tároló neve.|Vagy `header-name` vagy `query-parameter-name` megadott; de nem mindkettőn keresztül kell lennie.|–|  
|id|A `id` attribútuma a `key` elem lehetővé teszi a karakterláncot, amely elleni megfeleltetésének `kid` tudja meg a megfelelő kulcsot az aláírás-ellenőrzés használata (ha van ilyen) a jogkivonat jogcímek.|Nem|–|  
|Egyezés|A `match` attribútuma a `claim` elem meghatározza, hogy a házirend minden jogcím értékét kell a jogkivonat az érvényesítés sikeres szerepel. Lehetséges értékek:<br /><br /> -                          `all` – a szabályzat minden jogcím értékét a jogkivonat az érvényesítés sikeres jelen kell lennie.<br /><br /> -                          `any` -legalább egy jogcím értékét a jogkivonat az érvényesítés sikeres jelen kell lennie.|Nem|összes|  
|lekérdezés-paremeter-neve|Neve az a következő lekérdezésparaméter a tokent tároló.|Vagy `header-name` vagy `query-paremeter-name` megadott; de nem mindkettőn keresztül kell lennie.|–|  
|igényelnek-lejárati-idő|Logikai érték. Meghatározza, hogy szükséges-e egy lejárati jogcímet a tokenben.|Nem|true|
|szükséges rendszer|A token neve sémáját, pl. "Tulajdonos". Az attribútum van beállítva, ha a házirend biztosítja, hogy a megadott séma szerepel az engedélyezési fejléc értéke.|Nem|–|
|igényelnek-aláírt-tokenek|Logikai érték. Megadja, hogy egy jogkivonatot kell aláírni.|Nem|true|  
|Elválasztó|Karakterlánc. Meghatározza az elválasztó (pl. ",") értékek beolvasása a többértékű jogcím használandó.|Nem|–| 
|url|Azonosító konfigurációs végponti URL-cím megnyitása ahol nyitott azonosító konfigurációs metaadatok érhető el. A válasz meg kell felelnie a specifikációk, meghatározott URL-címen:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`.  Az Azure Active Directory használata a következő URL-cím: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` a directory-bérlő neve, pl. és `contoso.onmicrosoft.com`.|Igen|–|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)   

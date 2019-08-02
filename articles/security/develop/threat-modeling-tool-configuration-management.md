---
title: Configuration Management – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: a Threat Modeling Toolban elérhető fenyegetések enyhítése
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
ms.openlocfilehash: dbe3526444bc35815dd4323a3a5290696619e3f9
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728350"
---
# <a name="security-frame-configuration-management--mitigations"></a>Biztonsági keret: Konfigurálási felügyelet | Enyhítését 
| Termék vagy szolgáltatás | Cikk |
| --------------- | ------- |
| **Webalkalmazás** | <ul><li>[A Content Security Policy (CSP) implementálása és a beágyazott JavaScript letiltása](#csp-js)</li><li>[A böngésző XSS-szűrőjének engedélyezése](#xss-filter)</li><li>[A ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt](#trace-deploy)</li><li>[Harmadik féltől származó JavaScriptek elérése kizárólag megbízható forrásokból](#js-trusted)</li><li>[Győződjön meg arról, hogy a hitelesített ASP.NET-lapok felhasználói felületi felöltözködni vagy kattintásos védelem](#ui-defenses)</li><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webalkalmazásokban](#cors-aspnet)</li><li>[ValidateRequest attribútum engedélyezése a ASP.NET lapokon](#validate-aspnet)</li><li>[A JavaScript-kódtárak helyileg üzemeltetett legújabb verzióinak használata](#local-js)</li><li>[Automatikus MIME-elemzés letiltása](#mime-sniff)</li><li>[A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyein az ujjlenyomat elkerüléséhez](#standard-finger)</li></ul> |
| **Adatbázis** | <ul><li>[A Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez](#firewall-db)</li></ul> |
| **Webes API** | <ul><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webes API-ban](#cors-api)</li><li>[A webes API bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása](#config-sensitive)</li></ul> |
| **IoT-eszköz** | <ul><li>[Győződjön meg arról, hogy az összes felügyeleti felület erős hitelesítő adatokkal van védve](#admin-strong)</li><li>[Győződjön meg arról, hogy az ismeretlen kód nem hajtható végre az eszközökön](#unknown-exe)</li><li>[Az operációs rendszer és a IoT-eszköz további partícióinak titkosítása a bites zárolással](#partition-iot)</li><li>[Győződjön meg arról, hogy csak a minimális szolgáltatások/szolgáltatások engedélyezve vannak az eszközökön](#min-enable)</li></ul> |
| **IoT-mező átjárója** | <ul><li>[Az operációs rendszer és a IoT további partícióinak titkosítása a bit-Locker használatával](#field-bit-locker)</li><li>[Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során](#default-change)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Győződjön meg arról, hogy a felhőalapú átjáró egy folyamatot valósít meg a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartásához](#cloud-firmware)</li></ul> |
| **Számítógép-megbízhatósági kapcsolat határa** | <ul><li>[Győződjön meg arról, hogy az eszközökön szervezeti házirendként konfigurált végponti biztonsági vezérlők vannak konfigurálva](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Az Azure Storage-hozzáférési kulcsok biztonságos kezelésének biztosítása](#secure-keys)</li><li>[Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure Storage-ban](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[A WCF szolgáltatás-szabályozási funkciójának engedélyezése](#throttling)</li><li>[WCF – információk közzététele metaadatokon keresztül](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>A Content Security Policy (CSP) implementálása és a beágyazott JavaScript letiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | A tartalom biztonsági házirendjének, a [tartalmi biztonsági házirendek referenciájának](https://content-security-policy.com/), a [biztonsági funkcióknak](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [a tartalmi biztonsági házirend](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy)bevezetésének bemutatása, használhatom a CSP- [t](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) [?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Lépések** | <p>A Content Security Policy (CSP) egy, a W3C szabványnak megfelelő védelmi mechanizmus, amely lehetővé teszi, hogy a webalkalmazások tulajdonosai szabályozzák a helyükön beágyazott tartalmat. A CSP a webkiszolgálón HTTP-válasz fejlécként van hozzáadva, és az ügyféloldali böngészőkben kényszerítve van. Ez egy engedélyezési listára épülő szabályzat – a webhely deklarálhatja azokat a megbízható tartományokat, amelyekről az aktív tartalom, például a JavaScript be van töltve.</p><p>A CSP a következő biztonsági előnyöket nyújtja:</p><ul><li>**Az XSS elleni védelem:** Ha egy oldal sebezhető az XSS-vel, a támadó a következő két módon használhatja ki:<ul><li>Adja `<script>malicious code</script>`meg a t. Ez a biztonsági rés a CSP alapkorlátozása miatt nem fog működni – 1</li><li>Adja `<script src=”http://attacker.com/maliciousCode.js”/>`meg a t. Ez a biztonsági rés nem fog működni, mivel a támadó által vezérelt tartomány nem a CSP engedélyezési tartományában található.</li></ul></li><li>**Az adatkiszűrése vezérlése:** Ha a weblapon lévő bármilyen kártékony tartalom megpróbál csatlakozni egy külső webhelyhez, és ellopja az adatokat, a rendszer megszakítja a kapcsolatot a CSP-vel. Ennek az az oka, hogy a célként megadott tartomány nem a CSP engedélyezési listájában jelenik meg</li><li>**Védelem a Click-Jacks:** Click-jacking olyan támadási technika, amellyel egy támadó egy valódi webhelyet tud kialakítani, és rákényszerítheti a felhasználókat, hogy a felhasználói felület elemeire kattintanak. Jelenleg a Click-jacking elleni védelem a válasz fejlécének (X-frame-Options) konfigurálásával érhető el. Nem minden böngésző tartja tiszteletben ezt a fejlécet, és a továbbiakban a CSP szabványos módszert biztosít a kattintások elleni védelemhez</li><li>**Valós idejű támadások jelentése:** Ha egy CSP-t támogató webhelyen van egy injekciós támadás, a böngészők automatikusan elindítanak egy értesítést a webkiszolgálón konfigurált végpontnak. Így a CSP valós idejű figyelmeztetési rendszerként szolgál.</li></ul> |

### <a name="example"></a>Példa
Példa házirend: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ez a szabályzat lehetővé teszi a parancsfájlok betöltését csak a webalkalmazás kiszolgálója és a Google Analytics-kiszolgáló között. A más helyekről betöltött parancsfájlok el lesznek utasítva. Ha a CSP engedélyezve van egy webhelyen, a következő funkciók automatikusan le lesznek tiltva az XSS-támadások enyhítése érdekében. 

### <a name="example"></a>Példa
A beágyazott parancsfájlok nem lesznek végrehajtva. A következő példák a beágyazott parancsfájlokra 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Példa
A karakterláncok nem lesznek kódként kiértékelve. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>A böngésző XSS-szűrőjének engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [XSS védelmi szűrő](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Lépések** | <p>X-XSS – a védelmi válasz fejlécének beállításai vezérlik a böngésző többhelyes parancsfájl-szűrőjét. A válasz fejlécének értékei a következők lehetnek:</p><ul><li>`0:`Ezzel letiltja a szűrőt.</li><li>`1: Filter enabled`Ha a rendszer helyek közötti parancsfájl-megtámadást észlel a támadás leállítása érdekében, a böngésző megtisztítja a lapot</li><li>`1: mode=block : Filter enabled`. A lap tisztítása helyett az XSS-támadás észlelése után a böngésző megakadályozza az oldal megjelenítését</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. A böngésző megtisztítja a lapot, és jelentést küld a szabálysértésről.</li></ul><p>Ez egy Chromium-függvény, amely CSP-megsértési jelentéseket használ, hogy adatokat küldjön a választott URI-nak. Az utolsó 2 lehetőség biztonságos értéknek számít.</p>|

## <a id="trace-deploy"></a>A ASP.NET alkalmazásoknak le kell tiltaniuk a nyomkövetést és a hibakeresést a telepítés előtt

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [ASP.net-hibakeresés áttekintése](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.net-nyomkövetés áttekintése](https://msdn.microsoft.com/library/bb386420.aspx), [útmutató: Nyomkövetés engedélyezése egy ASP.net-](https://msdn.microsoft.com/library/0x5wc973.aspx)alkalmazáshoz [, útmutató: ASP.NET-alkalmazások hibakeresésének engedélyezése](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Lépések** | Ha a nyomkövetés engedélyezve van az oldalon, minden azt kérő böngésző a belső kiszolgáló állapotáról és a munkafolyamatról adatokat tartalmazó nyomkövetési adatokat is beolvassa. Ez az információ biztonsági szempontból érzékeny lehet. Ha a hibakeresés engedélyezve van az oldalon, a kiszolgálón előforduló hibák a böngészőben megjelenített teljes verem nyomkövetési adatát eredményezik. Ezek az adatok biztonsági szempontból bizalmas adatokat tehetnek elérhetővé a kiszolgáló munkafolyamatáról. |

## <a id="js-trusted"></a>Harmadik féltől származó JavaScriptek elérése kizárólag megbízható forrásokból

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | a harmadik féltől származó JavaScripteket csak megbízható forrásból lehet hivatkozni. A hivatkozási végpontoknak mindig SSL-alapúnak kell lenniük. |

## <a id="ui-defenses"></a>Győződjön meg arról, hogy a hitelesített ASP.NET-lapok felhasználói felületi felöltözködni vagy kattintásos védelem

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [OWASP Click-the Defense cheat sheet](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [IE Internals – a Click-Jacks with X-frame-Options elleni küzdelem](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Lépések** | <p>Ha a támadó több transzparens vagy átlátszatlan réteget használ, a felhasználó egy gombra kattintva vagy egy másik lapon lévő hivatkozásra kattinthat, amikor a legfelső szintű lapra kattintanak, a "felhasználói felületi jogorvoslati támadás" néven is ismert.</p><p>Ezt a rétegződést úgy érheti el, ha egy rosszindulatú oldalt biztosít egy iframe-sel, amely betölti az áldozat oldalát. Így a támadó "eltéríti" az oldalát, és átirányítja őket egy másik oldalra, valószínűleg egy másik alkalmazás, tartomány vagy mindkettő tulajdonosa. Ha meg szeretné akadályozni, hogy a kattintásos támadásokat engedélyezze, állítsa be a megfelelő X-frame-Options HTTP-válasz fejléceket, amelyek arra utasítja a böngészőt, hogy ne engedélyezzék más tartományok</p>|

### <a name="example"></a>Példa
Az X-FRAME-OPTIONs fejléc az IIS web. config használatával állítható be. Web. config kódrészlet olyan helyekhez, amelyeknek soha nem szabad keretbe esniük: 
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
Web. config kód olyan helyekhez, amelyek csak ugyanabban a tartományban lévő lapok szerint vannak bekeretezett: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webalkalmazásokban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatokat olvasson be egy másik helyről. Előfordulhat azonban, hogy szükség van az API-k biztonságos elérhetővé tételére, amelyet más helyek is használhatnak. A több forrásból származó erőforrás-megosztás (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára, hogy az azonos eredetű házirendet kikapcsolódjon. A CORS használatával a kiszolgáló explicit módon engedélyezheti a több forrásból származó kérelmeket, miközben mások elutasítása is megtörtént.</p><p>A CORS biztonságosabb és rugalmasabb, mint a korábbi módszerek, például a JSNOP támogatással. A CORS lehetővé teszi, hogy a lefordítsa néhány HTTP-válaszüzenet (hozzáférés-vezérlés-*) hozzáadását a webalkalmazáshoz, és ez több módon is elvégezhető.</p>|

### <a name="example"></a>Példa
Ha elérhető a web. config fájl, akkor a CORS a következő kód használatával adható hozzá: 
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
Ha a web. config fájlhoz való hozzáférés nem érhető el, a CORS a következő CSharp-kód hozzáadásával konfigurálható: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Fontos megjegyezni, hogy a "hozzáférés-vezérlés-engedélyezés-eredet" attribútumban található Origins lista véges és megbízható származási csoportra van beállítva. Ha nem megfelelően konfigurálja ezt a beállítást (például ha az értéket "*" értékre állítja be), a rosszindulatú helyek a webalkalmazások felé irányuló kérelmeket válthatnak be > korlátozás nélkül, így az alkalmazás sebezhetővé válik a CSRF támadásokkal szemben. 

## <a id="validate-aspnet"></a>ValidateRequest attribútum engedélyezése a ASP.NET lapokon

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Web Forms, MVC5 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Kérelem érvényesítése – parancsfájlok elleni támadások megakadályozása](https://www.asp.net/whitepapers/request-validation) |
| **Lépések** | <p>A 1,1-es verzió óta a ASP.NET szolgáltatásának ellenőrzése a kérelem érvényesítésével megakadályozhatja, hogy a kiszolgáló fogadja a nem kódolt HTML-kódot tartalmazó tartalmat. Ez a szolgáltatás úgy lett kialakítva, hogy segítsen megakadályozni bizonyos parancsfájl-befecskendezéses támadásokat, amelyekben az ügyfél-parancsfájl vagy a HTML-kód tudatlanul módon elküldhető egy kiszolgálónak, tároltnak és más felhasználóknak. Továbbra is erősen ajánlott az összes bemeneti adat és HTML-kódolás ellenőrzése, ha szükséges.</p><p>A kérelem érvényesítése az összes bemeneti adatnak a potenciálisan veszélyes értékek listájára való összehasonlításával történik. Ha egyezés történik, a ASP.NET megemel `HttpRequestValidationException`. Alapértelmezés szerint a kérelem-ellenőrzési funkció engedélyezve van.</p>|

### <a name="example"></a>Példa
Ez a funkció azonban a lap szintjén is letiltható: 
```XML
<%@ Page validateRequest="false" %> 
```
vagy alkalmazás szintjén 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Vegye figyelembe, hogy a kérelem-ellenőrzési funkció nem támogatott, és nem része a MVC6 folyamatnak. 

## <a id="local-js"></a>A JavaScript-kódtárak helyileg üzemeltetett legújabb verzióinak használata

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | <p>A hagyományos JavaScript-kódtárakat (például a JQuery) használó fejlesztőknek olyan általános JavaScript-kódtárak jóváhagyott verzióit kell használniuk, amelyek nem tartalmaznak ismert biztonsági hibákat. Helyes gyakorlat a kódtárak legújabb verziójának használata, mivel ezek a régebbi verziókban az ismert biztonsági rések biztonsági javításait tartalmazzák.</p><p>Ha a legutóbbi kiadás kompatibilitási okok miatt nem használható, az alábbi minimális verziókat kell használni.</p><p>Elfogadható minimális verziók:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery-ellenőrzés 1,9</li><li>JQuery Mobile 1.0.1</li><li>JQuery-ciklus 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Ajax Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms és AJAX**<ul><li>ASP.NET Web Forms és AJAX 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Soha ne töltsön be JavaScript-függvénytárat külső webhelyekről, például nyilvános CDNs</p>|

## <a id="mime-sniff"></a>Automatikus MIME-elemzés letiltása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [IE8 biztonsági rész V: Átfogó védelem](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [MIME-típus](https://en.wikipedia.org/wiki/Mime_type) |
| **Lépések** | Az X-Content-Type-Options fejléc egy HTTP-fejléc, amely lehetővé teszi a fejlesztők számára, hogy a tartalmuk ne legyenek MIME-szippantva. Ez a fejléc a MIME-szippantás elleni támadások enyhítésére szolgál. Minden olyan oldal esetében, amely tartalmazhatja a felhasználó által ellenőrizhető tartalmat, az X-Content-Type-Options: deszippantás HTTP-fejlécet kell használnia. Ha az alkalmazás összes oldalához globálisan szeretné engedélyezni a szükséges fejlécet, a következők egyikét teheti:|

### <a name="example"></a>Példa
Adja hozzá a fejlécet a web. config fájlhoz, ha az alkalmazást Internet Information Services (IIS) 7-től futtatja. 
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
A fejléc hozzáadása a globális alkalmazás\_BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Példa
Egyéni HTTP-modul implementálása 
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
A szükséges fejlécet csak adott lapokon engedélyezheti, ha hozzáadja azt az egyes válaszokhoz: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyein az ujjlenyomat elkerüléséhez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Web Application | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | EnvironmentType – Azure |
| **Hivatkozik**              | [A szabványos kiszolgálók fejlécének eltávolítása a Windows Azure webhelyek szolgáltatásban](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Lépések** | Az olyan fejlécek, mint a kiszolgáló, az X-alapú, X-AspNet-Version a kiszolgálóval és a mögöttes technológiákkal kapcsolatos információkat mutatják ki. Ajánlott letiltani ezeket a fejléceket, így megakadályozva az alkalmazás ujjlenyomatát |

## <a id="firewall-db"></a>A Windows tűzfal konfigurálása az adatbázismotor-hozzáféréshez

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Adatbázis | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | SQL Azure, OnPrem |
| **Attribútumok**              | N/A, SQL-verzió – V12 |
| **Hivatkozik**              | [Azure SQL Database-tűzfal konfigurálása](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Windows tűzfal konfigurálása az adatbázismotor](https://msdn.microsoft.com/library/ms175043) -hozzáféréshez |
| **Lépések** | A tűzfal-rendszerek segítenek megakadályozni a számítógép erőforrásainak jogosulatlan hozzáférését. Ha tűzfalon keresztül szeretné elérni a SQL Server adatbázismotor egy példányát, akkor a hozzáférés engedélyezéséhez konfigurálnia kell a tűzfalat a SQL Server rendszert futtató számítógépen. |

## <a id="cors-api"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van a ASP.NET webes API-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 5 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Az ASP.net web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), a [ASP.net web API-CORS támogatása a ASP.net web API 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Lépések** | <p>A böngésző biztonsági beállításai megakadályozzák, hogy egy weblap AJAX-kérelmeket küldjön egy másik tartományba. Ezt a korlátozást azonos eredetű házirendnek nevezzük, és megakadályozza, hogy egy rosszindulatú hely bizalmas adatokat olvasson be egy másik helyről. Előfordulhat azonban, hogy szükség van az API-k biztonságos elérhetővé tételére, amelyet más helyek is használhatnak. A több forrásból származó erőforrás-megosztás (CORS) egy W3C szabvány, amely lehetővé teszi a kiszolgáló számára, hogy az azonos eredetű házirendet kikapcsolódjon.</p><p>A CORS használatával a kiszolgáló explicit módon engedélyezheti a több forrásból származó kérelmeket, miközben mások elutasítása is megtörtént. A CORS biztonságosabb és rugalmasabb, mint a korábbi módszerek, például a JSNOP támogatással.</p>|

### <a name="example"></a>Példa
A App_Start/WebApiConfig. cs kódban adja hozzá a következő kódot a WebApiConfig. register metódushoz 
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
A EnableCors attribútum a vezérlőben a következő módon alkalmazható a műveleti metódusokra: 

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

Fontos, hogy a EnableCors attribútumban lévő Origins-lista véges és megbízható eredetű készletre legyen állítva. Ha nem megfelelően konfigurálja ezt a beállítást (például a "*" érték megadásával), a rosszindulatú helyek az API-k számára korlátozás nélkül indíthatnak kereszthivatkozásokat az API-hoz, > így az API sebezhetővé válik a CSRF támadásokkal szemben. A EnableCors a vezérlő szintjén lehet megdíszítve. 

### <a name="example"></a>Példa
Egy adott metódus CORS letiltásához a DisableCors attribútum a lent látható módon használható: 
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

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | MVC 6 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Az CORS ASP.NET Core 1,0-es verziójának engedélyezése](https://docs.asp.net/en/latest/security/cors.html) |
| **Lépések** | <p>ASP.NET Core 1,0-ben a CORS-t middleware használatával vagy MVC használatával lehet engedélyezni. Ha MVC-t használ a CORS engedélyezéséhez, a rendszer a CORS-szolgáltatásokat is használja, de a CORS middleware nem.</p>|

**Megközelítés – 1** CORS engedélyezése a middleware-vel: Ha engedélyezni szeretné a teljes alkalmazás CORS, adja hozzá a CORS middleware-t a kérési folyamathoz a UseCors-bővítmény módszerének használatával. A CORS middleware a CorsPolicyBuilder osztály használatával történő hozzáadásakor a rendszer több eredetű házirendet is megadhat. Ehhez két módja van:

### <a name="example"></a>Példa
Az első a UseCors meghívása lambda használatával. A lambda egy CorsPolicyBuilder objektumot vesz igénybe: 
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
A második egy vagy több elnevezett CORS-szabályzatot határoz meg, majd a házirendet a futtatási idő szerint válassza ki. 
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

**Megközelítés – 2** Az CORS engedélyezése az MVC-ben: A fejlesztők az MVC használatával konkrét CORS alkalmazhatnak műveleti, vezérlőn vagy globálisan az összes vezérlőn.

### <a name="example"></a>Példa
/Művelet: Egy adott művelet CORS-házirendjének megadásához adja hozzá a [EnableCors] attribútumot a művelethez. Adja meg a szabályzat nevét. 
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
/Vezérlő: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Példa
Globálisan 
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
Fontos, hogy a EnableCors attribútumban lévő Origins-lista véges és megbízható eredetű készletre legyen állítva. Ha nem megfelelően konfigurálja ezt a beállítást (például a "*" érték megadásával), a rosszindulatú helyek az API-k számára korlátozás nélkül indíthatnak kereszthivatkozásokat az API-hoz, > így az API sebezhetővé válik a CSRF támadásokkal szemben. 

### <a name="example"></a>Példa
Egy vezérlő vagy művelet CORS letiltásához használja a [DisableCors] attribútumot. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>A webes API bizalmas adatokat tartalmazó konfigurációs fájljainak részeinek titkosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Webes API | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Útmutató: A ASP.NET 2,0 konfigurációs fejezeteinek titkosítása](https://msdn.microsoft.com/library/ff647398.aspx)a DPAPI használatával, [védett konfigurációs szolgáltató](https://msdn.microsoft.com/library/68ze1hb2.aspx)megadásával, [Azure Key Vault használatával az alkalmazások titkos](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) adatainak védelméhez |
| **Lépések** | A konfigurációs fájlok, például a web. config, a appSettings. JSON gyakran a bizalmas adatok tárolására szolgálnak, beleértve a felhasználóneveket, a jelszavakat, az adatbázis-kapcsolati karakterláncokat és a titkosítási kulcsokat. Ha nem védik ezeket az információkat, az alkalmazás sebezhetővé válik a támadók és a rosszindulatú felhasználók számára bizalmas információk, például a fiókok felhasználónevei és jelszavaik, az adatbázisok nevei és a kiszolgálók nevei alapján. A központi telepítési típus (Azure/helyszíni) alapján Titkosítsa a konfigurációs fájlok bizalmas részeit a DPAPI vagy olyan szolgáltatásokkal, mint például a Azure Key Vault. |

## <a id="admin-strong"></a>Győződjön meg arról, hogy az összes felügyeleti felület erős hitelesítő adatokkal van védve

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Az eszköz vagy a mező átjárója által elérhető felügyeleti felületek erős hitelesítő adatokkal vannak védve. Emellett az egyéb kitett felületek, például a WiFi, az SSH, a fájlmegosztás, az FTP erős hitelesítő adatokkal is biztonságossá válnak. Az alapértelmezett gyenge jelszavakat nem szabad használni. |

## <a id="unknown-exe"></a>Győződjön meg arról, hogy az ismeretlen kód nem hajtható végre az eszközökön

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Biztonságos rendszerindítási és bites zárolási eszközök titkosításának engedélyezése a Windows 10 IoT Core-ban](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Lépések** | A UEFI biztonságos rendszerindítás korlátozza a rendszert, hogy csak a megadott szolgáltató által aláírt bináris fájlok végrehajtását engedélyezze. Ez a szolgáltatás megakadályozza, hogy az ismeretlen kód fusson a platformon, és hogy esetleg gyengítse a biztonsági helyzetét. Engedélyezze az UEFI biztonságos rendszerindítását, és korlátozza az aláíró kód számára megbízható hitelesítésszolgáltatók listáját. Az eszközön központilag telepített összes kód aláírása az egyik megbízható hatóság használatával. |

## <a id="partition-iot"></a>Az operációs rendszer és a IoT-eszköz további partícióinak titkosítása a bites zárolással

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A Windows 10 IoT Core a bit-Locker-eszközök titkosításának egyszerűsített verzióját valósítja meg, amely erős függőséggel rendelkezik a platformon található TPM jelenlétével, beleértve az UEFI-ben szükséges preOS protokollt, amely a szükséges méréseket végzi. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később az operációs rendszer elindításának pontos rekordjaival rendelkezik. Titkosítsa az operációsrendszer-partíciókat a bitek és a további partíciók használatával is, ha bizalmas adatokat tárolnak. |

## <a id="min-enable"></a>Győződjön meg arról, hogy csak a minimális szolgáltatások/szolgáltatások engedélyezve vannak az eszközökön

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-eszköz | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Ne engedélyezze vagy tiltsa le az operációs rendszer azon funkcióit és szolgáltatásait, amelyek nem szükségesek a megoldás működéséhez. Például ha az eszközön nincs szükség felhasználói felület telepítésére, telepítse a Windows IoT Core-t a fej nélküli módban. |

## <a id="field-bit-locker"></a>Az operációs rendszer és a IoT további partícióinak titkosítása a bit-Locker használatával

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | A Windows 10 IoT Core a bit-Locker-eszközök titkosításának egyszerűsített verzióját valósítja meg, amely erős függőséggel rendelkezik a platformon található TPM jelenlétével, beleértve az UEFI-ben szükséges preOS protokollt, amely a szükséges méréseket végzi. Ezek a preOS-mérések biztosítják, hogy az operációs rendszer később az operációs rendszer elindításának pontos rekordjaival rendelkezik. Titkosítsa az operációsrendszer-partíciókat a bitek és a további partíciók használatával is, ha bizalmas adatokat tárolnak. |

## <a id="default-change"></a>Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT-mező átjárója | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy a helyszíni átjáró alapértelmezett bejelentkezési hitelesítő adatai módosulnak a telepítés során |

## <a id="cloud-firmware"></a>Győződjön meg arról, hogy a felhőalapú átjáró egy folyamatot valósít meg a csatlakoztatott eszközök belső vezérlőprogram naprakészen tartásához

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | IoT Cloud Gateway | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | Átjáró választása – Azure IoT Hub |
| **Hivatkozik**              | [IoT hub eszközkezelés áttekintése](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [az eszköz belső vezérlőprogram frissítése](https://docs.microsoft.com/azure/iot-hub/tutorial-firmware-update) |
| **Lépések** | A LWM2M a IoT-eszközök felügyeletének nyílt mobil Szövetségének protokollja. Az Azure IoT-eszközkezelés lehetővé teszi, hogy az eszköz feladataival kommunikáljon a fizikai eszközökkel. Győződjön meg arról, hogy a felhőalapú átjáró olyan folyamatot valósít meg, amely az Azure IoT Hub-eszközkezelés használatával rendszeresen megtartja az eszköz és az egyéb konfigurációs adatok naprakészen tartását. |

## <a id="controls-policies"></a>Győződjön meg arról, hogy az eszközökön szervezeti házirendként konfigurált végponti biztonsági vezérlők vannak konfigurálva

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Számítógép-megbízhatósági kapcsolat határa | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | –  |
| **Lépések** | Győződjön meg arról, hogy az eszközökön olyan végponti biztonsági vezérlők találhatók, mint a lemez szintű titkosítás, a víruskereső, a frissített aláírások, a gazdagép-alapú tűzfal, az operációsrendszer-frissítések, a csoportházirendek stb. a szervezeti biztonsági házirendek szerint konfigurálva. |

## <a id="secure-keys"></a>Az Azure Storage-hozzáférési kulcsok biztonságos kezelésének biztosítása

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Környezet |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [Azure Storage – biztonsági útmutató – a Storage-fiók kulcsainak kezelése](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Lépések** | <p>Kulcstároló: Azt javasoljuk, hogy az Azure Storage elérési kulcsait Azure Key Vault titkos kulcsként tárolja, és az alkalmazásokból kérje le a kulcsot a Key vaultból. Ez a következő okok miatt ajánlott:</p><ul><li>Az alkalmazásnak soha nem lesz hardcoded egy konfigurációs fájlban, ami eltávolítja, hogy valaki hozzáférést kap a kulcsokhoz adott engedély nélkül.</li><li>A kulcsok elérését Azure Active Directory használatával lehet vezérelni. Ez azt jelenti, hogy a fiók tulajdonosa hozzáférést biztosíthat a kulcsoknak a Azure Key Vaultból való lekéréséhez szükséges alkalmazásokhoz. Más alkalmazások nem fognak tudni hozzáférni a kulcsokhoz anélkül, hogy az engedélyt kifejezetten megadják</li><li>Kulcs újragenerálása: Az Azure Storage-hozzáférési kulcsok biztonsági okokból történő újragenerálása javasolt egy folyamattal. A kulcs újragenerálásának okairól és tervezéséről az Azure Storage biztonsági útmutatójának dokumentációjában olvashat bővebben.</li></ul>|

## <a id="cors-storage"></a>Győződjön meg arról, hogy csak a megbízható eredetek engedélyezettek, ha a CORS engedélyezve van az Azure Storage-ban

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | Azure Storage | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | Általános |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [CORS-támogatás az Azure Storage-szolgáltatásokhoz](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Lépések** | Az Azure Storage lehetővé teszi, hogy engedélyezze a CORS – több eredetű erőforrás-megosztást. Minden egyes Storage-fiókhoz megadhat olyan tartományokat, amelyek hozzáférhetnek a Storage-fiók erőforrásaihoz. Alapértelmezés szerint a CORS le van tiltva az összes szolgáltatáson. A CORS a REST API vagy a Storage ügyféloldali kódtár használatával engedélyezheti, hogy az egyik módszert hívja meg a szolgáltatási házirendek beállításához. |

## <a id="throttling"></a>A WCF szolgáltatás-szabályozási funkciójának engedélyezése

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com) |
| **Lépések** | <p>A rendszererőforrások használatának korlátozása nem helyezheti el az erőforrások kimerülését és végső soron a szolgáltatás megtagadását.</p><ul><li>**MAGYARÁZAT** A Windows Communication Foundation (WCF) lehetőséget nyújt a szolgáltatási kérelmek szabályozására. Ha túl sok ügyfél-kérést tud elárasztani, a rendszer kimeríti az erőforrásait. Másfelől azonban csak kis mennyiségű kérést engedélyez a szolgáltatásnak, így megakadályozhatja, hogy a legitim felhasználók használják a szolgáltatást. Minden szolgáltatásnak külön kell beállítania és konfigurálva kell lennie, hogy a megfelelő mennyiségű erőforrást engedélyezze.</li><li>**Javaslatok** Engedélyezze a WCF szolgáltatás-szabályozási funkcióját, és állítsa be az alkalmazáshoz szükséges korlátokat.</li></ul>|

### <a name="example"></a>Példa
Az alábbi példa a szabályozást engedélyező konfigurációt szabályozza:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>WCF – információk közzététele metaadatokon keresztül

| Beosztás                   | Részletek      |
| ----------------------- | ------------ |
| **Összetevő**               | WCF | 
| **SDL Phase**               | Felépítés |  
| **Alkalmazható technológiák** | .NET-keretrendszer 3 |
| **Attribútumok**              | –  |
| **Hivatkozik**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [megerősítő Királyság](https://vulncat.fortify.com) |
| **Lépések** | A metaadatok segítségével a támadók megismerhetik a rendszerrel kapcsolatos információkat, és megtervezhetik a támadási formákat. A WCF-szolgáltatások lehetővé teszik a metaadatok megjelenítését. A metaadatok részletesen ismertetik a szolgáltatás leírását, és nem lehetnek éles környezetben. AServiceMetaDataosztálytulajdonságaihatározzákmeg,hogyegyszolgáltatáselérhetővéteszi-eametaadatokat`HttpGetEnabled`  /  `HttpsGetEnabled` | 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-t, hogy közvetítse a szolgáltatás metaadatait
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Ne közvetítse a szolgáltatás metaadatait éles környezetben. Állítsa a ServiceMetaData osztály HttpGetEnabled/HttpsGetEnabled tulajdonságát false (hamis) értékre. 

### <a name="example"></a>Példa
Az alábbi kód arra utasítja a WCF-t, hogy ne sugározza a szolgáltatás metaadatait. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```

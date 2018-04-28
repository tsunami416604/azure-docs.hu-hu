---
title: Ajánlott eljárások és hibaelhárítási útmutatójában csomópont alkalmazások Azure-webalkalmazásokban
description: Ismerje meg az ajánlott eljárásokról és a csomópont-alkalmazások az Azure Web Apps hibaelhárítási lépéseket.
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: ''
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: ranjithr
ms.openlocfilehash: 860874ed49056e6b4695c060b06bf061820c390e
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Ajánlott eljárások és hibaelhárítási útmutatójában csomópont alkalmazások Azure-webalkalmazásokban

Ez a cikk további ajánlott eljárásokról és a hibaelhárítási lépéseket [csomópont alkalmazások](app-service-web-get-started-nodejs.md) futó Azure Web Apps (a [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Körültekintően járjon el, ha a hibaelhárítási lépéseket a munkakörnyezeti helyet. Javasoljuk, hogy például az átmeneti helyet egy nem éles a beállítás az alkalmazás hibaelhárítása, és ha a probléma fennáll, felcserélni az átmeneti rendelkező az éles webalkalmazásra.
>

## <a name="iisnode-configuration"></a>Az IISNODE konfigurálása

Ez [sémafájl](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) jeleníti meg a beállításokat konfigurálhat az iisnode-hoz. Egyes, melyek hasznosak lehetnek az alkalmazás beállításai:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Ez a beállítás szabályozza, hogy az IIS alkalmazásonként működését csomópont folyamatok száma. Az alapértelmezett értéke 1. Ha megváltoztatja az értéket 0-ra annyi node.exes a virtuális gép vCPU darabszámként indíthatja el. Ajánlott értéke 0, a legtöbb alkalmazás, hogy használhassa a Vcpu mindegyikét a számítógépen. NODE.exe az egyszálas, egy node.exe legfeljebb 1 vCPU használ fel. A csomópont alkalmazás kívül a legjobb teljesítmény eléréséhez használni kívánt összes Vcpu.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Ez a beállítás szabályozza a node.exe elérési útja. Beállíthatja, hogy ezt az értéket a node.exe verziójára mutasson.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Ez a beállítás minden node.exe iisnode által küldött egyidejű kérelmek maximális számát szabályozza. Az Azure Web Apps az alapérték végtelen. Ha nem az Azure Web Apps, az alapérték 1024. Beállíthatja, hogy az érték, attól függően, hogy hány kér az alkalmazás fogad, és hogy milyen gyorsan az alkalmazás minden kérést dolgoz fel.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Ez a beállítás szabályozza a maximálisan megengedett számú node.exe a kérelmeket küldeni a nevesített csövön a kapcsolat létrehozása az iisnode újrapróbálkozások. Ez a beállítás namedPipeConnectionRetryDelay együtt meghatározza, hogy az egyes kérelmek iisnode belül a teljes időtúllépés. Alapértelmezett érték 200-as Azure-webalkalmazásokban. Időtúllépés másodpercben teljes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Ez a beállítás határozza meg az egyes újra elküldeni a kérelmet a nevesített csövön keresztül node.exe közötti idő (ms) iisnode vár. Az alapértelmezett érték 250 ms.
Időtúllépés másodpercben teljes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Alapértelmezés szerint az Azure-webalkalmazásokban iisnode időtúllépés teljes értéke 200 \* 250 ms = 50 másodperc.

### <a name="logdirectory"></a>logDirectory

Ez a beállítás szabályozza a könyvtárat, amelyben az iisnode naplózza stdout/stderr. Az alapértelmezett érték: az iisnode, amely relatív a fő parancsfájlba könyvtár (könyvtárat, amelyben fő server.js jelen)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Ez a beállítás szabályozza a node-inspector iisnode melyik verzióját használja, a csomópont-alkalmazásban. Az iisnode-inspector-0.7.3.dll és az iisnode-inspector.dll jelenleg a csak két érvényes érték ehhez a beállításhoz. Az alapértelmezett érték: az iisnode-inspector-0.7.3.dll. Az iisnode-inspector-0.7.3.dll verzió csomópont-inspector-0.7.3 használ, és webes szoftvercsatornák használja. Webes szoftvercsatornák a jelenlegi verzióját használják az Azure webalkalmazás engedélyezése. Lásd: <http://ranjithblogs.azurewebsites.net/?p=98> iisnode az új node-Inspector segítségével történő konfigurálásával kapcsolatos további részletekért.

### <a name="flushresponse"></a>flushResponse

Az alapértelmezett IIS működése, hogy azt puffereli érkezett válasz adatait mentése 4 MB kiürítése előtt, illetve a válasz végéig amelyik előbb következik be. az iisnode Ez a viselkedés felülbírálásához a konfigurációs beállításokat kínál: kiüríteni válasz entitástörzsében egy kódrészletet, amint iisnode kap a node.exe, be kell állítani a iisnode/@flushResponse a Web.config fájlban a "true" attribútum:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Engedélyezze a könyvelési mindegyik töredék a válasz a entitástörzsében lassítja a, amely csökkenti az átviteli sebessége a rendszer (az v0.1.13) ~ 5 %-kal. A legjobb ezt a beállítást csak adatfolyam-választ igénylő végpontok hatókörének (használata esetén például a `<location>` a Web.config elemben)

Továbbá a az alkalmazások, is be kell responseBufferLimit az iisnode leíró 0.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Pontosvesszővel elválasztott módosítások vannak figyelt fájlokat. Egy fájl bármely módosítása hatására az alkalmazás újrahasznosítása. Minden bejegyzéshez tartozik egy választható könyvtár nevét, valamint a szükséges fájl nevét, amelyek a könyvtárat, amelyben az alkalmazás fő belépési pont található viszonyítva. A fájl neve funkciója csak helyettesítő karakterek megengedettek. Az alapértelmezett érték: `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Az alapértelmezett értéke hamis. Ha engedélyezve van, a csomópont alkalmazás csatlakozhat egy nevesített csövet (környezeti változót az IISNODE\_vezérlő\_CSŐ) és "újrahasznosítást" üzenet. Ennek hatására a w3wp szabályosan újrahasznosítása.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Az alapértelmezett értéke 0, ami azt jelenti, hogy ez a funkció le van tiltva. Ha értéke egy értéket 0-nál nagyobbnak, iisnode fog lapon minden gyermek folyamatainak ki minden "idlePageOutTimePeriod" ezredmásodpercben. Lásd: [dokumentáció](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) kimenő azt jelenti, hogy mi lapon megértése. A beállítás akkor hasznos, az alkalmazásokat, amelyek a nagy méretű memória felhasználását, és szeretné kimenő lemezre alkalmanként, hogy felszabadítsa a RAM memória lapon.

> [!WARNING]
> Legyen körültekintő, ha engedélyezi az éles környezetben a következő konfigurációs beállításait. Az ajánljuk, hogy engedélyezze azokat éles alkalmazásokban.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Az alapértelmezett értéke hamis. Ha értéke igaz, az iisnode ad hozzá egy HTTP-válaszfejléc `iisnode-debug` az összes HTTP-válasz küldi el a `iisnode-debug` fejléc értéke egy URL-címet. Egyes adatra diagnosztikai érhető el az URL-cím töredék megtekintésével, azonban a képi megjelenítés elérhető egy böngészőben nyissa meg az URL-címet.

### <a name="loggingenabled"></a>loggingEnabled

Ez a beállítás az iisnode által az stdout és az stderr naplózását szabályozza. Az Iisnode stdout/stderr csomópont folyamatok elindul, és a "logDirectory" beállításban megadott Directory könyvtárba ír rögzíti. Ha engedélyezve van, az alkalmazás írja a naplókat, a fájlrendszerben és az alkalmazás által végzett naplózás mennyiségétől függően, a teljesítményre gyakorolt hatása lehet.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Az alapértelmezett értéke hamis. Ha értéke igaz, az iisnode és jeleníti meg a HTTP-állapotkód: Win32 hibakód: a böngésző. A win32 kódban akkor hasznos, a hibakeresés bizonyos típusú problémákat.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (ne engedélyezze az éles hely)

Ez a beállítás szabályozza a hibakeresési funkciója. Az Iisnode integrálva van a node-Inspector használatával. Ha engedélyezi ezt a beállítást, engedélyezi a csomópont alkalmazás hibakeresést. A beállítás engedélyezése esetén az iisnode a csomópont alkalmazás felé irányuló első debug kérelem "debuggerVirtualDir" könyvtárában a node-inspector fájlokat hozza létre. A node-inspector kérelem küldésével betöltése http://yoursite/server.js/debug. A hibakeresési URL-szegmenseket "debuggerPathSegment" beállítással szabályozható. Alapértelmezés szerint debuggerPathSegment = "debug". Beállíthat `debuggerPathSegment` egyedi azonosítóvá, például úgy, hogy az informatikai nehezebb mások észlelhetők.

Olvasási [Debug a node.js-alkalmazást a Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) kapcsolatban további részleteket a hibakeresést.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Forgatókönyvek és javaslatok/hibaelhárítása

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>A csomópont-alkalmazás, hogy így túl sok kimenő hívások

Számos alkalmazás szeretné kimenő kapcsolatok létrehozása a rendszeres művelet részeként. Például ha a kérelem érkezik, a csomópont app szeretné lépjen kapcsolatba a REST API máshol, és néhány adatra feldolgozni a kérelmet. Szeretné használni kívánt megtartása életben ügynök http vagy https hívása esetén. Használhatja a agentkeepalive modul a keep-alive megbízottként, a kimenő hívása esetén.

A agentkeepalive modul biztosítja, hogy a a virtuális gép Azure webalkalmazás sockets fel van-e újra. Többletterheléssel hoz létre egy új szoftvercsatorna minden kimenő kérelem az alkalmazáshoz. Az alkalmazás használja fel a kimenő kérelmek sockets biztosítja, hogy az alkalmazás nem haladja meg a virtuális gépenként kiosztott maxsocket. Az Azure-webalkalmazásokban ajánlás is, hogy a agentKeepAlive maxsocket érték összesen (4 példányai node.exe \* 40 maxsocket/példány) virtuális gépenként 160 sockets.

Példa [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguráció:

```nodejs
var keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Ez a példa feltételezi, hogy rendelkezik-e a virtuális gépen 4 node.exe. Ha a virtuális gépen node.exe eltérő számú, módosítania kell a beállítást ennek megfelelően maxsocket.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>A csomópont-alkalmazás által felhasznált túl sok Processzor

A portál magas cpu-felhasználás vonatkozó ajánlást az Azure Web Apps jelenhet meg. Is beállíthat figyelők egyes bemutató [metrikák](web-sites-monitor.md). Ha a CPU-használat ellenőrzése a következőn: a [Azure Portal irányítópult](../application-insights/app-insights-web-monitor-performance.md), ellenőrizze a maximális CPU, hogy nem hagy ki a maximális értéket.
Ha úgy érzi, az alkalmazás által felhasznált túl sok CPU, és miért nem ismertetik, is profilhoz, a csomópont alkalmazás megállapítása.

#### <a name="profiling-your-node-application-on-azure-web-apps-with-v8-profiler"></a>A csomópont alkalmazás Azure-webalkalmazásokban V8-Profilkészítő a profilkészítési

Például tegyük fel, a hello world alkalmazás, amelyet szeretne profil a következőképpen:

```nodejs
var http = require('http');
function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    WriteConsoleLog();
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Nyissa meg a hibakereső konzol helyhez https://yoursite.scm.azurewebsites.net/DebugConsole

Nyissa meg a hely/wwwroot könyvtárba. A következő példában látható módon jelenik meg egy parancssort:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Futtassa a parancsot `npm install v8-profiler`.

Ez a parancs elvégzi a csomópont alatt v8-Profilkészítő\_modulok directory és az összes függősége.
Most szerkessze a server.js profil az alkalmazáshoz.

```nodejs
var http = require('http');
var profiler = require('v8-profiler');
var fs = require('fs');

function WriteConsoleLog() {
    for(var i=0;i<99999;++i) {
        console.log('hello world');
    }
}

function HandleRequest() {
    profiler.startProfiling('HandleRequest');
    WriteConsoleLog();
    fs.writeFileSync('profile.cpuprofile', JSON.stringify(profiler.stopProfiling('HandleRequest')));
}

http.createServer(function (req, res) {
    res.writeHead(200, {'Content-Type': 'text/html'});
    HandleRequest();
    res.end('Hello world!');
}).listen(process.env.PORT);
```

Az előző kód profilok a WriteConsoleLog funkciót, és majd a profil kimeneti fájlba ír, amelynek a "profile.cpuprofile" a hely wwwroot alatt. Az alkalmazás kérelmet küld. Megjelenik a hely wwwroot alapján létrehozott "profile.cpuprofile" fájl.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Töltse le a fájlt, és nyissa meg a Chrome F12 eszközökkel. Kattintson az F12 Chrome, majd kattintson a **profilok** fülre. Válassza ki a **terhelés** gombra. Válassza ki a letöltött profile.cpuprofile fájlt. Kattintson a profil épp.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Láthatja, hogy a WriteConsoleLog függvény által felhasznált 95 %-ában. A kimenet a pontos sorok számát és a hibát okozó forrásfájlokat is megjeleníti.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A csomópont alkalmazás van túl sok memória fogyasztása

Ha az alkalmazás által felhasznált túl sok memóriát, a portál kapcsolatos leterheli a rendszermemóriát látható Azure Web Apps az értesítés. Figyelendő egyes figyelők állíthat be [metrikák](web-sites-monitor.md). Ha a memóriahasználat ellenőrzése a következőn: a [Azure Portal irányítópult](../application-insights/app-insights-web-monitor-performance.md), a memória maximális értékeit ellenőrizze, hogy nem hagy ki a maximális értéket.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>A node.js felderítését és a halommemória különbözeti szivárgás lépett fel

Használhat [csomópont-memwatch](https://github.com/lloyd/node-memwatch) segítségével azonosíthatja a memória-szivárgást.
Telepítése `memwatch` hasonlóan v8-Profilkészítő és szerkesztése a kódot a rögzítési és a különbözeti által kezelt halommemóriákból azonosítására a memóriavesztések az alkalmazásban.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A node.exe vannak első véletlenszerűen leállítása

Néhány oka miért node.exe le van állítva véletlenszerűen:

1. Az alkalmazás szűrész nem kezelt kivételek – jelölőnégyzet d:\\otthoni\\naplófájlok\\alkalmazás\\a kivétel lépett fel a részletes naplózás-errors.txt fájlt. Ez a fájl rendelkezik az alábbi veremkivonatban hibakeresését és hárítsa el az alkalmazás segítségével.
2. Az alkalmazás által felhasznált túl sok memóriát, amely érinti a bevezetés más folyamatokkal. Ha az összes virtuális gép memória megközelíti a 100 %-os, a node.exe sikerült lehet leállítani, a folyamat-kezelő. Folyamat manager használhatatlanná teszi az egyes folyamatok ahhoz, hogy más folyamatok néhány munkájuk is. A probléma megoldásához, az alkalmazás memóriavesztés profilját. Ha az alkalmazás nagy mennyiségű memóriát igényel, vertikális felskálázás a nagyobb virtuális gépek (Ez növeli a virtuális gép számára elérhető memória).

### <a name="my-node-application-does-not-start"></a>A csomópont alkalmazás nem indul el

Az alkalmazás indításakor 500 hibák vissza, ha néhány oka lehet:

1. NODE.exe nincs jelen a megfelelő helyen. Jelölje be nodeProcessCommandLine beállítást.
2. Fő parancsfájl verziója nem található megfelelő helyére. Ellenőrizze a Web.config fájlban, és győződjön meg arról, hogy a kezelők szakaszában a fő parancsfájl neve megegyezik a fő parancsfájlt.
3. Web.config konfigurációra nincs megfelelő – ellenőrizze a beállításokat neveket és-értékek.
4. Hidegindítás – az alkalmazás túl sokáig tart elindításához. Ha az alkalmazás tovább tart, mint (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 másodperc, a iisnode 500 hibát ad vissza. Ezeket a beállításokat, az alkalmazás kezdete időtúllépés törlését és az 500 hiba az iisnode megelőzése érdekében kereséséhez értékek növelése.

### <a name="my-node-application-crashed"></a>A csomópont alkalmazás összeomlott

Az alkalmazás szűrész nem kezelt kivételek – jelölőnégyzet `d:\\home\\LogFiles\\Application\\logging-errors.txt` a kivétel lépett fel a fájlt a részletekért. Ez a fájl az alábbi veremkivonatban segítségével diagnosztizálhatja és hárítsa el az alkalmazás rendelkezik.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A csomópont-alkalmazás indítása (Cold indítás) túl sok időt vesz igénybe

A közös hosszú az alkalmazás indítási idejének oka az olyan fájlok csomópontjában\_modulok. Az alkalmazás megkísérli betölteni ezeket a fájlokat a legtöbb indításakor. Alapértelmezés szerint tárolja a fájlokat a hálózati megosztás, az Azure Web Apps, mivel betöltése sok fájl időt vehet igénybe.
Gyorsabb, ez a folyamat egyes megoldások a következők:

1. Győződjön meg arról, hogy rendelkezik egy egyszerű függőségi struktúra és a nem ismétlődő függőség npm3 használatával telepítse a modulokat.
2. Lusta változtassa meg a csomópont betöltése\_modulok és nem tölthető be az összes alkalmazás indítása a modulok. A Lusta terhelés modulokhoz require('module') hívása kell arról, ha ténylegesen szüksége a modul az első modul kód végrehajtása előtt a függvényen belül.
3. Az Azure Web Apps által nyújtott nevű a helyi gyorsítótárban. Ez a szolgáltatás másolja át a tartalmat a hálózati megosztáshoz a helyi lemez a virtuális Gépen. Mivel a fájlok helyi, a betöltési idő csomópont\_modulok sokkal gyorsabb.

## <a name="iisnode-http-status-and-substatus"></a>Az IISNODE http-állapot és részállapot

A `cnodeconstants` [forrásfájl](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) listák összes a lehetséges állapota/részállapot kombinációk iisnode visszatérhet egy hiba miatt.

A win32 hibakód megtekintéséhez az alkalmazás FREB engedélyezése (lehet, hogy csak a nem éles helyeken a teljesítményre vonatkozó megfontolásból FREB engedélyezi).

| HTTP-állapot | HTTP-Substatus | Lehetséges ok? |
| --- | --- | --- |
| 500 |1000 |Hiba történt az egyes IISNODE kérelem terjesztéséhez probléma – ellenőrizze, hogy node.exe indult-e. NODE.exe indításakor sikerült összeomlott. Ellenőrizze a web.config konfigurációs hibák. |
| 500 |1001 |-Win32Error 0x2 - alkalmazás az URL-cím nem válaszol. Ellenőrizze az URL-cím átdolgozás szabályok vagy ellenőrizze, hogy az express alkalmazást a helyes útvonalak definiálva van. -Win32Error 0x6d – nevesített cső elfoglalva, – Node.exe nem fogad el kérelmeket, mert a cső elfoglalva. Ellenőrizze a nagy cpu-használat. -Más hibák – ellenőrizze, hogy ha node.exe összeomlott. |
| 500 |1002 |NODE.exe összeomlott – d: Ellenőrizze\\otthoni\\naplófájlok\\naplózási-errors.txt Veremkivonat a. |
| 500 |1003 |Az adatcsatorna konfigurációs probléma – a nevesített cső konfigurációja nem megfelelő. |
| 500 |1004-1018 |Néhány hiba történt a kérelem küldésekor, vagy node.exe és a válasz feldolgozását. Ellenőrizze, hogy node.exe összeomlott. Ellenőrizze a d:\\otthoni\\naplófájlok\\naplózási-errors.txt Veremkivonat a. |
| 503 |1000 |Foglaljon le több nevesített cső kapcsolat nem elegendő memória. Ellenőrizze, hogy miért az alkalmazás nem használ a sok memóriát. Ellenőrizze a maxConcurrentRequestsPerProcess beállítás értékét. Ha nem végtelen, és hogy hány kérésnek, növelje meg ezt az értéket, hogy ez a hiba megakadályozza. |
| 503 |1001 |Kérelem nem sikerült node.exe továbbítani, mert az alkalmazás újrahasznosítása van. Miután az alkalmazás rendelkezik felhasználását, kérelmek általában fel kell dolgozni. |
| 503 |1002 |Nem sikerült továbbítani a jelölőnégyzet win32 hibakód tényleges okból – kérelem egy node.exe számára. |
| 503 |1003 |Nevesített cső jelenleg túl elfoglalt – győződjön meg arról, ha node.exe túlzott CPU nem használ-e |

NODE.exe van egy nevű beállítás `NODE_PENDING_PIPE_INSTANCES`. Alapértelmezés szerint nincs telepítve az Azure Web Apps, ha az értéke 4. Ami azt jelenti, hogy node.exe betöltéshez legfeljebb négy kéréseket a nevesített csövön egyszerre. Az Azure Web Apps az alapérték 5000. Ez az érték elég jó Azure-webalkalmazásokban futó legtöbb csomópont alkalmazások kell lennie. Nem megjelenik 503.1003 Azure Web Apps miatt a nagy értéket a `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>További erőforrások

Az alábbi hivatkozásokból tudhat meg többet az Azure App Service a node.js-alkalmazások.

* [Ismerkedés a Node.js webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started-nodejs.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](app-service-web-tutorial-nodejs-mongodb-app.md)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js fejlesztői központ](../nodejs-use-node-modules-azure-apps.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
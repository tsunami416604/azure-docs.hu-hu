---
title: Ajánlott eljárások és hibaelhárítás a node.js-szel – Azure App Service-ben
description: Ismerje meg az ajánlott eljárások és hibaelhárítási lépések csomópont-alkalmazásokhoz az Azure App Service-ben.
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
ms.custom: seodec18
ms.openlocfilehash: db412d3fd0af84d528ad0c83d86cc5d055359914
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632687"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Ajánlott eljárások és hibaelhárítási útmutató csomópont-alkalmazásokhoz az Azure App Service Windows

Ebből a cikkből megtudhatja, ajánlott eljárások és hibaelhárítási lépések [node-alkalmazások](app-service-web-get-started-nodejs.md) futó Azure App Service szolgáltatásban (az [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Körültekintően járjon el a webhelyet működtetheti élesben hibaelhárítási lépéseket használatakor. Javaslat, hogy például az előkészítési ponton az alkalmazás egy nem éles telepítés hibaelhárítása, és ha a probléma elhárítása a az üzemelési és előkészítési pont cseréje.
>

## <a name="iisnode-configuration"></a>Az IISNODE konfigurálása

Ez [sémafájl](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) beállításokról jeleníti meg, hogy konfigurálhatja az iisnode-hoz. Néhány hasznos, ha az alkalmazás beállítások:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication

Ez a beállítás szabályozza a csomópont folyamatok, melyeket az IIS alkalmazásonként számát. Az alapértelmezett érték az 1. A virtuális gép vCPU-számot, annyi node.exes 0 érték módosításával indíthatja el. A javasolt érték: 0 a legtöbb alkalmazás, így használhatja a vcpu-k mindegyikének a gépen. NODE.exe az egyszálas így egy node.exe legfeljebb 1 vCPU használ fel. A node-alkalmazás maximális teljesítménye lekéréséhez használni kívánt összes vcpu-k.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine

Ez a beállítás szabályozza a node.exe elérési útját. Beállíthatja, hogy ezt az értéket a node.exe verzióra mutasson.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Ez a beállítás szabályozza az egyes node.exe iisnode által küldött egyidejű kérelmek maximális számát. Az Azure App Service az alapértelmezett érték a végtelen. Konfigurálhatja az értékét, hogy hány kér az alkalmazás fogad, és milyen gyors az alkalmazás minden kérést dolgoz fel.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Ez a beállítás maximális száma határozza meg a nevesített cső a kérelmek küldéséhez node.exe a kapcsolat létrehozása az iisnode újrapróbálkozások. Ez a beállítás namedPipeConnectionRetryDelay együtt az iisnode belül minden egyes kérés időtúllépés teljes határozza meg. Az alapértelmezett értéke 200 Azure App Service szolgáltatásban. Teljes időkorlátja másodpercben = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Ez a beállítás szabályozza a kérelmet kell küldenie a node.exe a nevesített csövön keresztül az egyes újrapróbálkozások közötti idő (ms) iisnode vár. Az alapértelmezett érték: 250 ms.
Teljes időkorlátja másodpercben = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000

Alapértelmezés szerint az összes időkorlátot az az Azure App Service iisnode a 200-as \* 250 ms = 50 másodperc.

### <a name="logdirectory"></a>logDirectory

Ez a beállítás szabályozza a könyvtárban, ahol az iisnode naplók stdout/stderr. Az alapértelmezett értéke az iisnode, amely képest a fő parancsfájlba könyvtár (ahol a fő server.js megtalálható könyvtár)

### <a name="debuggerextensiondll"></a>debuggerExtensionDll

Ez a beállítás szabályozza a node-inspector iisnode melyik verzióját használja, a node-alkalmazás hibakeresése során. Az iisnode-vizsgáló-0.7.3.dll és az iisnode-inspector.dll jelenleg ezt a beállítást csak két érvényes értékei. Az alapértelmezett értéke az iisnode-vizsgáló-0.7.3.dll. Az iisnode-vizsgáló-0.7.3.dll verzió node-inspector-0.7.3 és web sockets használja. Engedélyezze a web sockets az Azure webalkalmazás verziója. Lásd: <https://ranjithblogs.azurewebsites.net/?p=98> iisnode az új node-Inspector segítségével a konfigurálása a további részletekért.

### <a name="flushresponse"></a>flushResponse

Az IIS alapértelmezett viselkedését, hogy azt puffereli válasz adatok mentése előtt írta, vagy a válasz végéig 4 MB-ra hamarabb. az iisnode kínál ezt a viselkedést felülírhatja a konfigurációs beállítások:, amint az iisnode node.exe kap, a válasz törzse egy kódrészletet ürítéséhez kell beállítani a iisnode/@flushResponse a Web.config fájlban a "true" attribútum:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Az ürítés engedélyezéséhez, minden töredék a válasz törzse lassítja a, ami csökkenti az átviteli sebességet, a rendszer ~ 5 %-a (cikk v0.1.13). A legjobb ezt a beállítást csak igénylő válasz streamelési végpontok hatóköre (például a `<location>` Web.config eleme)

Emellett az alkalmazások, adatfolyamként is be kell az iisnode-kezelő responseBufferLimit 0-ra.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Pontosvesszővel elválasztott módosítások vannak vizsgált fájlok listáját. Egy fájl bármely módosítása hatására az alkalmazás újraindítása. Mindegyik bejegyzés egy választható könyvtár nevét, valamint a szükséges fájl nevét, amelyek a könyvtárat, ahol a fő alkalmazását belépési pont található viszonyítva áll. A fájl neve funkciója csak helyettesítő karakterek használata engedélyezett. Az alapértelmezett érték `*.js;web.config`

### <a name="recyclesignalenabled"></a>recycleSignalEnabled

Az alapértelmezett értéke FALSE (hamis). Ha engedélyezve van, a node-alkalmazás csatlakozhat egy nevesített csövet (környezeti változót az IISNODE\_vezérlő\_függőleges vonal) és a egy "újrahasznosítás" üzenet küldése. Ennek hatására a w3wp szabályosan újraindításához.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Az alapértelmezett értéke 0, ami azt jelenti, hogy ez a funkció le van tiltva. Ha megadott néhány értéket 0-nál nagyobbnak, iisnode fog lapon minden gyermekfolyamata ki minden "idlePageOutTimePeriod" ezredmásodpercben. Lásd: [dokumentáció](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx) megérteni, milyen lapon azt jelenti, hogy ki. A beállítás akkor hasznos, ha az alkalmazásokat, amelyek egy nagy mennyiségű memória felhasználását, és szeretné ki lemez alkalmanként RAM memória felszabadítása érdekében memória lapon.

> [!WARNING]
> Legyen körültekintő, amikor engedélyezi az éles üzemi alkalmazások pedig az alábbi konfigurációs beállításai. A javaslat, hogy nem engedélyezheti őket, az éles alkalmazások.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Az alapértelmezett értéke FALSE (hamis). Ha értéke igaz, az iisnode ad hozzá egy HTTP-válaszfejléc `iisnode-debug` , minden HTTP-választ küld a `iisnode-debug` fejléc értéke egy URL-címet. Egyéni diagnosztikai adatokat szerezhető be az URL-cím töredék megnézzük, azonban egy Vizualizáció érhető el nyissa meg az URL-címet egy böngészőben.

### <a name="loggingenabled"></a>loggingEnabled

Ez a beállítás az iisnode által az stdout és stderr naplózását szabályozza. Az Iisnode rögzíti a stdout/stderr-csomópont folyamatok elindítja, és beírja a "logDirectory" beállításban megadott könyvtárban. Ha ez engedélyezve van, az alkalmazás ír a naplókat, a fájlrendszer és az alkalmazás által végzett naplózás mennyiségétől függően, a teljesítményre gyakorolt hatása lehet.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Az alapértelmezett értéke FALSE (hamis). Ha értéke igaz, az iisnode megjeleníti a HTTP-állapotkódot és Win32 hibakód a böngészőben. A win32 hibakód hasznos bizonyos típusú problémákat hibakeresés közben.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>debuggingEnabled (ne engedélyezze az éles helyet)

Ez a beállítás szabályozza a hibakeresési funkció. Az Iisnode integrálva van a node-inspector. Ez a beállítás engedélyezése esetén engedélyezi a node-alkalmazás a hibakeresés. A beállítás engedélyezése esetén az iisnode "debuggerVirtualDir" könyvtárában, a node-alkalmazás hibakeresési első kérés a node-inspector fájlokat hoz létre. A node-inspector betöltheti irányuló kérelem küldésével http://yoursite/server.js/debug. A hibakeresési URL-szegmenst "debuggerPathSegment" beállítással szabályozhatja. Alapértelmezésben a debuggerPathSegment = "debug". Beállíthat `debuggerPathSegment` GUID, például úgy, hogy az informatikai nehezebb a mások által felderíteni.

Olvasási [Debug a node.js-alkalmazások a Windows](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) hibakeresés további részleteiért.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Forgatókönyvek és javaslatok és hibaelhárítás

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>A node-alkalmazás, hogy, így a túlzott kimenő hívások

Számos alkalmazás szeretné a kimenő kapcsolatokat létesíthet a normál művelet részeként. Például amikor kérelem érkezik, a node-alkalmazás szeretné lépjen kapcsolatba a REST API-val máshol, és néhány adatra feldolgozni a kérelmet. Szeretne használni egy életben tartási ügynök http vagy https-hívást. Használhatja a agentkeepalive modul a életben tartási ügynökként, ezek kimenő hívása esetén.

A agentkeepalive modul biztosítja, hogy sockets újra felhasználhatók az Azure webalkalmazás virtuális Gépet. Az alkalmazás egy új szoftvercsatorna létrehozását az egyes kimenő kérelmek hozzáadja többletterhelést okoz. Kimenő kérelmek sockets újra felhasználhatja az alkalmazás biztosítja, hogy az alkalmazás nem haladja meg a virtuális gépenkénti kiosztott maxsocket. Az Azure App Service ajánlása, hogy a agentKeepAlive maxsocket értékét állítsa összesen (node.exe 4 példányait \* 40 maxsocket/példány) 160 sockets virtuális gépenként.

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
> Ez a példa feltételezi, hogy a virtuális Gépen futó 4 node.exe. Ha a virtuális gépen futó node.exe különböző számú, a maxsocket beállítást ennek megfelelően kell módosítania.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>A node-alkalmazás túl sok CPU is használja.

Az Azure App Service ajánlása a Portal tudnivalók a magas cpu-felhasználás jelenhet meg. Tekintse meg az egyes is állíthatja figyelők [metrikák](web-sites-monitor.md). A CPU-használat a ellenőrzésekor a [Azure Portal irányítópultján](../application-insights/app-insights-web-monitor-performance.md), ellenőrizze a maximális processzor maradjon le a maximális értékeket.
Ha úgy véli, hogy az alkalmazás túl sok CPU is használja, és miért nem magyarázni, ismerje meg, hogy a node-alkalmazás is készíthet profilt.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>A node-alkalmazás az Azure App Service-ben V8-Profiler profilkészítés

Például tegyük fel, hogy a hello world alkalmazás, amelyet szeretne profil a következőképpen:

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

Látogasson el a hibakeresési konzolt https://yoursite.scm.azurewebsites.net/DebugConsole

Nyissa meg a site/wwwroot könyvtárba. Az alábbi példában látható módon jelenik meg egy parancssort:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Futtassa a parancsot `npm install v8-profiler`.

Ez a parancs telepíti a csomópont alatt v8-profiler\_modulok könyvtárat és annak minden függőségét.
Most szerkessze a server.js az alkalmazás profilját.

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

Az előző kód profilokat a WriteConsoleLog működik, és majd a profil kimenet fájlba ír, a "profile.cpuprofile" a hely wwwroot alatt. Az alkalmazás egy kérelmet küld. Láthatja, hogy a hely wwwroot alapján létrehozott "profile.cpuprofile" fájl.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Töltse le ezt a fájlt, és nyissa meg a Chrome F12 eszközökkel. Nyomja le az F12 billentyű a Chrome-ban, majd válassza a **profilok** fülre. Válassza ki a **terhelés** gombra. Válassza ki a letöltött profile.cpuprofile fájlt. Kattintson a profil épp.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Láthatja, hogy a WriteConsoleLog függvény által felhasznált 95 %-ában. A kimenet a pontos sorok számának és a hibát okozó forrásfájlokat is megjeleníti.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A node-alkalmazás túl sok memóriát is használja.

Ha az alkalmazás túl sok memóriát is használja, lásd: az értesítés az Azure App Service kapcsolatos magas memóriahasználat a portálon. Beállíthat figyelők tekintse meg az egyes [metrikák](web-sites-monitor.md). A memóriahasználat a ellenőrzésekor a [Azure Portal irányítópultján](../application-insights/app-insights-web-monitor-performance.md), ügyeljen arra, hogy ellenőrizze a maximális memóriát maradjon le a maximális értékeket.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Észlelését és a node.js-hez készült halommemória összehasonlítása

Használhat [csomópont-memwatch](https://github.com/lloyd/node-memwatch) elveszít segít azonosítani a memória.
Telepíthet `memwatch` hasonlóan v8-profiler és a Szerkesztés és összehasonlítás rögzítése a kód által kezelt halommemóriákból pillanatnyilag azonosíthatja a memória elveszít az alkalmazásban.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>Saját node.exe fájlok vannak első véletlenszerűen leállított

Néhány oka miért node.exe leállításakor véletlenszerűen:

1. Az alkalmazás szűrész nem kezelt kivételek – jelölőnégyzet d:\\otthoni\\LogFiles\\alkalmazás\\a kivétel lépett fel a részletes naplózás – errors.txt fájlt. Ez a fájl rendelkezik a híváslánc hibakeresése és az alkalmazás javítása érdekében.
2. Az alkalmazás túl sok memóriát, amely befolyásolja az első lépésektől más folyamatok is használja. Ha az összes virtuális gép memória közel 100 %-os, a node.exe sikerült le fognak állni a folyamat által. Folyamatkezelő megszakítja az egyes folyamatok, hogy a más folyamatok segítségével is némi munkát. A probléma megoldásához, az alkalmazás a memóriavesztéssel profilt. Ha az alkalmazás nagy mennyiségű memóriát, vertikális felskálázás legfeljebb egy nagyobb méretű VM (ami növeli a virtuális gép számára elérhető RAM) igényel.

### <a name="my-node-application-does-not-start"></a>A node-alkalmazás nem indul el

Ha az alkalmazás 500-as hibák ad vissza, amikor elkezdi, néhány oka lehet:

1. NODE.exe jelenleg nem található a megfelelő helyre. Ellenőrizze a nodeProcessCommandLine beállítását.
2. Fő parancsfájlba fájl nem található a megfelelő helyen található. Ellenőrizze a web.config, és ellenőrizze, hogy a kezelők szakaszában a fő parancsfájlba fájl neve megegyezik a fő parancsfájlt.
3. Web.config konfigurációja helytelen – ellenőrizze a beállításokat nevek /.
4. Hidegindítási – az alkalmazás túl sokáig tart elindításához. Ha az alkalmazás több időt vesz igénybe, mint (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 másodperc iisnode adja vissza egy 500-as hiba. Növelje az alkalmazás kezdete iisnode túllépik az időkorlátot, és vissza az 500-as hiba elkerülése érdekében megfelelően ezek a beállítások értékeit.

### <a name="my-node-application-crashed"></a>Összeomlott a node-alkalmazás

Az alkalmazás szűrész nem kezelt kivételek – jelölőnégyzet `d:\\home\\LogFiles\\Application\\logging-errors.txt` a kivétel lépett fel a fájlt a részletekért. Ez a fájl rendelkezik diagnosztizálhatja és megoldhatja az alkalmazás segítségével a híváslánc.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>Saját node-alkalmazás indítása (hideg indítás) túl sok időt vesz igénybe.

Mennyi ideig alkalmazás kezdési idejének gyakori oka a csomóponton lévő fájlok nagy számú\_modulok. Az alkalmazás megpróbálja betölteni ezeket a fájlokat a legtöbb indításakor. Alapértelmezés szerint az Azure App Service-ben a hálózati megosztáson tárolt fájlok óta számos fájlok betöltése időbe telhet.
A gyorsabb, ez a folyamat egyes megoldások a következők:

1. Győződjön meg arról, hogy rendelkezik egy egybesimított függőségi szerkezetének és ismétlődő függőségek npm3 használatával a modulok telepítéséhez.
2. Megpróbálja Lusta betölteni a csomópont\_modulokat, és nem tölthető be összes alkalmazás indításakor. A Lusta terhelés modulokhoz require('module') hívása kell arról, amikor ténylegesen szüksége van a modul modul kód első végrehajtása előtt a függvényen belül.
3. Az Azure App Service egy helyi gyorsítótár nevű funkciót kínál. Ez a funkció másolja át a tartalmat a hálózati megosztáshoz a helyi lemezre a virtuális gépen. Mivel a fájlok helyi, a betöltési időt csomópont\_modulok, sokkal gyorsabb.

## <a name="iisnode-http-status-and-substatus"></a>Az IISNODE http-állapot és a részállapot

A `cnodeconstants` [forrásfájl](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) listák az összes a lehetséges állapota/substatus kombinációk iisnode adhat vissza egy hiba miatt.

Sikertelen kérelmek Eseménytárolási win32 hibakód megtekintéséhez az alkalmazás engedélyezése (lehet, hogy engedélyezi a nem éles helyek a megfelelő teljesítmény biztosítása érdekében csak a sikertelen kérelmek Eseménytárolási).

| HTTP-állapot | HTTP-Substatus | Lehetséges oka? |
| --- | --- | --- |
| 500 |1000 |Hiba történt az IISNODE irányuló kérelem zahájeno probléma – ellenőrizze, hogy ha node.exe lett elindítva. NODE.exe indításakor sikerült összeomlott. Ellenőrizze a hibákat a web.config konfigurációját. |
| 500 |1001 |-Win32Error 0x2 - alkalmazás nem válaszol az URL-címre. Ellenőrizze az URL-újraírási szabályok vagy ellenőrzést, ha az express alkalmazást a megfelelő útvonalak rendelkezik. -Win32Error 0x6d – nevesített cső foglalt – Node.exe van nem fogadja a kéréseket, a függőleges vonal elfoglalva. Ellenőrizze a magas cpu-használatot. – Egyéb hibák – ellenőrizze, hogy ha node.exe összeomlott. |
| 500 |1002 |NODE.exe összeomlott – d: Ellenőrizze\\otthoni\\LogFiles\\naplózás – errors.txt híváslánc számára. |
| 500 |1003 |Függőleges vonal konfigurációs probléma – a nevesített cső konfigurációja helytelen. |
| 500 |1004-1018 |Valamilyen hiba történt a kérelem küldése vagy a válasz és-tárolókról node.exe feldolgozásakor. Ellenőrizze, hogy a node.exe összeomlott. Ellenőrizze a d:\\otthoni\\LogFiles\\naplózás – errors.txt híváslánc számára. |
| 503 |1000 |Nincs elég memória lefoglalása több nevesített cső kapcsolatok. Miért érdemes az alkalmazás sok memóriát is használja ezt az ellenőrzést. Ellenőrizze a maxConcurrentRequestsPerProcess beállítás értékét. Ha nem végtelen, hogy hány kérésnek, növelje az ezt az értéket, hogy ezt a hibát. |
| 503 |1001 |Kérelem nem sikerült továbbítani a node.exe, mert az alkalmazás mindig újraindul. Miután az alkalmazást a rendszer újrahasznosítja, általában kérelmek kell kiszolgálni. |
| 503 |1002 |Ellenőrzés win32 hibakód tényleges ok miatt – a kérelem nem sikerült továbbítani a egy node.exe. |
| 503 |1003 |Nevesített cső túl elfoglalt – győződjön meg arról, ha node.exe fogyassza túlzott CPU |

NODE.exe van egy nevű beállítás `NODE_PENDING_PIPE_INSTANCES`. Az Azure App Service-ben ez az érték 5000-es értéke. Ami azt jelenti, hogy node.exe is 5000-es kérelmek fogadásához egy időben a nevesített cső. Ez az érték esetén a legtöbb csomópont-alkalmazásokhoz az Azure App Service-ben futó használatos kell lennie. Nem kell megjelennie 503.1003 az Azure App Service nagy értéke miatt a `NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>További erőforrások

Az alábbi hivatkozásokból tudhat meg többet az Azure App Service node.js-alkalmazások.

* [Ismerkedés a Node.js-webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started-nodejs.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](app-service-web-tutorial-nodejs-mongodb-app.md)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Az Azure App Service Web Apps: NODE.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js fejlesztői központ](../nodejs-use-node-modules-azure-apps.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)
---
title: A Node.js gyakorlati tanácsok és hibaelhárítás
description: Ismerje meg az Azure App Service-ben futó Node.js alkalmazások ajánlott eljárásokat és hibaelhárítási lépéseit.
author: msangapu-msft
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.devlang: nodejs
ms.topic: article
ms.date: 11/09/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 682884d11b298a97e27056af3c10802dfd410e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430566"
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-app-service-windows"></a>Gyakorlati tanácsok és hibaelhárítási útmutató az Azure App Service Windows csomópontalkalmazásaihoz

Ebben a cikkben az Azure App Service szolgáltatásban (iisnode-mal) futó [csomópontalkalmazások](app-service-web-get-started-nodejs.md) ajánlott eljárásokat és hibaelhárítási lépéseket ismerhet [isznoddal.](https://github.com/azure/iisnode)

> [!WARNING]
> Legyen óvatos, ha hibaelhárítási lépéseket használ az éles webhelyen. Javasoljuk, hogy az alkalmazás hibaelhárítása egy nem éles beállítás, például az átmeneti tárolóhely, és ha a probléma megoldódott, cserélje ki az átmeneti tárolóhelyet az éles tárolóhely.
>

## <a name="iisnode-configuration"></a>IISNODE-konfiguráció

Ez [a sémafájl](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) az iisnode-hoz konfigurálható összes beállítást megjeleníti. Az alkalmazás hoz hasznos beállítások közül néhány:

### <a name="nodeprocesscountperapplication"></a>nodeProcessCountPerApplication alkalmazás

Ez a beállítás szabályozza az IIS-alkalmazásonként elindított csomópontfolyamatok számát. Az alapértelmezett érték az 1. Az érték 0-ra való módosításával annyi node.exes- értéket indíthat el, ahány virtuális gép vCPU-száma. Az ajánlott érték 0 a legtöbb alkalmazás, így használhatja az összes vCPU-k a számítógépen. A Node.exe egyszálas, így egy node.exe legfeljebb 1 vCPU-t fogyaszt. A maximális teljesítmény a csomópontalkalmazáson kívül, szeretné használni az összes vCPU-k.

### <a name="nodeprocesscommandline"></a>nodeProcessCommandLine vonal

Ez a beállítás szabályozza a node.exe elérési útját. Ezt az értéket beállíthatja úgy, hogy a node.exe verzióra mutasson.

### <a name="maxconcurrentrequestsperprocess"></a>maxConcurrentRequestsPerProcess

Ez a beállítás szabályozza az iisnode által az egyes node.exe-nek küldött egyidejű kérelmek maximális számát. Az Azure App Service-ben az alapértelmezett érték az Infinite. Az értéket attól függően konfigurálhatja, hogy az alkalmazás hány kérelmet kap, és hogy az alkalmazás milyen gyorsan dolgozza fel az egyes kérelmeket.

### <a name="maxnamedpipeconnectionretry"></a>maxNamedPipeConnectionRetry

Ez a beállítás azt szabályozza, hogy az iisnode-újrapróbálkozások legfeljebb hányszor kíséreljék meg a kapcsolatot a névvel ellátott csőben, hogy a kérelmeket a node.exe fájlba küldjék. Ez a beállítás a nevű PipeConnectionRetryDelay-lel együtt határozza meg az isznode-on belüli egyes kérelmek teljes időtúllépését. Az alapértelmezett érték 200 az Azure App Service-en. Teljes időtúllépés másodpercben = (maxNamedPipeConnectionRetry \* nevűPipeConnectionRetryDelay) / 1000

### <a name="namedpipeconnectionretrydelay"></a>namedPipeConnectionRetryDelay

Ez a beállítás határozza meg, hogy az egyes újrapróbálkozások között mennyi ideig (ms-ban) várjon az isznode, hogy a kérelmet a node.exe-nek küldje el a névvel ellátott cső fölé. Az alapértelmezett érték 250 ms.
Teljes időtúllépés másodpercben = (maxNamedPipeConnectionRetry \* nevűPipeConnectionRetryDelay) / 1000

Alapértelmezés szerint az Azure App Service iisnode teljes időjárata 200 \* 250 ms = 50 másodperc.

### <a name="logdirectory"></a>logKönyvtár

Ez a beállítás vezérli azt a könyvtárat, ahol az iisnode naplók stdout/stderr. Az alapértelmezett érték az iisnode, amely a fő parancsfájlkönyvtárhoz képest van (könyvtár, ahol a fő server.js van jelen)

### <a name="debuggerextensiondll"></a>hibakeresőKiterjesztés

Ez a beállítás határozza meg, hogy a csomópont-felügyelő isznode melyik verzióját használja a csomópontalkalmazás hibakereséséhez. Jelenleg az iisnode-inspector-0.7.3.dll és az iisnode-inspector.dll az egyetlen két érvényes érték ehhez a beállításhoz. Az alapértelmezett érték az iisnode-inspector-0.7.3.dll. Az iisnode-inspector-0.7.3.dll verzió csomópont-ellenőr-0.7.3-at használ, és webszoftver-szoftvercsatornákat használ. Engedélyezze a webszoftver-szoftvercsatornákat az Azure webappon ennek a verziónak a használatához. További <https://ranjithblogs.azurewebsites.net/?p=98> részletek az iisnode konfigurálásáról az új csomópont-felügyelő használatára.

### <a name="flushresponse"></a>flushResponse (flushResponse)

Az IIS alapértelmezett viselkedése az, hogy a kiürítés előtt legfeljebb 4 MB-ig puffereli a válaszadatokat, vagy a válasz végéig , attól függően, hogy melyik következik be előbb. Az iisnode konfigurációs beállítást kínál a viselkedés felülbírálásához: a válaszentitás törzsének egy töredékének kiürítéséhez, amint iisnode/@flushResponse az iisnode megkapja azt a node.exe fájltól, a web.config attribútumát "true" értékre kell állítania:

```xml
<configuration>
    <system.webServer>
        <!-- ... -->
        <iisnode flushResponse="true" />
    </system.webServer>
</configuration>
```

Engedélyezze a válaszentitás testének minden töredékének kiürítését, ami növeli a teljesítményterhelést, ami ~5%-kal csökkenti a rendszer átviteli teljesítményét (a 0.1.13-as értéktől). A legjobb, ha ezt a beállítást csak a válaszstreamelést igénylő végpontokra szeretné kiterjeszteni (például a `<location>` web.config elemének használatával)

Ezenkívül a streamelési alkalmazások esetében az iisnode-kezelő válaszPufferLimit beállítását is 0-ra kell állítania.

```xml
<handlers>
    <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>
</handlers>
```

### <a name="watchedfiles"></a>watchedFiles

Pontosvesszővel elválasztott lista a módosításokat figyelő fájlokról. A fájl bármilyen módosítása az alkalmazás újrahasznosítását eredményezi. Minden bejegyzés egy választható könyvtárnévből és egy kötelező fájlnévből áll, amely ahhoz a könyvtárhoz viszonyítva található, ahol a fő alkalmazásbelépési pont található. A helyettesítő karakterek csak a fájlnév részben engedélyezettek. Az alapértelmezett érték `*.js;iisnode.yml`

### <a name="recyclesignalenabled"></a>újrahasznosítottSignalEnabled

Az alapértelmezett érték a hamis. Ha engedélyezve van, a csomópontalkalmazás csatlakozhat egy elnevezett\_csőhöz (IISNODE CONTROL\_PIPE környezeti változó), és "újrahasznosító" üzenetet küldhet. Ez okozza a w3wp újrahasznosítása kecsesen.

### <a name="idlepageouttimeperiod"></a>idlePageOutTimePeriod

Az alapértelmezett érték 0, ami azt jelenti, hogy ez a szolgáltatás le van tiltva. Ha 0-nál nagyobb értékre van beállítva, az iisnode minden "idlePageOutTimePeriod" gyermekfolyamatát ezredmásodpercben kilapozta. Tekintse meg a [dokumentációt,](/windows/desktop/api/psapi/nf-psapi-emptyworkingset) hogy megtudja, mit jelent az oldalki. Ez a beállítás olyan alkalmazások esetében hasznos, amelyek nagy mennyiségű memóriát fogyasztanak, és időnként lemezre szeretnék lapozni a memóriát a RAM felszabadítása érdekében.

> [!WARNING]
> Legyen óvatos, amikor engedélyezi a következő konfigurációs beállításokat az éles alkalmazásokon. A javaslat az, hogy ne engedélyezze őket az élő éles alkalmazások.
>

### <a name="debugheaderenabled"></a>debugHeaderEnabled

Az alapértelmezett érték a hamis. Ha értéke igaz, az iisnode `iisnode-debug` egy HTTP-válaszfejlécet `iisnode-debug` ad hozzá minden http-válaszhoz, amelyet a fejlécérték url-nek küld. Az egyes diagnosztikai információk az URL-töredéket tekintve szerezhetők be, azonban a vizualizáció elérhető az URL-cím böngészőben való megnyitásával.

### <a name="loggingenabled"></a>naplózás engedélyezve

Ez a beállítás szabályozza az stdout és stderr iisnode általi naplózását. Az Iisnode rögzíti az stdout/stderr-t az általa indított és a "logDirectory" beállításban megadott könyvtárba írt csomópontfolyamatokból. Ha ez engedélyezve van, az alkalmazás naplót ír a fájlrendszerbe, és az alkalmazás által végzett naplózás mennyiségétől függően teljesítménybeli következményekkel járhat.

### <a name="deverrorsenabled"></a>devErrorsEnabled

Az alapértelmezett érték a hamis. Ha értéke igaz, az iisnode megjeleníti a HTTP állapotkódot és a Win32 hibakódot a böngészőben. A win32-kód hasznos bizonyos típusú problémák hibakeresésében.

### <a name="debuggingenabled-do-not-enable-on-live-production-site"></a>hibakeresésEngedélyezve (nem engedélyezi az élő éles környezetben)

Ez a beállítás vezérli a hibakeresési szolgáltatást. Az isznod integrálva van a csomópont-ellenőrrel. A beállítás engedélyezésével engedélyezheti a csomópontalkalmazás hibakeresését. A beállítás engedélyezésekor az iisnode csomópont-felügyelő fájlokat hoz létre a "debuggerVirtualDir" könyvtárban a csomópontalkalmazáselső hibakeresési kérésén. A csomópont-felügyelőt úgy töltheti be, hogy kérést küld a számára. `http://yoursite/server.js/debug` A hibakeresési URL-szegmenst a "debuggerPathSegment" beállítással szabályozhatja. Alapértelmezés szerint a debuggerPathSegment='debug'. Beállíthatja `debuggerPathSegment` például a GUID azonosítót, hogy mások nehezebben fedezhessék fel őket.

A hibakeresésről a Windows hibakereséséről további részletekért olvassa el [a Debug node.js alkalmazásokat](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) a Windows rendszeren.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Forgatókönyvek és javaslatok/hibaelhárítás

### <a name="my-node-application-is-making-excessive-outbound-calls"></a>A csomópontalkalmazás túlzott kimenő hívásokat hajt végre

Sok alkalmazás azt szeretné, hogy a kimenő kapcsolatok részeként a rendszeres működés. Például, ha egy kérelem érkezik, a csomópont alkalmazás szeretné felvenni a kapcsolatot a REST API máshol, és szerezzen be néhány információt a kérelem feldolgozásához. Http vagy https hívások hozadékához érdemes életben tartani a ügynököt. Használhatja az agentkeepalive modult életben tartani ügynökként, amikor ezeket a kimenő hívásokat hajt végre.

Az agentkeepalive modul biztosítja, hogy a szoftvercsatornák újra felhasználhatók az Azure webapp virtuális gépén. Új szoftvercsatorna létrehozása minden kimenő kérelem többlettöbbletet jelent az alkalmazás számára. Miután az alkalmazás újrafelhasználja a szoftvercsatornák at kimenő kérelmek biztosítja, hogy az alkalmazás nem lépi túl a virtuális gépenként lefoglalt maxSockets. Az Azure App Service javaslata az, hogy az agentKeepAlive maxSockets értékét összesen \* (4 példány node.exe 40 maxSockets/instance) 160 sockets virtuális gépenként.

Példa [ügynökKeepALive-konfigurációra:](https://www.npmjs.com/package/agentkeepalive)

```nodejs
let keepaliveAgent = new Agent({
    maxSockets: 40,
    maxFreeSockets: 10,
    timeout: 60000,
    keepAliveTimeout: 300000
});
```

> [!IMPORTANT]
> Ebben a példában feltételezi, hogy 4 node.exe fut a virtuális gépen. Ha a virtuális gépen más számú node.exe fut, ennek megfelelően módosítania kell a maxSockets beállítást.
>

#### <a name="my-node-application-is-consuming-too-much-cpu"></a>A csomópontalkalmazásom túl sok CPU-t fogyaszt

Előfordulhat, hogy az Azure App Service-től a portálon a magas cpu-fogyasztás javaslatot kap. Beállíthatja azt is, hogy a monitorok bizonyos [mutatókat figyeljék.](web-sites-monitor.md) Amikor az [Azure Portal irányítópultján](../azure-monitor/app/web-monitor-performance.md)ellenőrzi a CPU-használatot, ellenőrizze a CPU MAX értékeit, hogy ne maradjon le a csúcsértékekről.
Ha úgy gondolja, hogy az alkalmazás túl sok CPU-t használ, és nem tudja megmagyarázni, hogy miért, profillal láthatja a csomópontalkalmazást, hogy megtudja.

#### <a name="profiling-your-node-application-on-azure-app-service-with-v8-profiler"></a>A csomópontalkalmazás profilkészítése az Azure App Service-en a V8-Profiler segítségével

Tegyük fel például, hogy van egy hello world alkalmazása, amelyet a következőképpen szeretne profilozni:

```nodejs
const http = require('http');
function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
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

Ugrás a Hibakeresési konzol webhelyére`https://yoursite.scm.azurewebsites.net/DebugConsole`

Lépjen be a webhely/wwwroot könyvtárba. Megjelenik egy parancssor, ahogy az a következő példában látható:

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Futtassa a következő parancsot: `npm install v8-profiler`.

Ez a parancs telepíti a v8-profiler\_csomópontmodulok könyvtár és annak összes függősége.
Most, szerkesztheti a server.js profilt az alkalmazás.

```nodejs
const http = require('http');
const profiler = require('v8-profiler');
const fs = require('fs');

function WriteConsoleLog() {
    for(let i=0;i<99999;++i) {
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

Az előző kód profilok a WriteConsoleLog függvényt, majd írja a profil kimenetet a "profile.cpuprofile" fájlt a webhely wwwroot. Küldjön egy kérelmet a jelentkezési lapnak. Megjelenik egy "profile.cpuprofile" fájl létre a webhely wwwroot.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Töltse le ezt a fájlt, és nyissa meg a Chrome F12 Eszközök. Nyomja le az F12 billentyűt a **Load** Chrome-on, majd válassza a **Profilok** lapot. Válassza ki a letöltött profile.cpuprofile fájlt. Kattintson az imént betöltött profilra.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Láthatja, hogy az idő 95%-át a WriteConsoleLog függvény felhasználta. A kimenet a pontos sorszámokat és forrásfájlokat is megjeleníti, amelyek a problémát okozták.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A csomópontalkalmazás túl sok memóriát fogyaszt

Ha az alkalmazás túl sok memóriát fogyaszt, megjelenik egy értesítés az Azure App Service-ből a portálon a magas memóriafelhasználásról. Beállíthatja, hogy a figyelők bizonyos [mutatókat figyeljék.](web-sites-monitor.md) Amikor az [Azure Portal irányítópultján](../azure-monitor/app/web-monitor-performance.md)ellenőrzi a memóriahasználatot, ellenőrizze a MAX memóriaértékeket, hogy ne maradjon le a csúcsértékekről.

#### <a name="leak-detection-and-heap-diff-for-nodejs"></a>Szivárgásészlelés és halomkülönbség a node.js fájlhoz

Használhatja [a node-memwatch-ot](https://github.com/lloyd/node-memwatch) a memóriavesztések azonosításához.
Telepítheti, `memwatch` mint a v8-profiler, és szerkesztheti a kódot, hogy rögzítse és diff a halom, hogy azonosítsa a memóriaszivárgás az alkalmazásban.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A node.exe-met véletlenszerűen ölik meg.

Több oka is van annak, hogy a node.exe-t véletlenszerűen leállították:

1. Az alkalmazás nem fogott kivételeket vet\\be\\–\\\\Ellenőrizze a d: home LogFiles Application logging-errors.txt fájlt a kidobott kivétel részleteiért. Ez a fájl a verem nyomkövetési segítségével debug és kijavítani az alkalmazást.
2. Az alkalmazás túl sok memóriát fogyaszt, ami hatással van más folyamatokra az első lépésektől. Ha a virtuális gép teljes memóriája megközelíti a 100%-ot, a node.exe-t a folyamatkezelő leölheti. A folyamatkezelő megöl néhány folyamatot, hogy más folyamatok lehetőséget kapjanak a munkára. A probléma megoldásához profilozza az alkalmazást memóriavesztésre. Ha az alkalmazás nagy mennyiségű memóriát igényel, egy nagyobb virtuális gépre skálázható (ami növeli a virtuális gép számára elérhető RAM-ot).

### <a name="my-node-application-does-not-start"></a>A csomópontalkalmazás nem indul el

Ha az alkalmazás 500 hibát ad vissza az indításkor, annak több oka is lehet:

1. A Node.exe nem a megfelelő helyen található. Ellenőrizze a nodeProcessCommandLine beállítást.
2. A fő parancsfájl nem a megfelelő helyen található. Ellenőrizze a web.config fájlt, és győződjön meg arról, hogy a kezelők szakaszban lévő fő parancsfájl neve megegyezik a fő parancsfájlfájllal.
3. A Web.config konfigurációja nem megfelelő – ellenőrizze a beállítások nevét/értékeit.
4. Hidegindítás – Az alkalmazás indítása túl sokáig tart. Ha az alkalmazás hosszabb időt vesz igénybe, mint (maxNamedPipeConnectionRetry \* nevűPipeConnectionRetryDelay) / 1000 másodperc, iisnode ad vissza 500 hiba. Növelje ezeknek a beállításoknak az értékeit, hogy megfeleljen az alkalmazás kezdési időpontjának, hogy megakadályozza az iisnode időtúllépésének és az 500-as hiba visszaadását.

### <a name="my-node-application-crashed"></a>Összeomlott a csomópontalkalmazásom

Az alkalmazás nem feledkező `d:\\home\\LogFiles\\Application\\logging-errors.txt` kivételeket vet be – Ellenőrizze a fájlt a kidobott kivétel részleteiért. Ez a fájl a verem nyomkövetési segítségével diagnosztizálni és kijavítani az alkalmazást.

### <a name="my-node-application-takes-too-much-time-to-start-cold-start"></a>A csomópontalkalmazás indítása túl sok időt vesz igénybe (Hidegindítás)

A hosszú alkalmazásindítási idők gyakori oka a\_csomópontmodulokban lévő fájlok nagy száma. Az alkalmazás megpróbálja betölteni a legtöbb ilyen fájlokat indításkor. Alapértelmezés szerint, mivel a fájlok az Azure App Service hálózati megosztásán tárolódnak, sok fájl betöltése időbe telhet.
Néhány megoldás, hogy ez a folyamat gyorsabb:

1. Győződjön meg arról, hogy egy lapos függőségi struktúra, és nem ismétlődő függőségek használatával npm3 a modulok telepítéséhez.
2. Próbálja meg lusta\_betölteni a csomópont modulokat, és nem tölti be az összes modult az alkalmazás indításakor. A Lusta terhelésmodulokhoz a require('module') hívásakkor történik meg, amikor ténylegesen szüksége van a modulra a funkción belül a modulkód első végrehajtása előtt.
3. Az Azure App Service egy helyi gyorsítótár nevű funkciót kínál. Ez a szolgáltatás másolja a tartalmat a hálózati megosztásról a helyi lemezre a virtuális gép. Mivel a fájlok helyiek, a csomópontmodulok\_betöltési ideje sokkal gyorsabb.

## <a name="iisnode-http-status-and-substatus"></a>IISNODE http állapota és alállapota

A `cnodeconstants` [forrásfájl](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) felsorolja az összes lehetséges állapot/alállapot kombinációt, amelyet az iisnode hiba miatt visszaadhat.

Engedélyezze a FREB-t az alkalmazáshoz, hogy lássa a win32 hibakódot (győződjön meg róla, hogy a FREB-t csak nem éles helyeken engedélyezi teljesítménybeli okokból).

| Http állapota | Http alállapot | Lehetséges ok? |
| --- | --- | --- |
| 500 |1000 |Probléma történt a kérelem iISNODE-nak történő elküldésével – Ellenőrizze, hogy a node.exe elindult-e. A Node.exe indításkor összeomlhatott. Ellenőrizze a web.config konfigurációját, hogy vannak-e hibák. |
| 500 |1001 |- Win32Error 0x2 - Az alkalmazás nem válaszol az URL-re. Ellenőrizze az URL-újraírási szabályokat, vagy ellenőrizze, hogy az expressz alkalmazás a megfelelő útvonalakat definiált-e. - Win32Error 0x6d – a neves cső foglalt – a Node.exe nem fogad el kéréseket, mert a cső foglalt. Ellenőrizze a magas cpu-használat. - Egyéb hibák - ellenőrizze, node.exe összeomlott. |
| 500 |1002 |Node.exe összeomlott - check\\\\d:\\home LogFiles logging-errors.txt a verem nyomkövetés. |
| 500 |1003 |Csőkonfigurációs probléma – A megnevezett csőkonfiguráció helytelen. |
| 500 |1004-1018 |Hiba történt a kérés elküldése vagy a válasz feldolgozása közben a node.exe-nek/ Ellenőrizze, hogy a node.exe összeomlott-e. check\\d:\\home\\LogFiles logging-errors.txt a veremnyomkövetéshez. |
| 503 |1000 |Nincs elég memória több elnevezett csőkapcsolat lefoglalásához. Ellenőrizze, hogy az alkalmazás miért fogyaszt annyi memóriát. Ellenőrizze a maxConcurrentRequestsPerProcess beállításértékét. Ha nem végtelen, és sok kéréssel rendelkezik, növelje ezt az értéket a hiba megelőzése érdekében. |
| 503 |1001 |A kérés nem küldhető el a node.exe programba, mert az alkalmazás újrahasznosítás. Az alkalmazás újrahasznosítása után a kérelmeket a szokásos módon kell kézbesíteni. |
| 503 |1002 |Ellenőrizze a win32 hibakódot a tényleges ok miatt – A kérés nem küldhető el egy node.exe-nek. |
| 503 |1003 |A névvel ellátott cső túl elfoglalt – Ellenőrizze, hogy a node.exe túlzott processzort fogyaszt-e |

A NODE.exe-nek `NODE_PENDING_PIPE_INSTANCES`van egy beállítása . Az Azure App Service-ben ez az érték 5000 értékre van állítva. Ami azt jelenti, hogy a node.exe egyszerre 5000 kérést fogadhat el a megnevezett csőben. Ez az érték elég jónak kell lennie az Azure App Service-en futó legtöbb csomópont-alkalmazás számára. Az 503.1003-as érték nem jelenik meg az Azure App Service szolgáltatásban, mert a`NODE_PENDING_PIPE_INSTANCES`

## <a name="more-resources"></a>További erőforrások

Kövesse ezeket a hivatkozásokat, ha többet szeretne megtudni a node.js alkalmazásokról az Azure App Service szolgáltatásban.

* [Ismerkedés a Node.js-webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started-nodejs.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](https://blogs.msdn.microsoft.com/azureossds/2018/08/03/debugging-node-js-apps-on-azure-app-services/)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js fejlesztői központ](../nodejs-use-node-modules-azure-apps.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)

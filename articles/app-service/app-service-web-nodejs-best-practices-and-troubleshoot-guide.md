---
title: "Ajánlott eljárások és hibaelhárítási útmutatójában csomópont alkalmazások Azure-webalkalmazásokban"
description: "Ismerje meg az ajánlott eljárásokról és a csomópont-alkalmazások az Azure Web Apps hibaelhárítási lépéseket."
services: app-service\web
documentationcenter: nodejs
author: ranjithr
manager: wadeh
editor: 
ms.assetid: 387ea217-7910-4468-8987-9a1022a99bef
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 06/06/2016
ms.author: ranjithr
ms.openlocfilehash: 8f39b5e6faf5f9121ec2abe347f50653c5c3e4f9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="best-practices-and-troubleshooting-guide-for-node-applications-on-azure-web-apps"></a>Ajánlott eljárások és hibaelhárítási útmutatójában csomópont alkalmazások Azure-webalkalmazásokban
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Ebből a cikkből megtudhatja, az ajánlott eljárásokról és a hibaelhárítási lépéseket [csomópont alkalmazások](app-service-web-get-started-nodejs.md) Azure webalkalmazás fut (a [iisnode](https://github.com/azure/iisnode)).

> [!WARNING]
> Körültekintően járjon el, ha a hibaelhárítási lépéseket a munkakörnyezeti helyet. Javasoljuk, hogy például az átmeneti helyet egy nem éles a beállítás az alkalmazás hibaelhárítása, és ha a probléma fennáll, felcserélni az átmeneti rendelkező az éles webalkalmazásra.
> 
> 

## <a name="iisnode-configuration"></a>Az IISNODE konfigurálása
Ez [sémafájl](https://github.com/Azure/iisnode/blob/master/src/config/iisnode_schema_x64.xml) jeleníti meg az iisnode-hoz konfigurált beállításokat. A beállításokat, akkor lehet hasznos, az alkalmazás a következők:

* nodeProcessCountPerApplication
  
    Ez a beállítás szabályozza, hogy az IIS alkalmazásonként működését csomópont folyamatok száma. Alapértelmezett értéke 1. Elindíthatja a lehető legtöbb node.exe a virtuális gép magok száma, úgy, hogy ez 0-ra. Javasolt érték 0 a legtöbb alkalmazás, használhatja az összes mag a számítógépen. NODE.exe az egyetlen, egy node.exe fogyaszt legfeljebb 1 mag, valamint kívül az összes mag használatára történő kívánt csomópont alkalmazás maximális teljesítménye érdekében szabadszálas.
* nodeProcessCommandLine
  
    Ez a beállítás szabályozza a node.exe elérési útja. Beállíthatja, hogy ezt az értéket a node.exe verziójára mutasson.
* maxConcurrentRequestsPerProcess
  
    Ez a beállítás minden node.exe iisnode által küldött egyidejű kérelmek maximális számát szabályozza. Az azure webalkalmazás az alapértelmezett érték a végtelen. Nem kell foglalkoznia az ezt a beállítást. Azure webalkalmazás kívül a az alapértelmezett érték: 1024. Érdemes a beállítás attól függően, hogy hány kér az alkalmazás lekérdezi, és hogy milyen gyorsan az alkalmazás minden kérést dolgoz fel.
* maxNamedPipeConnectionRetry
  
    Ez a beállítás az iisnode visszaállítja a nevesített csövön keresztül elküldeni a kérelmet node.exe élő kapcsolatot a maximálisan megengedett számú szabályozza. Ez a beállítás namedPipeConnectionRetryDelay együtt meghatározza, hogy az egyes kérelmek iisnode belül a teljes időtúllépés. Alapértelmezett érték 200 található Azure webalkalmazás. Időtúllépés másodpercben teljes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
* namedPipeConnectionRetryDelay
  
    Ez a beállítás vezérli ideje (ms) iisnode mennyisége megvárja, hogy minden kérést küldeni az node.exe a nevesített csövön keresztül újrapróbálkozási között. Alapértelmezett érték: 250ms.
    Időtúllépés másodpercben teljes = (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000
  
    Alapértelmezés szerint az iisnode azure webalkalmazás a teljes időtúllépés értéke 200 \* 250ms = 50 másodperc.
* logDirectory
  
    Ez a beállítás szabályozza a könyvtárban, ahol a iisnode stdout/stderr naplózza. Alapértelmezett érték: iisnode, amely relatív a fő parancsfájlba könyvtár (könyvtárat, amelyben fő server.js jelen)
* debuggerExtensionDll
  
    Ez a beállítás határozza meg, melyik verzióját a node-inspector iisnode fogja használni, amikor a csomópont-alkalmazásban. Az iisnode-inspector-0.7.3.dll és az iisnode-inspector.dll jelenleg ez a beállítás csak 2 érvényes értékei. Alapértelmezett érték: az iisnode-inspector-0.7.3.dll. az iisnode-inspector-0.7.3.dll verzió csomópont-inspector-0.7.3 használ, és websocket elemeket, használ, ezért szüksége lesz ahhoz, hogy a jelenlegi verzióját használják az azure webalkalmazás websocket elemeket. Lásd: <http://www.ranjithr.com/?p=98> iisnode az új node-Inspector segítségével történő konfigurálásával kapcsolatos további részletekért.
* flushResponse
  
    Az alapértelmezett IIS működése, hogy azt puffereli érkezett válasz adatait mentése 4 MB kiürítése előtt, illetve a válasz végéig amelyik előbb következik be. az iisnode Ez a viselkedés felülbírálásához a konfigurációs beállításokat kínál: kiüríteni válasz entitástörzsében egy kódrészletet, amint iisnode kap a node.exe, be kell állítani a iisnode/@flushResponse a Web.config fájlban a "true" attribútum:
  
    ```
    <configuration>    
        <system.webServer>    
            <!-- ... -->    
            <iisnode flushResponse="true" />    
        </system.webServer>    
    </configuration>
    ```
  
    Minden válasz entitástörzsében kódrészletet a könyvelési engedélyezése lassítja a, amely csökkenti az átviteli sebessége a rendszer (az v0.1.13), ~ 5 %-kal hatókörének ezt a beállítást csak adatfolyam-választ igénylő végpontok a legjobb (pl. használatával a <location>a Web.config elemben)
  
    Továbbá a az alkalmazások, akkor is responseBufferLimit az iisnode leíró 0 értékre állítva.
  
    ```
    <handlers>    
        <add name="iisnode" path="app.js" verb="\*" modules="iisnode" responseBufferLimit="0"/>    
    </handlers>
    ```
* watchedFiles
  
    Ez az fog kell figyelt módosítások fájlok egy pontosvesszővel elválasztott listája. Egy fájl módosításának hatására az alkalmazás újrahasznosítása. Mindegyik bejegyzés egy választható könyvtár nevét és a szükséges fájl nevét, amelyek a könyvtárat, amelyben az alkalmazás fő belépési pont található viszonyítva áll. A fájl neve funkciója csak helyettesítő karakterek megengedettek. Alapértelmezett érték "\*. js;web.config"
* recycleSignalEnabled
  
    Alapértelmezett értéke hamis. Ha engedélyezve van, a csomópont alkalmazás csatlakozhat egy nevesített csövet (környezeti változót az IISNODE\_vezérlő\_CSŐ) és "újrahasznosítást" üzenet. Ennek hatására a w3wp szabályosan újrahasznosítása.
* idlePageOutTimePeriod
  
    Alapértelmezett érték 0, ami azt jelenti, hogy ez a funkció le van tiltva. Ha értéke egy értéket 0-nál nagyobbnak, iisnode fog lapon minden gyermek folyamatainak ki minden "idlePageOutTimePeriod" ezredmásodperc. Ha szeretné megtudni, milyen lapon azt jelenti, hogy ki, tekintse meg a [dokumentáció](https://msdn.microsoft.com/library/windows/desktop/ms682606.aspx). Ez a beállítás hasznos, ha alkalmazásokat, amelyek nagy mennyiségű memóriát használnak, és szeretne pageout memória lemezre alkalmanként szabadítson fel némi RAM lesz.

> [!WARNING]
> Legyen körültekintő, ha engedélyezi az éles környezetben a következő konfigurációs beállításait. Javasoljuk, hogy engedélyezze azokat éles alkalmazásokban.
> 
> 

* debugHeaderEnabled
  
    Az alapértelmezett értéke hamis. Ha értéke igaz, az iisnode felveszi egy HTTP válasz fejléc az iisnode-debug minden HTTP-válasz küld az iisnode-debug fejléc értéke egy URL-címet. Egyes adatra diagnosztikai adatokat is a URL-cím töredéke megtekintésével, de sok jobb képi megjelenítés érik el az URL-cím megnyitása a böngészőben.
* loggingEnabled
  
    Ez a beállítás az iisnode által az stdout és az stderr naplózását szabályozza. Az Iisnode stdout/stderr csomópont folyamatok elindítja azt rögzíti, és a "logDirectory" beállításban megadott könyvtárban írni. A beállítás engedélyezése, az alkalmazás fogja kell írása naplók a fájlrendszerhez, és attól függően, hogy az alkalmazás által végzett naplózás mennyisége, a teljesítményre gyakorolt hatása lehet.
* devErrorsEnabled
  
    Alapértelmezett értéke hamis. Ha értéke igaz, az iisnode megjeleníti a HTTP-állapotkód és a Win32-hibakód: a böngésző. A win32 kódban megoldani a problémákat bizonyos típusú hasznos lesz.
* debuggingEnabled (ne engedélyezze az éles hely)
  
    Ez a beállítás szabályozza a hibakeresési funkciója. Az Iisnode integrálva van a node-Inspector használatával. Ha engedélyezi ezt a beállítást, engedélyezi a csomópont alkalmazás hibakeresést. Ha engedélyezi ezt a beállítást, a iisnode a csomópont alkalmazás felé irányuló első debug kérelem a "debuggerVirtualDir" könyvtárban található a szükséges a node-inspector fájl lesz elrendezés. A node-inspector betöltése http://yoursite/server.js/debug kérelmet küld. A hibakeresési URL-szegmenseket "debuggerPathSegment" beállítással szabályozható. Alapértelmezett debuggerPathSegment által = "debug". Állíthat egy GUID, például úgy, hogy megnehezíti mások számára felderítését.
  
    Ennek ellenőrzéséhez [hivatkozás](https://tomasz.janczuk.org/2011/11/debug-nodejs-applications-on-windows.html) kapcsolatban további részleteket a hibakeresést.

## <a name="scenarios-and-recommendationstroubleshooting"></a>Forgatókönyvek és javaslatok/hibaelhárítása
### <a name="my-node-application-is-making-too-many-outbound-calls"></a>A csomópont-alkalmazás, hogy így túl sok kimenő hívásokat.
Számos alkalmazás szeretné kimenő kapcsolatok létrehozása a rendszeres művelet részeként. Például ha a kérelem érkezik, a csomópont app szeretné lépjen kapcsolatba a REST API máshol, és néhány adatra feldolgozni a kérelmet. Szeretné használni kívánt megtartása életben ügynök http vagy https hívása esetén. Például használhatja a agentkeepalive modul a keep-alive megbízottként ezek kimenő hívása esetén. Ez biztosítja, hogy, hogy a sockets újra felhasználja a rendszer a virtuális gép azure webalkalmazás a és a kimenő kérelmek új szoftvercsatornák létrehozásának adódó terhelését. Is ez biztosítja, hogy kevesebb szoftvercsatornák számát segítségével számos kimenő kéréseket, és ezért nem lehet a kiosztott virtuális gépenként maxsocket. Azure-webalkalmazás ajánlást lenne, virtuális gépenként 160 sockets összesen agentKeepAlive maxsocket értékre állítja be. Ez azt jelenti, hogy ha 4 node.exe a virtuális gépen, akkor szeretné beállítása a agentKeepAlive maxsocket node.exe teljes virtuális gépenként 160 pedig 40.

Példa [agentKeepALive](https://www.npmjs.com/package/agentkeepalive) konfiguráció:

```
var keepaliveAgent = new Agent({    
    maxSockets: 40,    
    maxFreeSockets: 10,    
    timeout: 60000,    
    keepAliveTimeout: 300000    
});
```

Ez a példa feltételezi, hogy rendelkezik-e a virtuális gépen 4 node.exe. Ha a virtuális gépen node.exe eltérő számú, akkor módosítsa a beállítást ennek megfelelően maxsocket.

### <a name="my-node-application-is-consuming-too-much-cpu"></a>A csomópont-alkalmazás által felhasznált túl sok CPU.
A portál magas cpu-felhasználás kapcsolatos az Azure-webalkalmazás valószínűleg kap egy javaslatot. Figyelendő egyes figyelők is beállíthatja [metrikák](web-sites-monitor.md). Ha a CPU-használat ellenőrzése a következőn: a [Azure Portal irányítópult](../application-insights/app-insights-web-monitor-performance.md), tekintse meg a maximális értékek CPU, hogy nem hagy ki ki a maximális értékeket.
Amennyiben úgy véli, hogy az alkalmazás által felhasznált túl sok CPU, és miért nem ismertetik esetben szüksége lesz profilhoz, a node.js-alkalmazásokban.

### 
#### <a name="profiling-your-node-application-on-azure-webapps-with-v8-profiler"></a>A csomópont alkalmazás V8-Profilkészítő rendelkező azure webalkalmazás profilkészítés
Például lehet mondja ki a hello world alkalmazás kívánt profil alább látható módon:

```
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

Ugrás az scm hely https://yoursite.scm.azurewebsites.net/DebugConsole

Alább látható módon jelenik meg egy parancssort. Nyissa meg a hely/wwwroot könyvtárba

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_install_v8.png)

Futtassa a parancsot "npm telepítés v8-Profilkészítő"

Ez telepítse v8-Profilkészítő csomópont alatt\_modulok directory és az összes függősége.
Most szerkessze a server.js profil az alkalmazáshoz.

```
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

A fenti módosítások WriteConsoleLog függvény profilt, és a majd a profil kimeneti írni a hely wwwroot "profile.cpuprofile" fájlt. Az alkalmazás kérelmet küld. A hely wwwroot alapján létrehozott "profile.cpuprofile" fájl jelenik meg.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/scm_profile.cpuprofile.png)

Töltse le a fájlt, és nyissa meg a fájlt a Chrome F12 eszközökkel kell. Kattintson az F12 billentyű lenyomása a chrome, majd kattintson a "Profilok lap". Kattintson a "Load" gombra. Válassza ki az imént letöltött profile.cpuprofile fájlt. Kattintson a profil épp.

![](./media/app-service-web-nodejs-best-practices-and-troubleshoot-guide/chrome_tools_view.png)

Látni fogja, hogy 95 %-ában felhasznált WriteConsoleLog függvény alább látható módon. Ez azt is bemutatja, a pontos sorok számát és a problémát okozó forrásfájlokat.

### <a name="my-node-application-is-consuming-too-much-memory"></a>A csomópont-alkalmazás által felhasznált túl sok memóriát.
A portál kapcsolatos leterheli a rendszermemóriát az Azure-webalkalmazás valószínűleg kap egy javaslatot. Figyelendő egyes figyelők is beállíthatja [metrikák](web-sites-monitor.md). Ha a memóriahasználat ellenőrzése a következőn: a [Azure Portal irányítópult](../application-insights/app-insights-web-monitor-performance.md), a memória tartozó maximális értékeket. részletekért tekintse meg, hogy nem hagy ki ki a maximális értékeket.

#### <a name="leak-detection-and-heap-diffing-for-nodejs"></a>A node.js felderítését és a halommemória Diffing szivárgás lépett fel
Használhat [csomópont-memwatch](https://github.com/lloyd/node-memwatch) segítségével azonosíthatja a memória-szivárgást.
Hasonlóan v8-Profilkészítő memwatch telepítheti, és szerkesztheti a kódot a rögzítési és a különbözeti azonosítására, a memória halommemóriákban-szivárgást az alkalmazásban.

### <a name="my-nodeexes-are-getting-killed-randomly"></a>A node.exe vannak első véletlenszerűen leállítása
Néhány oka miért Ez történhet:

1. Az alkalmazás szűrész nem kezelt kivételek – kérjük ellenőrzés d:\\otthoni\\naplófájlok\\alkalmazás\\a kivétel lépett fel a részletes naplózás-errors.txt fájlt. Ez a fájl rendelkezik a Veremkivonat, így ezt úgy javíthatja ki az alkalmazás ennek alapján.
2. Az alkalmazás által felhasznált túl sok memóriát, amely érinti a bevezetés más folyamatokkal. Ha az összes virtuális gép memória megközelíti a 100 %-os, a node.exe a folyamat-kezelő ahhoz, hogy más folyamatok néhány munkájuk is sikertelen lehet leállítani. A javítás érdekében győződjön meg arról, hogy az alkalmazás nem memóriaszivárgást, vagy ha alkalmazás valóban nagy mennyiségű memória használatára van szüksége, adjon vertikális felskálázás sokkal több RAM memóriával rendelkező egy nagyobb virtuális géphez.

### <a name="my-node-application-does-not-start"></a>A csomópont alkalmazás nem indul el
Az alkalmazás indításkor 500 hibát ad vissza, ha néhány oka lehet:

1. NODE.exe nincs jelen a megfelelő helyen. Jelölje be nodeProcessCommandLine beállítást.
2. Fő parancsfájl verziója nem található megfelelő helyére. Ellenőrizze a Web.config fájlban, és győződjön meg arról, hogy a kezelők szakaszában a fő parancsfájl neve megegyezik a fő parancsfájlt.
3. Web.config konfigurációra nincs megfelelő – ellenőrizze a beállításokat neveket és-értékek.
4. Hidegindítás – az alkalmazás túl sokáig tart el. Ha az alkalmazás tovább tart, mint (maxNamedPipeConnectionRetry \* namedPipeConnectionRetryDelay) / 1000 másodperc, az iisnode 500 hibát ad vissza. Ezeket a beállításokat, az alkalmazás kezdete időtúllépés törlését és az 500 hiba az iisnode megelőzése érdekében kereséséhez értékek növelése.

### <a name="my-node-application-crashed"></a>A csomópont alkalmazás összeomlott
Az alkalmazás szűrész nem kezelt kivételek – kérjük ellenőrzés d:\\otthoni\\naplófájlok\\alkalmazás\\a kivétel lépett fel a részletes naplózás-errors.txt fájlt. Ez a fájl rendelkezik a Veremkivonat, így ezt úgy javíthatja ki az alkalmazás ennek alapján.

### <a name="my-node-application-takes-too-much-time-to-startup-cold-start"></a>A csomópont alkalmazás túl sok időt vesz igénybe el (Cold indítás)
Ennek leggyakoribb oka az, hogy az alkalmazás rendelkezik-e nagy mennyiségű csomópontjában fájlok\_modulok és az alkalmazás megkísérli betölteni ezeket a fájlokat a legtöbb indításakor. Alapértelmezés szerint a fájlok találhatók, a hálózati megosztáson lévő Azure webalkalmazás, mivel betöltése sok fájlok némi időbe telhet.
Néhány megoldások gyorsabb legyen ez a következők:

1. Ellenőrizze, hogy az egyszerű függőségi struktúra és a nem ismétlődő függőség npm3 használatával telepítse a modulokat.
2. Lusta változtassa meg a csomópont betöltése\_modulok, és nem indításkor modulok mindegyikének történik. Ez azt jelenti, hogy require('module') hívása ténylegesen szükség esetén próbálja használni a modul a függvényen belülről kell elvégezni.
3. Az Azure webalkalmazás kínál a helyi gyorsítótár nevezett szolgáltatással. Ez a szolgáltatás másolja át a tartalmat a hálózati megosztáshoz a helyi lemez a virtuális Gépen. Mivel a fájlok helyi, a betöltési idő csomópont\_modulok sokkal gyorsabb.

## <a name="iisnode-http-status-and-substatus"></a>Az IISNODE http-állapot és részállapot
Ez [forrásfájl](https://github.com/Azure/iisnode/blob/master/src/iisnode/cnodeconstants.h) felsorolja az összes hiba esetén a lehetséges állapota/részállapot kombinációja iisnode lépjen vissza.

A win32 hibakód megtekintéséhez az alkalmazás FREB engedélyezése (Ellenőrizze, hogy csak a nem éles helyeken a teljesítményre vonatkozó megfontolásból FREB engedélyezi).

| HTTP-állapot | HTTP-SubStatus | Lehetséges ok? |
| --- | --- | --- |
| 500 |1000 |Hiba történt az egyes IISNODE kérelem terjesztéséhez probléma – ellenőrizze, hogy node.exe indult-e. Indításkor node.exe sikerült összeomlott. Ellenőrizze a web.config konfigurációs hibák. |
| 500 |1001 |-Win32Error 0x2 - alkalmazás az URL-cím nem válaszol. Ellenőrizze az URL-cím átdolgozás szabályok, vagy hogy rendelkezik-e a helyes definiált útvonalak az express alkalmazást. -Win32Error 0x6d – nevesített cső elfoglalva, – Node.exe nem fogad el kérelmeket, mert a cső elfoglalva. Ellenőrizze a nagy cpu-használat. -Más hibák – ellenőrizze, hogy ha node.exe összeomlott. |
| 500 |1002 |NODE.exe összeomlott – d: Ellenőrizze\\otthoni\\naplófájlok\\naplózási-errors.txt Veremkivonat a. |
| 500 |1003 |Konfigurációs probléma pipe – soha nem kell megjelennie a, de ha így tesz, a nevesített cső konfigurációja nem megfelelő. |
| 500 |1004-1018 |Néhány hiba történt a kérelem küldésekor, vagy node.exe és a válasz feldolgozását. Ellenőrizze, hogy node.exe összeomlott. Ellenőrizze a d:\\otthoni\\naplófájlok\\naplózási-errors.txt Veremkivonat a. |
| 503 |1000 |Foglaljon le több nevesített cső kapcsolat nem elegendő memória. Ellenőrizze, hogy miért az alkalmazás nem használ a sok memóriát. Ellenőrizze a maxConcurrentRequestsPerProcess beállítás értékét. Ha nem végtelen, és nagy mennyiségű kérést rendelkezik, a hiba az érték növelése. |
| 503 |1001 |Kérelem nem sikerült node.exe továbbítani, mert az alkalmazás újrahasznosítása van. Miután az alkalmazás rendelkezik felhasználását, kérelmek általában fel kell dolgozni. |
| 503 |1002 |Nem sikerült továbbítani a jelölőnégyzet win32 hibakód tényleges okból – kérelem egy node.exe számára. |
| 503 |1003 |Nevesített cső jelenleg túl elfoglalt – ellenőrizze, hogy ha a csomópont nem használ-e nagy mennyiségű Processzor |

Egy beállítás belül nevű csomópont NODE.exe\_FÜGGŐBEN\_CSŐ\_példányok. Azure webalkalmazás kívül alapértelmezés szerint ez az érték 4. Ez azt jelenti, hogy node.exe 4 kérés törléshez a nevesített csövön egyszerre. Az Azure-webalkalmazás Ez az érték 5000 van beállítva, és ennek az értéknek kell lennie elég jó azure webalkalmazás futó legtöbb csomópont alkalmazásokhoz. Nem megjelenik 503.1003 azure webalkalmazás mert nagy érték van a csomópont\_FÜGGŐBEN\_CSŐ\_példányok.  |

## <a name="more-resources"></a>További erőforrások
Az alábbi hivatkozásokból tudhat meg többet az Azure App Service a node.js-alkalmazások.

* [Ismerkedés a Node.js webalkalmazásokkal az Azure App Service-ben](app-service-web-get-started-nodejs.md)
* [A Node.js webalkalmazás hibakeresése az Azure App Service-ben](app-service-web-tutorial-nodejs-mongodb-app.md)
* [A Node.js modulok használata az Azure alkalmazásokkal](../nodejs-use-node-modules-azure-apps.md)
* [Azure App Service Web Apps: Node.js](https://blogs.msdn.microsoft.com/silverlining/2012/06/14/windows-azure-websites-node-js/)
* [Node.js fejlesztői központ](../nodejs-use-node-modules-azure-apps.md)
* [A Szupertitkos Kudu hibakereső konzol felfedezése](https://azure.microsoft.com/documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/)


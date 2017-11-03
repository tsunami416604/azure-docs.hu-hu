---
title: "Az App Service web app teljesítménycsökkenés |} Microsoft Docs"
description: "Ez a cikk segít az Azure App Service lassú web app teljesítménnyel kapcsolatos problémák hibaelhárítása."
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: top-support-issue
keywords: "webes alkalmazás teljesítménye, lassú app alkalmazást lassú"
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.openlocfilehash: 1cfe7ec37ad8b24a8bd9ab2bf67e95675a57b675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Az Azure App Service lassú web app teljesítménnyel kapcsolatos problémák hibaelhárítása
Ez a cikk segítséget nyújt a lassú web app teljesítménnyel kapcsolatos problémák elhárítása [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

Ha ez a cikk bármely pontján további segítségre van szüksége, forduljon az Azure-szakértők a [az MSDN Azure és a Stack Overflow fórumok](https://azure.microsoft.com/support/forums/). Másik lehetőségként is fájl is az Azure támogatási incidens. Lépjen a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás**.

## <a name="symptom"></a>Jelenség
Ha megnyitja a webalkalmazás, a lapok betöltési lassan és egyes esetekben időtúllépés.

## <a name="cause"></a>Ok
Ez a probléma gyakran alkalmazás szintű problémákat, például:

* hosszú ideig tart a hálózati kérelmek
* alkalmazás éppen nem elég hatékony kód vagy az adatbázis lekérdezések
* nagy memória/CPU-t használó alkalmazások
* egy kivétel miatt összeomló alkalmazás

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Hibaelhárítás három különböző feladatokat, egymás utáni sorrendben osztható:

1. [Figyelje meg, és figyelheti az alkalmazások viselkedését](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma elhárítása érdekében](#mitigate)

[App Service Web Apps](/services/app-service/web/) minden lépésnél különböző lehetőséget biztosít.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Figyelje meg, és figyelheti az alkalmazások viselkedését
#### <a name="track-service-health"></a>Nyomon követése szolgáltatásának állapota
A Microsoft Azure publicizes minden alkalommal, amikor a szolgáltatás megszakadásának vagy a teljesítmény romlását van. A a szolgáltatás állapotának nyomon követheti a [Azure-portálon](https://portal.azure.com/). További információkért lásd: [szolgáltatás állapotát nyomon](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>A webes alkalmazás figyelése
Ez a beállítás lehetővé teszi annak megállapítása, ha az alkalmazás van bármilyen probléma merül. A webalkalmazás panelen kattintson a **kérések és hibák követése** csempére. A **metrika** panelen látható adhat hozzá minden metrikákat.

A metrikák, amelyeket a webalkalmazás figyelésére érdemes vannak

* Munkakészlet átlagos memória
* Átlagos válaszidő
* CPU-idő
* Memória munkakészlete
* Kérelmek

![webes alkalmazás teljesítményének figyelése](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

További információkért lásd:

* [Az Azure App Service Web-alkalmazások figyelése](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Webes végpont állapotának figyelése
Ha a webalkalmazás fut a **szabványos** IP-címek, Web Apps lehetővé teszi, hogy két végpontot három földrajzi helyekről figyelése.

Webes tesztjeinek használatát, amely a válaszidő és a webalkalmazás URL-címeket üzemidőt tesztelése földrajzilag elosztott helyekről végpontmonitoring kijelző konfigurálása. A vizsgálati a webes URL-cím az egyes helyeken a válaszidő és a hasznos üzemidő meghatározásához egy HTTP GET műveletet hajtja végre. Minden egyes konfigurált helyről futtatja egy tesztet, ötpercenként.

Hasznos üzemidő rendszer figyeli a HTTP válaszkódot használatával, és válaszideje ezredmásodpercben. A figyelési teszt sikertelen, ha a HTTP válaszkódot nagyobb vagy egyenlő a 400-as, illetve a választ több mint 30 másodpercet vesz igénybe. A végpont érhető el, ha a figyelési tesztek futtatásához a megadott helyek sikeres legyen tekinthető.

Állítsa be, lásd: [az Azure App Service-alkalmazások figyelése](web-sites-monitor.md).

Lásd még [Azure webhelyek tartja be és a Végpontmonitoring kijelző - lengyel Schackow rendelkező](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) videó a végpontmonitoring kijelző.

#### <a name="application-performance-monitoring-using-extensions"></a>Alkalmazásteljesítmény-figyelési bővítményekkel
Ugyanígy figyelheti az alkalmazások teljesítményéről használatával *bővítmények hely*.

Minden egyes App Service web app egy bővíthető felügyeleti végpontot, amely lehetővé teszi telepített eszközök hatékony készletének használata a webhely kiterjesztéseket biztosít. Bővítmények a következők: 

- Szerkesztők kód, például [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- A kapcsolódó erőforrások, például a MySQL-adatbázis felügyeleti eszközeit a webes alkalmazás csatlakozik.

[Az Azure Application Insights](/services/application-insights/) és [New Relic](/marketplace/partners/newrelic/newrelic/) két a Teljesítményfigyelő rendelkezésre álló hely bővítmények. New Relic használatához futásidőben ügynököt telepít. Azure Application Insights használatához újra kell építeni a kódot az SDK-val, és bővítménye által biztosított további adatokhoz való hozzáférés is telepíthet. Az SDK-val lehetővé teszi, hogy írhat kódot a használati és részletesebben az alkalmazás teljesítményének figyelése.

Az Application Insights használatához tekintse meg a [a webes alkalmazások teljesítményének figyelésére](../application-insights/app-insights-web-monitor-performance.md).

New Relic használatát ismerteti [új New Relic teljesítmény Alkalmazáskezelés Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
A Web Apps környezet naplózási információkat a webkiszolgáló és a webes alkalmazás diagnosztikai funkciókat biztosítja. Az információk web server diagnostics és az application diagnostics van osztva.

#### <a name="enable-web-server-diagnostics"></a>Web server diagnosztika engedélyezése
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes naplózás hiba** -részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért a kiszolgáló a hibakódot adott vissza adatokat.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek nyomkövetési segítségével dolgozza fel a kérelmet, és minden egyes összetevő szükséges idő az IIS-összetevők többek között. Ez akkor lehet hasznos, ha a webes alkalmazás teljesítményének javítása vagy különítse el, mi okozza-e egy adott HTTP hiba kívánt.
* **Webalkalmazás-kiszolgáló naplózza a** -a W3C bővített naplófájlformátum használata HTTP tranzakciókkal kapcsolatos információkat. Ez akkor hasznos, teljes web app metrikákat, például a kezelt kéréseket, és hogy hány kérésnek tartoznak, amely egy adott IP-cím a meghatározásakor.

#### <a name="enable-application-diagnostics"></a>Application diagnostics engedélyezése
Alkalmazás teljesítményadatokat gyűjteni a Web Apps, az alkalmazás a Visual Studio eszközből live, vagy módosítsa az alkalmazás kódjában, a naplófájl további információkat és nyomkövetési profil több lehetőség áll rendelkezésre. Kiválaszthatja a beállítások alapján az alkalmazás és a felügyelet megfigyelt hozzáféréssel eszközei.

##### <a name="use-application-insights-profiler"></a>Application Insights Profilkészítő használata
Engedélyezheti a Application Insights Profilkészítő el részletes teljesítmény nyomkövetési adatokat. Végezheti el a nyomkövetések rögzített legfeljebb öt nappal ezelőtt amikor ki kell vizsgálni a problémákat a múltban történt. Ezt a lehetőséget választhatja, mindaddig, amíg az Azure-portál rendelkezik hozzáféréssel a webes alkalmazás Application Insights-erőforrást.

Application Insights Profilkészítő válaszideje minden webes hívás és a nyomkövetési adatokat, amely jelzi, hogy mely kódsort a lassú válaszok nyújt információkat. Egyes esetekben az App Service alkalmazás lassú mert bizonyos kód nem egy performant módon. Például a párhuzamos és a nemkívánatos adatbázis zárolási contentions futtató szekvenciális kódot. A szűk keresztmetszetek eltávolítása a kódban növeli a az alkalmazás teljesítményét, ugyanakkor rögzített fejlesztett ki nyomkövetéseket és a naplók beállítása nélkül észleléséhez. Az Application Insights Profilkészítő által gyűjtött nyomkövetési azonosító, amely az alkalmazás lelassítja kód segítségével, és App Service-alkalmazásokhoz a probléma megoldásához.

 További információkért lásd: [profilkészítési élő Azure-webalkalmazásokban az Application insights szolgáltatással](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Használja a távoli adatainak összegyűjtése
Az Azure App Service Web Apps, az API-alkalmazások és a webjobs-feladatok is távolról csatolást. Válassza ezt a beállítást, ha a webes alkalmazás erőforráshoz való hozzáférés és tudja, hogyan Reprodukálja a hibát, vagy történik, ha ismeri a pontos időtartam alatt a teljesítménycsökkenés oka.

Távoli profilkészítési akkor hasznos, ha a folyamat CPU-használata túl magas és a folyamat a vártnál lassabban fut-e, vagy a Tiltás késése a HTTP-kérelmek magasabbak, mint a normál, akkor távolról a folyamat kiértékelése és elemezheti a folyamat CPU mintavételi hívási verem beolvasása tevékenység és a kód gyors útvonal.

További információkért lásd: [távoli profilkészítési támogatás az Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Diagnosztikai nyomkövetési manuális beállítása
Ha hozzáfér a webes alkalmazás forráskódjához való, az Application diagnostics lehetővé teszi egy webes alkalmazás által létrehozott adatok rögzítését. ASP.NET alkalmazások használhatják a `System.Diagnostics.Trace` osztály az alkalmazásnaplóba diagnosztikai adatok naplózására. Azonban meg kell módosítani a kódot, és telepítse újra az alkalmazást. Ez a módszer akkor ajánlott, ha az alkalmazás fut egy tesztelési környezetben.

Az alkalmazás naplózási konfigurálásával kapcsolatos részletes utasításokért lásd: [az Azure App Service web Apps diagnosztikai naplózás engedélyezése](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Az Azure App Service támogatási portálon
Web Apps tesz lehetővé teszi a webalkalmazás HTTP naplókat, eseménynaplók, folyamat memóriaképek és több megtekintésével kapcsolatos problémák elhárítása. Végezheti el az összes ezt az információt a támogatási portálon, a **http://&lt;az alkalmazás neve >.scm.azurewebsites.net/Support**

Az Azure App Service támogatási portálon tesz lehetővé a három lépést az általános hibaelhárítási forgatókönyv támogatásához három különálló lappal:

1. Figyelje meg a jelenlegi viselkedése
2. Diagnosztikai adatok gyűjtése és a beépített elemzőkkel futtató elemzése
3. Csökkentése

Ha a probléma most történik, kattintson a **elemzés** > **diagnosztika** > **diagnosztizálása most** diagnosztikai munkamenet létrehozására, amelyek gyűjti a HTTP-naplókat, az Eseménynapló, memóriaképek, PHP hibanaplókat és a PHP-folyamat jelentés.

Miután az adatgyűjtés, a támogatási portálon az adatok elemzése fut, és lehetővé teszi egy HTML-jelentést.

Abban az esetben, ha szeretné letölteni az adatokat, alapértelmezés szerint, akkor legyen tárolva a D:\home\data\DaaS mappában.

Az Azure App Service-támogatás portál további információkért lásd: [hely bővítmény támogatja az Azure-webhelyek új frissítések](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>A Kudu hibakereső konzol használata
A hibakereső konzol hibakeresés, felfedezése, fájlok, valamint JSON végpontokat a környezettel kapcsolatos információk beolvasásakor feltöltése használható webes alkalmazásokat tartalmaz. Ez a konzol elnevezése a *Kudu konzol* vagy a *SCM irányítópult* a webalkalmazás.

Nyissa meg a hivatkozás férhet hozzá az irányítópulthoz **https://&lt;az alkalmazás neve >.scm.azurewebsites.net/**.

Az, amit a Kudu biztosítja a következők:

* az alkalmazás környezeti beállítások
* naplófolyamot
* diagnosztikai memóriakép
* a hibakeresési konzol, ahol futtathatja a Powershell-parancsmagok és alapvető DOS parancsok.

Egy másik fontos része a Kudu, abban az esetben, ha az alkalmazás első-alkalommal kivételek szűrész, használhatja a Kudu, és kiírja a SysInternals eszköz Procdump memória létrehozásához. Ezek memóriaképek pillanatképek a folyamat, és gyakran segítségével bonyolultabb webalkalmazásokba elhárítása.

A Kudu szolgáltatásainak további információkért lásd: [kell tudni Azure webhelyek Team Services eszközök](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma elhárítása érdekében
#### <a name="scale-the-web-app"></a>A webalkalmazás skálázása
Az Azure App Service a teljesítmény növelése és a teljesítményt, módosíthatja a skála, amelyen az alkalmazást futtat. A webes alkalmazás vertikális felskálázásával magában foglalja a két kapcsolódó műveletek: az App Service-csomag módosítása egy magasabb szintű tarifacsomagban használható, és bizonyos beállítások konfigurálása után a magasabb szintű tarifacsomagban használható váltott.

A méretezés további információkért lásd: [egy webalkalmazás skálázása az Azure App Service](web-sites-scale.md).

Továbbá ha szeretné, futtassa az alkalmazást a egynél több példány. Méretezési out nemcsak nyújt további feldolgozási képesség, de is lehetővé teszi bizonyos hibatűrést. Ha a folyamat leáll egy példányán, a többi példány folytatja a kérelmek kiszolgálását.

Manuális vagy automatikus skálázás állíthatja be.

#### <a name="use-autoheal"></a>Elindulásáról használata
Elindulásáról (például a konfigurációs módosításokat, kérelmek, memória-alapú korlátok vagy egy kérelem végrehajtásához szükséges idő) kiválasztott beállítások alapján az alkalmazás munkavégző folyamata újraindul. Az esetek többségében a folyamat újrahasznosítását leggyorsabban úgy tudja elhárítani a problémát az. Bár a webes alkalmazás közvetlenül az Azure-portálon belül mindig újraindíthatja, elindulásáról minderre automatikusan meg. Végre kell hajtani mindössze néhány eseményindítók hozzáadása a webalkalmazás a legfelső szintű web.config fájlban. Ezek a beállítások akkor ugyanúgy működnek, akkor is, ha az alkalmazás nem .net-alkalmazás.

További információkért lásd: [automatikus javítás Azure webhelyek](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>A webalkalmazás újraindítása
Újraindítás legtöbbször a legegyszerűbben úgy, hogy az egyszeri hibák. Az a [Azure-portálon](https://portal.azure.com/), a webalkalmazás panelen, lehetősége van a leállítása, vagy indítsa újra az alkalmazást.

 ![teljesítménnyel kapcsolatos problémák elhárítása érdekében webalkalmazás újraindítása](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

A webalkalmazás Azure Powershell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

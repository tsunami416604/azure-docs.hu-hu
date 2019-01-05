---
title: Teljesítménycsökkenés – az Azure App Service – hibaelhárítás |} A Microsoft Docs
description: Ez a cikk segít a lassú alkalmazástelepítések teljesítményproblémák elhárítása az Azure App Service-ben.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: webes alkalmazások teljesítményét, lassú alkalmazástelepítések alkalmazás lassú
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9c66a937ffd9155569820c47c99946d186c55cce
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54052155"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Az Azure App Service szolgáltatásban lassú teljesítménybeli problémáinak hibaelhárítása
Ez a cikk segítséget nyújt a lassú alkalmazástelepítések teljesítménnyel kapcsolatos problémáinak elhárítása [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás kérése**.

## <a name="symptom"></a>Jelenség
Amikor, keresse meg az alkalmazást, a lapok terhelés lassan és néha időkorlátja.

## <a name="cause"></a>Ok
Ezt gyakran okozza alkalmazás szintű problémákat, például:

* hosszú ideig tart a hálózati kérések
* alkalmazás éppen nem elég hatékony kód vagy az adatbázis lekérdezések
* magas memória és CPU-t használó alkalmazás
* az alkalmazás összeomlik kivétel miatt

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
Hibaelhárítási három különböző feladatokat, egymást követő sorrendben osztható:

1. [Figyelje meg, és figyelheti az alkalmazások viselkedése](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma megoldásához](#mitigate)

[App Service-ben](overview.md) minden lépésnél különféle lehetőségeket kínál.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Figyelje meg, és figyelheti az alkalmazások viselkedése
#### <a name="track-service-health"></a>Szolgáltatások állapotának nyomon követése
A Microsoft Azure publicizes minden alkalommal, amikor a szolgáltatás megszakítás és a teljesítmény teljesítménycsökkenés van. A szolgáltatás állapotának követheti a a [az Azure portal](https://portal.azure.com/). További információkért lásd: [szolgáltatások állapotának nyomon követése](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-app"></a>Figyelje alkalmazását
Ez a beállítás lehetővé teszi, hogy ismerje meg, ha az alkalmazás van-e bármilyen probléma fel. Az app (webalkalmazás) panelen kattintson a **kérelmek és hibák** csempére. A **metrika** panel tartalmazza a mérőszámok is hozzáadhat.

A metrikák, amelyeket érdemes az alkalmazás figyelése néhány

* Átlagos memória-munkakészlet
* Átlagos válaszidő
* CPU-idő
* Memória-munkakészlet
* Kérelmek

![alkalmazás teljesítményének monitorozása](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

További információkért lásd:

* [Alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Webes végpont állapotának figyelése
Ha az alkalmazás fut a **Standard** tarifacsomag, App Service lehetővé teszi két végpontot három földrajzi helyekről figyelése.

Végpont-monitorozás konfigurálása a webes tesztek a földrajzilag elosztott helyeken, amelyek a válaszidő és a webes URL-címek, hasznos üzemidőt teszteléséhez. A vizsgálat egy HTTP GET művelet a válaszidő és a hasznos üzemidő meghatározni az egyes helyek a webes URL-címen hajt végre. Minden egyes konfigurált helyre egy teszt 5 percenként fut.

HTTP-válaszkódot üzemidő felügyelik, és válasz ideje ezredmásodpercben. Egy figyelési teszt sikertelen, ha a HTTP-válaszkód nagyobb vagy egyenlő a 400-as vagy a válasz több mint 30 másodperc. A végpont elérhető, ha a figyelési tesztek futtatásához a megadott helyek sikeres számít.

Állítsa be, lásd: [alkalmazások figyelése az Azure App Service](web-sites-monitor.md).

Lásd még [megőrzése Azure-webhelyek mentése, valamint a végpont-Monitorozás – a Lengyel Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) videó a végpont-monitorozás.

#### <a name="application-performance-monitoring-using-extensions"></a>Alkalmazásteljesítmény-figyelési bővítményekkel
Az alkalmazás teljesítményéhez használatával is ellenőrizheti a *webhelybővítmény*.

Minden App Service-alkalmazás egy bővíthető felügyeleti végpontot, amely lehetővé teszi, hogy üzembe helyezett eszközök hatékony gyűjteménye, mint webhelybővítmények biztosít. Bővítmények a következők: 

- Kód szerkesztők, mint [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Felügyeleti eszközök csatlakoztatott források, például egy MySQL-adatbázishoz csatlakozik egy alkalmazást.

[Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/) egy teljesítményfigyelési webhelybővítmény is rendelkezésre áll. Az Application Insights használatához újra kell építeni a kód egy SDK-val. Bővítménye által biztosított további adatokhoz való hozzáférés is telepítheti. Az SDK lehetővé teszi, hogy írhat kódot a használatot és a további részleteket az alkalmazás teljesítményét. További információkért lásd: [webalkalmazások teljesítményének monitorozása](../azure-monitor/app/web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
App Service-ben diagnosztikai funkciókat biztosít a naplózási információk a webalkalmazás-kiszolgáló és a webes alkalmazás. Az információk elkülönített web server diagnostics és az application diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Web server diagnosztika engedélyezése
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes hibanaplózás** – részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért érdemes a kiszolgáló a következő hibakódot adta vissza információt.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek, beleértve a nyomkövetés feldolgozni a kérelmet, és az egyes összetevőkben ideje használja az IIS-összetevőt. Ez akkor lehet hasznos, ha próbált megnövelheti az alkalmazások teljesítményét, illetve meghatározhatja a mi egy adott HTTP hiba okozza.
* **Webalkalmazás-kiszolgáló naplózási** -információ a W3C bővített naplófájlformátum használata HTTP-tranzakciót. Ez akkor hasznos, ha a teljes alkalmazás mérőszámokat, például a kezelt kérések, vagy hogy hány kérésnek egy adott IP-címről száma meghatározása.

#### <a name="enable-application-diagnostics"></a>Az application diagnostics engedélyezése
Alkalmazásteljesítmény-adatokat gyűjteni az App Service-ben az alkalmazás a Visual Studióból élő, vagy módosítsa az alkalmazás kódjában, hogy a naplófájl további információkat és nyomkövetési profil többféle módon lehet. Lehetősége van a beállítások alapján az eszközök milyen mértékben fér hozzá az alkalmazás és a megfigyelt a figyelésből.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler használata
Engedélyezheti az Application Insights Profiler részletes teljesítmény-nyomkövetések rögzítése elindításához. Akár rögzített nyomkövetések érheti el a múltban történt öt nappal ezelőtt mikor kell vizsgálni a problémákat. Ezt a lehetőséget is választja, mindaddig, amíg az alkalmazás Application Insights-erőforráshoz hozzáféréssel rendelkezik az Azure Portalon.

Application Insights Profiler válaszideje minden webes hívás- és nyomkövetési, amely jelzi, hogy melyik kódsort az okozza, a lassú válaszai nyújt információkat. Egyes esetekben az App Service-alkalmazás azért lassú bizonyos kód nem fog szerepelni a nagy teljesítményt nyújtva módja. Ilyenek például a szekvenciális kódot futtathat párhuzamos és a nemkívánatos adatbázis zárolási versenyt válthat ki. A kód a szűk keresztmetszetek eltávolítása növeli a teljesítményt az alkalmazás, de nehezen észlelhető összetettebb nyomkövetéseket és a naplók beállítása nélkül. Az Application Insights Profiler által összegyűjtött nyomok segít a lelassul, az alkalmazás kódsorokat azonosítása, és a nehézség App Service-alkalmazások számára.

 További információkért lásd: [profilkészítés az Azure App Service az Application Insights élő alkalmazások](../azure-monitor/app/profiler.md).

##### <a name="use-remote-profiling"></a>Távoli profilkészítés használata
Az Azure App Service-webalkalmazások, API apps, mobil háttérrendszer, és a webjobs-feladatok távoli profilozhatók. Válassza ezt a lehetőséget, ha hozzáfér az alkalmazás-erőforrást, és tudja, hogyan reprodukálnia a hibát, vagy történik, ha ismeri a pontos időtartam alatt a teljesítménnyel kapcsolatos problémák.

Távoli Profilace akkor hasznos, ha a folyamat CPU-használata túl magas és a folyamat a vártnál lassabban fut, vagy a HTTP-kérések késleltetésére értéke a szokásosnál magasabb, akkor távolról a folyamat kiértékelése és elemezheti a folyamat CPU mintavételi hívás vermek beolvasása tevékenység és a kód gyors elérésű útvonalak.

További információkért lásd: [távoli profilkészítés támogatása az Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Állítsa be a diagnosztikai nyomkövetéseket manuálisan
Ha rendelkezik hozzáféréssel a webes alkalmazás forráskódjához, az Application diagnostics lehetővé teszi a webalkalmazások által létrehozott adatok rögzítését. ASP.NET-alkalmazások használhatják a `System.Diagnostics.Trace` osztályt az alkalmazásnaplóba diagnosztikai információk naplózása. Azonban szüksége a kódot, és újból üzembe helyeznie az alkalmazást. Ezt a módszert akkor ajánlott, ha az alkalmazás fut, a tesztelési környezeteket.

Az alkalmazás naplózásának konfigurálásáról részletes utasításokért lásd: [az Azure App Service-alkalmazások diagnosztikai célú naplózásának engedélyezése](troubleshoot-diagnostic-logs.md).

#### <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszközt használhatja
Az App Service biztosítja egy intelligens és interaktív élmény szükséges konfiguráció nélkül az alkalmazás háríthatja el. Ha problémákat tapasztal az alkalmazással, a diagnosztikai eszköz, mi okozza a végigvezeti Önt a megfelelő információk könnyen és gyorsan elhárítása és a probléma megoldásához fog mutatni.

Az App Service diagnosztikái eléréséhez keresse meg az App Service-alkalmazás vagy az App Service-környezet a [az Azure portal](https://portal.azure.com). A bal oldali navigációs sávján kattintson a **diagnosztizálása és a problémák megoldásához**.

#### <a name="use-the-kudu-debug-console"></a>A Kudu hibakereső konzol használata
App Service-ben tartalmaz egy hibakeresési konzolt, amely a hibakeresés, felfedezését, fájlok, valamint a környezet kapcsolatos információk lekérése JSON végpontjainak feltöltése is használhat. Ez a konzol nevezzük a *Kudu konzol* vagy a *SCM irányítópultján* az alkalmazáshoz.

Ezt az irányítópultot elérheti a hivatkozás a **https://&lt;az alkalmazás neve >.scm.azurewebsites.net/**.

Néhány dolog, ami a Kudu biztosít a következők:

* az alkalmazás környezeti beállítások
* naplóstream
* diagnosztikai memóriakép
* a hibakeresési konzolt, amelyben futtathatja a Powershell-parancsmagok és alapvető DOS-parancsok.

Egy másik hasznos funkció, a Kudu, abban az esetben, ha az alkalmazás az első-alkalommal kivételeket dob, használhatja a Kudu, és kiírja a SysInternals eszköz Procdump memória létrehozásához. Ezek a memóriaképek pillanatfelvételei a folyamat, és gyakran segíthet az alkalmazás összetettebb hibáinak elhárítása.

A Kudu elérhető funkciókról további információkért lásd: [kell tudni az Azure DevOps-eszközök](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma megoldásához
#### <a name="scale-the-app"></a>Alkalmazások skálázása
Az Azure App Service a jobb teljesítmény és az átviteli sebesség, módosíthatja a méretezési csoport, amikor az alkalmazás futtatásakor. Alkalmazás vertikális felskálázásával jár két kapcsolódó műveletek: az App Service-csomag módosítása egy magasabb díjszabási csomagot, és bizonyos beállítások konfigurálása után a magasabb tarifacsomagra vált.

Méretezéssel kapcsolatos további információkért lásd: [Skálázhatja őket egy Azure App Service-ben](web-sites-scale.md).

Emellett kiválaszthatja az alkalmazás futtatásához egynél több példányon. A horizontális skálázás nem csupán nyújt további feldolgozási képesség, de emellett bizonyos mennyiségű hibatűrést biztosít. Ha a folyamat több példányon leáll, a többi példány továbbra is kiszolgálni a kérelmeket.

Beállíthatja, hogy manuális vagy automatikus skálázása.

#### <a name="use-autoheal"></a>Használja az autoheal funkciót
Automatikus javítás funkció újrahasznosítja a munkavégző folyamat az alkalmazáshoz (például a konfigurációs módosítások, kérelmek, memória-alapú korlátok vagy a kérés végrehajtásához szükséges idő) kiválasztott beállítások alapján. Az esetek többségében a folyamat újraindítása a leggyorsabban úgy tudja elhárítani a problémát. Bár mindig újraindíthatja az alkalmazást közvetlenül az Azure Portalon, automatikus javítás funkció mindezt automatikusan megteszi Ön helyett. Ehhez szüksége néhány eseményindítók hozzáadása az alkalmazáshoz a legfelső szintű web.config. Ezek a beállítások akkor ugyanúgy működik, még ha az alkalmazás nem .net-alkalmazás.

További információkért lásd: [Azure-webhelyek automatikus javításáról](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Indítsa újra az alkalmazást
Újraindítás legtöbbször helyreállíthatja az egyszeri hibák legegyszerűbb módja. Az a [az Azure portal](https://portal.azure.com/), az alkalmazás paneljén a közül leállítani, vagy indítsa újra az alkalmazást.

 ![Indítsa újra az alkalmazást a teljesítménnyel kapcsolatos problémák megoldásához](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Az alkalmazás az Azure Powershell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

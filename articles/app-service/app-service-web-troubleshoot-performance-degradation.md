---
title: Az App Service-WebApp teljesítményének lassú |} A Microsoft Docs
description: Ez a cikk segítenek elhárítani a lassú webalkalmazások teljesítménybeli problémáinak Azure App Service-ben.
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
ms.openlocfilehash: b1ae61b5372fa1061448c02e7b5c589a167888c9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237213"
---
# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Lassú webalkalmazás teljesítménybeli problémáinak az Azure App Service hibaelhárítása
Ez a cikk segítséget nyújt a lassú webalkalmazások alkalmazás teljesítménnyel kapcsolatos problémáinak elhárítása [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

Ha ebben a cikkben bármikor további segítségre van szüksége, forduljon az Azure-szakértőket a [az MSDN Azure és a Stack Overflow-fórumok](https://azure.microsoft.com/support/forums/). Másik megoldásként is fájl is egy Azure-támogatási esemény. Nyissa meg a [Azure támogatási webhelyén](https://azure.microsoft.com/support/options/) , majd kattintson a **támogatás kérése**.

## <a name="symptom"></a>Jelenség
Amikor, keresse meg a webalkalmazást, a lapok terhelés lassan és néha időkorlátja.

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

[App Service Web Apps](app-service-web-overview.md) minden lépésnél különféle lehetőségeket kínál.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Figyelje meg, és figyelheti az alkalmazások viselkedése
#### <a name="track-service-health"></a>Szolgáltatások állapotának nyomon követése
A Microsoft Azure publicizes minden alkalommal, amikor a szolgáltatás megszakítás és a teljesítmény teljesítménycsökkenés van. A szolgáltatás állapotának követheti a a [az Azure portal](https://portal.azure.com/). További információkért lásd: [szolgáltatások állapotának nyomon követése](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>A webes alkalmazás figyelése
Ez a beállítás lehetővé teszi, hogy ismerje meg, ha az alkalmazás van-e bármilyen probléma fel. A web app (webalkalmazás) panelen kattintson a **kérelmek és hibák** csempére. A **metrika** panel tartalmazza a mérőszámok is hozzáadhat.

A metrikák, amelyeket érdemes a webalkalmazás figyelése néhány

* Átlagos memória-munkakészlet
* Átlagos válaszidő
* CPU-idő
* Memória-munkakészlet
* Kérelmek

![-WebApp teljesítményének figyelése](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

További információkért lásd:

* [Webes alkalmazások monitorozása az Azure App Service-ben](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Webes végpont állapotának figyelése
Ha a webalkalmazás fut a **Standard** tarifacsomag, Web Apps lehetővé teszi, hogy két-három földrajzi helyekről végpontot figyelését.

Végpont-monitorozás konfigurálása a webes tesztek a földrajzilag elosztott helyeken, amelyek a válaszidő és a webes URL-címek, hasznos üzemidőt teszteléséhez. A vizsgálat egy HTTP GET művelet a válaszidő és a hasznos üzemidő meghatározni az egyes helyek a webes URL-címen hajt végre. Minden egyes konfigurált helyre egy teszt 5 percenként fut.

HTTP-válaszkódot üzemidő felügyelik, és válasz ideje ezredmásodpercben. Egy figyelési teszt sikertelen, ha a HTTP-válaszkód nagyobb vagy egyenlő a 400-as vagy a válasz több mint 30 másodperc. A végpont elérhető, ha a figyelési tesztek futtatásához a megadott helyek sikeres számít.

Állítsa be, lásd: [alkalmazások figyelése az Azure App Service](web-sites-monitor.md).

Lásd még [megőrzése Azure-webhelyek mentése, valamint a végpont-Monitorozás – a Lengyel Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) videó a végpont-monitorozás.

#### <a name="application-performance-monitoring-using-extensions"></a>Alkalmazásteljesítmény-figyelési bővítményekkel
Az alkalmazás teljesítményéhez használatával is ellenőrizheti a *webhelybővítmény*.

Minden App Service webalkalmazás egy bővíthető felügyeleti végpontot, amely lehetővé teszi, hogy üzembe helyezett eszközök hatékony gyűjteménye, mint webhelybővítmények biztosít. Bővítmények a következők: 

- Kód szerkesztők, mint [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx). 
- Felügyeleti eszközök csatlakoztatott források, például egy MySQL-adatbázis egy webalkalmazás csatlakozik.

[Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/) egy teljesítményfigyelési webhelybővítmény is rendelkezésre áll. Az Application Insights használatához újra kell építeni a kód egy SDK-val. Bővítménye által biztosított további adatokhoz való hozzáférés is telepítheti. Az SDK lehetővé teszi, hogy írhat kódot a használatot és a további részleteket az alkalmazás teljesítményét. További információkért lásd: [webalkalmazások teljesítményének monitorozása](../application-insights/app-insights-web-monitor-performance.md).

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatok gyűjtése
A Web Apps-környezetben a naplózási információk a webalkalmazás-kiszolgáló és a webes alkalmazás diagnosztikai funkciót biztosít. Az információk elkülönített web server diagnostics és az application diagnostics.

#### <a name="enable-web-server-diagnostics"></a>Web server diagnosztika engedélyezése
Engedélyezheti vagy letilthatja a naplók a következő típusú:

* **Részletes hibanaplózás** – részletes (állapotkód: 400 vagy nagyobb) hibát jelző HTTP-állapotkódok hiba adatait. Ez tartalmazhat, amelyek segíthetnek meghatározni, miért érdemes a kiszolgáló a következő hibakódot adta vissza információt.
* **Sikertelen kérelmek nyomkövetésére vonatkozó** – részletes információk a sikertelen kérelmek, beleértve a nyomkövetés feldolgozni a kérelmet, és az egyes összetevőkben ideje használja az IIS-összetevőt. Ez akkor lehet hasznos, ha WebApp teljesítményének javítására, illetve különíteni egy adott HTTP hiba mi okozza.
* **Webalkalmazás-kiszolgáló naplózási** -információ a W3C bővített naplófájlformátum használata HTTP-tranzakciót. Ez akkor hasznos, ha a teljes webalkalmazás-metrikák, például kezelt kérések, vagy hogy hány kérésnek egy adott IP-címről számának meghatározásához.

#### <a name="enable-application-diagnostics"></a>Az application diagnostics engedélyezése
Alkalmazásteljesítmény-adatokat gyűjteni a Web Apps, az alkalmazás a Visual Studióból élő, vagy módosítsa az alkalmazás kódjában, hogy a naplófájl további információkat és nyomkövetési profil többféle módon lehet. Lehetősége van a beállítások alapján az eszközök milyen mértékben fér hozzá az alkalmazás és a megfigyelt a figyelésből.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler használata
Engedélyezheti az Application Insights Profiler részletes teljesítmény-nyomkövetések rögzítése elindításához. Akár rögzített nyomkövetések érheti el a múltban történt öt nappal ezelőtt mikor kell vizsgálni a problémákat. Ezt a beállítást is választja, mindaddig, amíg hozzáférhet a WebApp Application Insights-erőforrást az Azure Portalon.

Application Insights Profiler válaszideje minden webes hívás- és nyomkövetési, amely jelzi, hogy melyik kódsort az okozza, a lassú válaszai nyújt információkat. Egyes esetekben az App Service-alkalmazás azért lassú bizonyos kód nem fog szerepelni a nagy teljesítményt nyújtva módja. Ilyenek például a szekvenciális kódot futtathat párhuzamos és a nemkívánatos adatbázis zárolási versenyt válthat ki. A kód a szűk keresztmetszetek eltávolítása növeli a teljesítményt az alkalmazás, de nehezen észlelhető összetettebb nyomkövetéseket és a naplók beállítása nélkül. Az Application Insights Profiler által összegyűjtött nyomok segít a lelassul, az alkalmazás kódsorokat azonosítása, és a nehézség App Service-alkalmazások számára.

 További információkért lásd: [Profilace élő Azure-webalkalmazások az Application Insights](../application-insights/app-insights-profiler.md).

##### <a name="use-remote-profiling"></a>Távoli profilkészítés használata
Az Azure App Service Web Apps, API Apps és webjobs-feladatok távoli profilozhatók. Válassza ezt a lehetőséget, ha hozzáfér a webalkalmazás-erőforrásban, és tudja, hogyan reprodukálnia a hibát, vagy történik, ha ismeri a pontos időtartam alatt a teljesítménnyel kapcsolatos problémák.

Távoli Profilace akkor hasznos, ha a folyamat CPU-használata túl magas és a folyamat a vártnál lassabban fut, vagy a HTTP-kérések késleltetésére értéke a szokásosnál magasabb, akkor távolról a folyamat kiértékelése és elemezheti a folyamat CPU mintavételi hívás vermek beolvasása tevékenység és a kód gyors elérésű útvonalak.

További információkért lásd: [távoli profilkészítés támogatása az Azure App Service](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service).

##### <a name="set-up-diagnostic-traces-manually"></a>Állítsa be a diagnosztikai nyomkövetéseket manuálisan
Ha rendelkezik hozzáféréssel a webes alkalmazás forráskódjához, az Application diagnostics lehetővé teszi a webalkalmazások által létrehozott adatok rögzítését. ASP.NET-alkalmazások használhatják a `System.Diagnostics.Trace` osztályt az alkalmazásnaplóba diagnosztikai információk naplózása. Azonban szüksége a kódot, és újból üzembe helyeznie az alkalmazást. Ezt a módszert akkor ajánlott, ha az alkalmazás fut, a tesztelési környezeteket.

Az alkalmazás naplózásának konfigurálásáról részletes utasításokért lásd: [az Azure App Service web Apps-alkalmazások diagnosztikai célú naplózásának engedélyezése](web-sites-enable-diagnostic-log.md).

#### <a name="use-the-azure-app-service-support-portal"></a>Az Azure App Service támogatja a portálon
A Web Apps lehetővé teszi a webes alkalmazás HTTP naplók, eseménynaplókat, folyamat memóriaképek és további megtekintésével kapcsolatos problémák elhárítása biztosít. Mindez az információ támogatási portálunkon címen érheti **http://&lt;az alkalmazás neve >.scm.azurewebsites.net/Support**

Az Azure App Service-támogatási portal nyújt három külön lap az három lépést, a gyakori hibaelhárítási forgatókönyv támogatásához:

1. Figyelje meg a jelenlegi működése
2. Diagnosztikai adatok gyűjtése és a beépített elemzők futó elemzése
3. Problémamegoldás

Ha a probléma most történik, kattintson a **elemzés** > **diagnosztikai** > **diagnosztizálása most** diagnosztikai munkamenet létrehozásához, amely gyűjti a HTTP-naplókat, az Eseménynapló, memóriaképeket, a PHP-hibanaplók és PHP-folyamat jelentést.

Az adatok gyűjtése történik, ha a támogatási portál egy elemzést futtat az adatokon, és biztosít egy HTML-jelentést.

Abban az esetben, ha szeretné letölteni az adatokat, alapértelmezés szerint, azt kellene lennie D:\home\data\DaaS mappában lesz tárolva.

További információ az Azure App Service-támogatási portál: [Webhelybővítmény támogatja az Azure-webhelyek új frissítések](https://azure.microsoft.com/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>A Kudu hibakereső konzol használata
A Web Apps tartalmaz egy hibakeresési konzolt, amely a hibakeresés, felfedezését, fájlok, valamint a környezet kapcsolatos információk lekérése JSON végpontjainak feltöltése is használhat. Ez a konzol nevezzük a *Kudu konzol* vagy a *SCM irányítópultján* webalkalmazása számára.

Ezt az irányítópultot elérheti a hivatkozás a **https://&lt;az alkalmazás neve >.scm.azurewebsites.net/**.

Néhány dolog, ami a Kudu biztosít a következők:

* az alkalmazás környezeti beállítások
* naplóstream
* diagnosztikai memóriakép
* a hibakeresési konzolt, amelyben futtathatja a Powershell-parancsmagok és alapvető DOS-parancsok.

Egy másik hasznos funkció, a Kudu, abban az esetben, ha az alkalmazás az első-alkalommal kivételeket dob, használhatja a Kudu, és kiírja a SysInternals eszköz Procdump memória létrehozásához. Ezek a memóriaképek pillanatfelvételei a folyamat, és gyakran segíthet a webalkalmazás bonyolultabb hibáinak elhárítása.

A Kudu elérhető funkciókról további információkért lásd: [kell tudni az Azure DevOps-eszközök](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma megoldásához
#### <a name="scale-the-web-app"></a>A webalkalmazás skálázása
Az Azure App Service a jobb teljesítmény és az átviteli sebesség, módosíthatja a méretezési csoport, amikor az alkalmazás futtatásakor. Webes alkalmazás vertikális felskálázása magában foglalja a két kapcsolódó műveletek: az App Service-csomag módosítása egy magasabb díjszabási csomagot, és bizonyos beállítások konfigurálása után a magasabb tarifacsomagra vált.

Méretezéssel kapcsolatos további információkért lásd: [webalkalmazások méretezése az Azure App Service](web-sites-scale.md).

Emellett kiválaszthatja az alkalmazás futtatásához egynél több példányon. A horizontális skálázás nem csupán nyújt további feldolgozási képesség, de emellett bizonyos mennyiségű hibatűrést biztosít. Ha a folyamat több példányon leáll, a többi példány továbbra is kiszolgálni a kérelmeket.

Beállíthatja, hogy manuális vagy automatikus skálázása.

#### <a name="use-autoheal"></a>Használja az autoheal funkciót
Automatikus javítás funkció újrahasznosítja a munkavégző folyamat az alkalmazáshoz (például a konfigurációs módosítások, kérelmek, memória-alapú korlátok vagy a kérés végrehajtásához szükséges idő) kiválasztott beállítások alapján. Az esetek többségében a folyamat újraindítása a leggyorsabban úgy tudja elhárítani a problémát. Mindig közvetlenül az Azure Portalon a web-app újraindíthatja, de automatikus javítás funkció mindezt automatikusan megteszi Ön helyett. Mást nem kell tennie az egyes eseményindítók hozzáadása a webalkalmazása számára a legfelső szintű web.config fájlban. Ezek a beállítások akkor ugyanúgy működik, még ha az alkalmazás nem .net-alkalmazás.

További információkért lásd: [Azure-webhelyek automatikus javításáról](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-web-app"></a>Indítsa újra a webalkalmazást
Újraindítás legtöbbször helyreállíthatja az egyszeri hibák legegyszerűbb módja. Az a [az Azure portal](https://portal.azure.com/), a webalkalmazás panelén, a lehetőségek közül választhat az alkalmazás újraindítása vagy leállítása.

 ![Indítsa újra a webalkalmazást a teljesítménnyel kapcsolatos problémák megoldásához](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

A webalkalmazás az Azure Powershell használatával is kezelheti. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

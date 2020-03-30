---
title: Teljesítménycsökkenés – problémamegoldás
description: Megtudhatja, hogyan háríthatja el a lassú alkalmazásteljesítmény-problémákat az Azure App Service-ben, például az alkalmazások viselkedésének figyelését, az adatgyűjtést és a probléma elhárítását.
tags: top-support-issue
keywords: webalkalmazás teljesítménye, lassú alkalmazás, alkalmazás lassú
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.topic: article
ms.date: 08/03/2016
ms.custom: seodec18
ms.openlocfilehash: 98c11a72b5aea0fac15d943977402289dc33a970
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74688318"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Lassú alkalmazásteljesítménnyel kapcsolatos problémák elhárítása az Azure App Service-ben
Ez a cikk segítséget nyújt az Azure App Service lassú alkalmazásteljesítményével kapcsolatos problémák [elhárításához.](https://go.microsoft.com/fwlink/?LinkId=529714)

Ha további segítségre van szüksége a cikk bármely pontján, felveheti a kapcsolatot az Azure szakértőivel [az MSDN Azure-ban és a Veremtúlcsordulás fórumain.](https://azure.microsoft.com/support/forums/) Azt is megteheti, hogy egy Azure-támogatási incidenst is benyújthat. Nyissa meg az [Azure támogatási webhelyét,](https://azure.microsoft.com/support/options/) és kattintson a **Támogatás beszerezni**lehetőségre.

## <a name="symptom"></a>Hibajelenség
Az alkalmazás böngészésekén az oldalak lassan töltődnek be, és néha időtúlzók.

## <a name="cause"></a>Ok
Ezt a problémát gyakran alkalmazásszintű problémák okozzák, például:

* hálózati kérelmek hosszú időt vesz igénybe
* az alkalmazáskód vagy az adatbázis-lekérdezések nem hatékonyak
* alkalmazás nagy memóriával / CPU
* alkalmazás összeomlik egy kivétel miatt

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések
A hibaelhárítás három különböző feladatra osztható, sorrendben:

1. [Az alkalmazások viselkedésének megfigyelése és figyelése](#observe)
2. [Adatok gyűjtése](#collect)
3. [A probléma enyhítése](#mitigate)

[Az App Service](overview.md) különböző lehetőségeket kínál az egyes lépésein.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. Az alkalmazások viselkedésének megfigyelése és figyelése
#### <a name="track-service-health"></a>Szolgáltatás állapotának nyomon követése
A Microsoft Azure minden alkalommal nyilvánosságra hozza a szolgáltatás megszakítását vagy a teljesítmény romlását. A szolgáltatás állapotát az Azure [Portalon](https://portal.azure.com/)követheti nyomon. További információ: [A szolgáltatás állapotának nyomon követése.](../monitoring-and-diagnostics/insights-service-health.md)

#### <a name="monitor-your-app"></a>Az alkalmazás figyelése
Ez a beállítás lehetővé teszi, hogy megtudja, ha az alkalmazás, amelyek bármilyen probléma. Az alkalmazás paneljén kattintson a **Kérések és hibák** csempére. A **Metrikus** panel az összes hozzáadható metrikát jeleníti meg.

Az alkalmazáshoz figyelni kívánt mérőszámok némelyike

* Átlagos memóriamunkakészlet
* Átlagos válaszidő
* PROCESSZOR idő
* Memóriamunkakészlet
* Kérelmek

![az alkalmazás teljesítményének figyelése](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

További információkért lásd:

* [Alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md)
* [Riasztási értesítések fogadása](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>A webvégpont állapotának figyelése
Ha az alkalmazást a **standard** tarifacsomagban futtatja, az App Service lehetővé teszi két végpont figyelését három földrajzi helyről.

A végpontfigyelés olyan földrajzilag elosztott helyekről származó webes teszteket konfigurál, amelyek tesztelik a válaszidőt és a webes URL-ek uptime-ját. A teszt egy HTTP GET műveletet hajt végre a webes URL-címen a válaszidő és az üzemidő meghatározásához az egyes helyeken. Minden konfigurált hely ötpercenként futtat egy tesztet.

Az uptime-ot HTTP-válaszkódok segítségével figyeli a rendszer, és a válaszidőt ezredmásodpercben mérik. A figyelési teszt sikertelen, ha a HTTP-válaszkód nagyobb vagy egyenlő 400-ra, vagy ha a válasz 30 másodpercnél tovább tart. Egy végpont akkor tekinthető elérhetőnek, ha a figyelési tesztek sikeresek az összes megadott helyről.

A beállításához olvassa el [az Alkalmazások figyelése az Azure App Service-ben](web-sites-monitor.md).

Az [Azure-webhelyek megtartása és az Endpoint Monitoring – Stefan Schackow](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) segítségével a végpontfigyelésről szóló videóért is.

#### <a name="application-performance-monitoring-using-extensions"></a>Alkalmazásteljesítmény-figyelés bővítmények használatával
Az alkalmazások teljesítményét *helybővítmény*használatával is figyelheti.

Minden App Service-alkalmazás egy bővíthető felügyeleti végpontot biztosít, amely lehetővé teszi a helybővítményekként telepített hatékony eszközök használatát. A bővítmények a következők: 

- A forráskód-szerkesztők, például az [Azure DevOps.](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) 
- Felügyeleti eszközök csatlakoztatott erőforrásokhoz, például egy alkalmazáshoz csatlakoztatott MySQL-adatbázishoz.

[Az Azure Application Insights](https://azure.microsoft.com/services/application-insights/) egy teljesítményfigyelési webhelybővítmény, amely szintén elérhető. Az Application Insights használatához újra kell építenie a kódot egy SDK-val. Olyan bővítményt is telepíthet, amely további adatokhoz biztosít hozzáférést. Az SDK lehetővé teszi, hogy kódot írjon az alkalmazás használatának és teljesítményének részletesebb figyeléséhez. További információt a [Teljesítmény figyelése a webalkalmazásokban](../azure-monitor/app/web-monitor-performance.md)című témakörben talál.

<a name="collect" />

### <a name="2-collect-data"></a>2. Adatgyűjtés
Az App Service diagnosztikai funkciókat biztosít az információk nak a webkiszolgálóról és a webalkalmazásból történő naplózásához. Az információk webkiszolgáló- és alkalmazásdiagnosztikára vannak leválasztva.

#### <a name="enable-web-server-diagnostics"></a>Webkiszolgáló-diagnosztika engedélyezése
A következő típusú naplókat engedélyezheti vagy tilthatja le:

* **Részletes hibanaplózás** – Részletes hibainformáció a HTTP-állapotkódokhoz, amelyek hibát jeleznek (400-as vagy nagyobb állapotkód). Ez olyan információkat tartalmazhat, amelyek segíthetnek meghatározni, hogy a kiszolgáló miért adta vissza a hibakódot.
* **Sikertelen kérelmek nyomkövetése** – részletes információk a sikertelen kérelmekről, beleértve a kérelem feldolgozásához használt IIS-összetevők és az egyes összetevőkben eltöltött idő nyomon követését. Ez akkor lehet hasznos, ha az alkalmazás teljesítményének javítását vagy egy adott HTTP-hiba okozójának elkülönítését próbálja meg.
* **Webkiszolgáló naplózása** – A W3C kiterjesztett naplófájlformátumot használó HTTP-tranzakciókkal kapcsolatos információk. Ez akkor hasznos, ha általános alkalmazásmetrikák, például a kezelt kérelmek száma, vagy hány kérelmek egy adott IP-címet.

#### <a name="enable-application-diagnostics"></a>Alkalmazásdiagnosztika engedélyezése
Számos lehetőség van az alkalmazás teljesítményadatainak gyűjtésére az App Service szolgáltatásból, az alkalmazás élő ben való profilozására a Visual Studióból, vagy az alkalmazáskód módosítására további információk és nyomkövetések naplózásához. Kiválaszthatja a beállításokat attól függően, hogy mennyi hozzáférése van az alkalmazáshoz, és mit figyelt meg a figyelési eszközökből.

##### <a name="use-application-insights-profiler"></a>Az Application Insights-profilozó használata
Engedélyezheti, hogy az Application Insights Profiler részletes teljesítmény-nyomkövetéseket rögzítsen. Elérheti a legfeljebb öt nappal ezelőtt rögzített nyomkövetéseket, amikor ki kell vizsgálnia a múltban történt problémákat. Ezt a lehetőséget mindaddig választhatja, amíg rendelkezik hozzáféréssel az alkalmazás Application Insights-erőforrásához az Azure Portalon.

Az Application Insights Profiler statisztikai adatokat biztosít az egyes webes hívások válaszidejéről, és nyomon követi, hogy melyik kódsor okozta a lassú válaszokat. Néha az App Service-alkalmazás lassú, mert bizonyos kód nem írt teljesítmény módon. Ilyenek például a párhuzamos an- és nem kívánt adatbáziszárolási versengések futtatható szekvenciális kód. Ezek a szűk keresztmetszetek eltávolítása a kódban növeli az alkalmazás teljesítményét, de nehéz észlelni anélkül, hogy bonyolult nyomkövetések és naplók beállítása nélkül. Az Application Insights Profiler által gyűjtött nyomkövetések segítenek azonosítani az alkalmazás lelassulását lelassító kódsorokat, és leküzdik ezt a kihívást az App Service-alkalmazások számára.

 További információ: [Profilkészítés élő alkalmazások az Azure App Service alkalmazáselemzéssel.](../azure-monitor/app/profiler.md)

##### <a name="use-remote-profiling"></a>Távoli profilkészítés használata
Az Azure App Service-ben a webalkalmazások, az API-alkalmazások, a mobil háttérrendszerek és a WebJobs távolról profilozhatók. Akkor válassza ezt a lehetőséget, ha hozzáfér az alkalmazás-erőforráshoz, és tudja, hogyan kell reprodukálni a problémát, vagy ha tudja, hogy a teljesítményprobléma pontosan milyen időintervallumban fordul elő.

A távoli profilkészítés akkor hasznos, ha a folyamat CPU-használata magas, és a folyamat a vártnál lassabban fut, vagy a HTTP-kérések késése a szokásosnál magasabb, távolról profilozhatja a folyamatot, és lekéri a CPU mintavételezési híváshalmozásait a folyamat elemzéséhez tevékenység és a kód forró utak.

További információ: [Távoli profilkészítés támogatása az Azure App Service-ben.](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)

##### <a name="set-up-diagnostic-traces-manually"></a>Diagnosztikai nyomkövetések manuális beállítása
Ha hozzáfér a webalkalmazás forráskódjához, az alkalmazásdiagnosztika lehetővé teszi a webalkalmazás által előállított információk rögzítését. ASP.NET alkalmazások az `System.Diagnostics.Trace` osztály segítségével naplózhatják az információkat az alkalmazásdiagnosztikai naplóba. Azonban módosítania kell a kódot, és újra telepítenie kell az alkalmazást. Ez a módszer akkor ajánlott, ha az alkalmazás tesztelési környezetben fut.

Az alkalmazás naplózásra való konfigurálásával kapcsolatos részletes útmutatásért olvassa el az [Alkalmazások diagnosztika naplózásának engedélyezése az Azure App Service-ben című témakört.](troubleshoot-diagnostic-logs.md)

#### <a name="use-the-diagnostics-tool"></a>A diagnosztikai eszköz használata
Az App Service intelligens és interaktív élményt nyújt az alkalmazás konfigurálás nélküli hibaelhárításához. Ha problémákba ütközik az alkalmazással kapcsolatban, a diagnosztikai eszköz rámutat arra, hogy mi a hiba, amely a megfelelő információkhoz vezeti a problémát, hogy könnyebben és gyorsabban elháríthassa és megoldhassa a problémát.

Az App Service-diagnosztika eléréséhez keresse meg az App Service-alkalmazást vagy az App Service-környezetet az [Azure Portalon.](https://portal.azure.com) A bal oldali navigációs, kattintson **a diagnosztizálása és a problémák megoldása**.

#### <a name="use-the-kudu-debug-console"></a>A Kudu Debug konzol használata
Az App Service tartalmaz egy hibakeresési konzolt, amely segítségével hibakeresés, feltárása, fájlok feltöltése, valamint a JSON-végpontok a környezettel kapcsolatos információk megszerzéséhez. Ezt a konzolt *Kudu konzolnak* vagy az alkalmazás *SCM irányítópultjának* nevezik.

Ezt az irányítópultot az **&lt;>.scm.azurewebsites.net/** https:// linkre kattintva érheti el.

Néhány dolog, hogy Kudu nyújt a következők:

* az alkalmazás környezeti beállításai
* naplófolyam
* diagnosztikai memóriakép
* debug konzol, amelyben futtathatja a Powershell parancsmagjait és az alapvető DOS parancsokat.

Egy másik hasznos jellemzője Kudu, hogy abban az esetben, ha az alkalmazás dobott első esély kivételek, használhatja Kudu és a SysInternals eszköz Procdump létrehozni memóriaképek. Ezek a memóriaképek a folyamat pillanatképei, és gyakran segítenek az alkalmazással kapcsolatos bonyolultabb problémák elhárításában.

A Kudu ban elérhető funkciókról az [Azure DevOps-eszközök](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)című témakörben talál további információt.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. A probléma enyhítése
#### <a name="scale-the-app"></a>Az alkalmazás méretezése
Az Azure App Service-ben a nagyobb teljesítmény és átviteli teljesítmény érdekében módosíthatja az alkalmazás futtatásának léptékét. Egy alkalmazás felskálázása két kapcsolódó műveletet foglal magában: az App Service-csomag magasabb tarifacsomagra történő módosítása, és bizonyos beállítások konfigurálása a magasabb tarifacsomagra való váltás után.

A méretezésről további információt az [Alkalmazás méretezése az Azure App Service-ben című témakörben talál.](manage-scale-up.md)

Emellett dönthet úgy, hogy az alkalmazást több példányon is futtatja. A horizontális felskálázás nem csak több feldolgozási képességet biztosít, hanem némi hibatűrést is biztosít. Ha a folyamat leáll az egyik példányban, a többi példány továbbra is a kérelmeket szolgálja ki.

Beállíthatja, hogy a méretezés manuális vagy automatikus legyen.

#### <a name="use-autoheal"></a>Az Automatikus javítás használata
Az AutoHeal a kiválasztott beállítások (például konfigurációs módosítások, kérések, memóriaalapú korlátok vagy a kérelem végrehajtásához szükséges idő) alapján újrahasznosítja az alkalmazás munkavégző folyamatát. Az idő nagy részében, újra a folyamat a leggyorsabb módja annak, hogy visszaszerezze a problémát. Bár az alkalmazást mindig újraindíthatja közvetlenül az Azure Portalon belül, az Automatikus javítás automatikusan elvégez. Mindössze annyit kell tennie, hogy néhány eseményindítót ad hozzá az alkalmazás gyökérweb.config gyökérében. Ezek a beállítások ugyanúgy működnének, még akkor is, ha az alkalmazás nem .NET alkalmazás.

További információt az [Azure-webhelyek automatikus meggyógyítása című témakörben talál.](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)

#### <a name="restart-the-app"></a>Az alkalmazás újraindítása
Az újraindítás gyakran a legegyszerűbb módja az egyszeri problémák helyreállításának. Az [Azure Portalon](https://portal.azure.com/)az alkalmazás paneljén lehetőség van az alkalmazás leállítására vagy újraindítására.

 ![az alkalmazás újraindítása a teljesítménnyel kapcsolatos problémák megoldásához](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Az Azure Powershell használatával is kezelheti az alkalmazást. További információ: [Az Azure PowerShell használata az Azure Resource Manager eszközzel](../powershell-azure-resource-manager.md).

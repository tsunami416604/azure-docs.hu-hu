---
title: "Tarifa- és adatok kötet kezelése az Azure Application Insights |} Microsoft Docs"
description: "Telemetria kötetek kezelése, és figyelje az Application Insightsban költségeket."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: mbullwin
ms.openlocfilehash: 95c5195ac2ea832586211cce37eb2094e06eaf03
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Az Application Insightsban tarifa- és adatok kötet kezelése


Az árképzés [Azure Application Insights] [ start] adatmennyiség alkalmazásonként alapul. Kevésbé aktív a fejlesztés során, vagy egy kis alkalmazás várhatóan szabad, mivel nincs a telemetriai adatok 1 GB-os havi juttatást.

Minden egyes Application Insights-erőforrás külön szolgáltatás fel van töltve, és hozzájárul az Azure-előfizetéshez tartozó számlázási.

Nincsenek két árképzési tervek. Az alapértelmezett terv Basic nevezik. Dönthet úgy is, a vállalati terv, amely napi díj rendelkezik, de lehetővé teszi, hogy bizonyos további szolgáltatások, mint [a folyamatos exportálás](app-insights-export-telemetry.md).

Ha árképzés működésével kapcsolatos az Application Insights kérdése van, nyugodtan fel a kérdést a [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights). 

## <a name="the-price-plans"></a>Az árlista csomagok

Tekintse meg a [árképzést ismertető oldalra Application Insights] [ pricing] a pénznemben számított aktuális árak.

### <a name="basic-plan"></a>Alapszintű

Az alapszintű csomag az alapértelmezett beállítás, ha egy új Application Insights-erőforrás jön létre, és a legtöbb felhasználó elegendő.

* Az alapszintű csomag, az adatok kötetenként van szó: a telemetriai adatok az Application Insights által fogadott bájtok száma. Az Application Insights által kapott az alkalmazás tömörítetlen JSON adatcsomag méretének adatmennyiség mérése történik.
A [Analytics importált adatok](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), adatmennyiség mérik, mert a tömörítetlen Application insights szolgáltatásnak elküldött fájlok mérete.  
* Az első 1 GB az egyes alkalmazásokhoz szabad, így ha csupán kísérletezés vagy fejlesztésével, még valószínűleg nem kell fizetnie.
* [Élő Stream metrikák](app-insights-live-stream.md) adatok díjszabási célra nem számítanak.
* [A folyamatos exportálás](app-insights-export-telemetry.md) érhető el az extra / GB-os kell fizetni az alapszintű csomag található.

### <a name="enterprise-plan"></a>Vállalati terv

* A vállalati tervben az alkalmazás használható az Application Insights összes funkcióját. [A folyamatos exportálás](app-insights-export-telemetry.md) és 

[Naplófájl Analytics összekötő](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) érhetők el minden további költség nélkül a vállalati tervben.
* A vállalati tervben alkalmazások telemetriai adatokat küldő csomópontonként kell fizetnie. 
 * A *csomópont* egy fizikai vagy virtuális gépet, vagy a Platform,--szolgáltatás szerepkör-példány, az alkalmazás.
 * Fejlesztési gépek, az ügyfélböngészők és a mobileszközök nem számítanak csomópontok.
 * Ha az alkalmazás még több összetevők által küldött telemetriai adatok, például egy webszolgáltatás-bővítmény és a háttér-feldolgozók ezek nem számítanak bele.
 * [Élő Stream metrikák](app-insights-live-stream.md) adatok nem számítanak purposes.* díjszabási egy előfizetésből, a díjak nem alkalmazásonkénti csomópontonként. Ha az öt csomóponttal 12 telemetriai adat küldése az öt csomóponttal alkalmazásokat, majd az elsők között van.
* Bár a havi díjak korlátozott akkor van szó, csak a minden órában, amelyben egy csomópont telemetriai adatokat küld az alkalmazásokból. Az óránkénti kell fizetni az ajánlatban szereplő havi kell fizetni az / 744 (a 31 napos hónap órák száma).
* Az egyes csomópontok (az óránkénti részletességű) észlelt kap egy adatok kötet foglalási, 200 MB / nap. Nem használt adatok foglalási nem átkerül egy nap után a Tovább gombra.
 * Ha a vállalati árképzési beállítást választja, minden egyes előfizetés lekérdezi a napi támogatás a telemetriai adatok küldése az Application Insights-erőforrások, az adott előfizetés csomópontok száma alapján. Ezért ha 5 csomópontok adatküldés minden nap, hogy egy készletezett támogatás az adott előfizetés Application Insights-erőforrások alkalmazott 1 GB. Nem számít, ha az egyes csomópontok más csomópontok-nál több adatot küld, mivel a befoglalt adatok meg vannak osztva az összes csomópont között. Ha egy adott napon az Application Insights-erőforrások jelenik meg több adat ehhez az előfizetéshez a napi adatok-foglalás szerepel, a / GB-os keretét adatok díjak vonatkoznak. 
 * A napi adatok támogatás számítása a napra (UTC) órák száma, hogy minden csomópont küld telemetriai 24 alkalommal 200 MB hányadosa. Így ha 4 csomópontok 15 nap 24 óra során telemetriai adatok küldését, az adott napon adatát lenne ((4 x 15) / 24) x 200 MB = 500 MB. Adatok túlhasználati 2.30-as USD / GB díjakon a költség, a lenne 1,15 USD Ha a csomópontok küldött 1 GB adatot adott napon.
 * Vegye figyelembe, hogy a vállalati terv napi támogatás nincsenek megosztva, alkalmazások, amelyekre az alapvető lehetőséget választotta, és használaton kívüli támogatás nem átkerülnek a napi. 
* Íme néhány példa a különböző csomópont számának meghatározása:
| Forgatókönyv                               | Teljes napi száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás használ 3 Azure App Service-példány és az 1 virtuális kiszolgáló | 4 |
| 3, 2 virtuális gép, és az Application Insights-erőforrások ezeket az alkalmazásokat a futó alkalmazások: ugyanazt az előfizetést, és a vállalati terv | 2 | 
| 4 alkalmazások, amelyek alkalmazások Insights-erőforrások vannak ugyanahhoz az előfizetéshez. Minden alkalmazás fut, 16 csúcsidőn 2 példányok és 8 csúcsidőben 4 példányok. | 13.33 | 
| Az 1 feldolgozói szerepkör és 1 webes szerepkör, minden futó 2 példányait cloud services csomag | 4 | 
| 5-csomópont Service Fabric-fürt minden egyes micro-szolgáltatást, futó 3 példányait futtató 50 micro-szolgáltatások, | 5|

* A pontos leltár viselkedés csomópont függ, amelyen az Application Insights SDK az alkalmazás használatával. 
  * SDK verzióiban 2.2-es és újabb verziók esetében, mind az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) vagy [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fogja jelentés minden egyes alkalmazás-csomópontként, például a fizikai kiszolgáló és a Virtuálisgép-gazdák számítógépnév vagy a példány neve felhőszolgáltatások esetén.  Az egyetlen kivétel csak alkalmazások használatával [.NET Core](https://dotnet.github.io/) és az Application Insights Core SDK, amelyben eset csak egy csomópont lesz jelentve állomások, mert az állomás neve nem érhető el. 
  * Az SDK korábbi verzióiban a [webes SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) fog viselkedni, ahogy az SDK újabb verziók, azonban a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópont függetlenül a tényleges alkalmazásgazdája számát jelenti. 
  * Vegye figyelembe, hogy az alkalmazás az SDK segítségével roleInstance beállítása egy egyéni értékre, ha alapértelmezés szerint ugyanezt az értéket fogja a csomópontok száma határozza meg. 
  * Ügyfélgépek vagy mobil eszközökhöz futó alkalmazáshoz egy új SDK verzióját használja, akkor lehetséges, hogy a csomópontok száma előfordulhat, hogy vissza egy szám, amely túl nagy (az ügyfél gépek vagy a mobil eszközök nagy száma). 

### <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

Nincs olyan kiegészítő díjat [többlépéses webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests). A webes tesztjeinek használatát, amely egy műveletsorozatot végre vonatkozik. 

Nincs "ping-vizsgálatok" egy oldal külön díjmentes. A többlépéses tesztek és ping-vizsgálatok telemetriai fel van töltve, és egyéb telemetriai az alkalmazásból.
 
## <a name="operations-management-suite-subscription-entitlement"></a>Az Operations Management Suite előfizetés jogosultság

Mint [nemrég jelentette be](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/), felhasználókat, akik megvásárolják a Microsoft Operations Management Suite E1 és E2, kérhető le a Application Insights vállalati további összetevőként minden további költség nélkül. Az Operations Management Suite E1 és E2 tárolóegységekhez konkrétan az Application Insights a vállalati terv 1 csomópont jogosultságot. A fentiek szerint minden Application Insights csomópont tartalmazza az adatok naponta (elkülönül Naplóelemzési adatfeldolgozást), az adatok 90 napos megőrzési további költségek nélkül okozhatnak legfeljebb 200 MB. 

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, kell rendelkeznie az Application Insights-erőforrások vállalati árképzési terv. Ez a jogosultság lesz csak csomópontokat, az alapszintű csomag az Application Insights-erőforrások nem valósíthat meg hasznos. Vegye figyelembe, hogy ez a jogosultság nem látható lesz a becsült költség, a szolgáltatások és árképzési panelen látható. 
>
 
## <a name="review-pricing-plans-and-estimate-costs"></a>Tekintse át a csomagok és a becsült költség díjszabása

Az Application Insights megkönnyíti az elérhető árképzési csomagok, és milyen költségeket valószínűleg kell alapján a legutóbbi használati szokások megismerése. Megnyitásával indítsa el a **szolgáltatások + árazás** panel az Application Insights-erőforrások az Azure-portálon:

![Tarifacsomag kiválasztása](./media/app-insights-pricing/01-pricing.png)

**a.** Tekintse át a adatmennyiség az adott hónap. Ez magában foglalja a fogadott és megőrzi minden adat (után bármely [mintavételi](app-insights-sampling.md) a kiszolgáló és az ügyfélalkalmazások, és rendelkezésreállás figyelésére szolgáló tesztek.

**b.** Egy külön kell fizetni történik [többlépéses webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Ebbe nem számítanak bele egyszerű rendelkezésreállás figyelésére szolgáló tesztek, amelyek szerepelnek az adatok kötet kell fizetni.)

**c.** A vállalati terv engedélyezése.

**d.** Kattintson keresztül adatokat szeretné megtekinteni a adatainak mennyisége az elmúlt hónapban, napi kap, vagy adatfeldolgozást mintavételi felügyeleti beállításokkal.

Application Insights költségek az Azure számlázásának hozzáadódnak. A számlázási szakaszban az Azure portál vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![Az oldalsó menüben válassza ki a számlázási.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Átviteli sebesség
A kötet küldött adatok korlátozódik három módja van:

* **Mintavételi:** Ez az eljárás használható a kiszolgáló és az ügyfél alkalmazásoknak, a minimális torzulásának metrikák küldött telemetriai adatok mennyiségének csökkentésére. Ez az adatmennyiség hangolására, hogy az elsődleges eszköz. További információ [szolgáltatások mintavétel](app-insights-sampling.md). 
* **Napi cap:** Ha létrehozása az Application Insights-erőforrást az Azure portálról ennek értéke 100 GB/nap. A Visual Studio Application Insights-erőforrás létrehozásakor alapértelmezés szerint csak a faciliate tesztelést készült kis (csak 32,3 megabájt MB/nap). Ebben az esetben célja, hogy a felhasználó emeli a napi kap éles környezetben az alkalmazás telepítése előtt. A maximális kap kért nagy forgalmú alkalmazások magasabb legfeljebb 1000 GB/nap esetén. Körültekintően járjon el a napi kap beállításakor a leképezés kell lennie **soha nem az, hogy a napi kap találati**, mert fog majd adatvesztés, a nap hátralevő és kell az alkalmazás figyelése nem lehetséges. Az módosításához használja a napi kötet cap panelen kapcsolódó adatok kötetkezelés paneljén (lásd alább). Ne feledje, hogy néhány előfizetéstípusok jóváírás, amely nem használható az Application insights szolgáltatással. Ha az előfizetésnek a költségkeret maximumát, a napi cap panelen lesz utasításokat távolítsa el, és a napi kap túl 32,3 megabájt MB/nap felmerülő engedélyezésével.  
* **Sávszélesség-szabályozás:** ez korlátozza az események (Event), átlagosan több mint 1 perces 32-k sebesség. 


*Mi történik, ha az alkalmazás meghaladja a szabályozási értéket?*

* Tartalmazza az alkalmazás által percenként megfelelőségét ellenőrizni kell. Ha a percre átlagolva másodpercenkénti aránya meghaladja, a kiszolgáló visszautasítja bizonyos kérelmek. Az SDK puffereli, az adatokat, és megkísérli újraküldéséhez megugrását terjednek több perc. Az alkalmazás rendszeresen küld adatokat a fent a szabályozási értéket, ha egyes adatok ki lesznek hagyva. (Az ASP.NET, Java és JavaScript SDK-k megpróbálja újra elküldeni, így; Csomagjától előfordulhat, hogy egyszerűen az vetett szabályozott adatokat.) Sávszélesség-szabályozás történik, akkor megjelenik egy értesítés, figyelmeztetés, hogy ez történik.

*Honnan tudhatom, hogy mennyi adatot küld alkalmazásom?*

* Nyissa meg a **adatok kötetkezelés** panelt, és tekintse meg az adatok napi kötet diagram. 
* A Metrikaböngészőben, új diagram hozzáadása, és válassza ki **adatpontok mennyisége** a metrika szerint. Csoportosítás váltson, és szerint kell csoportosítani a **adattípus**.

## <a name="to-reduce-your-data-rate"></a>Az arány csökkentése érdekében
A következőkben az adatmennyiség csökkentése érdekében teheti:

* Használjon [mintavételi](app-insights-sampling.md). Ez a technológia csökkenti az átviteli sebesség döntés a metrikákat, és keresse meg a keresési kapcsolódó elemek között olyan megszakítása nélkül. Kiszolgálói alkalmazások esetében működik automatikusan.
* [Korlátozza az jelenthetők Ajax-hívások számát](app-insights-javascript.md#detailed-configuration) minden lap nézetben vagy kapcsoló Ajax reporting ki.
* Kapcsolja ki a gyűjtemény modulok által a felesleges [ApplicationInsights.config szerkesztése](app-insights-configuration-with-applicationinsights-config.md). Például dönthet úgy, hogy teljesítményszámlálókkal és a függőségi adatokat inessential.
* Ossza fel a telemetria bekapcsolásával instrumentation kulcsok külön. 
* Előre összesített metrikákat. TrackMetric hívások vezettek az alkalmazásban, ha forgalmat csökkentheti a túlterhelést, amely fogadja a átlagának kiszámítása és a mérési köteg szórása használatával. Vagy használhat egy [előre összesítése csomag](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi kötet kap használatával korlátozható az összegyűjtött adatokat, de a kap teljesülése esetén, a nap hátralevő az alkalmazás által küldött összes telemetriai adat adatvesztést okoz. Az **nem ajánlott** szeretné, hogy az alkalmazás elérte a napi kap, mivel Ön nem tudja követni a állapotának és az alkalmazás teljesítményének után azt talált. 

Ehelyett használjon [mintavételi](app-insights-sampling.md) finomhangolhatják a szintre adatmennyiség kívánja, majd használja a napi kap csak "utolsó lehetőségként", abban az esetben, ha az alkalmazás indítása telemetriai sokkal nagyobb mennyiségű váratlanul küldésekor. 

A napi kap, a konfigurálási csoportban az Application Insights-erőforrás módosításához kattintson a **adatok kötetkezelés** majd **napi maximális**.

![A napi telemetriai kötet kap beállítása](./media/app-insights-pricing/daily-cap.png) 

## <a name="sampling"></a>Mintavételezés
[A mintavételi](app-insights-sampling.md) egy metódust, így csökkentve a telemetriai van küldésének sebessége az alkalmazásban, ugyanakkor megmaradnak helyreállításra a kapcsolódó események megkeresését diagnosztikai keresések során, és továbbra is írásmódja megfelelő esemény száma. 

Mintavételi hatékonyan csökkentheti a költségeket és a havi kvótán belül maradnak. A mintavételi algoritmus megőrzi telemetriai adatokat a kapcsolódó elemeket, hogy az, például a kereséssel, található egy adott kivételhez kapcsolódó a kérést. Az algoritmus is megfelelő számát, őrzi meg, hogy a kérelem sebességét, a kivétel díjszabás és egyéb számok a megfelelő értéke metrika Explorer látható.

Nincsenek mintavételi több űrlap.

* [Adaptív mintavételi](app-insights-sampling.md) az ASP.NET SDK-ban, amely automatikusan igazodni fog a kötet telemetriai adatot az alkalmazás által az alapértelmezett beállítás. Működést automatikusan az SDK-t a webalkalmazás, így csökken a telemetriai adatokat a hálózati forgalom. 
* *Adatfeldolgozást mintavételi* alternatív megoldás, amely működik, ahol az alkalmazásból telemetriai belép az Application Insights szolgáltatással. Ez nincs hatással az alkalmazásból küldött telemetriai adatok mennyiségét, de ez csökkenti a szolgáltatás megőrzi. Használhatja a kvóta telemetriai böngészők és egyéb SDK-k által használt csökkentése érdekében.

Adatfeldolgozást mintavételi beállítása, állítsa be a vezérlő az árazás panelen:

![A kvóta és árképzési panelen kattintson a minták csempéjére, és válassza a mintavételi töredéke alatt.](./media/app-insights-pricing/04.png)

> [!WARNING]
> Az adatok mintavételi panel csak az adatfeldolgozást mintavételi érték határozza meg. A mintavételi ráta alkalmazott az Application Insights SDK az alkalmazás által nem is. Ha a bejövő telemetriai már mintavételezett: az SDK-t, az adatfeldolgozást mintavételi nem lesz alkalmazva.
> 

Annak megállapításához, a tényleges mintavételi ráta, függetlenül attól, hol van érvényben, használjon egy [Analytics lekérdezési](app-insights-analytics.md) Ez például:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Az egyes megőrzi a rekord, `itemCount` azt jelzi, amely azt jelöli, eredeti rekordok száma egyenlő 1 + az előző elvetett rekordok száma. 


## <a name="automation"></a>Automatizálás

Írhat egy parancsfájlt a ár terv beállítása Azure Resource Manager használatával. [Megtudhatja, hogyan](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Korlátozások összegzése
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>További lépések

* [Mintavételezés](app-insights-sampling.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/


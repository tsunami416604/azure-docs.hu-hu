---
title: Az Azure Monitor áttekintése | Microsoft dokumentumok
description: Az Azure-szolgáltatások és -alkalmazások teljes monitorozási stratégiájának részét képező Microsoft-szolgáltatások és funkciók áttekintése.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: cfdd0beac7d257a424d327df71602b4612c3da3b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536853"
---
# <a name="azure-monitor-overview"></a>Azure Monitor – áttekintés

Az Azure Monitor maximalizálja az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét azáltal, hogy átfogó megoldást kínál a felhőbeli és helyszíni környezetekt tartalmazó telemetriai adatok gyűjtésére, elemzésére és a telemetriai adatokra való reagálásra. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek.

Csak néhány példa arra, hogy mit tehet az Azure Monitorral:

- Az [Application Insights](app/app-insights-overview.md)segítségével észlelheti és diagnosztizálhatja az alkalmazások és függőségek közötti problémákat.
- Az [azure-figyelő vel és a tárolókhoz](insights/vminsights-overview.md) való Azure Monitorlal korrelálnak az infrastruktúra [problémáival.](insights/container-insights-overview.md)
- A [Log Analytics](log-query/log-query-overview.md) segítségével részletesen figyelheti meg a figyelési adatokat a hibaelhárításés és a mélydiagnosztika érdekében.
- Támogatja a nagy léptékű műveleteket [intelligens riasztásokkal](platform/alerts-smartgroups-overview.md) és [automatizált műveletekkel.](platform/alerts-action-rules.md)
- Vizualizációkat hozhat létre az Azure [irányítópultjaival](learn/tutorial-logs-dashboards.md) és [munkafüzeteivel.](app/usage-workbooks.md)

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4qXeL]


[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="overview"></a>Áttekintés

Az alábbi diagramon az Azure Monitor magas szintű áttekintése látható. A diagram középpontjában a metrikák és naplók adattárai találhatók, amelyek az Azure Monitor két alapvető adatfelhasználási típusa. A bal oldalon vannak a [források monitoring adatok](platform/data-sources.md) feltöltésére ezeket az [adattárak](platform/data-platform.md). A jobb oldalon vannak a különböző funkciókat, amelyek et az Azure Monitor végez az összegyűjtött adatok, például elemzés, riasztási és streamelés külső rendszerekre.

![Azure Monitor – áttekintés](media/overview/overview.png)

## <a name="monitoring-data-platform"></a>Adatplatform figyelése

Az Azure Monitor által gyűjtött összes adat két alapvető típus, [metrika és napló](platform/data-platform.md)egyikébe illeszkedik. [A metrikák](platform/data-platform-metrics.md) olyan numerikus értékek, amelyek egy adott időpontban a rendszer bizonyos aspektusait írják le. Ezek a könnyű és képes támogatni közel valós idejű forgatókönyvek. [A naplók](platform/data-platform-logs.md) különböző típusú adatokat tartalmaznak, amelyek az egyes típusokhoz különböző tulajdonságkészletekkel rendelkező rekordokba vannak rendezve. Telemetriai adatok, például az események és a nyomkövetések a teljesítményadatok mellett naplókként tárolják, így az összes kombinálható elemzéscéljából.

Számos Azure-erőforrás esetében az Azure Monitor által gyűjtött adatokat közvetlenül az Azure Portal on Overview lapján láthatja. Tekintse meg például bármelyik virtuális gépet, és több, teljesítménymutatókat megjelenítő diagramot fog látni. Kattintson a grafikonok bármelyikére az Azure Portal [metrikakezelőjében](platform/metrics-charts.md) lévő adatok megnyitásához, amely lehetővé teszi több metrika értékeinek időbeli ábrázolását.  A diagramokat interaktívan megtekintheti, vagy rögzítheti őket egy irányítópulton, hogy más vizualizációkkal együtt megtekinthesse őket.

![Mérőszámok](media/overview/metrics.png)

Az Azure Monitor által gyűjtött naplóadatok [elemezhetők lekérdezésekkel](log-query/log-query-overview.md) az összegyűjtött adatok gyors lekéréséhez, összesítéséhez és elemzéséhez.  A lekérdezéseket az Azure Portalon a [Log Analytics](log-query/portals.md) használatával hozhat létre és tesztelheti, majd ezekkel az eszközökkel közvetlenül elemezheti az adatokat, vagy mentheti a lekérdezéseket [vizualizációkkal](visualizations.md) vagy [riasztási szabályokkal](platform/alerts-overview.md)való használatra.

Az Azure Monitor az Azure Data Explorer által használt [Kusto lekérdezési nyelv](/azure/kusto/query/) egy olyan verzióját használja, amely egyszerű naplólekérdezésekhez alkalmas, de speciális funkciókat is tartalmaz, például összesítéseket, illesztéseket és intelligens elemzéseket. A lekérdezési nyelvet gyorsan megtanulhatja [több leckével.](log-query/get-started-queries.md)  Azon felhasználók számára, akik már ismerik az [SQL](log-query/sql-cheatsheet.md) és [Splunk](log-query/splunk-cheatsheet.md) használatát, külön útmutató áll rendelkezésére.

![Naplók](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Milyen adatokat gyűjt az Azure Monitor?

Az Azure Monitor számos különféle forrásból képes adatokat gyűjteni. Az alkalmazással kapcsolatos adatgyűjtést többféle rétegként lehet elképzelni, melyek az alkalmazástól, a különféle operációs rendszereken és a mögöttes szolgáltatásokon át egészen a platformig terjedhetnek. Az Azure Monitor az alábbi szintek mindegyikéről gyűjt adatokat:

- **Alkalmazásfigyelési adatok**: Az Ön által írt kód teljesítményére és működésére vonatkozó adatok, függetlenül annak platformjának.
- **Vendég operációs rendszer figyelési adatok**: Az alkalmazás futtatásának operációs rendszerére vonatkozó adatok. A futtatás történhet az Azure-ban, egy másik felhőben vagy a helyszínen is. 
- **Azure erőforrás-figyelési adatok:** Egy Azure-erőforrás működésére vonatkozó adatok.
- **Azure-előfizetés figyelése adatok:** Az Azure-előfizetés működésére és felügyeletére vonatkozó adatok, valamint az Azure állapotára és működésére vonatkozó adatok. 
- **Azure-bérlőfigyelési adatok:** A bérlői szintű Azure-szolgáltatások, például az Azure Active Directory működésére vonatkozó adatok.

Amint létrehoz egy Azure-előfizetést és elkezdi hozzáadni az erőforrásokat (például virtuális gépeket vagy webalkalmazásokat), az Azure Monitor elkezdi az adatokat gyűjteni.  [A tevékenységnaplók rögzítik](platform/platform-logs-overview.md) az erőforrások létrehozásának vagy módosításának időpontját. [Metrikák ismerteti,](platform/data-platform.md) hogyan teljesít az erőforrás, és az erőforrásokat, hogy az általa felhasznált. 

Bővítse ki az összegyűjtött adatokat az erőforrások tényleges működésére a [diagnosztika engedélyezésével](platform/platform-logs-overview.md) és [egy ügynök hozzáadásával](platform/agent-windows.md) az erőforrások kiszámításához. Ez összegyűjti az erőforrás belső működéséhez telemetriai adatokat, és lehetővé teszi, hogy különböző [adatforrásokat](platform/agent-data-sources.md) konfiguráljon a Windows és a Linux vendég operációs rendszernaplóinak és metrikáinak gyűjtéséhez. 

Engedélyezze az [App Services-alkalmazás](app/azure-web-apps.md) vagy a virtuális gép és a [virtuálisgép-méretezési csoport](app/azure-vm-vmss-apps.md)alkalmazásfigyelésének engedélyezését, hogy az Application Insights részletes információkat gyűjtsön az alkalmazásról, beleértve az oldalnézeteket, az alkalmazáskérelmeket és a kivételeket. További ellenőrizze az alkalmazás rendelkezésre állását egy [rendelkezésre állási teszt](app/monitor-web-app-availability.md) konfigurálásával a felhasználói forgalom szimulálására.

### <a name="custom-sources"></a>Egyéni források

Az Azure Monitor bármely REST-ügyféltől naplóadatokat gyűjthet az [adatgyűjtő API használatával.](platform/data-collector-api.md) Ez lehetővé teszi, hogy egyéni figyelési forgatókönyvek létrehozása, és kiterjeszti a figyelés az erőforrásokra, amelyek nem teszik elérhetővé a telemetriai adatok más forrásokon keresztül.

## <a name="insights"></a>Insights
A figyelési adatok csak akkor hasznosak, ha növelhetik a számítógépes környezet működésének láthatóságát. Az Azure Monitor számos olyan funkciót és eszközt tartalmaz, amelyek értékes betekintést nyújtanak az alkalmazásokba és más erőforrásokba, amelyektől függenek. [A figyelési megoldások](insights/solutions.md) és funkciók, például [az Application Insights](app/app-insights-overview.md) és az Azure Monitor [tárolók](insights/container-insights-overview.md) hoz mély betekintést az alkalmazás és az adott Azure-szolgáltatások különböző aspektusait. 

### <a name="application-insights"></a>Application Insights
[Az Application Insights](app/app-insights-overview.md) figyeli a webes alkalmazások rendelkezésre állását, teljesítményét és használatát, függetlenül attól, hogy azok a felhőben vagy a helyszínen vannak-e. Kihasználja az Azure Monitor hatékony adatelemzési platformját, hogy mélyreható betekintést nyújtson az alkalmazás műveleteibe, és diagnosztizálja a hibákat anélkül, hogy megvárna egy felhasználójelentésre. Az Application Insights számos fejlesztői eszközhöz tartalmaz csatlakozási pontokat, és integrálja a Visual Studióval a DevOps-folyamatok támogatásához.

![App Insights](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
[Az Azure Monitor tárolók egy](insights/container-insights-overview.md) olyan szolgáltatás, amelynek célja az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett felügyelt Kubernetes-fürtökbe üzembe helyezett tárolószámítási feladatok teljesítményének figyelésére. Ez biztosítja a teljesítmény láthatóságát a memória és a processzor metrikák gyűjtése a vezérlők, csomópontok és tárolók, amelyek a Kubernetes a Metrikák API-n keresztül érhetők el. A rendszer a tárolónaplókat is gyűjti.  Miután engedélyezte a figyelést a Kubernetes-fürtökből, ezek a metrikák és naplók automatikusan összegyűjti az Ön számára egy tárolóba a Linux-ügynök tárolós verziója.

![Konténer állapota](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
[Az Azure Monitor virtuális gépek figyeli](insights/vminsights-overview.md) az Azure virtuális gépek (VM) a Windows és a Linux virtuális gépek teljesítményének és állapotának elemzésével, beleértve a különböző folyamatok és az összekapcsolt függőségek más erőforrások és külső folyamatok. A megoldás támogatja a helyszíni vagy más felhőszolgáltató által üzemeltetett virtuális gépek teljesítményének és alkalmazásfüggőségeinek figyelését.  


![Virtuális gép elemzési adatai](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Monitorozási megoldások
Az Azure Monitor [figyelési megoldásai](insights/solutions.md) olyan logikai csoportok, amelyek egy adott alkalmazás vagy szolgáltatás elemzési adatait biztosítják. Ezek közé tartozik az alkalmazás vagy szolgáltatás figyelési adatainak gyűjtésére szolgáló logika, az adatok elemzésére szolgáló [lekérdezések](log-query/log-query-overview.md) és [a vizualizációs nézetek.](../log-analytics/log-analytics-view-designer.md) Figyelési megoldások [állnak rendelkezésre a Microsoft](insights/solutions-inventory.md) és a partnerek, hogy a figyelés a különböző Azure-szolgáltatások és egyéb alkalmazások.

![Monitorozási megoldások](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Reagálás a kritikus helyzetekre
Amellett, hogy lehetővé teszi a figyelési adatok interaktív elemzését, egy hatékony figyelési megoldásnak képesnek kell lennie arra, hogy proaktív módon reagáljon az általa gyűjtött adatokban azonosított kritikus feltételekre. Ez lehet egy sms vagy e-mail küldése egy, a probléma kivizsgálásáért felelős rendszergazdának. Vagy elindíthat egy automatikus folyamatot, amely megpróbálja kijavítani a hibafeltételt.


### <a name="alerts"></a>Riasztások
[Az Azure Monitorban lévő riasztások](platform/alerts-overview.md) proaktív módon értesítik a kritikus feltételekről, és esetleg megkísérlik a korrekciós intézkedések et. A metrikákon alapuló riasztási szabályok közel valós idejű riasztást biztosítanak numerikus értékek alapján, míg a naplókon alapuló szabályok összetett logikát biztosítanak több forrásból származó adatok között.

Az Azure Monitor riasztási szabályai [műveletcsoportokat](platform/action-groups.md)használnak, amelyek egyedi címzettkészleteket és több szabály között megosztható műveleteket tartalmaznak. A követelmények alapján a műveletcsoportok végrehajthatnak ilyen műveleteket, például webhookok használatával, hogy a riasztások külső műveleteket indítsanak, vagy integrálhatók az ITSM-eszközökkel.

![Riasztások](media/overview/alerts.png)

### <a name="autoscale"></a>Automatikus méretezés
Automatikus skálázás lehetővé teszi, hogy a megfelelő mennyiségű erőforrás fut az alkalmazás terhelésének kezeléséhez. Lehetővé teszi olyan szabályok létrehozását, amelyek az Azure Monitor által gyűjtött metrikák at használnak annak meghatározásához, hogy mikor kell automatikusan erőforrásokat hozzáadni a terhelésnövekedés kezeléséhez, és pénzt takarít meg az írásbeli erőforrások eltávolításával. Megadhatja a példányok minimális és maximális számát, valamint azt a logikát, hogy mikor kell növelni vagy csökkenteni az erőforrásokat.

![Automatikus méretezés](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Figyelési adatok megjelenítése
[A vizualizációk,](visualizations.md) például a diagramok és a táblázatok hatékony eszközök a figyelési adatok összegzésére és különböző közönségek számára való megjelenítésére. Az Azure Monitor saját funkciókkal rendelkezik a figyelési adatok megjelenítéséhez, és más Azure-szolgáltatásokat használ a különböző közönségek számára való közzétételhez.

### <a name="dashboards"></a>Irányítópultok
[Az Azure-irányítópultok](../azure-portal/azure-portal-dashboards.md) lehetővé teszik, hogy különböző típusú adatokat egyesítsen, beleértve a metrikákat és a naplókat is, egyetlen ablaktáblában az [Azure Portalon.](https://portal.azure.com) Az irányítópultot megoszthatja más Azure-felhasználókkal. Az Azure Monitor elemei bármely naplólekérdezés vagy metrikadiagram kimenete mellett hozzáadhatók egy Azure-irányítópulthoz. Létrehozhat például egy irányítópultot, amely kombinálja a metrikák grafikonját, a tevékenységnaplók táblázatát, az Application Insights használati diagramját és a naplólekérdezés kimenetét bemutató csempéket.

![Irányítópult](media/overview/dashboard.png)

### <a name="views"></a>Nézetek
[Nézetek](../log-analytics/log-analytics-view-designer.md) vizuálisan jelen naplóadatok az Azure Monitorban.  Minden nézet tartalmaz egy mozaikot, amely a kritikus adatokat összegző listák mellett a vizualizációk , például a sáv- és vonaldiagramok kombinációjáig is leás.  A figyelési megoldások olyan nézeteket tartalmaznak, amelyek egy adott alkalmazás adatait összegzi, és létrehozhat saját nézeteket bármely naplólekérdezés ből származó adatok bemutatásához. Az Azure Monitor többi eleméhez hasonlóan a nézetek is hozzáadhatók az Azure-irányítópultokhoz.

![Nézet](media/overview/view.png)

### <a name="power-bi"></a>Power BI
[A Power BI](https://powerbi.microsoft.com) egy üzleti elemzési szolgáltatás, amely interaktív vizualizációkat biztosít különböző adatforrásokban, és hatékony eszköz arra, hogy az adatokat mások számára is elérhetővé tegyék a szervezeten belül és kívül. Beállíthatja, hogy a Power BI [automatikusan importálja a naplóadatokat az Azure Monitorból,](../log-analytics/log-analytics-powerbi.md) hogy kihasználja ezeket a további vizualizációkat.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Adatok integrálása és exportálása
Gyakran követelmény, hogy integrálja az Azure Monitort más rendszerekkel, és olyan egyéni megoldásokat hozzon létre, amelyek a figyelési adatokat használják. Más Azure-szolgáltatások együttműködnek az Azure Monitorral az integráció biztosítása érdekében.

### <a name="event-hub"></a>Eseményközpont
[Az Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs) egy streamelési platform és eseménybetöltési szolgáltatás, amely bármely valós idejű elemzési szolgáltató vagy kötegelési/tárolási adapter ek használatával képes átalakítani és tárolni az adatokat. Az Event Hubs használatával [streamelheti az Azure Monitor adatait](platform/stream-monitoring-data-event-hubs.md) a SIEM és a figyelési eszközök partneri kapcsolatára.


### <a name="logic-apps"></a>Logic Apps
[A Logic Apps](https://azure.microsoft.com/services/logic-apps) egy olyan szolgáltatás, amely lehetővé teszi a feladatok és üzleti folyamatok automatizálását a különböző rendszerekkel és szolgáltatásokkal integrálható munkafolyamatokkal. Olyan tevékenységek állnak rendelkezésre, amelyek az Azure Monitorban írják és írják a metrikákat és naplókat, így számos más rendszerrel integrálható munkafolyamatokat hozhat létre.


### <a name="api"></a>API
Több API-k állnak rendelkezésre a metrikák és naplók olvasásához és írásához az Azure Monitorba és az Azure Monitorról a generált riasztások elérése mellett. A riasztásokkonfigurálhatók és lekérésre is. Ez lényegében korlátlan lehetőségeket biztosít az Azure Monitorba integrálható egyéni megoldások létrehozásához.

## <a name="next-steps"></a>További lépések
További információk:

* Az Azure Monitor által gyűjtött adatok [metrikák és naplók.](platform/data-platform.md)
* [Adatforrások,](platform/data-sources.md) hogyan az alkalmazás különböző összetevői telemetriai adatokat küld.
* [Naplólekérdezések](log-query/log-query-overview.md) az összegyűjtött adatok elemzéséhez.
* [Gyakorlati tanácsok](https://docs.microsoft.com/azure/architecture/best-practices/monitoring) a felhőalapú alkalmazások és szolgáltatások figyeléséhez.

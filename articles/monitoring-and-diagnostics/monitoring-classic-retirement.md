---
title: Egyesített riasztások és -monitorozás az Azure monitorban váltja fel a klasszikus riasztások és -monitorozás
description: Kivezetési klasszikus figyelési szolgáltatásokat és funkciókat, korábban látható a riasztások (klasszikus) Azure Portal áttekintése. Klasszikus riasztások és -monitorozás klasszikus metrikariasztásokat magában foglalja az Azure-erőforrásokhoz, az Application Insights, az Application Insights, klasszikus webteszt riasztások klasszikus metrikariasztásokat egyéni metrika klasszikus riasztások alapú Application Insights és a klasszikus Application Insights SmartDetection v1 kapcsolatos riasztások
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: da866145f9c1ad39987b0f9a4449d4e5ff7f18f5
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337121"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Egyesített riasztások és -monitorozás az Azure monitorban váltja fel a klasszikus riasztások és -monitorozás

Az Azure Monitor most vált egy figyelési szolgáltatást, amely mostantól támogatja az "Egy metrika" és "Egy riasztások" erőforrásoknak; egységes teljes verem További információkért lásd: a [blogbejegyzés az új Azure Monitor](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Az új Azure monitorozási és riasztási platformok gyorsabb, megalapozottabb állították össze, és a felhő-számítástechnikában és beágyazott a Microsoft Intelligent Cloud filozófia egyre bővülő expanse bővíthető – megtartja lépést. 

Az új Azure monitorozási és riasztási platform helyben, az azt fogja kell kivonása a "klasszikus" figyelési és riasztási platform - ban üzemeltetett *klasszikus riasztások megtekintése* szakaszban az Azure-riasztások elavulttá válik. június 2019.

 ![Az Azure Portalon a klasszikus riasztás](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Javasoljuk, hogy első lépéseket, és hozza létre újra a riasztásokat az új platformon. Nagy számú riasztást rendelkező ügyfelek hogy folyamatban van az új riasztások rendszerre, hogy megszakítás nélkül helyezze át a létező klasszikus riasztások automatikus módot biztosítanak a, vagy hozzáadhatja költségek.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Egyesített metrikákkal és riasztásokkal az Application insights szolgáltatásban

Az Azure Monitor újabb metrika platform most fog power Application Insights figyelési érkező. Ez azt jelenti, hogy az Application Insights a rendszer környezet igénybe vételét a Műveletcsoportok, így sokkal több lehetőség csak a korábbi e-mail és webhook-hívások. A riasztások mostantól küldhetnek hanghívás, az Azure Functions, a Logic Apps, az SMS és ITSM-eszközeit, például a ServiceNow és az Automation-Runbookok. A közel valós idejű figyelés és riasztás, az új platform lehetővé teszi az Application Insights felhasználók használhatja ugyanazt a technológiát a motorja olyan egyéb Azure-erőforrások keresztül történő figyelés és alátámasztó, a Microsoft monitoring.

Az új egységesített figyelés és riasztások kezelése az Application Insights kell foglalnia:

- **Application Insights Platform metrikák** – amely biztosít a népszerű előre összeállított metrikákat az Application Insights terméket. További információkért lásd: Ez a cikk használatával [Platform metrikák az új Azure Monitor az Application Insights](../application-insights/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics).
- **Application Insights rendelkezésre állás és a webes teszt** – amely lehetővé teszi a felmérni a válaszképesség és a webes alkalmazás vagy a kiszolgáló rendelkezésre állását. További információkért lásd: Ez a cikk használatával [rendelkezésre állási tesztek és a riasztások az új Azure Monitor az Application Insights](../application-insights/app-insights-monitor-web-app-availability.md).
- **Application Insights egyéni metrikákat** – amely segítségével definiálhatja és gridre bocsáthatja ki saját metrikákat a figyelés és riasztások. További információkért lásd: Ez a cikk használatával [egyéni metrika az új Azure Monitor az Application Insights](../application-insights/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights Hibaanomáliák (az intelligens detektálás része)** – amely szabály automatikus értesítést küld közel valós időben Ha a webalkalmazás szokatlan mértékben megnőtt a sikertelen HTTP-kérések vagy függőségi hívások sebessége. Application Insights rendellenes hibák (az intelligens detektálás része) az új Azure Monitor, részeként hamarosan elérhető lesz, és frissíteni fogjuk ezt a dokumentumot mutató hivatkozásokat tartalmaz a következő verzió továbbfejlesztésében meg azt a vezethető az elkövetkező hónapokban.

## <a name="unified-metrics--alerts-for-other-azure-resources"></a>Egyesített metrikák és értesítések más Azure-erőforrások

2018 március óta új generációs figyelmeztetések és a többdimenziós monitorozás az Azure-erőforrások rendelkezésre állási lett. Most már az újabb metrika platform és a riasztási közel valós idejű lehetőségeket a gyorsabb. Ami még fontosabb a metrika platform újabb riasztások biztosít részletesebben követheti nyomon, az újabb platform tartalmazza a beállítást, a dimenziók, melyek lehetővé teszik a szelet és szűrő az adott érték kombinációja, az állapot vagy műveletet. Hasonlóan az összeset együtt az új Azure Monitor az újabb metrikariasztásokat is több bővíthető ActionGroups – lehetővé teszi az értesítések bontsa ki a túl az e-mailben vagy SMS, hang, Azure-függvény, Automation-Runbook és több webhook használatával.
Új mérőszámok az Azure-erőforrásokhoz, érhetők el:

- **Az Azure Monitor Standard platform metrikák** – amely biztosítja, hogy a különböző Azure-szolgáltatások és termékek népszerű előre megadott metrikák. További információkért lásd: Ez a cikk a [támogatott mérőszámok az Azure Monitor](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported) és [metrikariasztásokat támogatja az Azure Monitor](../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts).
- **Az Azure Monitor egyéni metrikák** – amely metrikákat biztosít a felhasználó közreműködésével források, például az Azure Diagnostics-ügynök. További információkért lásd: Ez a cikk a [egyéni mérőszámok az Azure Monitor](../azure-monitor/platform/metrics-custom-overview.md). Egyéni metrikák használata esetén is közzéteheti az által gyűjtött metrikák [Windows Azure Diagnostics-ügynök](../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) és [InfluxData Telegraf ügynök](../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md).

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>Klasszikus monitorozási és riasztási platform kivonása

A korábban leírtaknak, monitorozási és riasztási jelenleg használható a platform a klasszikus a [riasztások (klasszikus) szakasz](../azure-monitor/platform/alerts-classic.overview.md) az Azure portal kivezetjük a hónap adott érkező azok felváltották a újabb rendszerre.
Monitorozási és riasztási régebbi klasszikus kivonjuk a forgalomból. június 30 verzióját 2019; beleértve a kapcsolódó API-k, az Azure portál felület és a szolgáltatások megszüntetés. Pontosabban ezek a funkciók megszűnnek:

- Régebbi (klasszikus) metrikákkal és riasztásokkal, jelenleg Azure-erőforrások rendelkezésre állása [riasztások (klasszikus) szakasz](../azure-monitor/platform/alerts-classic.overview.md) az Azure portal; érhető el [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrás
- Régebbi (klasszikus) platform, és egyéni metrikákat az Application Insights, valamint azokat jelenleg elérhető a riasztási [riasztások (klasszikus) szakasz](../azure-monitor/platform/alerts-classic.overview.md) az Azure Portalon, és elérhetőnek [microsoft.insights/ alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrás
- Régebbi (klasszikus) rendellenes hibák riasztás jelenleg elérhető [belül az Application Insights intelligens detektálás](../application-insights/app-insights-proactive-diagnostics.md) ; riasztás konfigurálva az Azure Portalon látható [riasztások (klasszikus) szakasz](../azure-monitor/platform/alerts-classic.overview.md) Azure portál

Az összes klasszikus monitorozási és riasztási rendszer, köztük a megfelelő [API](https://msdn.microsoft.com/library/azure/dn931945.aspx), [PowerShell](../azure-monitor/platform/alerts-classic-portal.md), [CLI](../azure-monitor/platform/alerts-classic-portal.md), [oldala az Azure portal](../azure-monitor/platform/alerts-classic-portal.md), és [ Erőforrás-sablon](../azure-monitor/platform/alerts-enable-template.md) 2019. június végén marad használható. 

Június 2019, az Azure monitorban végén:

- Klasszikus figyelés és riasztások szolgáltatás elavult, és már nem elérhető Új riasztási szabályok létrehozását lesz.
- Riasztási szabályt, amely továbbra is megtalálhatók a riasztások (klasszikus) túli június 2019 végrehajtása és a értesítések továbbra is, de nem áll rendelkezésre, a módosítás.
- Július a 2019-től kezdődően naplóriasztási szabályok a klasszikus monitorozás és riasztások automatikusan átkerülnek a Microsoft által az megfelelőikre az új Azure monitor-platformon. A folyamat zökkenőmentes állásidő nélkül, és ügyfelek lefedettség figyelési adatvesztés nélkül fog rendelkezni.

Hamarosan biztosítjuk, hogy önkéntesen áttelepítése a riasztásokat az eszközök [riasztások (klasszikus) szakasz](../azure-monitor/platform/alerts-classic.overview.md) Azure Portal az új Azure-riasztások. A riasztások (klasszikus), amely lesznek áttelepítve az új Azure monitornak konfigurált összes szabályok ingyenes lesz, és nem számítunk fel. Az áttelepített klasszikus riasztási szabályok is nem szerepelhet értesítések elküldése e-mailben, a webhook vagy LogicApp valamilyen díja. Az újabb értesítés és a művelet típusú (például SMS, hanghívás, ITSM-integrációról stb.) használatát azonban felszámítható, hogy hozzáadott egy áttelepített vagy egy új riasztás lesz. További információkért lásd: [Azure Monitor szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Ezenkívül a következő, az ambit szerint lesz [Azure Monitor szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/monitor/):

- Minden olyan új (nem áttelepített) riasztási szabály, az új platformon az Azure Monitor ingyenes egységek túl létrehozott
- Betöltött és által az Azure Monitor szolgáltatásban foglalt ingyenes egységek nem megőrzött adatok
- Bármely több teszt Application Insights által futtatott webtesztek
- Az Azure Monitor szolgáltatásban foglalt ingyenes egységek túl tárolt bármely egyéni metrikák

Ez a cikk lesz, folyamatosan frissített lesz hivatkozások & Részletek kapcsolatos, az új Azure monitoring és riasztási funkciók, valamint segítséget nyújtanak a felhasználók bevezetése az új Azure Monitor-platform az eszközök rendelkezésre állását.


## <a name="next-steps"></a>További lépések

* További információ a [új, egységes Azure Monitor](../azure-monitor/overview.md).
* További információ az új [Azure Alerts](../azure-monitor/platform/alerts-overview.md).

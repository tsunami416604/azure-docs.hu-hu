---
title: Az Azure Log Analytics felügyeleti megoldás riasztás |} A Microsoft Docs
description: A riasztási felügyeleti megoldás a Log Analytics segítségével dolgozhatják fel a riasztásokat a környezetben.  Mellett konszolidálása riasztások a Log Analytics-ben létrejött akkor importálja riasztások csatlakoztatott System Center Operations Manager felügyeleti csoportokból Log Analytics.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2018
ms.openlocfilehash: 48a825f31a1c5f2eab2fbb71b6f030b8acb5617d
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77668383"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Az Azure Log Analytics felügyeleti megoldás riasztás

![Kezelési figyelmeztető ikon](media/alert-management-solution/icon.png)

> [!NOTE]
>  A Azure Monitor mostantól támogatja a [riasztások nagy léptékű kezelésére](https://aka.ms/azure-alerts-overview)szolgáló továbbfejlesztett képességeket, beleértve az olyan [figyelési eszközök által generált eszközöket is, mint a System Center Operations Manager, a Zabbix vagy a Nagios](https://aka.ms/managing-alerts-other-monitoring-services).
>  


A riasztási felügyeleti megoldás segít dolgozhatják fel a riasztások a Log Analytics-adattárban.  Ezek a riasztások különböző forrásokból származnak, beleértve a [log Analytics által létrehozott](../../azure-monitor/platform/alerts-overview.md) vagy [a Nagios-ból vagy Zabbix importált](../../azure-monitor/learn/quick-collect-linux-computer.md)forrásokból származó forrásokat is. A megoldás a [csatlakoztatott System Center Operations Manager felügyeleti csoportok](../../azure-monitor/platform/om-agents.md)riasztásait is importálja.

## <a name="prerequisites"></a>Előfeltételek
A megoldás a Log Analytics-tárházban található összes rekordtal működik, és a **riasztások**típusával a rekordok összegyűjtéséhez bármilyen konfigurációt kell végrehajtania.

- Log Analytics riasztások esetén [hozzon létre riasztási szabályokat](../../azure-monitor/platform/alerts-overview.md) a riasztási rekordok közvetlen létrehozásához a tárházban.
- A Nagios-és Zabbix-riasztások esetében [konfigurálja ezeket a kiszolgálókat](../../azure-monitor/learn/quick-collect-linux-computer.md) a riasztások log Analyticsba küldéséhez.
- System Center Operations Manager riasztások esetén a [Operations Manager felügyeleti csoportot a log Analytics munkaterülethez kell kötni](../../azure-monitor/platform/om-agents.md).  A System Center Operations Managerben létrehozott riasztásokat is importálja a Log Analytics.  

## <a name="configuration"></a>Konfiguráció
Adja hozzá a Alert Management megoldást a Log Analytics munkaterülethez a [megoldások hozzáadása](../../azure-monitor/insights/solutions.md)című témakörben leírt eljárással. Nincs szükség további konfigurációra.

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportban a Log Analytics-munkaterülethez van csatlakoztatva, majd a következő felügyeleti csomagokat telepíti a System Center Operations Manager a megoldás hozzáadásakor.  Nincs, konfigurációs vagy karbantartási szükséges felügyeleti csomagok.

* A Microsoft System Center Advisor Riasztáskezelés (Microsoft.IntelligencePacks.AlertManagement)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../../azure-monitor/platform/om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="agents"></a>Ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|:--- |:--- |:--- |
| [Windows-ügynökök](agent-windows.md) | Nem |A közvetlen Windows-ügynökök nem hoznak létre riasztásokat.  Log Analytics-riasztásokkal események hozható létre, és teljesítményadatot összegyűjt Windows ügynökök. |
| [Linux-ügynökök](../../azure-monitor/learn/quick-collect-linux-computer.md) | Nem |Közvetlen Linux-ügynökök nem hoznak létre riasztásokat.  Log Analytics-riasztásokkal hozható létre az eseményeket és teljesítményadatokat gyűjtött Linux-ügynököktől.  Nagios- és Zabbix-riasztások ezeken a kiszolgálókon, amelyek igényelnek a Linux-ügynök gyűjtenek. |
| [System Center Operations Manager felügyeleti csoport](../../azure-monitor/platform/om-agents.md) |Igen |Az Operations Manager-ügynökök generált riasztások, a felügyeleti csoporthoz és az majd továbbítja a Log Analytics szolgáltatásba.<br><br>A Log Analyticshez való közvetlen kapcsolat legyen az Operations Manager-ügynökök nem kötelező. Riasztási adatokat a felügyeleti csoportból a Log Analytics-tárházba lesznek továbbítva. |


### <a name="collection-frequency"></a>A gyűjtés gyakorisága
- Riasztási rekordok tartalmaz a megoldáshoz, amint a tárházban tárolódnak.
- Riasztási adatokat küld az Operations Manager felügyeleti csoportból a Log Analytics percen át 3 percenként.  

## <a name="using-the-solution"></a>A megoldás használata
Amikor hozzáadja a Alert Management megoldást a Log Analytics munkaterülethez, a **Alert Management** csempe hozzá lesz adva az irányítópulthoz.  Ez a csempe és grafikus ábrázolását a jelenleg aktív az elmúlt 24 órában létrehozott riasztások számát jeleníti meg.  Ebben az időtartományban nem módosítható.

![Riasztási felügyeleti csempe](media/alert-management-solution/tile.png)

A **Alert Management** csempére kattintva nyissa meg a **Alert Management** irányítópultot.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak.  Mindegyik oszlop felsorolja a 10 leggyakoribb riasztások a megadott hatóköri és időtartományi az adott oszlop feltételeknek megfelelő száma szerint.  Futtathat egy naplóbeli keresést, amely a teljes listát az oszlop alján **található az összes** megjelenítése elemre kattintva vagy az oszlop fejlécére kattintva jeleníti meg.

| Oszlop | Leírás |
|:--- |:--- |
| Kritikus riasztások |Minden riasztás az a súlyossági kritikus riasztás neve szerint csoportosítva.  Kattintson a riasztás minden rekordot ad vissza egy Naplókeresés futtatásához riasztás nevét. |
| Figyelmeztető riasztások |Minden riasztás és a egy figyelmeztető riasztás neve szerint csoportosítva súlyosságát.  Kattintson a riasztás minden rekordot ad vissza egy Naplókeresés futtatásához riasztás nevét. |
| Aktív System Center Operations Manager riasztások |A Operations Managerból gyűjtött összes riasztás a riasztást generáló forrástól *eltérő* állapotú. |
| Összes aktív riasztás |Minden riasztás neve szerint csoportosítva súlyossági az összes riasztás. Csak a *lezárt*állapotú Operations Manager riasztásokat tartalmazza. |

Ha a jobb oldali görgetésre kattint, az irányítópulton számos gyakori lekérdezés látható, melyre kattintva elvégezheti a riasztási adatok [naplóbeli keresését](../../azure-monitor/log-query/log-query-overview.md) .

![Riasztási kezelési irányítópult](media/alert-management-solution/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Alert Management megoldás bármilyen típusú **riasztással**rendelkező rekordot elemez.  Riasztások a Log Analytics által létrehozott vagy Nagios vagy Zabbix gyűjtött nem közvetlenül gyűjti össze a megoldással.

A megoldás nem importálja a riasztásokat a System Center Operations Managerból, és létrehoz egy megfelelő rekordot a **riasztási** típussal és a **OpsManager**SourceSystem.  Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:  

| Tulajdonság | Leírás |
|:--- |:--- |
| `Type` |*Riasztás* |
| `SourceSystem` |*OpsManager* |
| `AlertContext` |Az adatelem XML formátumban kell létrehozni a riasztást kiváltó részletek. |
| `AlertDescription` |A riasztás részletes leírása. |
| `AlertId` |A riasztás GUID azonosítója. |
| `AlertName` |A riasztás neve. |
| `AlertPriority` |A riasztás prioritását. |
| `AlertSeverity` |A riasztás súlyossági szintje. |
| `AlertState` |A riasztás legutóbbi feloldási állapota. |
| `LastModifiedBy` |A felhasználó, aki utoljára módosította a riasztás neve. |
| `ManagementGroupName` |A felügyeleti csoport nevét, ahol a riasztást kiváltó. |
| `RepeatCount` |Hányszor a azonos riasztás generálása a figyelt objektum óta feloldva. |
| `ResolvedBy` |A riasztás feloldva felhasználó neve. Üres, ha a riasztás nem még lett feloldva. |
| `SourceDisplayName` |A riasztást kiváltó figyelési objektum megjelenített neve. |
| `SourceFullName` |A riasztást kiváltó figyelési objektum teljes neve. |
| `TicketId` |Jegyazonosító a riasztás, ha a System Center Operations Manager-környezet integrálva van egy folyamat riasztások jegyek hozzárendeléséhez.  Üres nincs jegy azonosító hozzá van rendelve. |
| `TimeGenerated` |Dátum és idő, hogy a riasztás létrejött. |
| `TimeLastModified` |Dátum és a riasztás legutóbbi módosításának időpontja. |
| `TimeRaised` |Dátum és idő, amely a riasztást kiváltó. |
| `TimeResolved` |Dátum és idő, hogy a riasztás feloldva. Üres, ha a riasztás nem még lett feloldva. |

## <a name="sample-log-searches"></a>Naplókeresési minták
Az alábbi táblázat riasztási, a megoldás által összegyűjtött rekordokkal kapcsolatos naplókeresési mintákat tartalmazza: 

| Lekérdezés | Leírás |
|:---|:---|
| Riasztási &#124; ahol SourceSystem "OpsManager" és a AlertSeverity == "error" és a TimeRaised == > ago(24h) |Az elmúlt 24 órában kiadott kritikus riasztások |
| Riasztási &#124; ahol AlertSeverity "figyelmeztetés" és a TimeRaised == > ago(24h) |Az elmúlt 24 órában kiadott figyelmeztető riasztások |
| Riasztási &#124; ahol SourceSystem == "OpsManager" és az AlertState! = "Lezárt" és a TimeRaised > ago(24h) &#124; összesíteni a Count = count() by SourceDisplayName |Az elmúlt 24 órában kiadott aktív riasztásokkal rendelkező források |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a AlertSeverity == "error" és a TimeRaised == > ago(24h) és AlertState! = "Lezárt" |Az elmúlt 24 órában, továbbra is aktív kiadott kritikus riasztások |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(24h) és AlertState == "Lezárva" |Az elmúlt 24 órában, amely, már lezárt riasztások |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(1d) &#124; összesíteni a Count = count() by AlertSeverity |Az elmúlt 1 napban kiadott riasztások súlyosságuk szerint csoportosítva |
| Riasztási &#124; ahol SourceSystem "OpsManager" és a TimeRaised == > ago(1d) &#124; RepeatCount csökkenő rendezés |Az elmúlt 1 napban kiadott riasztások ismétléseik száma szerint rendezve |



## <a name="next-steps"></a>Következő lépések
* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../../azure-monitor/platform/alerts-overview.md).

---
title: Az Azure Log Analytics felügyeleti megoldás riasztás |} A Microsoft Docs
description: A riasztási felügyeleti megoldás a Log Analytics segítségével dolgozhatják fel a riasztásokat a környezetben.  Mellett konszolidálása riasztások a Log Analytics-ben létrejött akkor importálja riasztások csatlakoztatott System Center Operations Manager felügyeleti csoportokból Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: fe5d534e-0418-4e2f-9073-8025e13271a8
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/19/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 534c47e1c3189258617505808f7a8c7b2e3a3c6e
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711038"
---
# <a name="alert-management-solution-in-azure-log-analytics"></a>Az Azure Log Analytics felügyeleti megoldás riasztás

![Kezelési figyelmeztető ikon](media/log-analytics-solution-alert-management/icon.png)

A riasztási felügyeleti megoldás segít dolgozhatják fel a riasztások a Log Analytics-adattárban.  Ezek a riasztások előfordulhat, hogy többféle forrásból, többek között olyan forrásból kell származnia [Log Analytics által létrehozott](../monitoring-and-diagnostics/monitoring-overview-alerts.md) vagy [Nagios vagy Zabbix importált](log-analytics-quick-collect-linux-computer.md). A megoldás is importálja a riasztások bármelyik [csatlakoztatott felügyeleti csoportok System Center Operations Manager](log-analytics-om-agents.md).

## <a name="prerequisites"></a>Előfeltételek
A megoldás együttműködik a Log Analytics-tárházban típussal rendelkező rekordok **riasztási**, így bármilyen konfigurációra szükség, ezeket a rekordokat gyűjtéséhez kell végrehajtania.

- A Log Analytics-riasztásokkal [riasztási szabályok létrehozásával](../monitoring-and-diagnostics/monitoring-overview-alerts.md) riasztási-rekordok létrehozása közvetlenül a tárházban.
- Nagios- és Zabbix-riasztások esetén [konfigurálja az ezeken a kiszolgálókon](log-analytics-quick-collect-linux-computer.md) riasztások a Log Analyticshez való küldéséhez.
- A System Center Operations Manager riasztásaihoz [az Operations Manager felügyeleti csoport csatlakozni a Log Analytics-munkaterület](log-analytics-om-agents.md).  A System Center Operations Managerben létrehozott riasztásokat is importálja a Log Analytics.  

## <a name="configuration"></a>Konfiguráció
Adja hozzá a riasztási felügyeleti megoldás a Log Analytics-munkaterülethez ismertetett folyamatot [megoldások hozzáadása](../azure-monitor/insights/solutions.md). Nincs szükség további konfigurációra.

## <a name="management-packs"></a>Felügyeleti csomagok
Ha a System Center Operations Manager felügyeleti csoportban a Log Analytics-munkaterülethez van csatlakoztatva, majd a következő felügyeleti csomagokat telepíti a System Center Operations Manager a megoldás hozzáadásakor.  Nincs, konfigurációs vagy karbantartási szükséges felügyeleti csomagok.

* A Microsoft System Center Advisor Riasztáskezelés (Microsoft.IntelligencePacks.AlertManagement)

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](log-analytics-om-agents.md).

## <a name="data-collection"></a>Adatgyűjtés
### <a name="agents"></a>Ügynökök
Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Leírás |
|:--- |:--- |:--- |
| [Windows-ügynökök](log-analytics-agent-windows.md) | Nem |A közvetlen Windows-ügynökök nem hoznak létre riasztásokat.  Log Analytics-riasztásokkal események hozható létre, és teljesítményadatot összegyűjt Windows ügynökök. |
| [Linux-ügynökök](log-analytics-quick-collect-linux-computer.md) | Nem |Közvetlen Linux-ügynökök nem hoznak létre riasztásokat.  Log Analytics-riasztásokkal hozható létre az eseményeket és teljesítményadatokat gyűjtött Linux-ügynököktől.  Nagios- és Zabbix-riasztások ezeken a kiszolgálókon, amelyek igényelnek a Linux-ügynök gyűjtenek. |
| [System Center Operations Manager felügyeleti csoport](log-analytics-om-agents.md) |Igen |Az Operations Manager-ügynökök generált riasztások, a felügyeleti csoporthoz és az majd továbbítja a Log Analytics szolgáltatásba.<br><br>A Log Analyticshez való közvetlen kapcsolat legyen az Operations Manager-ügynökök nem kötelező. Riasztási adatokat a felügyeleti csoportból a Log Analytics-tárházba lesznek továbbítva. |


### <a name="collection-frequency"></a>A gyűjtés gyakorisága
- Riasztási rekordok tartalmaz a megoldáshoz, amint a tárházban tárolódnak.
- Riasztási adatokat küld az Operations Manager felügyeleti csoportból a Log Analytics percen át 3 percenként.  

## <a name="using-the-solution"></a>A megoldás használata
A Log Analytics-munkaterületet a Riasztáskezelési megoldás hozzáadásakor az **Riasztáskezelés** csempe hozzá van adva az irányítópulthoz.  Ez a csempe és grafikus ábrázolását a jelenleg aktív az elmúlt 24 órában létrehozott riasztások számát jeleníti meg.  Ebben az időtartományban nem módosítható.

![Riasztási felügyeleti csempe](media/log-analytics-solution-alert-management/tile.png)

Kattintson a **Riasztáskezelés** csempére kattintva nyissa meg a **Riasztáskezelés** irányítópult.  Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak.  Mindegyik oszlop felsorolja a 10 leggyakoribb riasztások a megadott hatóköri és időtartományi az adott oszlop feltételeknek megfelelő száma szerint.  Egy naplókeresést, amely a teljes listáját jeleníti meg kattintva futtathatja **összes** alján az oszlop, vagy az oszlop fejlécére kattintva.

| Oszlop | Leírás |
|:--- |:--- |
| Kritikus riasztások |Minden riasztás az a súlyossági kritikus riasztás neve szerint csoportosítva.  Kattintson a riasztás minden rekordot ad vissza egy Naplókeresés futtatásához riasztás nevét. |
| Figyelmeztető riasztások |Minden riasztás és a egy figyelmeztető riasztás neve szerint csoportosítva súlyosságát.  Kattintson a riasztás minden rekordot ad vissza egy Naplókeresés futtatásához riasztás nevét. |
| Aktív SCOM-riasztások |Minden riasztás az Operations Manager az gyűjtött minden más, *lezárva* a riasztást kiváltó forrás szerint csoportosítva. |
| Összes aktív riasztás |Minden riasztás neve szerint csoportosítva súlyossági az összes riasztás. Csak más, az Operations Manager riasztásait bármely állapotú tartalmazza *lezárva*. |

Görgessen jobbra, ha az irányítópult megjeleníti-e a számos gyakori lekérdezések végrehajtásához kattinthat egy [naplóbeli keresés](log-analytics-queries.md) riasztási adatok.

![Riasztási kezelési irányítópult](media/log-analytics-solution-alert-management/dashboard.png)


## <a name="log-analytics-records"></a>Log Analytics-rekordok
A riasztási felügyeleti megoldás elemzi a típusú rekordot **riasztási**.  Riasztások a Log Analytics által létrehozott vagy Nagios vagy Zabbix gyűjtött nem közvetlenül gyűjti össze a megoldással.

A megoldás riasztások importálása a System Center Operations Manager és a egy megfelelő rekordot hoz létre az egyes típusú **riasztási** és SourceSystem **OpsManager**.  Ezeket a rekordokat az alábbi táblázatban az jellemzőkkel rendelkeznek:  

| Tulajdonság | Leírás |
|:--- |:--- |
| Típus |*Riasztás* |
| SourceSystem |*OpsManager* |
| AlertContext |Az adatelem XML formátumban kell létrehozni a riasztást kiváltó részletek. |
| AlertDescription |A riasztás részletes leírása. |
| AlertId |A riasztás GUID azonosítója. |
| AlertName |A riasztás neve. |
| AlertPriority |A riasztás prioritását. |
| AlertSeverity |A riasztás súlyossági szintje. |
| AlertState |A riasztás legutóbbi feloldási állapota. |
| LastModifiedBy |A felhasználó, aki utoljára módosította a riasztás neve. |
| ManagementGroupName |A felügyeleti csoport nevét, ahol a riasztást kiváltó. |
| RepeatCount |Hányszor a azonos riasztás generálása a figyelt objektum óta feloldva. |
| ResolvedBy |A riasztás feloldva felhasználó neve. Üres, ha a riasztás nem még lett feloldva. |
| SourceDisplayName |A riasztást kiváltó figyelési objektum megjelenített neve. |
| SourceFullName |A riasztást kiváltó figyelési objektum teljes neve. |
| TicketId |Jegyazonosító a riasztás, ha a System Center Operations Manager-környezet integrálva van egy folyamat riasztások jegyek hozzárendeléséhez.  Üres nincs jegy azonosító hozzá van rendelve. |
| TimeGenerated |Dátum és idő, hogy a riasztás létrejött. |
| TimeLastModified |Dátum és a riasztás legutóbbi módosításának időpontja. |
| TimeRaised |Dátum és idő, amely a riasztást kiváltó. |
| TimeResolved |Dátum és idő, hogy a riasztás feloldva. Üres, ha a riasztás nem még lett feloldva. |

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



## <a name="next-steps"></a>További lépések
* A Log Analytics-riasztások létrehozásával kapcsolatos információkért lásd: [Riasztások a Log Analyticsben](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

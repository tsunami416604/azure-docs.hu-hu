---
title: Az ügynök adatforrásainak konfigurálása a Azure Monitorban | Microsoft Docs
description: Az adatforrások határozzák meg azokat a naplózási adatokat, amelyeket a Azure Monitor ügynökökből és más csatlakoztatott forrásokból gyűjt.  Ez a cikk a Azure Monitor adatforrások felhasználásának fogalmát ismerteti, ismerteti a konfigurálásának részleteit, és összefoglalja a különböző elérhető adatforrásokat.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: a183589c3e5274cf747164cdc33d46044f95e716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073683"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Ügynök-adatforrások a Azure Monitorban
Az ügynököktől Azure Monitor gyűjtött adatokat a konfigurált adatforrások határozzák meg.  Az ügynököktől származó adatokat a rendszer egy rekordhalmazsal rendelkező [naplófájlként](data-platform-logs.md) tárolja.  Minden adatforrás egy adott típusú rekordokat hoz létre, amelyek mindegyike rendelkezik saját tulajdonságokkal.

![Naplózási adatgyűjtés](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Adatforrások összefoglalása
A következő táblázat felsorolja a Azure Monitor jelenleg elérhető ügynök-adatforrásokat.  Mindegyiknek van egy külön cikkre mutató hivatkozása, amely részletesen ismerteti az adatforrás részleteit.   Emellett a metódussal és a gyűjtemények gyakoriságával kapcsolatos információkat is tartalmaz. 


| Adatforrás | Platform | Log Analytics-ügynök | Operations Manager-ügynök | Azure Storage tárterület | Operations Manager kötelező? | A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | érkezéskor |
| [Egyéni naplók](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | érkezéskor |
| [IIS-naplók](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |a naplófájl-átváltási beállítástól függ |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |ütemezett, legalább 10 másodperc |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |ütemezett, legalább 10 másodperc |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |Azure Storage-ból: 10 perc; ügynöktől: érkezéskor |
| [Windows-eseménynaplók](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | érkezéskor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Az adatforrásokat a munkaterület **speciális beállításainak** **adatok** menüjéből konfigurálhatja.  A rendszer bármilyen konfigurációt továbbít a munkaterület összes csatlakoztatott forrásához.  Jelenleg nem zárható ki ügynökök ebből a konfigurációból.

![Windows-események konfigurálása](media/agent-data-sources/configure-events.png)

1. A Azure Portal válassza a **log Analytics munkaterületek** > a munkaterület > **Speciális beállítások**lehetőséget.
2. Válassza **az**adatelemet.
3. Kattintson a konfigurálni kívánt adatforrásra.
4. A konfigurációval kapcsolatos részletekért kövesse a fenti táblázatban található egyes adatforrásokhoz tartozó dokumentációra mutató hivatkozást.


## <a name="data-collection"></a>Adatgyűjtés
Az adatforrás-konfigurációk olyan ügynököknek érkeznek, amelyek néhány percen belül közvetlenül kapcsolódnak Azure Monitorhoz.  A megadott adatokat a rendszer az ügynöktől gyűjti, és közvetlenül az egyes adatforrásokra vonatkozó időközönként továbbítja Azure Monitor.  Tekintse meg az egyes adatforrások dokumentációját ezekre a részletekre.

A csatlakoztatott felügyeleti csoportok System Center Operations Manager ügynökei esetében az adatforrás-konfigurációk felügyeleti csomagokba lesznek lefordítva, és alapértelmezés szerint 5 percenként érkeznek a felügyeleti csoportba.  Az ügynök letölti a felügyeleti csomagot, és a megadott adatokat gyűjti. Az adatforrástól függően a rendszer elküldi az adatforrást egy olyan felügyeleti kiszolgálónak, amely továbbítja az adatAzure Monitor, vagy az ügynök elküldi az adatAzure Monitornak a felügyeleti kiszolgáló nélkül. További részletekért lásd: az [Azure-beli figyelési megoldások adatgyűjtési adatai](../monitor-reference.md) .  A Operations Manager összekapcsolásával és Azure Monitorával kapcsolatos részletekért olvassa el a konfigurációt a System Center Operations Manager- [integráció konfigurálása](om-agents.md)című cikkből.

Ha az ügynök nem tud csatlakozni a Azure Monitorhoz vagy Operations Managerhoz, akkor továbbra is gyűjti az adatokat, amelyeket a kapcsolat létrehozásakor fog teljesíteni.  Az adatvesztés elvész, ha az adatmennyiség eléri a gyorsítótár maximális méretét az ügyfél számára, vagy ha az ügynök 24 órán belül nem tud kapcsolatot létesíteni.

## <a name="log-records"></a>Rekordok naplózása
A Azure Monitor által gyűjtött összes naplózási adatokat a munkaterületen rekordként tárolja a rendszer.  A különböző adatforrások által gyűjtött rekordok saját tulajdonságokkal rendelkeznek, és a **Type (típus** ) tulajdonságuk alapján azonosíthatók.  Az egyes bejegyzéstípusokkal kapcsolatos részletekért tekintse meg az egyes adatforrások és megoldások dokumentációját.

## <a name="next-steps"></a>További lépések
* Ismerkedjen meg azokkal a [figyelési megoldásokkal](../insights/solutions.md) , amelyek a Azure monitor funkciókat bővítik, és adatokat gyűjtenek a munkaterületen.
* További információ az adatforrásokból és a figyelési megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .  
* [Riasztások](alerts-overview.md) konfigurálása az adatforrásokból és a figyelési megoldásokból gyűjtött kritikus adatok proaktív értesítésére.

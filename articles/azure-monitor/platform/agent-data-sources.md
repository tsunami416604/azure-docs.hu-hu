---
title: Ügynökadatforrások konfigurálása az Azure Monitorban | Microsoft dokumentumok
description: Az adatforrások határozzák meg az Okat, amelyeket az Azure Monitor az ügynököktől és más csatlakoztatott forrásokból gyűjt.  Ez a cikk ismerteti a koncepció, hogy az Azure Monitor hogyan használja az adatforrásokat, ismerteti a konfigurálásuk részleteit, és összefoglalót ad a rendelkezésre álló különböző adatforrások.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/28/2018
ms.openlocfilehash: aec3fe2386ce916c556f6da295a8554fff140259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249099"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Ügynökadatforrások az Azure Monitorban
Az Azure Monitor által az ügynököktől gyűjtött adatokat a konfigurált adatforrások határozzák meg.  Az ügynököktől származó adatok at rekordkészlettel rendelkező [naplóadatként](data-platform-logs.md) tárolják.  Minden adatforrás egy adott típusú rekordokat hoz létre, amelyek mindegyik típusának saját tulajdonságkészlete van.

![Naplóadatgyűjtés](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Adatforrások összefoglalása
Az alábbi táblázat az okat az ügynökadatforrásokat sorolja fel, amelyek jelenleg elérhetők az Azure Monitorban.  Mindegyik egy külön cikkre mutató hivatkozást tartalmaz, amely részletezi az adatforrást.   Tájékoztatást nyújt továbbá a gyűjtés módjáról és gyakoriságáról. 


| Adatforrás | Platform | Naplóelemző ügynök | Operations Manager-ügynök | Azure Storage-tárterület | A műveleti vezetőre van szükség? | Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | érkezéskor |
| [Egyéni naplók](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | érkezéskor |
| [IIS-naplók](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |a naplófájl váltási beállításátó beállítástól függ |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |az ütemezés szerint, legalább 10 másodperc |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |az ütemezés szerint, legalább 10 másodperc |
| [Rendszernapló](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |az Azure storage-ból: 10 perc; ügynöktől: érkezéskor |
| [Windows eseménynaplók](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | érkezéskor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Az adatforrásokat a munkaterület **Speciális beállításai** **menüjének Adatok** menüjéből állíthatja be.  A rendszer a munkaterület összes csatlakoztatott forrásához eljuttatja a konfigurációt.  Jelenleg nem zárhat ki ügynököket ebből a konfigurációból.

![Windows-események konfigurálása](media/agent-data-sources/configure-events.png)

1. Az Azure Portalon válassza a **Log Analytics-munkaterületeket,** > a munkaterületet > **a speciális beállításokat.**
2. Válassza **az Adatok**lehetőséget.
3. Kattintson a konfigurálni kívánt adatforrásra.
4. A fenti táblázatban található egyes adatforrások dokumentációjára mutató hivatkozást a konfigurációjukkal kapcsolatos részletekért kövesse.


## <a name="data-collection"></a>Adatgyűjtés
Az adatforrás-konfigurációk néhány percen belül közvetlenül kapcsolódnak az Azure Monitorhoz.  A megadott adatokat az ügynökgyűjti, és közvetlenül az Azure Monitor nak az egyes adatforrásokra jellemző időközönként.  Lásd a dokumentációt az egyes adatforrások ezeket a részleteket.

A Csatlakoztatott felügyeleti csoport System Center Operations Manager ügynökei esetében az adatforrás-konfigurációkat felügyeleti csomagokká alakítják le, és alapértelmezés szerint 5 percenként kézbesítik a felügyeleti csoportnak.  Az ügynök letölti a felügyeleti csomagot, mint bármely más, és összegyűjti a megadott adatokat. Az adatforrástól függően az adatokat vagy elküldi kontbéba egy felügyeleti kiszolgáló, amely továbbítja az adatokat az Azure Monitor, vagy az ügynök elküldi az adatokat az Azure Monitor anélkül, hogy a felügyeleti kiszolgálón keresztül. A részletekért tekintse meg [az adatgyűjtés részleteit az Azure-beli figyelési megoldásokért.](../insights/solutions-inventory.md)  Az Operations Manager és az Azure Monitor csatlakoztatásának részleteiről, valamint a konfiguráció gyakoriságának módosításáról a [System Center Operations Manager rel való integráció konfigurálása](om-agents.md)című részében olvashat.

Ha az ügynök nem tud csatlakozni az Azure Monitorhoz vagy az Operations Managerhez, továbbra is gyűjti azokat az adatokat, amelyeket a kapcsolat létrehozásakor fog kézbesíteni.  Az adatok elveszhetnek, ha az adatmennyiség eléri az ügyfél gyorsítótárának maximális méretét, vagy ha az ügynök 24 órán belül nem tud kapcsolatot létesíteni.

## <a name="log-records"></a>Rekordok naplózása
Az Azure Monitor által gyűjtött összes naplóadat a munkaterületen rekordokként tárolódik.  A különböző adatforrások által gyűjtött rekordok saját tulajdonságkészlettel rendelkeznek, és a **Típus** tulajdonságuk alapján lesznek azonosítva.  Az egyes bejegyzéstípusokra vonatkozó részleteket az egyes adatforrások és megoldások dokumentációjában találja.

## <a name="next-steps"></a>További lépések
* Ismerje meg az Okat tartalmazó [figyelési megoldásokat,](../insights/solutions.md) amelyek funkciókat adnak hozzá az Azure Monitorhoz, és adatokat gyűjtenek a munkaterületre.
* Ismerje meg a [naplólekérdezéseket](../log-query/log-query-overview.md) az adatforrásokból gyűjtött adatok elemzéséhez és a figyelési megoldásokhoz.  
* Riasztások [konfigurálása,](alerts-overview.md) hogy proaktív módon értesítse az adatforrásokból és figyelési megoldásokból gyűjtött kritikus adatokról.

---
title: Adatforrások konfigurálása az Azure Log Analyticsben |} A Microsoft Docs
description: Adatforrások határozzák meg, hogy a Log Analytics gyűjti az ügynökök és az egyéb kapcsolódó források adatait.  Ez a cikk azt ismerteti, hogyan Log Analytics adatforrásokkal ismerteti konfigurálásuk módját részleteit és a különböző adatforrások elérhető összegzést tartalmaz a fogalmat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/26/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 6f5296844541db774610f5a46161f2e06673d99e
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711565"
---
# <a name="data-sources-in-log-analytics"></a>A Log Analytics adatforrások
Log Analytics gyűjti az adatokat a csatlakoztatott források, és azt a Log Analytics-munkaterületen tárolja.  Az adatok az egyes gyűjtött határozza meg azokat az adatforrásokat, konfigurálhat.  A Log Analytics Data rekordkészletet van tárolva.  Minden adatforrás egy adott típusú rekordot hoz létre a saját tulajdonságkészlettel minden típus.

![Log Analytics-adatok gyűjtése](./media/log-analytics-data-sources/overview.png)

Adatforrások eltérnek [felügyeleti megoldások](../azure-monitor/insights/solutions.md), amely is csatlakoztatva forrásból gyűjthet adatokat, és a Log Analytics-rekordok létrehozása.  Adatgyűjtés, megoldások általában mellett közé tartozik a naplókereséseken és nézeteken, amelyek segítségével elemezheti az egy adott alkalmazás vagy szolgáltatás a műveletet.


## <a name="summary-of-data-sources"></a>Adatforrások áttekintése
A következő táblázat sorolja fel az adatforrásokat, amelyek jelenleg a Log Analyticsben.  Mindegyik rendelkezik egy külön cikk tárgyalja részletesen biztosítása, hogy az adatforrás mutató hivatkozást.   A módszer és a Log analyticsbe adatok gyűjtésének gyakorisága információkat is biztosít.  Használhatja az információkat ebben a cikkben, azonosíthatja a különféle elérhető megoldások és más felügyeleti megoldásokkal data flow és a kapcsolat követelményeinek megismeréséhez. Az egyes oszlopok, lásd: [felügyeleti megoldások az Azure-beli adatok gyűjtemény adatait](../azure-monitor/insights/solutions-inventory.md).


| Adatforrás | Platform | A Microsoft-figyelőügynököt | Operations Manager-ügynök | Azure Storage tárterület | Az Operations Manager szükséges? | A felügyeleti csoport Operations Manager-ügynök adatok küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | a beérkezéskor |
| [Egyéni naplók](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | a beérkezéskor |
| [IIS-naplók](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |naplófájlváltás beállítás függ |
| [Teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |ütemezett, legalább 10 másodperc |
| [Teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |ütemezett, legalább 10 másodperc |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |az Azure storage-ból: 10 perc; ügynök: a beérkezéskor |
| [Windows-eseménynaplók](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | a beérkezéskor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Az adatforrásokat kell konfigurálnia a **adatok** menü a Log Analytics **speciális beállítások**.  A munkaterület minden csatlakoztatott források érkeznek semmilyen konfigurálást.  Ez a konfiguráció jelenleg ügynököket nem lehet kizárni.

![Windows-események konfigurálása](./media/log-analytics-data-sources/configure-events.png)

1. Az Azure Portalon válassza ki a **Log Analytics** > a munkaterület > **speciális beállítások**.
2. Válassza ki **adatok**.
3. Kattintson a konfigurálni kívánt adatforrás.
4. Kövesse a hivatkozást a konfigurációjuk részleteiről a fenti táblázatban szereplő minden adatforrás a dokumentációban.


## <a name="data-collection"></a>Adatgyűjtés
Az adatforrások konfigurációját az ügynököknek a Log Analytics közvetlenül csatlakoztatott néhány percen belül lépnek.  A megadott adat az ügynöktől gyűjtött és kézbesítése közvetlenül a Log Analytics minden adatforráshoz adott időközönként.  Az egyes adatforrások ezen bírálattal dokumentációjában talál.

A System Center Operations Manager-ügynökök a csatlakoztatott felügyeleti csoport az adatforrások konfigurációját fordítja, a felügyeleti csomagok és a felügyeleti csoport 5 percenként által szállított alapértelmezett.  Az ügynök letölti a felügyeleti csomagban, mint minden más, és a megadott adatokat gyűjti össze. Az adatforrástól függően az adatok lesznek, vagy elküldi a felügyeleti kiszolgálóra, amely továbbítja az adatokat a Log Analytics vagy az ügynök az adatokat küld a Log Analytics a felügyeleti kiszolgáló áthaladás nélkül. Lásd: [felügyeleti megoldások az Azure-beli adatok gyűjtemény adatait](../azure-monitor/insights/solutions-inventory.md) részleteiről.  Itt olvashat részleteket összekapcsolása az Operations Manager és a Log Analytics és a gyakoriság módosítása, hogy a konfigurációs érkeznek [integráció konfigurálása a System Center Operations Managerrel](log-analytics-om-agents.md).

Ha az ügynök nem tud kapcsolódni a Log Analytics vagy az Operations Manager, továbbra is azt fog nyújtani, amikor kapcsolatot létesít, adatok gyűjtésére.  Ha az adatok mennyisége eléri a gyorsítótár maximális mérete az ügyfél, vagy ha az ügynök nem tud 24 órán belül kapcsolatot létesíteni a adat elveszhet.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Rekordokat a munkaterületet a Log Analytics által gyűjtött összes adat van tárolva.  Különböző adatforrások által összegyűjtött rekordokkal saját tulajdonságkészlettel fog rendelkezni, és azonosítja a **típus** tulajdonság.  A dokumentáció minden adatforráshoz, és a megoldás részletes típusához jelenik meg.

## <a name="next-steps"></a>További lépések
* Ismerje meg [megoldások](../azure-monitor/insights/solutions.md) , amelyek további funkciókkal bővítik a Log Analytics és a is gyűjthet adatokat a munkaterületre.
* Ismerje meg [naplókereséseket](log-analytics-queries.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.  
* Konfigurálása [riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md) proaktívan értesíti az adatforrások és megoldások kritikus fontosságú adatok.

---
title: Az ügynök adatok forrásainak konfigurálása a Log Analytics |} A Microsoft Docs
description: Adatforrások határozzák meg, hogy a Log Analytics gyűjti az ügynökök és más források csatlakozik a naplóadatokat.  Ez a cikk azt ismerteti, hogyan Log Analytics adatforrásokkal ismerteti konfigurálásuk módját részleteit és a különböző adatforrások elérhető összegzést tartalmaz a fogalmat.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2018
ms.author: bwren
ms.openlocfilehash: d9bedeeb2e354dab8bc6a7be56826f28914326be
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101530"
---
# <a name="agent-data-sources-in-log-analytics"></a>Az ügynök az adatforrásokat a Log Analyticsben
Az adatok, amelyek a Log Analytics gyűjti össze az ügynökök határozza meg azokat az adatforrásokat, konfigurálhat.  Az ügynököktől származó adatokat tárolja [adatok](data-collection.md) rekordok vannak beállítva.  Minden adatforrás egy adott típusú rekordot hoz létre a saját tulajdonságkészlettel minden típus.

![Naplózási adatok gyűjtése](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Adatforrások áttekintése
A következő táblázat felsorolja az ügynök adatforrások, amelyek jelenleg a Log Analyticsben.  Mindegyik rendelkezik egy külön cikk tárgyalja részletesen biztosítása, hogy az adatforrás mutató hivatkozást.   A módszer és a gyűjtés gyakorisága információkat is biztosít. 


| Adatforrás | Platform | A Microsoft-figyelőügynököt | Operations Manager-ügynök | Azure Storage tárterület | Az Operations Manager szükséges? | A felügyeleti csoport Operations Manager-ügynök adatok küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | a beérkezéskor |
| [Egyéni naplók](data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | a beérkezéskor |
| [IIS-naplók](data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |naplófájlváltás beállítás függ |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |ütemezett, legalább 10 másodperc |
| [Teljesítményszámlálók](data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |ütemezett, legalább 10 másodperc |
| [Syslog](data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |az Azure storage: 10 perc; ügynök: a beérkezéskor |
| [Windows-eseménynaplók](data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | a beérkezéskor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Az adatforrásokat kell konfigurálnia a **adatok** menüjében **speciális beállítások** a munkaterület számára.  A munkaterület minden csatlakoztatott források érkeznek semmilyen konfigurálást.  Ez a konfiguráció jelenleg ügynököket nem lehet kizárni.

![Windows-események konfigurálása](./media/agent-data-sources/configure-events.png)

1. Az Azure Portalon válassza ki a **Log Analytics** > a munkaterület > **speciális beállítások**.
2. Válassza ki **adatok**.
3. Kattintson a konfigurálni kívánt adatforrás.
4. Kövesse a hivatkozást a konfigurációjuk részleteiről a fenti táblázatban szereplő minden adatforrás a dokumentációban.


## <a name="data-collection"></a>Adatgyűjtés
Az adatforrások konfigurációját az ügynököknek a Log Analytics közvetlenül csatlakoztatott néhány percen belül lépnek.  A megadott adat az ügynöktől gyűjtött és kézbesítése közvetlenül a Log Analytics minden adatforráshoz adott időközönként.  Az egyes adatforrások ezen bírálattal dokumentációjában talál.

A System Center Operations Manager-ügynökök a csatlakoztatott felügyeleti csoport az adatforrások konfigurációját fordítja, a felügyeleti csomagok és a felügyeleti csoport 5 percenként által szállított alapértelmezett.  Az ügynök letölti a felügyeleti csomagban, mint minden más, és a megadott adatokat gyűjti össze. Az adatforrástól függően az adatok lesznek, vagy elküldi a felügyeleti kiszolgálóra, amely továbbítja az adatokat a Log Analytics vagy az ügynök az adatokat küld a Log Analytics a felügyeleti kiszolgáló áthaladás nélkül. Lásd: [adatok gyűjtemény adatait figyelési megoldások az Azure-ban](../../azure-monitor/insights/solutions-inventory.md) részleteiről.  Itt olvashat részleteket összekapcsolása az Operations Manager és a Log Analytics és a gyakoriság módosítása, hogy a konfigurációs érkeznek [integráció konfigurálása a System Center Operations Managerrel](../../log-analytics/log-analytics-om-agents.md).

Ha az ügynök nem tud kapcsolódni a Log Analytics vagy az Operations Manager, továbbra is azt fog nyújtani, amikor kapcsolatot létesít, adatok gyűjtésére.  Ha az adatok mennyisége eléri a gyorsítótár maximális mérete az ügyfél, vagy ha az ügynök nem tud 24 órán belül kapcsolatot létesíteni a adat elveszhet.

## <a name="log-records"></a>Rekordok naplózása
Rekordokat a munkaterületet a Log Analytics által gyűjtött összes adat van tárolva.  Különböző adatforrások által összegyűjtött rekordokkal saját tulajdonságkészlettel fog rendelkezni, és azonosítja a **típus** tulajdonság.  A dokumentáció minden adatforráshoz, és a megoldás részletes típusához jelenik meg.

## <a name="next-steps"></a>További lépések
* Ismerje meg [figyelési megoldások](../../azure-monitor/insights/solutions.md) , amelyek további funkciókkal bővítik a Azure Monitor és is gyűjthet adatokat a munkaterületre.
* Ismerje meg [lekérdezések naplózását](../../log-analytics/log-analytics-queries.md) elemezheti az összegyűjtött adatok forrásból származó és figyelési megoldások.  
* Konfigurálása [riasztások](../../monitoring-and-diagnostics/monitoring-overview-alerts.md) segítségével proaktívan kaphat értesítést gyűjtött adatforrások és megoldások monitorozása kritikus fontosságú adatok.

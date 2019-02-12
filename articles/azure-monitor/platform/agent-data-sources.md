---
title: Az ügynök adatforrások konfigurálása az Azure Monitor |} A Microsoft Docs
description: Adatforrások a naplózási adatokat, hogy az Azure Monitor gyűjti az ügynökök és az egyéb kapcsolódó források határozzák meg.  Ez a cikk azt ismerteti, hogyan Azure Monitor adatforrásokkal ismerteti konfigurálásuk módját részleteit és a különböző adatforrások elérhető összegzést tartalmaz a fogalmat.
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
ms.date: 11/28/2018
ms.author: bwren
ms.openlocfilehash: ad4270700712311d8bea2e2014701a99e44c735f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992445"
---
# <a name="agent-data-sources-in-azure-monitor"></a>Az ügynök adatforrások az Azure monitorban
Az Azure Monitor az ügynökök által gyűjtött adatok határozza meg azokat az adatforrásokat, konfigurálhat.  Az ügynököktől származó adatokat tárolja [adatok](data-collection.md) rekordok vannak beállítva.  Minden adatforrás egy adott típusú rekordot hoz létre a saját tulajdonságkészlettel minden típus.

![Naplózási adatok gyűjtése](media/agent-data-sources/overview.png)

## <a name="summary-of-data-sources"></a>Adatforrások áttekintése
Az alábbi táblázat az ügynök adatforrásokhoz, amelyek jelenleg az Azure Monitor érhető el.  Mindegyik rendelkezik egy külön cikk tárgyalja részletesen biztosítása, hogy az adatforrás mutató hivatkozást.   A módszer és a gyűjtés gyakorisága információkat is biztosít. 


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

![Windows-események konfigurálása](media/agent-data-sources/configure-events.png)

1. Az Azure Portalon válassza ki a **Log Analytics-munkaterületek** > a munkaterület > **speciális beállítások**.
2. Válassza ki **adatok**.
3. Kattintson a konfigurálni kívánt adatforrás.
4. Kövesse a hivatkozást a konfigurációjuk részleteiről a fenti táblázatban szereplő minden adatforrás a dokumentációban.


## <a name="data-collection"></a>Adatgyűjtés
Az adatforrások konfigurációját az ügynökök, amelyek közvetlenül csatlakozik az Azure Monitor néhány percen belül lépnek.  A megadott adat az ügynöktől gyűjtött és kézbesítése közvetlenül az Azure Monitor minden adatforráshoz adott időközönként.  Az egyes adatforrások ezen bírálattal dokumentációjában talál.

A System Center Operations Manager-ügynökök a csatlakoztatott felügyeleti csoport az adatforrások konfigurációját fordítja, a felügyeleti csomagok és a felügyeleti csoport 5 percenként által szállított alapértelmezett.  Az ügynök letölti a felügyeleti csomagban, mint minden más, és a megadott adatokat gyűjti össze. Az adatforrástól függően az adatok lesznek, vagy elküldi a felügyeleti kiszolgálóra, amely az Azure Monitor továbbítja az adatokat, vagy az ügynök az adatokat küld az Azure Monitor a felügyeleti kiszolgáló áthaladás nélkül. Lásd: [adatok gyűjtemény adatait figyelési megoldások az Azure-ban](../insights/solutions-inventory.md) részleteiről.  Itt olvashat részleteket összekapcsolása az Operations Manager és az Azure Monitor és a gyakoriság módosítása, hogy a konfigurációs érkeznek [integráció konfigurálása a System Center Operations Managerrel](om-agents.md).

Ha az ügynök nem tud kapcsolódni az Azure Monitor vagy az Operations Manager, továbbra is azt fog nyújtani, amikor kapcsolatot létesít, adatok gyűjtésére.  Ha az adatok mennyisége eléri a gyorsítótár maximális mérete az ügyfél, vagy ha az ügynök nem tud 24 órán belül kapcsolatot létesíteni a adat elveszhet.

## <a name="log-records"></a>Rekordok naplózása
Az Azure Monitor által gyűjtött összes naplóadatok rögzíti a munkaterület van tárolva.  Különböző adatforrások által összegyűjtött rekordokkal saját tulajdonságkészlettel fog rendelkezni, és azonosítja a **típus** tulajdonság.  A dokumentáció minden adatforráshoz, és a megoldás részletes típusához jelenik meg.

## <a name="next-steps"></a>További lépések
* Ismerje meg [figyelési megoldások](../insights/solutions.md) , amelyek további funkciókkal bővítik a Azure Monitor és is gyűjthet adatokat a munkaterületre.
* Ismerje meg [lekérdezések naplózását](../log-query/log-query-overview.md) elemezheti az összegyűjtött adatok forrásból származó és figyelési megoldások.  
* Konfigurálása [riasztások](alerts-overview.md) segítségével proaktívan kaphat értesítést gyűjtött adatforrások és megoldások monitorozása kritikus fontosságú adatok.
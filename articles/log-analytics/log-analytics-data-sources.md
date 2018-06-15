---
title: Adatforrások konfigurálása az Azure Naplóelemzés |} Microsoft Docs
description: Adatforrások határozza meg, hogy a Naplóelemzési gyűjti az ügynökök és egyéb kapcsolódó források adatait.  A cikkből megtudhatja, hogyan Naplóelemzési az adatforrás, konfigurálásukról részleteit ismerteti, és a különböző forrásokból elérhető összegzését tartalmazza.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2018
ms.author: bwren
ms.openlocfilehash: 5201d02b4f70f964f39b4fe135e4715732b9741a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31792548"
---
# <a name="data-sources-in-log-analytics"></a>A Naplóelemzési adatforrások
A Naplóelemzési adatokat gyűjti össze a csatlakoztatott források, és azt a Naplóelemzési munkaterület tárolja.  Az egyes összegyűjtött adatokon az adatforrásokat, amelyeknél megadta határozzák meg.  A Naplóelemzési adatok rekordkészlet tárolja.  Az egyes adatforrások egy adott típusú rekordot hoz létre a saját tulajdonságkészletbe minden típus.

![Jelentkezzen az Analytics-adatok gyűjtése](./media/log-analytics-data-sources/overview.png)

Adatforrások eltérnek [megoldások](log-analytics-add-solutions.md), amely is csatlakoztatott források adatokat gyűjteni, és Naplóelemzési rekordokat létrehozni.  Mellett gyűjtött adatokat, megoldások rendszerint napló keresések és nézetek egy adott alkalmazás vagy szolgáltatás működésének elemzéséhez.


## <a name="summary-of-data-sources"></a>Adatforrások áttekintése
A következő táblázat sorolja fel az adatforrásokat, amelyek jelenleg a Naplóelemzési.  Mindegyik rendelkezik-e egy külön cikk tárgyalja részletek megadása, hogy az adatforrás mutató hivatkozást.   A metódus és a Log Analyticshez való használatra vonatkozó adatok gyűjtésének gyakorisága információkat is biztosít.  Ez a cikk a információt elérhető különböző megoldások azonosítására és a különböző felügyeleti megoldások adatok megismeréséhez és kapcsolat követelmények megértése érdekében használhatja. Az egyes oszlopok, lásd: [az gyűjtemény adatait az Azure-ban a felügyeleti megoldásokra](../monitoring/monitoring-solutions-inventory.md).


| Adatforrás | Platform | A Microsoft figyelési ügynök | Operations Manager-ügynök | Azure Storage tárterület | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| [Egyéni naplók](log-analytics-data-sources-custom-logs.md) | Windows |&#8226; |  | |  |  | érkezésükkor |
| [Egyéni naplók](log-analytics-data-sources-custom-logs.md) | Linux   |&#8226; |  | |  |  | érkezésükkor |
| [IIS-naplók](log-analytics-data-sources-iis-logs.md) | Windows |&#8226; |&#8226; |&#8226; |  |  |5 perc |
| [Teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) | Windows |&#8226; |&#8226; |  |  |  |Ütemezés szerint, a 10 másodperces minimális |
| [Teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) | Linux |&#8226; |  |  |  |  |Ütemezés szerint, a 10 másodperces minimális |
| [Syslog](log-analytics-data-sources-syslog.md) | Linux |&#8226; |  |  |  |  |az Azure storage: 10 perc; az ügynök: érkezésükkor |
| [Windows-Eseménynapló](log-analytics-data-sources-windows-events.md) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | érkezésükkor |


## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Konfigurálja az adatforrást a **adatok** Naplóelemzési menüjében **speciális beállítások**.  A munkaterületen lévő összes csatlakoztatott források kerülnek beállításra.  Ez a konfiguráció jelenleg nem összes ügynököt kizárni.

![Windows-események konfigurálása](./media/log-analytics-data-sources/configure-events.png)

1. Válassza ki az Azure-portálon **Naplóelemzési** > a munkaterület > **speciális beállítások**.
2. Válassza ki **adatok**.
3. Kattintson a konfigurálni kívánt adatforrást.
4. Kövesse a hivatkozást minden adatforrás a fenti táblázatban további részletek a dokumentációban a konfigurációban.


## <a name="data-collection"></a>Adatgyűjtés
Adatok forrás konfigurációk érkeznek ügynökök Naplóelemzési közvetlenül csatlakoztatott néhány percen belül.  A megadott adatok gyűjtése az ügynöktől, és minden adatforráshoz adott időközönként közvetlenül Naplóelemzési kerülnek.  A részletekről az egyes adatforrások dokumentációjában olvashatók.

A System Center Operations Manager-ügynökök a csatlakoztatott felügyeleti csoporthoz adatok forrás konfigurációk lefordítani a felügyeleti csomagok és a felügyeleti csoport 5 percenként által szállított alapértelmezett.  Az ügynök tölti le a felügyeleti csomag más, és a megadott adatokat gyűjti. Attól függően, hogy az adatforrás adatok lesznek vagy elküldi a felügyeleti kiszolgálóra, amely továbbítja az adatokat a szolgáltatáshoz, vagy az ügynök az adatokat küld a Naplóelemzési a management kiszolgálón keresztül nélkül. Tekintse meg [az gyűjtemény adatait](log-analytics-add-solutions.md#data-collection-details) részleteiről.  Áttekintheti az Operations Manager és a Naplóelemzési összekötő és gyakoriságának módosítása, hogy a konfigurálás a rendszer [integráció konfigurálása a System Center Operations Manager](log-analytics-om-agents.md).

Ha az ügynök nem tud kapcsolódni a szolgáltatáshoz, vagy az Operations Manager, adatokat gyűjthet, amelyek akkor fog továbbítani, amikor kapcsolatot létesít az folytatódik.  Ha az adatok el nem éri a gyorsítótár maximális méretét az ügyfél, vagy ha az ügynök nem képes 24 órán belül kapcsolatot létesíteni a adat elveszhet.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
Naplóelemzési által gyűjtött összes adat rögzíti a munkaterületen tárolja.  Különböző forrásokból gyűjtött rögzíti a saját tulajdonságkészletbe lesz, és azonosítható, ha azok **típus** tulajdonság.  A dokumentáció az egyes adatforrások és a megoldás részletes kapcsolatban típusához.

## <a name="next-steps"></a>További lépések
* További tudnivalók [megoldások](log-analytics-add-solutions.md) , amely Naplóelemzési funkciók hozzáadása, és is gyűjthet adatokat a munkaterületre.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.  
* Konfigurálása [riasztások](log-analytics-alerts.md) proaktív értesítik a kritikus fontosságú adatok összegyűjtése az adatforrások és megoldásokat.

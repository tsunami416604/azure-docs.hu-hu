---
title: "Adatforrások konfigurálása az OMS szolgáltatáshoz |} Microsoft Docs"
description: "Adatforrások határozza meg, hogy a Naplóelemzési gyűjti az ügynökök és egyéb kapcsolódó források adatait.  A cikkből megtudhatja, hogyan Naplóelemzési az adatforrás, konfigurálásukról részleteit ismerteti, és a különböző forrásokból elérhető összegzését tartalmazza."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 67710115-c861-40f8-a377-57c7fa6909b4
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2017
ms.author: bwren
ms.openlocfilehash: 00d030a502cf70ea9a5dea767f560cdf2919573e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="data-sources-in-log-analytics"></a>A Naplóelemzési adatforrások
A Naplóelemzési adatokat gyűjti össze a csatlakoztatott források szakaszát az OMS-munkaterület, és tárolja a OMS-tárházban.  Az egyes összegyűjtött adatokon az adatforrásokat, amelyeknél megadta határozzák meg.  Az OMS-tárházban található adatok rekordkészlet tárolja.  Az egyes adatforrások egy adott típusú rekordot hoz létre a saját tulajdonságkészletbe minden típus.

![Jelentkezzen az Analytics-adatok gyűjtése](./media/log-analytics-data-sources/overview.png)

Az adatforrások azok is adatokat gyűjteni a csatlakoztatott források és rekordok létrehozása az OMS-tárházban OMS-megoldások eltérő.  Megoldások is hozzáadhatók a munkaterületen a megoldások gyűjteményből, és általában nyújt további elemzésére szolgáló eszközöket az OMS-portálon.  

## <a name="summary-of-data-sources"></a>Adatforrások áttekintése
Az adatforrásokat, amelyek jelenleg a Naplóelemzési a következő táblázatban láthatók.  Mindegyik rendelkezik-e egy külön cikk tárgyalja részletek megadása, hogy az adatforrás mutató hivatkozást.

| Adatforrás | Esemény típusa | Leírás |
|:--- |:--- |:--- |
| [Egyéni naplók](log-analytics-data-sources-custom-logs.md) |\<Naplónév\>_CL |Windows vagy Linux ügynökök naplófájl-információkat tartalmazó szövegfájlok. |
| [Windows-Eseménynapló](log-analytics-data-sources-windows-events.md) |Esemény |Az eseménynaplóban talál gyűjtött eseményeket a Windows rendszerű számítógépeken. |
| [Windows-teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) |A Teljesítményfigyelő |Windows rendszerű számítógépek gyűjtött teljesítményszámlálók. |
| [Linux-teljesítményszámlálók](log-analytics-data-sources-performance-counters.md) |A Teljesítményfigyelő |A Linux rendszerű számítógépekről gyűjtött teljesítményszámlálók. |
| [IIS-naplók](log-analytics-data-sources-iis-logs.md) |W3CIISLog |Az Internet Information Services W3C formátumban naplózza. |
| [Syslog](log-analytics-data-sources-syslog.md) |Rendszernapló |Syslog-események Windows vagy Linux rendszerű számítógépeken. |

## <a name="configuring-data-sources"></a>Adatforrások konfigurálása
Konfigurálja az adatforrást a **adatok** Naplóelemzési menüjében **beállítások**.  Az OMS-munkaterület minden csatlakoztatott források kerülnek beállításra.  Ez a konfiguráció jelenleg nem összes ügynököt kizárni.

![Windows-események konfigurálása](./media/log-analytics-data-sources/configure-events.png)

1. Az OMS-konzolon kattintson a **beállítások** csempére vagy a **beállítások** gomb a képernyő tetején.
2. Válassza ki **adatok**.
3. Kattintson a konfigurálni kívánt adatforrást.
4. Kövesse a hivatkozást minden adatforrás a fenti táblázatban további részletek a dokumentációban a konfigurációban.

> [!NOTE]
> Az Azure-portál jelenleg Naplóelemzési adatforrások nem lehet konfigurálni.

## <a name="data-collection"></a>Adatgyűjtés
Adatok forrás konfigurációk érkeznek ügynökök Naplóelemzési közvetlenül csatlakoztatott néhány percen belül.  A megadott adatok gyűjtése az ügynöktől, és minden adatforráshoz adott időközönként közvetlenül Naplóelemzési kerülnek.  A részletekről az egyes adatforrások dokumentációjában olvashatók.

A System Center Operations Manager (SCOM) ügynökök a csatlakoztatott felügyeleti csoportban található adatok forrás konfigurációk lefordítani a felügyeleti csomagok és a felügyeleti csoport 5 percenként által szállított alapértelmezett.  Az ügynök tölti le a felügyeleti csomag más, és a megadott adatokat gyűjti. Attól függően, hogy az adatforrás adatok lesznek vagy elküldi a felügyeleti kiszolgálóra, amely továbbítja az adatokat a szolgáltatáshoz, vagy az ügynök az adatokat küld a Naplóelemzési a management kiszolgálón keresztül nélkül. Tekintse meg [az gyűjtemény adatait az OMS-szolgáltatásaival és megoldásaival](log-analytics-add-solutions.md#data-collection-details) részleteiről.  Áttekintheti az SCOM és OMS összekötő és gyakoriságának módosítása, hogy a konfigurálás a rendszer [integráció konfigurálása a System Center Operations Manager](log-analytics-om-agents.md).

Ha az ügynök nem tud kapcsolódni a szolgáltatáshoz, vagy az Operations Manager, adatokat gyűjthet, amelyek akkor fog továbbítani, amikor kapcsolatot létesít az folytatódik.  Ha az adatok el nem éri a gyorsítótár maximális méretét az ügyfél, vagy ha az ügynök nem képes 24 órán belül kapcsolatot létesíteni a adat elveszhet.

## <a name="log-analytics-records"></a>Log Analytics-rekordok
A Naplóelemzési által gyűjtött összes adat az OMS-tárház rekordok tárolja.  Különböző forrásokból gyűjtött rögzíti a saját tulajdonságkészletbe lesz, és azonosítható, ha azok **típus** tulajdonság.  A dokumentáció az egyes adatforrások és a megoldás részletes kapcsolatban típusához.

## <a name="next-steps"></a>Következő lépések
* További tudnivalók [megoldások](log-analytics-add-solutions.md) , amely Naplóelemzési funkciók hozzáadása, és is gyűjthet adatokat a OMS tárházba.
* További tudnivalók [keresések jelentkezzen](log-analytics-log-searches.md) az adatforrások és a megoldások gyűjtött adatok elemzésére.  
* Konfigurálása [riasztások](log-analytics-alerts.md) proaktív értesítik a kritikus fontosságú adatok összegyűjtése az adatforrások és megoldásokat.

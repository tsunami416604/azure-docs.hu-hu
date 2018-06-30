---
title: Az Azure Log Analytics lekérdezési nyelv Adatlap |} Microsoft Docs
description: Ez a cikk segítséget nyújt ha már ismeri az örökölt nyelvhez tartozó Naplóelemzési az új lekérdezési nyelv való váltás.
services: operations-management-suite
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.component: na
ms.openlocfilehash: 7c2158d8e6f64c7c356ba40b3bf56684f00cb8c0
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132938"
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Azure Naplóelemzés új lekérdezési nyelv való váltás
A Naplóelemzési nemrég végrehajtott egy új lekérdezési nyelv.  Ez a cikk segítséget nyújt, ha már ismeri az örökölt nyelv, és továbbra is az egyes segítségre van szüksége a Naplóelemzési ezen a nyelven való váltás.

## <a name="resources"></a>További források


## <a name="language-converter"></a>Nyelvi konverter

Ha ismeri az örökölt Log Analytics lekérdezési nyelv, a ugyanabban a lekérdezésben létrehozni az új nyelven legkönnyebben használatára a nyelvi átalakító, amely a keresési napló portál telepítve van, ha a munkaterületet alakítja át.  Az átalakító használata éppolyan egyszerű, mintha egy örökölt lekérdezést a felső mezőbe, írja be, majd kattintson **átalakítása**.  A Keresés gombra a lekérdezés vagy a példány futtatásához, és illessze be azt máshol használandó vagy kattinthat.

![Nyelvi konverter](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>További források
A [dokumentációs oldalát a napló Analytics Query Language](https://docs.loganalytics.io) rendelkezik az erőforrásokhoz, új nyelvi gyorsan lesz.  Ez magában foglalja, oktatóanyagok, példák és a teljes nyelvi dokumentáció.


## <a name="cheat-sheet"></a>Hasznos tanácsok

A következő táblázat általános lekérdezések egyenértékű parancsok között az új és az örökölt lekérdező nyelve az Azure Log Analytics számos összehasonlítása.

| Leírás | Örökölt | új |
|:--|:--|:--|
| Minden olyan táblát keresése      | error | keressen az "error" (nem kis-és nagybetűket) |
| Válassza ki az adatok táblázatból | Type=Event |  Esemény |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Típus = esemény &#124; top 100 | Esemény &#124; 100 igénybe |
| Karakterláncok összehasonlításának      | Type=Event Computer=srv01.contoso.com   | Esemény &#124; ahol számítógép == "srv01.contoso.com" |
|                        | Típus esemény Computer=contains("contoso") = | Esemény &#124; ahol számítógép tartalmazza a "contoso" (nem kis-és nagybetűket)<br>Esemény &#124; ahol számítógép contains_cs "Contoso" (kis-és nagybetűket) |
|                        | Típus = esemény számítógép = RegEx ("\@contoso @")  | Esemény &#124; ahol számítógép megfelel a reguláris kifejezéssel ". *contoso*" |
| Dátum összehasonlítása        | Típus esemény TimeGenerated = > most-1DAYS | Esemény &#124; ahol TimeGenerated > ago(1d) |
|                        | Típus esemény TimeGenerated = > 2017-05-01 TimeGenerated < 2017-05-31 | Esemény &#124; ahol TimeGenerated között (datetime(2017-05-01)... datetime(2017-05-31)) |
| Logikai összehasonlítása     | Type=Heartbeat IsGatewayInstalled=false  | Szívverés \| ahol IsGatewayInstalled == false |
| Rendezés                   | Típus = esemény &#124; számítógép asc, az eseménynaplóban desc, EventLevelName asc rendezése | Esemény \| számítógép asc, az eseménynaplóban desc, EventLevelName asc rendezés |
| Különböző               | Típus = esemény &#124; deduplikáció számítógép \| számítógép kiválasztása | Esemény &#124; összesítse a számítógépen, az eseménynaplóban talál |
| Oszlopok kiterjesztése         | Típus = telj CounterName = "kihasználtsága (%)" &#124; BŐVÍTÉSE if(map(CounterValue,0,50,0,1),"HIGH","LOW") KIHASZNÁLTSÁGÁT, | A Teljesítményfigyelő &#124; adott CounterName == "kihasználtsága (%) \| kihasználtsági kiterjesztése = iff ("Alacsony"a"Felső"> 50. ellenértéknek) |
| Összesítés            | Típus = esemény &#124; count() mérésére számítógépenként darabszámként | Esemény &#124; összesíteni a Count = count() számítógépenként |
|                                | Típus = telj ObjectName processzor CounterName = "kihasználtsága (%) = &#124; avg(CounterValue) mértékcsoport által számítógép időköz 5 perc | A Teljesítményfigyelő &#124; ahol ObjectName == "Processzor" és a CounterName == "kihasználtsága (%) &#124; összefoglalója avg(CounterValue) számítógépenként bin (TimeGenerated, azaz 5 perc) |
| Összesítő és a korlátja | Típus = esemény &#124; számítógépenként count() mérésére &#124; felső 10 | Esemény &#124; AggregatedValue összefoglalója számítógépenként count() = &#124; 10 korlátozása |
| a UNION                  | Típus = esemény vagy típus = Syslog | Syslog esemény, Unió |
| Csatlakozás                   | Típus = NetworkMonitoring &#124; belső AgentIP csatlakozás (típus = szívverés) ComputerIP | NetworkMonitoring &#124; jellegű csatlakozás belső = (keresési típus == "Szívverés") a $left. AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>További lépések
- Tekintse meg a [útmutató a lekérdezések írásáról](https://go.microsoft.com/fwlink/?linkid=856078) az új lekérdezés nyelven.
- Tekintse meg a [Query Language Reference](https://go.microsoft.com/fwlink/?linkid=856079) minden parancs, a kezelők és a lekérdezési nyelv új funkciók leírását.  

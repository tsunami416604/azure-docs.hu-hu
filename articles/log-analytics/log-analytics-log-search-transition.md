---
title: "Az Azure Log Analytics lekérdezési nyelv Adatlap |} Microsoft Docs"
description: "Ez a cikk segítséget nyújt ha már ismeri az örökölt nyelvhez tartozó Naplóelemzési az új lekérdezési nyelv való váltás."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: bwren
ms.openlocfilehash: 9c487ab33859ae453a0074ef0344f61de19c7b4d
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>Azure Naplóelemzés új lekérdezési nyelv való váltás
A Naplóelemzési nemrég végrehajtott egy új lekérdezési nyelv.  Ez a cikk segítséget nyújt, ha már ismeri az örökölt nyelv, és továbbra is az egyes segítségre van szüksége a Naplóelemzési ezen a nyelven való váltás.

## <a name="resources"></a>Erőforrások


## <a name="language-converter"></a>Nyelvi konverter

Ha ismeri az örökölt Log Analytics lekérdezési nyelv, a ugyanabban a lekérdezésben létrehozni az új nyelven legkönnyebben használatára a nyelvi átalakító, amely a keresési napló portál telepítve van, ha a munkaterületet alakítja át.  Az átalakító használata éppolyan egyszerű, mintha egy örökölt lekérdezést a felső mezőbe, írja be, majd kattintson **átalakítása**.  A Keresés gombra a lekérdezés vagy a példány futtatásához, és illessze be azt máshol használandó vagy kattinthat.

![Nyelvi konverter](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="resources"></a>Erőforrások
A [dokumentációs oldalát a napló Analytics Query Language](https://docs.loganalytics.io) rendelkezik az erőforrásokhoz, új nyelvi gyorsan lesz.  Ez magában foglalja, oktatóanyagok, példák és a teljes nyelvi dokumentáció.


## <a name="cheat-sheet"></a>Adatlap

A következő táblázat általános lekérdezések egyenértékű parancsok között az új és az örökölt lekérdező nyelve az Azure Log Analytics számos összehasonlítása.

| Leírás | Örökölt | új |
|:--|:--|:--|
| Minden olyan táblát keresése      | error | keressen az "error" (nem kis-és nagybetűket) |
| Válassza ki az adatok táblázatból | Típus = esemény |  Esemény |
|                        | Típus = esemény & #124; Válassza ki a forrás, az eseménynaplóban, eseményazonosító | Esemény & #124; a projekt forrás, az eseménynaplóban, eseményazonosító |
|                        | Típus = esemény & #124; az első 100 | Esemény & #124; 100 igénybe |
| Karakterláncok összehasonlításának      | Típus esemény Computer=srv01.contoso.com =   | Esemény & #124; Ha számítógép == "srv01.contoso.com" |
|                        | Típus esemény Computer=contains("contoso") = | Esemény & #124; Ha a számítógépen található a "contoso" (nem kis-és nagybetűket)<br>Esemény & #124; Ha számítógép contains_cs "Contoso" (kis-és nagybetűket) |
|                        | Típus = esemény számítógép = RegEx ("@contoso@")  | Esemény & #124; Ha a számítógép megegyezik regex ". *contoso*" |
| Dátum összehasonlítása        | Típus esemény TimeGenerated = > most-1DAYS | Esemény & #124; Ha TimeGenerated > ago(1d) |
|                        | Típus esemény TimeGenerated = > 2017-05-01 TimeGenerated < 2017-05-31 | Esemény & #124; Ha TimeGenerated között (datetime(2017-05-01)... datetime(2017-05-31)) |
| Logikai összehasonlítása     | Típus = szívverés IsGatewayInstalled = false  | Szívverés \| Ha IsGatewayInstalled == false |
| Rendezés                   | Típus = esemény & #124; Számítógép asc, az eseménynaplóban desc, EventLevelName asc rendezése | Esemény \| Rendezze a számítógép asc, az eseménynaplóban desc, EventLevelName asc |
| Különböző               | Típus = esemény & #124; a deduplikáció számítógép \| Jelölje be a számítógép | Esemény & #124; számítógép, az eseménynaplóban összefoglalója |
| Oszlopok kiterjesztése         | Típus = telj CounterName = "kihasználtsága (%)" & #124; BŐVÍTÉSE if(map(CounterValue,0,50,0,1),"HIGH","LOW"), kihasználtsága | A Teljesítményfigyelő & #124; Ha CounterName == "kihasználtsága (%)" \| Kihasználtság kiterjesztése = iff ("Alacsony" a "Felső" > 50. ellenértéknek) |
| Összesítés            | Típus = esemény & #124; mérték count() számítógépenként darabszámként | Esemény & #124; összesíteni a Count = count() számítógépenként |
|                                | Típus = telj ObjectName processzor CounterName = = "kihasználtsága (%)" & #124; mérték avg(CounterValue) által számítógép időköz 5 perc | A Teljesítményfigyelő & #124; Ha ObjectName == "Processzor" és a CounterName == "kihasználtsága (%)" & #124; összefoglalója avg(CounterValue) számítógépenként bin (TimeGenerated, azaz 5 perc) |
| Összesítő és a korlátja | Típus = esemény & #124; mérték count() számítógép & #124; első 10 | Esemény & #124; AggregatedValue összefoglalója = count() számítógép & #124; 10 korlátozása |
| a UNION                  | Típus = esemény vagy típus = Syslog | Syslog esemény, Unió |
| Csatlakozás                   | Típus = NetworkMonitoring & #124; Csatlakozás a belső AgentIP (típus = szívverés) ComputerIP | NetworkMonitoring & #124; Csatlakozás típusú belső = (keresési típus == "Szívverés") a $left. AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>Következő lépések
- Tekintse meg a [útmutató a lekérdezések írásáról](https://go.microsoft.com/fwlink/?linkid=856078) az új lekérdezés nyelven.
- Tekintse meg a [Query Language Reference](https://go.microsoft.com/fwlink/?linkid=856079) minden parancs, a kezelők és a lekérdezési nyelv új funkciók leírását.  

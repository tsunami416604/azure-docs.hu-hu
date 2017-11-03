---
title: "Előre jelezni a vehicle állapotát, és ki irányítja az szokásait - Azure |} Microsoft Docs"
description: "A Cortana Intelligence szolgáltatásai segítségével a vehicle állapotát, és ki irányítja a valós idejű és prediktív dcu szokásokat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: af8b3d5bf891c93c30a05c5f02d86639a466dde5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>A járműtelemetria-elemzési megoldás forgatókönyve
Ez **menü** Ez a forgatókönyv a fejezetek mutató hivatkozásokat tartalmaz. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Áttekintés
Super számítógépek kívül a labor került át, és most leparkolni vannak a garázs! Ezek a legmodernebb autók érzékelők, nyomon követheti és felügyelheti a több millió esemény másodpercenként teszi lehetővé a számtalan tartalmaz. Elvárjuk, hogy által 2020, ezek autók többsége fogja csatlakoztatni az internethez. Képzelje el azokat a számos olyan adatokat annak érdekében, nagyobb biztonsági, megbízhatósági és jobban vezetői élmény koppintva! A Microsoft tett ezt a helyzetet a Cortana Intelligence megváltozz.

A Microsoft a Cortana Intelligence egy teljes körűen felügyelt big Data típusú adatok és a speciális analytics suite, amely lehetővé teszi az adatok átalakítása intelligens művelet. Azt szeretnénk, bemutatja a Cortana Intelligence Vehicle Telemetriai Analytics megoldás sablont. Ez a megoldás bemutatja, hogyan car kereskedők autó gyártók és biztosítási vállalatok segítségével a Cortana Intelligence képességeit valós idejű és prediktív elemzések a vehicle állapotát, és ki irányítja a szokásokat. 

A megoldás bevezetése egy [lambda architektúra mintát](https://en.wikipedia.org/wiki/Lambda_architecture) megjelenítése a teljes lehetséges a Cortana Intelligence Platform for valós idejű és kötegelt feldolgozást. A megoldás: 

* a Vehicle telematika szimulátor biztosít
* kihasználja a választásával dolgozhat fel szimulált vehicle telemetriai események több millió az Azure Event Hubs 
* használja a Stream Analytics vehicle egészségügyi valós idejű dcu
* továbbra is fennáll az adatokat a hosszú távú tároló gazdagabb kötegelt elemzéséhez. 
* kihasználja a Machine Learning közüli a valós idejű és a batch-prediktív dcu feldolgozása.
* kihasználja a HDInsight átalakítására léptékű adatok és a Data Factory vezénylési, ütemezés, erőforrás-kezelés és figyelés, a köteges feldolgozás folyamatának kezelése 
* Ez a megoldás részletes irányítópult ad valós idejű adatok és a prediktív elemzés képi megjelenítések Power BI használatával

## <a name="architecture"></a>Architektúra
![Megoldás architektúrája](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*1. ábra – Vehicle Telemetriai Analytics megoldás architektúrája*

Ez a megoldás tartalmaz a következő **Cortana Intelligence összetevők** és a végpontok közötti integráció bővíthető:

* **Az Event Hubs** választásával dolgozhat fel a vehicle telemetriai események több millió az Azure.
* **Stream Analytics** való vehicle egészségügyi valós idejű elemzése és továbbra is fennáll, hogy az adatok a hosszú távú tároló gazdagabb kötegelt elemzéséhez.
* **Gépi tanulás** közüli valós idejű és prediktív dcu kötegfeldolgozási.
* **HDInsight** rendszer elkészítéséhez használja léptékű adatok átalakítása
* **Adat-előállító** vezénylési, ütemezés, erőforrás-kezelés és figyelés, a köteges feldolgozás folyamatának kezeli.
* **A Power BI** Ez a megoldás részletes irányítópult ad valós idejű adatok és a prediktív elemzés képi megjelenítések.

Ez a megoldás két különböző hozzáfér **adatforrások**: 

* **Szimulált vehicle jelek és diagnosztikai**: bocsát ki diagnosztikai adatokat, és azt jelzi, hogy megfelelnek a vehicle és adott vezetői mintát állapotának vehicle telematika szimulátor időben. 
* **Vehicle katalógus**: az modell-lel egy VIN tartalmazó referencia-adatkészletnek.


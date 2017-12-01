---
title: "Előre jelezni a vehicle állapotát, és ki irányítja az szokásait - Azure |} Microsoft Docs"
description: "A Cortana Intelligence szolgáltatásai segítségével a vehicle állapotát, és ki irányítja a valós idejű és prediktív dcu szokásokat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: bradsev
ms.openlocfilehash: 8584cade8e12b4f06ae455227e702844a06f512a
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Vehicle Telemetriai elemzési megoldások forgatókönyv
Ez a forgatókönyv a fejezetek menü hivatkozásokat: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Áttekintés
Szuperszámítógépek kívül a labor került át és garázsok leparkolni most vannak. Ezek a legmodernebb autók tárfiókokhoz érzékelők, amely lehetővé teszi, nyomon követheti és figyelheti több millió esemény másodpercenként tartalmaz. Által 2020 ezek járművekről gyűjtött többsége fog csatlakozni az internethez. Koppintson a számos olyan adatokat a nagyobb biztonságot, a megbízhatóság és a jobban vezetői élményt biztosíthat. A Microsoft teszi ezt a helyzetet a Cortana Intelligence megváltozz.

A Cortana Intelligence egy teljes körűen felügyelt big Data típusú adatok és a speciális analytics suite, amelyek segítségével az adatok átalakítása intelligens művelet. A Cortana Intelligence Vehicle Telemetriai Analytics Megoldássablonban bemutatja, hogyan car kereskedők autó gyártók és biztosítási vállalatok kaphatnak a valós idejű és prediktív elemzések a vehicle állapotát, és ki irányítja a szokásokat. 

A megoldás bevezetése egy [lambda architektúra mintát](https://en.wikipedia.org/wiki/Lambda_architecture), amely mutatja a teljes lehetséges a Cortana Intelligence Platform for valós idejű és kötegelt feldolgozást.

## <a name="architecture"></a>Architektúra
A Vehicle Telemetriai elemzési megoldások architektúrájának ezen a diagramon látható:

![Megoldás architektúrája](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)


Ez a megoldás a következő Cortana Intelligence összetevőket tartalmazza, és a végpontok közötti integráció bővíthető:

* **Az Azure Event Hubs** vehicle telemetriai események több millió ingests az Azure.
* **Az Azure Stream Analytics** biztosít a valós idejű elemzése a vehicle állapotát, és továbbra is fennáll, hogy az adatok a hosszú távú tároló gazdagabb kötegelt elemzéséhez.
* **Az Azure Machine Learning** rendellenességeket észleli a valós idejű és kötegfeldolgozási prediktív elemzések biztosításához használt.
* **Az Azure HDInsight** átalakítja az adatokat a skála.
* **Az Azure Data Factory** vezénylési, ütemezés, erőforrás-kezelés és figyelés, a köteges feldolgozás folyamatának kezeli.
* **A Power BI** Ez a megoldás részletes irányítópult ad valós idejű adatok és a prediktív elemzés képi megjelenítések.

Ez a megoldás két különböző adatforrásokhoz fér hozzá: 

* **Szimulált vehicle jelek és diagnosztikai**: bocsát ki diagnosztikai adatokat, és azt jelzi, hogy megfelelnek a vehicle és adott vezetői mintát állapotának vehicle telematika szimulátor időben. 
* **Vehicle katalógus**: A referencia-adatkészlet VINs leképezve modellek.


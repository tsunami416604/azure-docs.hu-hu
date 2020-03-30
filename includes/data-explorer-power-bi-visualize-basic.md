---
author: mgblythe
ms.service: data-explorer
ms.topic: include
ms.date: 11/14/2018
ms.author: mblythe
ms.openlocfilehash: 9624856841ec7473543575c31928c6eefd1404c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178860"
---
Most, hogy már rendelkezik adatokkal a Power BI Desktopban, jelentéseket hozhat létre az adatok alapján. Hozzon létre egy egyszerű jelentést egy oszlopdiagrammal, amely állapotonként mutatja a vágási károkat.

1. A fő Power BI ablak bal oldalán válassza ki a jelentésnézetet.

    ![Jelentésnézet](media/data-explorer-power-bi-visualize-basic/report-view.png)

1. A **VIZUALIZÁCIÓK** panelen válassza a fürtözött oszlopdiagramot.

    ![Oszlopdiagram hozzáadása](media/data-explorer-power-bi-visualize-basic/add-column-chart.png)

    A vásznon megjelenik egy üres diagram.

    ![Üres diagram](media/data-explorer-power-bi-visualize-basic/blank-chart.png)

1. A **FIELDS** listában válassza a DamageCrops and State **(Meg)** és **az Állapot elemet.**

    ![Mezők kiválasztása](media/data-explorer-power-bi-visualize-basic/select-fields.png)

    Most már van egy diagramja, amely a táblázat első 1000 sorának növénytermesztési károsodását mutatja.

    ![Terméskárosodás állam onként](media/data-explorer-power-bi-visualize-basic/damage-column-chart.png)

1. Mentse a jelentést.
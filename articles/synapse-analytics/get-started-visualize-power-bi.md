---
title: 'Oktatóanyag: Ismerkedés az Azure szinapszis Analytics szolgáltatással – munkaterület-adatmegjelenítés Power BI'
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI munkaterületet, hogyan kapcsolhatja össze az Azure szinapszis-munkaterületet, és hogyan hozhat létre olyan Power BI-adatkészletet, amely az Azure szinapszis munkaterületen használja az információkat.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: business-intelligence
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 64a87df4e4fea9fb29e787ab3420f1a62f41323d
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592413"
---
# <a name="visualize-data-with-power-bi"></a>Adatok megjelenítése Power BI használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Power BI munkaterületet, hogyan kapcsolhatja össze az Azure szinapszis-munkaterületet, és hogyan hozhat létre olyan Power BI adatkészletet, amely az Azure szinapszis-munkaterületen található információkat használja. 

## <a name="overview"></a>Áttekintés

A New York-i taxi-adatokból összesített adatkészleteket hoztunk létre két táblában:
- **nyctaxi.passengercountstats**
- **SQLDB1. dbo. PassengerCountStats**

Power BI munkaterületet az Azure szinapszis-munkaterülethez kapcsolhat. Ez a funkció lehetővé teszi, hogy egyszerűen beolvassa az adatait a Power BI munkaterületre. Az Power BI-jelentéseket közvetlenül az Azure szinapszis munkaterületen szerkesztheti.

### <a name="create-a-power-bi-workspace"></a>Power BI munkaterület létrehozása

1. Jelentkezzen be a [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Hozzon létre egy új, **NYCTaxiWorkspace1** nevű Power bi-munkaterületet.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Az Azure szinapszis-munkaterület összekapcsolása az új Power BI munkaterülettel

1. A szinapszis Studióban lépjen a **Manage**  >  **társított szolgáltatások** kezelése elemre.
1. Válassza **New**  >  **az új kapcsolódás Power bi**
1. **Név** beállítása **NYCTaxiWorkspace1**
1. **Munkaterület nevének** beállítása **NYCTaxiWorkspace1**
1. Kattintson a **Létrehozás** gombra.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Az Azure szinapszis-munkaterületen adatokat használó Power BI adatkészlet létrehozása

1. A szinapszis Studióban lépjen a **fejlesztés**  >  **Power bi** elemre.
1. Lépjen a **NYCTaxiWorkspace1**  >  **Power bi adatkészletek** elemre, és válassza az **új Power bi adatkészlet** lehetőséget.
1. Vigye a kurzort a **SQLDB1** -adatbázis fölé, és válassza a **download. pbids fájlt**.
1. Nyissa meg a letöltött **. pbids** fájlt. Power BI az asztal megnyílik, és automatikusan csatlakozik a **SQLDB1** -hez az Azure szinapszis-munkaterületen.
1. Ha megjelenik egy párbeszédpanel, amely az **SQL Server-adatbázis** néven látható:
    1. Válassza a **Microsoft-fiók** lehetőséget.
    1. Válassza a **Bejelentkezés** lehetőséget, és jelentkezzen be a fiókjába.
    1. Válassza a **Kapcsolódás** lehetőséget.
1. A **navigátor** párbeszédpanel megnyitása után jelölje be a **PassengerCountStats** tábla jelölőnégyzetét, és válassza a **Betöltés** lehetőséget.
1. Ha megjelenik a **kapcsolatbeállítások** párbeszédpanel, válassza a **DirectQuery**  >  **OK** elemet.
1. Kattintson a bal oldalon található **jelentés** gombra.
1. **Diagram** hozzáadása a jelentéshez
    1. Húzza a **PassengerCount** oszlopot a **vizualizációk**  >  **tengelyére**.
    1. Húzza a **SumTripDistance** és a **AvgTripDistance** oszlopot a **vizualizációk**  >  **értékeire**.
1. A **Kezdőlap** lapon válassza a **Közzététel** lehetőséget.
1. A módosítások mentéséhez válassza a **Mentés** gombot.
1. Válassza ki a **PassengerAnalysis. pbix** fájl nevét, majd kattintson a **Mentés** gombra.
1. A **cél kiválasztása lapon** válassza a **NYCTaxiWorkspace1** lehetőséget, majd kattintson a **kiválasztás** elemre.
1. Várjon, amíg a közzététel befejeződik.

### <a name="configure-authentication-for-your-dataset"></a>Az adatkészlet hitelesítésének konfigurálása

1. Nyissa meg a [powerbi.microsoft.com](https://powerbi.microsoft.com/) , és **Jelentkezzen be**.
1. A bal oldalon, a **munkaterületek** területen válassza a **NYCTaxiWorkspace1** -munkaterületet.
1. A munkaterületen belül keresse meg az **utas-elemzés** nevű adatkészletet és egy **utas-elemzés** nevű jelentést.
1. Vigye a kurzort a **PassengerAnalysis** adatkészlet fölé, válassza a három pontot (...), majd válassza a **Beállítások** lehetőséget.
1. Az **adatforrás hitelesítő** adatai területen állítsa a **hitelesítési módszert** **OAuth2** értékre, majd válassza a **Bejelentkezés** lehetőséget.

### <a name="edit-a-report-in-synapse-studio"></a>Jelentés szerkesztése a szinapszis Studióban

1. Lépjen vissza a szinapszis studióba, és válassza a **Bezárás és frissítés** lehetőséget.
1. Nyissa meg a **fejlesztés** központot.
1. Mutasson a **Power bi** fölé, és válassza a **Power bi jelentések** csomópontjának frissítése lehetőséget.
1. **Power bi** alatt meg kell jelennie a következőknek:
    * A **NYCTaxiWorkspace1**  >  **Power bi adatkészletek** alatt egy új, **PassengerAnalysis** nevű adathalmazt.
    * A **NYCTaxiWorkspace1**  >  **Power bi-jelentések** területen egy új, **PassengerAnalysis** nevű jelentés szerepel.
1. Válassza ki a **PassengerAnalysis** jelentést. Megnyílik a jelentés, amely közvetlenül a szinapszis Studióban szerkeszthető.

## <a name="monitor-activities"></a>Tevékenységek monitorozása

1. A szinapszis Studióban nyissa meg a **figyelő** hubot.
1. Ezen a helyen megtekintheti a munkaterületen zajló összes tevékenység előzményeit, és azokat is, amelyek aktívak.
1. Ismerkedjen meg a **folyamat futtatásával** , **Apache Spark alkalmazásokkal** és **SQL-kérésekkel** , hogy megtekintse a munkaterületen már elvégzett munkát.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Figyelés](get-started-monitor.md)
                                 


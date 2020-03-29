---
title: Adatok megjelenítése a Microsoft Excel Azure Data Explorer-összekötője segítségével
description: Ebben a cikkben megtudhatja, hogyan használhatja az Excel-összekötő t az Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849054"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Adatok megjelenítése az Azure Data Explorer excelhez készült összekötője segítségével

Az Azure Data Explorer két lehetőséget biztosít az excelbeli adatokhoz való csatlakozáshoz: használja a natív összekötőt, vagy importáljon egy lekérdezést az Azure Data Explorerből. Ez a cikk bemutatja, hogyan használhatja a natív összekötőt az Excelben, és hogyan csatlakozhat az Azure Data Explorer-fürthöz az adatok leéséhez és megjelenítéséhez.

Az Azure Data Explorer natív összekötő lehetővé teszi a lekérdezési eredmények exportálását az Excelbe. További számításokhoz vagy vizualizációkhoz Excel-adatforrásként is hozzáadhat KQL-lekérdezést.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto-lekérdezés definiálása Excel-adatforrásként, és az adatok betöltése az Excelbe

1. Nyissa meg **a Microsoft Excelt**.
1. Az **Adatok** lapon válassza az Adatok >  **beszedése****az Azure-ból** > **az Azure Data Explorerből**lehetőséget.

    ![Adatok bekéselése az Azure Data Explorerből](media/excel-connector/get-data-from-adx.png)

1. Az **Azure Data Explorer (Kusto)** ablakban töltse ki a következő mezőket, és válassza az **OK gombot.**

    ![Az Azure Data Explorer (Kusto) ablaka](media/excel-connector/adx-connection-window.png)
    
    |Mező   |Leírás |
    |---------|---------|
    |**Fürt**   |   A fürt neve (kötelező)      |    
    |**Adatbázis**     |    Az adatbázis neve      |    
    |**Táblanév vagy Azure Data Explorer-lekérdezés**    |     Tábla vagy Azure Data Explorer-lekérdezés neve    | 
    
    **Speciális beállítások:**

     |Mező   |Leírás |
    |---------|---------|
    |**Lekérdezéseredmény-rekordszámának korlátozása**     |     Az excelbe betöltött rekordok számának korlátozása  |    
    |**Lekérdezési eredmény adatméretének korlátozása (bájt)**    |    Az adatok méretének korlátozása      |   
    |**Eredményhalmaz csonkolásának letiltása**    |         |      
    |**További Set utasítások (pontosvesszővel elválasztva)**    |    Adatforrásra alkalmazandó utasítások hozzáadása `set`     |   

1.  A **Navigátor** ablaktáblában keresse meg a megfelelő táblát. A táblázat betekintő ablaktáblájában válassza az **Adatok átalakítása** lehetőséget az adatok módosításához, vagy a **Betöltés** lehetőséget az Excelbe való betöltéshez.

![Táblázat előnézeti ablaka](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Ha **az adatbázis** és/vagy a tábla neve vagy az Azure Data Explorer **lekérdezés** már meg van adva, a megfelelő táblabetekintő ablaktábla automatikusan megnyílik. 

## <a name="analyze-and-visualize-data-in-excel"></a>Adatok elemzése és megjelenítése az Excelben

Miután az adatok betöltődnek az Excel-lapra, elemezheti, összegezheti és megjelenítheti az adatokat kapcsolatok és vizualizációk létrehozásával. 

1.  A **Táblázat terve** lapon válassza az **Összegzés kimutatással**lehetőséget. A **Kimutatás létrehozása** ablakban jelölje ki a megfelelő táblázatot, majd kattintson az **OK**gombra.

    ![Kimutatástábla létrehozása](media/excel-connector/create-pivot-table.png)

1. A **Kimutatásmezők** ablaktáblában jelölje ki a megfelelő táblázatoszlopokat az összesítő táblázatok létrehozásához. Az alábbi példában az **EventId** és **az State** van kiválasztva.
    
    ![Kimutatásmezők kijelölése](media/excel-connector/pivot-table-pick-fields.png)

1. A **Kimutatás elemzése** lapon válassza a **Kimutatásdiagram** lehetőséget a táblázaton alapuló vizualizációk létrehozásához. 

    ![Kimutatás diagram](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Az alábbi példában az **Eseményazonosító**, **a StartTime**és az **EventType** segítségével további információkat tekinthet meg az időjárási eseményekről.

    ![Adatok vizualizációja](media/excel-connector/visualize-excel-data.png)

1. Hozzon létre teljes irányítópultokat az adatok figyeléséhez.

## <a name="next-steps"></a>További lépések

[Adatok megjelenítése a Microsoft Excelbe importált Azure Data Explorer Kusto lekérdezéssel](excel-blank-query.md)
---
title: A Microsoft Excelhez készült Azure Adatkezelő-összekötő használatával megjelenítheti az adatmegjelenítést
description: Ebből a cikkből megtudhatja, hogyan használhatja az Excel-összekötőt az Azure Adatkezelőhoz.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849054"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Az Excelhez készült Azure Adatkezelő Connector használatával megjelenítheti az adatmegjelenítést

Az Azure Adatkezelő két lehetőséget kínál az adatokhoz való csatlakozásra az Excelben: használja a natív összekötőt, vagy importáljon egy lekérdezést az Azure Adatkezelőból. Ebből a cikkből megtudhatja, hogyan használhatja a natív összekötőt az Excelben, és hogyan csatlakozhat az Azure Adatkezelő-fürthöz az adatlekérdezéshez és megjelenítéshez.

Az Azure Adatkezelő Excel natív összekötője lehetőséget nyújt a lekérdezési eredmények Excelbe való exportálására. KQL-lekérdezést is hozzáadhat Excel-adatforrásként további számításokhoz és vizualizációk létrehozásához.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Kusto-lekérdezés definiálása Excel-adatforrásként és az Excelbe való betöltés

1. Nyissa meg a **Microsoft Excelt**.
1. Az **adatok** lapon válassza az azure- > Azure **-ból** > az **adatok lekérése** **Az Azure-** ból adatkezelő lehetőséget.

    ![Adatok beolvasása az Azure Adatkezelőból](media/excel-connector/get-data-from-adx.png)

1. Az **Azure adatkezelő (Kusto)** ablakban végezze el a következő mezőket, majd kattintson **az OK gombra**.

    ![Azure Adatkezelő (Kusto) ablak](media/excel-connector/adx-connection-window.png)
    
    |Mező   |Leírás |
    |---------|---------|
    |**Fürt**   |   Fürt neve (kötelező)      |    
    |**Adatbázis**     |    Az adatbázis neve      |    
    |**Tábla neve vagy Azure Adatkezelő lekérdezés**    |     A tábla vagy az Azure Adatkezelő lekérdezés neve    | 
    
    **Speciális beállítások:**

     |Mező   |Leírás |
    |---------|---------|
    |**Lekérdezési eredmény rekordjának maximális száma**     |     Az Excelbe betöltött rekordok számának korlátozása  |    
    |**Lekérdezési eredmények adatméretének korlátozása (bájt)**    |    Az adatméret korlátozása      |   
    |**Eredményhalmaz-csonkítás letiltása**    |         |      
    |**További set utasítások (pontosvesszővel elválasztva)**    |    Adatforrásra vonatkozó `set`-utasítások hozzáadása     |   

1.  A **navigátor** ablaktáblán navigáljon a megfelelő táblához. A táblázat előnézete ablaktáblán válassza az **adatátalakítás** lehetőséget az adatmódosítás módosításához, vagy válassza a **Load (Betöltés** ) lehetőséget az Excelbe való betöltéshez.

![Táblázat előnézetének ablaka](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Ha az **adatbázis** és/vagy a **tábla neve vagy az Azure adatkezelő Query** már meg van adva, a megfelelő tábla betekintő panelje automatikusan megnyílik. 

## <a name="analyze-and-visualize-data-in-excel"></a>Az Excel-adatelemzés és-megjelenítés

Ha az adatok betöltődik az Excel programba, és elérhetők az Excel-munkalapon, akkor a kapcsolatok és a vizualizációk létrehozásával elemezheti, összesítheti és megjelenítheti az adatait. 

1.  A **tábla kialakítása** lapon válassza az **összefoglalás a kimutatással**lehetőséget. A **Kimutatás létrehozása** ablakban válassza ki a megfelelő táblát, majd kattintson az **OK gombra**.

    ![Pivot tábla létrehozása](media/excel-connector/create-pivot-table.png)

1. A **kimutatás mezői** ablaktáblán válassza ki a megfelelő tábla oszlopokat az összegző táblák létrehozásához. Az alábbi példában a **Napszállta** és az **állapot** van kiválasztva.
    
    ![Kimutatás mezőinek kiválasztása](media/excel-connector/pivot-table-pick-fields.png)

1. A **kimutatás elemzése** lapon válassza a **kimutatásdiagram** lehetőséget a táblázat alapján létrehozott vizualizációk létrehozásához. 

    ![Pivot diagram](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. Az alábbi példában az **eseményazonosító**, a **kezdő időpont**és a **EventType** használatával tekintheti meg az időjárási eseményekre vonatkozó további információkat.

    ![Adatok vizualizációja](media/excel-connector/visualize-excel-data.png)

1. Hozzon létre teljes irányítópultokat az adatai figyeléséhez.

## <a name="next-steps"></a>Következő lépések

[A Microsoft Excelben importált Azure Adatkezelő Kusto-lekérdezés használatával megjelenítheti az adatmegjelenítést](excel-blank-query.md)
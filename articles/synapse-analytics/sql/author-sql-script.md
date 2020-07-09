---
title: SQL-parancsfájlok az Azure szinapszis Studióban (előzetes verzió)
description: Az Azure szinapszis Studio (előzetes verzió) SQL-parancsfájljainak bemutatása
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: ee384d6095ccbf25225a435fe8afe4281c5d62df
ms.sourcegitcommit: dee7b84104741ddf74b660c3c0a291adf11ed349
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85921367"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-szkript használata az Azure szinapszis Studióban (előzetes verzió)

Az Azure szinapszis Studio (előzetes verzió) egy SQL-parancsfájl webes felületét kínálja az SQL-lekérdezések létrehozásához. Csatlakozhat az SQL-készlethez (előzetes verzió) vagy az SQL on-demand (előzetes verzió) szolgáltatáshoz. 

## <a name="begin-authoring-in-sql-script"></a>Szerzői műveletek megkezdése az SQL-szkriptben 

A szerzői műveletek többféleképpen is elindíthatók az SQL-szkriptekben. A következő módszerek egyikével hozhat létre új SQL-parancsfájlt.

1. A fejlesztés menüben válassza a **"+"** ikont, és válassza az **SQL-szkript**elemet.

    ![newsqlscript](media/author-sql-script/newsqlscript.png)

2. A **műveletek** menüben válassza az **új SQL-parancsfájl**lehetőséget.
    
    ![newsqlscript2actions](media/author-sql-script/newsqlscript2actions.png)

Másik lehetőségként a következőt is teheti: 

3. Válassza az **Importálás** lehetőséget a **műveletek** menüből az SQL-parancsfájlok fejlesztése területen, és válasszon ki egy meglévő SQL-parancsfájlt a helyi tárolóból.
 
    ![newsqlscript3actions](media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL-parancsfájl létrehozása

1. Válassza ki az SQL-szkript nevét, ehhez kattintson a **tulajdonság** gombra, és cserélje le az SQL-parancsfájlhoz rendelt alapértelmezett nevet.
  
    ![newsqlscriptrename](media/author-sql-script/newsqlscriptrename.png)

2. Válassza ki az adott SQL-készletet vagy az igény szerinti SQL-t a **Kapcsolódás** legördülő menüből. Vagy szükség esetén válassza ki az adatbázist a **használati adatbázisból**.
 
    ![newsqlchoosepool](media/author-sql-script/newsqlchoosepool.png)

3. Indítsa el az SQL-szkriptet az IntelliSense szolgáltatás használatával.

    ![newsqlintellisense](media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL-parancsfájl futtatása

Kattintson a **Futtatás** gombra az SQL-parancsfájl végrehajtásához. Az eredmények alapértelmezés szerint egy táblában jelennek meg.

![newsqlscriptresultstable](media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Az eredmények exportálása

Az eredményeket a helyi tárhelyre különböző formátumokban (beleértve a CSV, az Excel, a JSON és az XML) is exportálhatja, ha az "exportálás eredménye" lehetőséget választja, és kiválasztja a bővítményt.

**A diagram gomb** kiválasztásával megjelenítheti az SQL-parancsfájl eredményeit is. Válassza a "diagram típusa" és a **category (kategória) oszlopot**. A diagramot képként is exportálhatja a **Mentés**képként lehetőség kiválasztásával. 

![newsqlscriptresultschart](media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Adatok feltárása a Parquet-fájlokból

A Storage-fiókokban lévő parketta-fájlokat az SQL-szkript használatával tekintheti meg a fájl tartalmának megtekintéséhez.

![newscriptsqlodparquet](media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-táblák, külső táblák, nézetek

Ha kiválasztja a **műveletek** menüt az adat területen, több műveletet is kijelölhet, például a következőket:

- Új SQL-parancsfájl
- TOP 1000-sorok kiválasztása
- LÉTREHOZÁSA
- Eldobás és létrehozás 
 
Az elérhető kézmozdulatok megismeréséhez kattintson a jobb gombbal az SQL-készlet és az SQL igény szerinti csomópontjaira.
 
![newscriptdatabase](media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>További lépések

Az SQL-parancsfájlok létrehozásával kapcsolatos további információkért lásd: az [Azure szinapszis Analytics](https://docs.microsoft.com/azure/synapse-analytics).

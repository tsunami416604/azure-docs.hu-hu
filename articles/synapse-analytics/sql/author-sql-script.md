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
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431071"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-szkript használata az Azure szinapszis Studióban (előzetes verzió)

Az Azure szinapszis Studio (előzetes verzió) egy SQL-parancsfájl webes felületét kínálja az SQL-lekérdezések létrehozásához. Csatlakozhat az SQL-készlethez (előzetes verzió) vagy az SQL on-demand (előzetes verzió) szolgáltatáshoz. 

## <a name="begin-authoring-in-sql-script"></a>Szerzői műveletek megkezdése az SQL-szkriptben 

A szerzői műveletek többféleképpen is elindíthatók az SQL-szkriptekben. A következő módszerek egyikével hozhat létre új SQL-parancsfájlt.

1. Válassza a "+" ikont, és válassza az SQL-szkriptet.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Az SQL-parancsfájlok fejlesztése területen található Műveletek menüből válassza az SQL-szkriptek fejlesztése alatt a "műveletek" menü "új SQL-parancsfájl" elemét. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

vagy 

3. Válassza az "Importálás" lehetőséget a "műveletek" menü SQL-parancsfájlok fejlesztése területén, és válasszon ki egy meglévő SQL-parancsfájlt a helyi tárolóból.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>SQL-parancsfájl létrehozása

1. Válassza ki az SQL-parancsfájl nevét a "tulajdonság" gomb kiválasztásával, és cserélje le az SQL-szkripthez rendelt alapértelmezett nevet.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Válassza ki az adott SQL-készletet vagy az igény szerinti SQL-t a "kapcsolódás a következőhöz" legördülő menüből. Vagy ha szükséges, válassza a Database (adatbázis használata) lehetőséget.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Indítsa el az SQL-szkriptet az IntelliSense szolgáltatás használatával.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>SQL-parancsfájl futtatása

Az SQL-szkript végrehajtásához kattintson a Futtatás gombra. Az eredmények alapértelmezés szerint egy táblában jelennek meg.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Az eredmények exportálása

Az eredményeket a helyi tárhelyre különböző formátumokban (beleértve a CSV, az Excel, a JSON és az XML) is exportálhatja, ha az "exportálás eredménye" lehetőséget választja, és kiválasztja a bővítményt.

Az SQL-szkript eredményei egy diagramon is megjeleníthetők a "diagram" gomb kiválasztásával. Válassza a "diagram típusa" és a "kategória oszlop" lehetőséget. A diagramot egy képbe exportálhatja a "Mentés képként" lehetőség kiválasztásával. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Adatok megismerése egy parketta-fájlból.

A Storage-fiókokban lévő parketta-fájlokat az SQL-szkript használatával tekintheti meg a fájl tartalmának megtekintéséhez. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-táblák, külső táblák, nézetek

Az "Actions" (műveletek) menü az adat területen való kiválasztásával több műveletet is kiválaszthat, például: "új SQL-parancsfájl", "válassza a TOP 1000 sort", a "létrehozás", az "eldobás és létrehozás" lehetőséget. Az elérhető kézmozdulatok megismeréséhez kattintson a jobb gombbal az SQL-készlet és az SQL igény szerinti csomópontjaira.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>További lépések

Az SQL-parancsfájlok létrehozásával kapcsolatos további információkért lásd: az [Azure szinapszis Analytics](https://docs.microsoft.com/azure/synapse-analytics).
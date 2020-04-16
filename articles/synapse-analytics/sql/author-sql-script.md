---
title: SQL-parancsfájlok az Azure Synapse Studio-ban (előzetes verzió)
description: Bevezetés az Azure Synapse Studio (előzetes verzió) SQL-parancsfájlok
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: omafnan
ms.openlocfilehash: 940c6d6d96c5c1aa062397d21ea96dace2c09bae
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431071"
---
# <a name="using-sql-script-in-azure-synapse-studio-preview"></a>SQL-parancsfájl használata az Azure Synapse Studio-ban (előzetes verzió)

Az Azure Synapse Studio (előzetes verzió) egy SQL-parancsfájl webes felületet biztosít az SQL-lekérdezések számára. Csatlakozhat az SQL-készlethez (előzetes verzió) vagy az IGÉNY szerinti SQL-hez (előzetes verzió). 

## <a name="begin-authoring-in-sql-script"></a>A szerzői jog az SQL-parancsfájlban 

Az SQL-parancsfájlban számos módon indíthatja el a szerzői élményt. Új SQL-parancsfájlt az alábbi módszerek egyikével hozhat létre.

1. Jelölje ki a "+" ikont, és válassza az SQL-parancsfájl t.

    > [!div class="mx-imgBorder"] 
    >![newsqlscript](./media/author-sql-script/newsqlscript.png)

2. Az SQL-parancsfájlok fejlesztése csoport Műveletek menüjében Válassza az "Új SQL-parancsfájl" lehetőséget az "Sql-parancsfájlok fejlesztése" menü "Műveletek" menüjében. 

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript2actions.png)

vagy 

3. Válassza az "Importálás" lehetőséget az SQL-parancsfájlok fejlesztése csoport "Műveletek" menüjében, és válasszon ki egy meglévő SQL-parancsfájlt a helyi tárolóból.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscript3actions.png)

## <a name="create-your-sql-script"></a>Az SQL-parancsfájl létrehozása

1. Válassza ki az SQL-parancsfájl nevét a "Tulajdonság" gombra kattintva, és cserélje le az SQL-parancsfájlhoz rendelt alapértelmezett nevet.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlscriptrename.png)

1. Válassza ki az adott SQL-készletet vagy az IGÉNY szerinti SQL-t a "Csatlakozás" legördülő menüből. Vagy ha szükséges, válassza ki az adatbázist az "Adatbázis használata" szóból.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlchoosepool.png)

1. Indítsa el az SQL-szkript szerzői használatát az intellisense funkcióval.

    > [!div class="mx-imgBorder"] 
    > ![newsqlscript](./media/author-sql-script/newsqlintellisense.png)

## <a name="run-your-sql-script"></a>Az SQL-parancsfájl futtatása

Az SQL-parancsfájl végrehajtásához kattintson a "Futtatás" gombra. Az eredmények alapértelmezés szerint egy táblázatban jelennek meg.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultstable.png)

## <a name="export-your-results"></a>Az eredmények exportálása

Az eredményeket különböző formátumokban (például CSV, Excel, JSON, XML) exportálhatja a helyi tárolóba az "Eredmények exportálása" és a bővítmény kiválasztásával.

Az SQL-parancsfájl eredményeit a "Diagram" gombra kattintva is megjelenítheti a diagramon. Válassza ki a "Diagram típusa" és a "Kategória oszlop". A diagramot a "Mentés képként" lehetőség kiválasztásával exportálhatja egy képbe. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newsqlscriptresultschart.png)

## <a name="explore-data-from-a-parquet-file"></a>Tárja fel a parkettafájl adatait.

A tárfiókban lévő parquet fájlokat sql-parancsfájlsegítségével fedezheti fel a fájl tartalmának megtekintéséhez. 

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptsqlodparquet.png)

## <a name="sql-tables-external-tables-views"></a>SQL-táblák, külső táblák, nézetek

Az adatok alatt található "Műveletek" menü kiválasztásával több műveletet is választhat, például: "Új SQL script", "Select TOP 1000 rows", "CREATE", "DROP and CREATE". Fedezze fel a rendelkezésre álló kézmozdulatot, ha a jobb gombbal kattint az SQL-készlet és az IGÉNY szerinti SQL csomópontjaira.

> [!div class="mx-imgBorder"] 
> ![newsqlscript](./media/author-sql-script/newscriptdatabase.png)

## <a name="next-steps"></a>További lépések

Az SQL-parancsfájlok írásával kapcsolatos további tudnivalókért az [Azure Synapse Analytics című témakörben](https://docs.microsoft.com/azure/synapse-analytics)talál további információt.
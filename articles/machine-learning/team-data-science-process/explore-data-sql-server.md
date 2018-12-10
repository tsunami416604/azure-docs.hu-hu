---
title: Az SQL Server virtuális gép – csoportos adatelemzési folyamat az adatok megismerése
description: Tudnivalók az SQL Server virtuális gép SQL vagy a programozási nyelvet, például a Python használatával Azure-on tárolt adatok megismerése.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45b842f7ff136f2d53b7724c11141f6b9104ebd7
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53140115"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Az SQL Server virtuális gépen tárolt adatok megismerése az Azure rendszerben

Ez a cikk bemutatja, hogyan fedezheti fel az SQL Server virtuális gép az Azure-ban tárolt adatokat. Ezt megteheti, adatok konvertálása SQL-lel vagy a programozási nyelvet, például a Python használatával.

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat](overview.md).

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server. Ha nem, tekintse meg a felhőbeli adatok adatelemzési folyamat térkép megtudhatja, hogyan helyezheti át az adatokat az SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>SQL-szkriptek SQL adatait
Az alábbiakban néhány minta SQL-parancsfájlok, amelyek segítségével ismerje meg az SQL Server adattárakban.

1. Napi megfigyeléseket megszámlálása
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek egy kategorikus oszlop beolvasása
   
    `select  distinct <column_name> from <databasename>`
3. Lépjen be két kategorikus oszlopok kombinációja szintek száma 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. A numerikus oszlopok terjesztési beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Gyakorlati példa, használhatja a [NYC Taxi adatkészlet](http://www.andresmh.com/nyctaxitrips/) , majd tekintse át a készülékén IPNB [NYC adatok konvertálása, az IPython Notebook és az SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy teljes körű útmutató az.
> 
> 

## <a name="python"></a>A Python az SQL-adatok feltárása
Python használatával az adatok feltárásához és szolgáltatások készítése, ha az adatokat az SQL Server hasonlít az adatfeldolgozás az Azure blob-be a Python, a dokumentált módon [Azure Blobadatok folyamat a data science környezetben](data-blob.md). Az adatok betöltve kell lennie az adatbázisból a pandas DataFrame-be, és akkor is feldolgozható további. Az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz, ebben a szakaszban a dokumentum azt.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](http://pandas.pydata.org/) pythonban adatkezelés Python programozási széles választékának datové struktury és az adatok elemzésére szolgáló eszközöket biztosít. Az alábbi kód beolvassa az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Most már használhatja a Pandas DataFrame a témakörben ismertetett [Azure Blobadatok folyamat a data science környezetben](data-blob.md).

## <a name="the-team-data-science-process-in-action-example"></a>A művelet a példában a csoportos adatelemzési folyamat
A Cortana Analytics-folyamat használatával egy nyilvános adatkészletet egy végpontok közötti forgatókönyv példa: [a csoportos adatelemzési folyamat működés közben: az SQL-kiszolgáló](sql-walkthrough.md).


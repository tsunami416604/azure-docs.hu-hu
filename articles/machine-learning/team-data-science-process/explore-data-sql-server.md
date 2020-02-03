---
title: Az SQL Server virtuális gép – csoportos adatelemzési folyamat az adatok megismerése
description: Tudnivalók az SQL Server virtuális gép SQL vagy a programozási nyelvet, például a Python használatával Azure-on tárolt adatok megismerése.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ae8c7c43ecbf9bc625e1e46be3e2c71c8d57b6f7
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720095"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Az SQL Server virtuális gépen tárolt adatok megismerése az Azure rendszerben

Ez a cikk bemutatja, hogyan fedezheti fel az SQL Server virtuális gép az Azure-ban tárolt adatokat. Az SQL vagy a Python használatával vizsgálja meg az adatelemzést.

Ez a feladat a [csoportos adatelemzési folyamat](overview.md)egyik lépése.

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server. Ha nem, tekintse meg a felhőbeli adatok adatelemzési folyamat térkép megtudhatja, hogyan helyezheti át az adatokat az SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>SQL-adatelemzés az SQL-szkriptekkel
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
> Gyakorlati példaként használhatja a New York-i [taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és a [IPython Notebook és a SQL Server segítségével](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) megtekintheti a IPNB című témakört a New York-i huzavona.
> 
> 

## <a name="python"></a>SQL-adatelemzés a Pythonban
A Python segítségével megismerheti az adatelemzéseket, és olyan szolgáltatásokat hozhatnak elő, amelyekben az adatai SQL Server hasonlóak az Azure-blobban lévő adatfeldolgozáshoz a Python használatával, az [Azure Blob adatainak az adatelemzési környezetben](data-blob.md)való feldolgozása során dokumentált módon. Az adatbázisból egy Panda DataFrame tölti be az adatok adatait, majd később is feldolgozható. Az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz, ebben a szakaszban a dokumentum azt.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A Pythonban található [Panda Library](https://pandas.pydata.org/) számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. Az alábbi kód beolvassa az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már dolgozhat a pandák DataFrame, ahogyan az az Azure Blob-adatfeldolgozási folyamat során az [adatelemzési környezetben](data-blob.md)is szerepel.

## <a name="the-team-data-science-process-in-action-example"></a>A művelet a példában a csoportos adatelemzési folyamat
A Cortana Analytics-folyamat egy nyilvános adatkészlettel való teljes körű bemutató példáját a [csoportos adatelemzési folyamat működés közben: a SQL Server használata](sql-walkthrough.md)című témakörben tekintheti meg.


---
title: "Áttekintheti az adatokat az SQL Server virtuális gép az Azure-on |} Microsoft Docs"
description: "Az SQL Server virtuális gép az Azure-on tárolt adatokba módjáról."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ccbb3085-af9e-4ec2-9df2-15dcab261d05
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: 4777a6acbf3b45ab207d64feb7cba52f61f38eeb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Az SQL Server virtuális gépen tárolt adatok megismerése az Azure rendszerben
Ez a dokumentum bemutatja, hogyan adhat az SQL Server virtuális gép az Azure-on tárolt adatokba. Ezt adatok wrangling SQL használatával, és Python hasonló programozási nyelv használatával is végrehajthatja.

A következő **menü** eszközök segítségével áttekintheti az különböző tárolási környezetekben adatokat leíró témakörökre mutató hivatkozásokat tartalmaz. Ez a feladat egy lépés a Cortana Analytics folyamat (CAP).

[!INCLUDE [cap-explore-data-selector](../../../includes/cap-explore-data-selector.md)]

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server kiszolgálón. Ha nem, nézze meg a felhő adatok tudományos folyamat leképezés megtudhatja, hogyan helyezze át az adatokat az SQL Server.
> 
> 

## <a name="sql-dataexploration"></a>Az SQL-parancsfájlok SQL adatokba
Íme néhány példa SQL-parancsfájlok, amelyek segítségével megismerheti az SQL Server adattárolókhoz.

1. Napi megfigyeléseket szám
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek kategorikus oszlopban beolvasása
   
    `select  distinct <column_name> from <databasename>`
3. A szintek számának beolvasása a kombinációja kategorikus kétoszlopos 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. A numerikus oszlopok terjesztése beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Gyakorlati például használhatja a [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) , majd tekintse át a című IPNB [NYC adatok wrangling IPython jegyzetfüzet és az SQL Server használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy végpontok közötti segédlet az.
> 
> 

## <a name="python"></a>Python SQL adatokba
Python adatokba és szolgáltatások létrehozása, amikor az adatok az SQL Server használata esetén hasonló adatfeldolgozás pythonos környezetekben, az Azure BLOB, ahogy [Azure Blobadatok folyamat adatok tudományos környezetében](data-blob.md). Az adatok betöltve kell lennie az adatbázisból történő egy pandas DataFrame, és majd dolgozhatók további. Azt a dokumentum az adatbázishoz való kapcsolódás, illetve az adatok betöltését az ebben a szakaszban DataFrame folyamatán.

A következő kapcsolati karakterlánc-formátum használható Python pyodbc (csere kiszolgálónév, dbname, a felhasználónevet és jelszót az adott értékek) használatával az SQL Server-adatbázis csatlakozni:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](http://pandas.pydata.org/) Python szolgáltatás széles választékának adatstruktúrák és adatok elemzésére szolgáló eszközöket ad adatkezelési Python programozáshoz. Az alábbi kód beolvassa az eredményt Pandas adatok keretbe egy SQL Server-adatbázist vissza:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Most már használhatja a Pandas DataFrame, a témakörben tárgyalt [Azure Blobadatok folyamat adatok tudományos környezetében](data-blob.md).

## <a name="cortana-analytics-process-in-action-example"></a>A művelet példa Cortana Analytics folyamat
A Cortana Analytics folyamat egy nyilvános adatkészlet-végpontok közötti forgatókönyv például, [a csapat adatok tudományos folyamat működés közben: SQL Server használatával](sql-walkthrough.md).


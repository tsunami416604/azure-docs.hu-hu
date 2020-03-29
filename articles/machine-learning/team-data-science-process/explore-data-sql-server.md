---
title: Adatok feltárása az SQL Server virtuális gépen – Csapatadat-elemzési folyamat
description: Az SQL Server virtuális gépében az Azure-ban tárolt adatok feltárása AZ SQL vagy a Python használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720095"
---
# <a name="explore-data-in-sql-server-virtual-machine-on-azure"></a>Az SQL Server virtuális gépen tárolt adatok megismerése az Azure rendszerben

Ez a cikk ismerteti, hogyan tárja fel az Azure-beli SQL Server virtuális gépeken tárolt adatokat. Az adatok vizsgálatához az SQL vagy a Python használatával vizsgálja meg.

Ez a feladat egy lépés a [csapat adatelemzési folyamatában.](overview.md)

> [!NOTE]
> A dokumentumban található SQL-mintautasítások feltételezik, hogy az adatok az SQL Server ben vannak. Ha nem, olvassa el a felhőalapú adatelemzési folyamatleképezést, amelyből megtudhatja, hogyan helyezheti át az adatokat az SQL Server kiszolgálóra.
> 
> 

## <a name="explore-sql-data-with-sql-scripts"></a><a name="sql-dataexploration"></a>SQL-adatok feltárása SQL-parancsfájlokkal
Íme néhány példa SQL-parancsfájl, amely az SQL Server adattárainak feltárására használható.

1. A megfigyelések száma naponta
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek beszereznie egy kategorikus oszlopban
   
    `select  distinct <column_name> from <databasename>`
3. A szintek számának beszereznie két kategorikus oszlop kombinációjával 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Numerikus oszlopok eloszlásának beszereznie
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [!NOTE]
> Gyakorlati példa: a NYC [Taxi adatkészletet használhatja,](https://www.andresmh.com/nyctaxitrips/) és az [IPython Notebook és](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) az SQL Server használatával nyc data nevű IPNB-dokumentumban található a végpontok közötti végigjáráshoz.
> 
> 

## <a name="explore-sql-data-with-python"></a><a name="python"></a>SQL-adatok felfedezése pythonnal
A Python használatával az adatok feltárása és a funkciók létrehozása, ha az adatok az SQL Server hasonló az adatok feldolgozása az Azure blob python használatával, dokumentált [Folyamat Azure Blob adatok az adatelemzési környezetben.](data-blob.md) Töltse be az adatokat az adatbázisból egy pandadataframe-be, majd tovább dolgozható fel. Ebben a szakaszban dokumentáljuk az adatbázishoz való csatlakozás és az adatok betöltésének folyamatát.

A következő kapcsolati karakterlánc-formátum mal(OK) pyodbc használatával csatlakozhat egy SQL Server-adatbázishoz (cserélje le a kiszolgálónevet, a dbname-t, a felhasználónevet és a jelszót a megadott értékekre):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Python Pandas könyvtára](https://pandas.pydata.org/) adatstruktúrák és adatelemző eszközök gazdag készletét biztosítja a Python-programozás adatkezeléséhez. A következő kód beolvassa az SQL Server adatbázisból visszaadott eredményeket egy Pandas adatkeretbe:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már együtt dolgozhat a Pandas DataFrame-el, ahogy azt az [Azure Blob-adatok feldolgozása az adatelemzési környezetben](data-blob.md)című témakör ben is lefedi.

## <a name="the-team-data-science-process-in-action-example"></a>A csapat adatelemzési folyamat a cselekvés példa
A Cortana Analytics folyamat egy nyilvános adatkészletet használó teljes körű forgatókönyv-példát [a "Team Data Science Process in action: in action: using SQL Server" című témakörben található.](sql-walkthrough.md)


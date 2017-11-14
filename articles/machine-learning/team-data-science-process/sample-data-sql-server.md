---
title: Az adatokat az SQL Server az Azure-on |} Microsoft Docs
description: A mintaadatok az SQL Server az Azure-on
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgeonlun
editor: cgronlun
ms.assetid: 33c030d4-5cca-4cc9-99d7-2bd13a3926af
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: fashah;garye;bradsev
ms.openlocfilehash: fd669f3951b1f7f05932634f039a04e02993399f
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="heading"></a>Mintaadatok az SQL Server az Azure-on
Ez a cikk bemutatja, hogyan az SQL Server az Azure-on tárolt adatokat SQL vagy a Python programozási nyelv használatával. Azt is bemutatja, hogyan mintaadatokat áthelyezi az Azure Machine Learning fájlba menti, feltölti az Azure blob, és olvassa Azure Machine Learning Studio.

A Python mintavételi használatát a [pyodbc](https://code.google.com/p/pyodbc/) ODBC könyvtár az Azure SQL-kiszolgálóhoz való csatlakozáshoz és a [Pandas](http://pandas.pydata.org/) könyvtár tennie, hogy a mintavétel.

> [!NOTE]
> Ebben a dokumentumban SQL példakód azt feltételezi, hogy az adatok egy SQL Server az Azure-on. Ha nem, tekintse meg [adatok áthelyezése az SQL Server Azure](move-sql-server-virtual-machine.md) a cikk útmutatást az adatok áthelyezése az SQL Server az Azure-on.
> 
> 

A következő **menü** hivatkozásokat ismertetik az adatokat a különböző tárolási környezetekben. 

[!INCLUDE [cap-sample-data-selector](../../../includes/cap-sample-data-selector.md)]

**Miért érdemes az az adatokat?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, akkor általában down kétmintás az adatokat, hogy az kisebb, de reprezentatív és könnyebben kezelhető méretű jó ötlet. Ez lehetővé teszi az adatok ismertetése, feltárása és a szolgáltatás mérnöki csapathoz. Szerepét a a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) az adatok feldolgozása funkciók és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="SQL"></a>SQL használatával
Ez a szakasz az adatok alapján egyszerű véletlenszerű mintavétel végrehajtásához az adatbázisban az SQL több módszerét ismerteti. Az adatok mérete és a terjesztési módjának kiválasztása.

A következő két elem használatát mutatják be `newid` az SQL Server a mintavételi végrehajtásához. Módszertől függ, hogyan véletlenszerű azt szeretné, a minta kell lennie (a következő példakód pk_id feltételezett, hogy egy automatikusan létrehozott elsődleges kulcsot kell).

1. Kevésbé szigorú véletlenszerű minta
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Több véletlenszerű minta 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Tablesample, valamint az adatok mintavétele használható. Jobb megközelítés erre akkor lehet, ha az adatok mérete nagy (feltéve, hogy az adatok különböző oldalain nem korrelált), és a lekérdezés elfogadható időn belül végrehajtani.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Vizsgálatát, és a mintaadatokat egy új tábla elhelyezésével szolgáltatások generálása
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure gépi tanulás
A fenti mintalekérdezések közvetlenül használható az Azure Machine Learning [és adatokat importálhat] [ import-data] lefelé-minta menet közben az adatok és az érdekében, hogy az Azure Machine Learning kísérlet a modult. Itt látható egy Képernyőkép a mintában szereplő adatokat olvasni az olvasó modullal:

![olvasó sql][1]

## <a name="python"></a>A Python programozási nyelv használatával
Ez a szakasz azt mutatja be, használja a [pyodbc könyvtár](https://code.google.com/p/pyodbc/) egy ODBC csatlakozni a Python egy SQL server-adatbázis létrehozásához. Adatbázis-kapcsolati karakterláncot a következőképpen történik: (cserélje kiszolgálónév, dbname, felhasználónevet és jelszót a konfiguráció):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas](http://pandas.pydata.org/) a Python kódtár adatkezelési Python programozási széles választékának adatstruktúrák és adatok elemzésére szolgáló eszközöket biztosít. Az alábbi kód beolvassa 0,1 % minta az adatok az Azure SQL-adatbázis egy táblából egy Pandas adatokat:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, cloumn2... from <table_name> tablesample (0.1 percent)''', conn)

Most már a Pandas adatok keretében a mintában szereplő adatokkal dolgozhat. 

### <a name="python-aml"></a>Csatlakozás az Azure gépi tanulás
Az alábbi példakód segítségével le mintát adatok mentése fájlba, és töltse fel az Azure-blobot. A blob adatait az Azure Machine Learning kísérlet közvetlenül olvasható használatával a [és adatokat importálhat] [ import-data] modul. A lépések a következők: 

1. A pandas adatok keret írni egy helyi fájlba
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Helyi fájl feltöltése az Azure-blobba
   
        from azure.storage import BlobService
        import tables
   
        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>
   
        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
   
        try:
   
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
   
        except:            
            print ("Something went wrong with uploading blob:"+BLOBNAME)
3. Adatokat olvasni az Azure Machine Learning segítségével az Azure blob [és adatokat importálhat] [ import-data] modul a következő képernyő fogd ismertetett módon:

![olvasó blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>A művelet a példában az Team tudományos folyamat
Forgatókönyv egy példa az Team tudományos folyamat használatával nyilvános dataset, lásd: [Team adatok tudományos folyamat működés közben: SQL Server használatával](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

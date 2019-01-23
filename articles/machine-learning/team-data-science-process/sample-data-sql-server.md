---
title: Mintaadatok az SQL Server az Azure-on – a csoportos adatelemzési folyamat
description: Mintaadatok az SQL Server, SQL és a Python programozási nyelv használatával Azure-on tárolt, majd helyezze át az Azure Machine Learning.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 076e2aec249407406d42357df0dc8e74e9362992
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446663"
---
# <a name="heading"></a>Mintaadatok az SQL Server az Azure-ban

Ez a cikk bemutatja az Azure-on futó SQL Serverben tárolt adatokat az SQL vagy a Python-programozási nyelv használatával. Azt is bemutatja a mintavételezett adatok áthelyezése az Azure Machine Learningbe fájlba történő mentése, Azure-blobba feltölteni és majd olvasása az Azure Machine Learning studióba.

A Python-mintavételt használ a [pyodbc](https://code.google.com/p/pyodbc/) ODBC-könyvtár az Azure SQL-kiszolgálóhoz való csatlakozáshoz és a [Pandas](http://pandas.pydata.org/) kódtárat a mintavételt.

> [!NOTE]
> Az SQL-mintakódot a dokumentum azt feltételezi, hogy az adatokat egy SQL Server az Azure-ban. Ha nem érhető el, [adatok áthelyezése az Azure-ban az SQL Server](move-sql-server-virtual-machine.md) cikkben útmutatást találhat az SQL Server az adatok áthelyezése az Azure-ban.
> 
> 

**Miért érdemes az az adatokat?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. Ez lehetővé teszi az adatok megértését, feltárási és funkciófejlesztési. A szerepkör a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) az adatfeldolgozás funkciók és a gépi tanulási modellek gyors prototípus-készítés engedélyezése.

Ez a mintavételi feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

## <a name="SQL"></a>SQL-lel
Ez a szakasz ismerteti a több módszert is egyszerű véletlenszerű mintavétel az adatokban az adatbázisban való végrehajtásához SQL használatával. Az adatok mérete és a telepítési módszer kiválasztása.

A következő két elemet használatát mutatják `newid` az SQL Server a mintavételezés végrehajtásához. Módszertől függ, hogyan véletlenszerű azt szeretné, a minta lehet (a következő mintakód pk_id van azt feltételezi, hogy egy automatikusan létrehozott elsődleges kulcs).

1. Kevésbé szigorú véletlenszerűen vett minta
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Több véletlenszerűen vett minta 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Mintavétel, valamint az adatok Tablesample is használható. Jobb módszer lehet az adatok mérete nagy esetén (feltéve, hogy az adatok különböző oldalain nem korrelált) és a lekérdezés végrehajtásához időn belül.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Ismerje meg, és szolgáltatások készítése a mintavételezett adatok tárolva egy új táblát
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure Machine Learning
A fenti mintalekérdezések közvetlenül használható az Azure Machine Learning [adatok importálása] [ import-data] modul le-minta menet közben az adatok és a egy Azure Machine Learning-kísérlet tárhelyhez. Itt látható egy Képernyőkép a mintavételezett adatok olvasása a reader module segítségével:

![olvasó sql][1]

## <a name="python"></a>A Python programozási nyelv használatával
Ez a szakasz használatát mutatja be a [pyodbc könyvtár](https://code.google.com/p/pyodbc/) egy ODBC csatlakozni Python az SQL server-adatbázis létrehozásához. Az adatbázis-kapcsolati karakterláncot a következőképpen történik: (cserélje le servername, adatbázisnév, felhasználónév és jelszó a konfiguráció):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas](http://pandas.pydata.org/) Python kódtár biztosít széles választékának datové struktury és adatelemző eszközökkel adatkezelés Python programozáshoz. A következő kódot az adatok 0,1 % minta olvas a Pandas adatokat nyerhet ki az Azure SQL database egyik táblájába:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Most már használhatja a mintavételezett adatok a Pandas adatok keretbe. 

### <a name="python-aml"></a>Csatlakozás az Azure Machine Learning
Az alábbi mintakód segítségével lefelé mintavételezett adatok mentése fájlba, és töltse fel az Azure-blobba. A blobban lévő adatok közvetlenül olvasható be az Azure Machine Learning-kísérlet használatával a [adatok importálása] [ import-data] modul. A lépések a következők: 

1. Egy helyi fájlba az pandas adathalmaz írása
   
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
3. Olvassa el az adatok Azure-blobból az Azure Machine Learning segítségével [adatok importálása] [ import-data] modul a következő képernyő fogd látható módon:

![olvasó blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>A művelet a példában a csoportos adatelemzési folyamat
A forgatókönyv egy példát a csoportos adatelemzési folyamat használatával egy nyilvános adatkészletet, lásd: [csoportos adatelemzési folyamat működés közben: az SQL-kiszolgáló](sql-walkthrough.md).

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

---
title: Mintaadatok az Azure SQL Server szolgáltatásában – Csapatadat-elemzési folyamat
description: Minta tárolt adatok sql Server az Azure-ban az SQL vagy a Python programozási nyelv, majd helyezze át az Azure Machine Learning.
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
ms.openlocfilehash: 71a2ec9dc4d644fb8739db3817e2cd1d09913da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717640"
---
# <a name="sample-data-in-sql-server-on-azure"></a><a name="heading"></a>Adatmintavétel Azure-on futó SQL Serverben

Ez a cikk bemutatja, hogyan minta tárolt adatok az SQL Server az Azure-ban az SQL vagy a Python programozási nyelv használatával. Azt is bemutatja, hogyan helyezheti át a mintaadatokat az Azure Machine Learningbe úgy, hogy egy fájlba menti őket, feltölti őket egy Azure blobba, majd elolvassa azokat az Azure Machine Learning Studióba.

A Python mintavételezési használja a [pyodbc](https://code.google.com/p/pyodbc/) ODBC könyvtár az Azure-beli SQL Server és a [Pandas](https://pandas.pydata.org/) könyvtár a mintavételezés.

> [!NOTE]
> A minta SQL-kód ebben a dokumentumban feltételezi, hogy az adatok egy SQL Server az Azure-ban. Ha nem, olvassa el az [Adatok áthelyezése az Sql Server az Azure-ban](move-sql-server-virtual-machine.md) cikket az adatok áthelyezéséről az Azure-beli SQL Serverbe.
> 
> 

**Miért érdemes mintát venni az adataiból?**
Ha az elemezni kívánt adatkészlet nagy, általában érdemes levenni az adatokat, hogy csökkentse azokat egy kisebb, de reprezentatívabb és kezelhetőbb méretre. A mintavételezés megkönnyíti az adatok megértését, feltárását és a szolgáltatástervezést. A Team [Data Science Process (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) szerepe az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípus-készítése.

Ez a mintavételezési feladat egy lépés a [csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

## <a name="using-sql"></a><a name="SQL"></a>Az SQL használata
Ez a szakasz számos módszert ismerteti az SQL használatával egyszerű véletlenszerű mintavételezésaz adatbázis ban lévő adatok kal. Válasszon egy módszert az adatok mérete és eloszlása alapján.

A következő két elem `newid` bemutatja, hogyan kell használni az SQL Server a mintavétel. A választott módszer attól függ, hogy a minta mennyire legyen véletlenszerű (pk_id a következő mintakódban automatikusan generált elsődleges kulcsnak minősül).

1. Kevésbé szigorú véletlenszerű minta
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Több véletlenszerű minta 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

A tablesample az adatok mintavételezéséhez is használható. Ez a beállítás jobb lehet, ha az adatok mérete nagy (feltéve, hogy a különböző oldalakon lévő adatok nem korrelálnak), és a lekérdezés ésszerű időn keresztül befejeződik.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> A mintában szereplő adatok funkcióit egy új táblában tárolva fedezheti fel és hozhatja létre.
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás az Azure Machine Learninghez
Közvetlenül használhatja a fenti mintalekérdezéseket az Azure Machine Learning [import adatok][import-data] modulban lefelé mintavétel ezeket az adatokat menet közben, és hozza be egy Azure Machine Learning-kísérlet. A képernyőkép segítségével az olvasó modul olvasni a mintavételezett adatok itt látható:

![olvasó sql][1]

## <a name="using-the-python-programming-language"></a><a name="python"></a>A Python programozási nyelv használata
Ez a szakasz bemutatja a [pyodbc függvénytár](https://code.google.com/p/pyodbc/) használatával egy ODBC-csatlakozás tanévenként egy SQL-kiszolgáló-adatbázishoz a Pythonban. Az adatbázis-kapcsolat karakterlánca a következő: (kiszolgálónév, dbname, felhasználónév és jelszó cseréje a konfigurációval):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Python Pandas](https://pandas.pydata.org/) könyvtára adatstruktúrák és adatelemző eszközök gazdag készletét biztosítja a Python-programozás adatkezeléséhez. A következő kód az Azure SQL Database egy táblájából származó adatok 0,1%-os mintáját olvassa be egy Pandas-adatba:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Most már dolgozhat a pandas adatkeretben lévő mintavételezett adatokkal. 

### <a name="connecting-to-azure-machine-learning"></a><a name="python-aml"></a>Csatlakozás az Azure Machine Learninghez
A következő mintakód segítségével mentheti az lefelé mintavételezett adatokat egy fájlba, és feltöltheti őket egy Azure-blobba. A blobban lévő adatok közvetlenül beolvashatók egy Azure Machine Learning-kísérletbe az [Adatok importálása][import-data] modul használatával. Ennek lépései a következők: 

1. Írja be a pandas adatkeretet egy helyi fájlba
   
        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)
2. Helyi fájl feltöltése az Azure blobba
   
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
3. Adatok olvasása az Azure Blobból az Azure Machine Learning [Import Data][import-data] moduljával, ahogy az a következő képernyőképben látható:

![olvasó blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>A csapat adatelemzési folyamat a cselekvés példa
Ha egy nyilvános adatkészletet használó csapatadat-elemzési folyamatpéldát szeretne áttekinteni, olvassa el a [Team Data Science Process in Action: using SQL Server című témakört.](sql-walkthrough.md)

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

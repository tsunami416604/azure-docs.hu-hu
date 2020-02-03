---
title: Mintaadatok az SQL Server az Azure-on – a csoportos adatelemzési folyamat
description: Mintaadatok az SQL Server, SQL és a Python programozási nyelv használatával Azure-on tárolt, majd helyezze át az Azure Machine Learning.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717640"
---
# <a name="heading"></a>Mintaadatok SQL Server az Azure-ban

Ez a cikk bemutatja az Azure-on futó SQL Serverben tárolt adatokat az SQL vagy a Python-programozási nyelv használatával. Azt is bemutatja a mintavételezett adatok áthelyezése az Azure Machine Learningbe fájlba történő mentése, Azure-blobba feltölteni és majd olvasása az Azure Machine Learning studióba.

A Python-mintavételezés a [pyodbc](https://code.google.com/p/pyodbc/) ODBC-függvénytárat használja az Azure-beli SQL Serverhoz való kapcsolódáshoz és a [Panda](https://pandas.pydata.org/) Library-hez a mintavételezés végrehajtásához.

> [!NOTE]
> Az SQL-mintakódot a dokumentum azt feltételezi, hogy az adatokat egy SQL Server az Azure-ban. Ha nem, olvassa el az [adatáthelyezés SQL Server az Azure](move-sql-server-virtual-machine.md) -ban című cikket, amelyből megtudhatja, hogyan helyezheti át az adatait az Azure-beli SQL Serverba.
> 
> 

**Miért érdemes felvenni az adatait?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. A mintavétel megkönnyíti az adatmegismerést, a feltárást és a funkciók mérnöki felépítését. Szerepe a [csoportos adatelemzési folyamatban (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/) az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

## <a name="SQL"></a>SQL használata
Ez a szakasz ismerteti a több módszert is egyszerű véletlenszerű mintavétel az adatokban az adatbázisban való végrehajtásához SQL használatával. Az adatok mérete és a telepítési módszer kiválasztása.

A következő két elem azt mutatja be, hogyan használhatók a `newid` a SQL Server a mintavételezés végrehajtásához. A választott módszer attól függ, hogy a mintát milyen véletlenszerű módon szeretné használni (pk_id a következő mintakód egy automatikusan létrehozott elsődleges kulcsnak számít).

1. Kevésbé szigorú véletlenszerűen vett minta
   
        select  * from <table_name> where <primary_key> in 
        (select top 10 percent <primary_key> from <table_name> order by newid())
2. Több véletlenszerűen vett minta 
   
        SELECT * FROM <table_name>
        WHERE 0.1 >= CAST(CHECKSUM(NEWID(), <primary_key>) & 0x7fffffff AS float)/ CAST (0x7fffffff AS int)

Mintavétel, valamint az adatok Tablesample is használható. Ez a lehetőség jobb megközelítés lehet, ha az adatméret nagy (feltéve, hogy a különböző lapokon lévő adatmennyiség nem összefügg egymással), és a lekérdezésnek ésszerű időn belül el kell végeznie.

    SELECT *
    FROM <table_name> 
    TABLESAMPLE (10 PERCENT)

> [!NOTE]
> Ismerje meg, és szolgáltatások készítése a mintavételezett adatok tárolva egy új táblát
> 
> 

### <a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Közvetlenül használhatja a fenti lekérdezéseket a Azure Machine Learning [adatimportálási][import-data] modulból lefelé – mintát vehet a menet közben, és egy Azure Machine learning kísérletbe helyezheti. Itt látható egy képernyőkép az olvasó modul használatáról a mintául szolgáló információk olvasásához:

![olvasó sql][1]

## <a name="python"></a>A Python programozási nyelv használata
Ez a szakasz bemutatja, hogyan hozhat létre ODBC-kapcsolatot egy Pythonban található SQL Server-adatbázishoz a [pyodbc-függvénytár](https://code.google.com/p/pyodbc/) használatával. Az adatbázis-kapcsolatok karakterlánca a következő: (a kiszolgálónév, a dbname, a Felhasználónév és a jelszó cseréje a konfigurációval):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A Pythonban található [Panda](https://pandas.pydata.org/) Library számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. A következő kód a Azure SQL Database egy táblázatból származó adatok 0,1%-os mintáját egy Panda-adatokba olvassa:

    import pandas as pd

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select column1, column2... from <table_name> tablesample (0.1 percent)''', conn)

Most már használhatja a mintavételezett adatok a Pandas adatok keretbe. 

### <a name="python-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az alábbi mintakód segítségével lefelé mintavételezett adatok mentése fájlba, és töltse fel az Azure-blobba. A blobban található adatai közvetlenül beolvashatók egy Azure Machine Learning kísérletbe az [adatimportálási][import-data] modul használatával. A konfigurálás lépései a következők: 

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
3. Adatok beolvasása az Azure blobból Azure Machine Learning [importálási][import-data] modul használatával, ahogy az a következő képernyőfelvételen látható:

![olvasó blob][2]

## <a name="the-team-data-science-process-in-action-example"></a>A művelet a példában a csoportos adatelemzési folyamat
Ha egy nyilvános adatkészletet használó csoportos adatelemzési folyamat példáját szeretné eljárni, tekintse [meg a csoportos adatelemzési folyamat](sql-walkthrough.md)című témakört, amely a SQL Server használatát mutatja be.

[1]: ./media/sample-sql-server-virtual-machine/reader_database.png
[2]: ./media/sample-sql-server-virtual-machine/reader_blob.png

[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

---
title: Az adatok funkciók létrehozása az SQL Server SQL és Python |} Microsoft Docs
description: Folyamat adatokat az SQL Azure-ból
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: ''
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: deguhath
ms.openlocfilehash: 432f7b9bb27c1dee396677c54edf48d9fdb027a0
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34836334"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ez a dokumentum bemutatja, hogyan létrehozni az SQL Server virtuális gép az Azure-on tárolt adatok, amelyek segítenek az adatokból hatékonyabban további algoritmusok szolgáltatásai. SQL- vagy egy programozási nyelv, például a Python segítségével ennek a feladatnak. Mindkét megközelítés itt egy.

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Ez **menü** szolgáltatások adatok létrehozása a különböző környezetek leíró témakörök hivatkozásait. Ez a feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

> [!NOTE]
> Gyakorlati például részleteket a [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) , majd tekintse át a című IPNB [NYC adatok wrangling IPython jegyzetfüzet és az SQL Server használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy végpontok közötti segédlet az.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Egy Azure storage-fiók létrehozása. Ha módosítania kell az utasításokat, lásd: [egy Azure Storage-fiók létrehozása](../../storage/common/storage-create-storage-account.md#create-a-storage-account)
* SQL Server adatait tárolja. Ha nem, olvassa el [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md) útmutatást nem helyezi át az adatokat.

## <a name="sql-featuregen"></a>Az SQL szolgáltatás létrehozása
Ez a szakasz azt módokat SQL funkciók generálása mutatják be:  

1. [Count alapú szolgáltatás létrehozása](#sql-countfeature)
2. [A dobozolás szolgáltatás létrehozása](#sql-binningfeature)
3. [A szolgáltatások csak egy oszlop működés közbeni](#sql-featurerollout)

> [!NOTE]
> Létrehozhat további szolgáltatásokat, ha oszlopokként vegye fel a meglévő táblázat, vagy hozzon létre egy új táblázat további funkciók és elsődleges kulcs, az eredeti táblázatban lehetne illeszteni.
> 
> 

### <a name="sql-countfeature"></a>Alapú száma a szolgáltatás létrehozása
Ez a dokumentum bemutatja kétféleképpen száma szolgáltatások generálására. Az első módszer Feltételes összegzés pedig a második metódust használja a "where" záradék. Ezek ezután össze lehet kapcsolni és az eredeti tábla (elsődleges kulcs oszlopok) mellett az eredeti adatok száma szolgáltatásokat.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>A dobozolás szolgáltatás létrehozása
A következő példa bemutatja, hogyan által (5 bins használatával) a dobozolás binned szolgáltatások létrehozásához egy numerikus oszlopot, amely a ehelyett szolgáltatásként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>A szolgáltatások csak egy oszlop működés közbeni
Ebben a szakaszban a bemutatjuk, hogyan számára egyetlen oszlop a tábla létrehozásához további szolgáltatásokat, hogy. A példa feltételezi, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből szolgáltatások létrehozni kívánt.

Íme egy rövid ismertetése a szélesség/hosszúság helyadatok (a stackoverflow forrásokat `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Az alábbiakban a helyadatok megismerkedett mezőjéből szolgáltatások létrehozása előtt hasznos a következőkre:

* A bejelentkezési azt jelzi, hogy azt északi vagy Dél, keleti vagy nyugati a világ.
* Egy nem nulla több száz számot jelzi a földrajzi hosszúság értéke, nem szélesség használatban van.
* A több számjegyet körülbelül 1000 kilométerben helyzetben biztosít. Milyen kontinensen vagy óceáni dolgozunk a hasznos információkat biztosít.
* Az egységek számjegy (egy decimális fok) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri miles, körülbelül 69 miles). Azt jelzi, nagyjából, milyen nagy állapot vagy a dolgozunk ország.
* Akár 11.1 km-érdemes van tizedes jegyre: azt is különbözteti meg a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes van akár az 1.1-es km-érdemes: azt is egy falu eltérő, külön a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi egyetemi is meghatározhatja.
* A negyedik tizedes ér legfeljebb 11 m: egy is meghatározhatja. Már nem zavarja a nem javított GPS egység tipikus pontosságának összehasonlítható.
* Az ötödik tizedes ér legfeljebb 1.1 m: azt fák különbözteti meg egymástól. A kereskedelmi GPS-egységekhez szintre pontossága különbözeti helyesbítéssel, csak érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: is használhatja ezt a részletes tájak, tervezéséhez struktúrák elrendezése utak létrehozása. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS, például a differentially javított GPS painstaking intézkedéseket.

A Tartózkodásihely-adatok lehet featurized terület, a hely és a várost elválasztva. Vegye figyelembe, hogy a többször is meghívhatja a REST-végpont például a Bing térképek API érhető el `https://msdn.microsoft.com/library/ff701710.aspx` a régió/körzeti adatainak megszerzése.

    select
        <location_columnname>
        ,round(<location_columnname>,0) as l1        
        ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
        ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
        ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
        ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
        ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
        ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
    from <tablename>

A hely alapú szolgáltatása további használható további száma szolgáltatások létrehozásához, a fentebb leírt módon.

> [!TIP]
> A választott nyelv a rekordok programozott módon is beszúrhat. Szükség lehet az adatokat beszúrni adattömbök írási hatékonyság növelése érdekében. [Íme egy példa bemutatja, hogyan ehhez pyodbc használja](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik lehetőség, hogy az adatbázis használatával adatokat beszúrni [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure gépi tanulás
Az újonnan létrehozott szolgáltatás fel van véve egy oszlop a meglévő tábla vagy tárolható egy új tábla, és az eredeti táblázatban a machine Learning szolgáltatáshoz csatlakozik. Szolgáltatások jön létre, vagy használatával érhető el, ha már létrehozott, a [és adatokat importálhat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul Azure ml alább látható módon:

![Az Azure ML-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Például a Python-programozási nyelv használatával
Python a szolgáltatások létrehozásához, amikor az adatok az SQL Server használata hasonló adatfeldolgozás Azure BLOB pythonos környezetekben. Kapcsolatban lásd: [Azure Blobadatok folyamat adatok tudományos környezetében](data-blob.md). További feldolgozandó pandas adatok keretbe betölteni az adatokat az adatbázisból. A folyamat az adatbázishoz csatlakozással, és az adatok betöltését az adatok keret ebben a szakaszban ismertetett.

A következő kapcsolati karakterlánc-formátum használható Python pyodbc (csere kiszolgálónév, dbname, a felhasználónevet és jelszót az adott értékek) használatával az SQL Server-adatbázis csatlakozni:

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](http://pandas.pydata.org/) Python szolgáltatás széles választékának adatstruktúrák és adatok elemzésére szolgáló eszközöket ad adatkezelési Python programozáshoz. Az alábbi kód beolvassa az eredményt Pandas adatok keretbe egy SQL Server-adatbázist vissza:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Most a témakörök kiterjed a Pandas adatok keret dolgozhat [létrehozása az Azure blob storage adatok Panda szolgáltatásai](create-features-blob.md).


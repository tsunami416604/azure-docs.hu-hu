---
title: "Az adatok funkciók létrehozása az SQL Server SQL és Python |} Microsoft Docs"
description: "Folyamat adatokat az SQL Azure-ból"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: 
ms.assetid: bf1f4a6c-7711-4456-beb7-35fdccd46a44
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev;fashah;garye
ms.openlocfilehash: 06c165d25361694cf660f391b3d221ad1d63e95d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ez a dokumentum bemutatja, hogyan létrehozni az SQL Server virtuális gép az Azure-on tárolt adatok, amelyek segítenek az adatokból hatékonyabban további algoritmusok szolgáltatásai. Ez végezhető SQL vagy Python, amelyek mindegyikét egy itt hasonló programozási nyelv használatával.

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

### <a name="sql-countfeature"></a>Count alapú szolgáltatás létrehozása
Ez a dokumentum bemutatja kétféleképpen száma szolgáltatások generálására. Az első módszer Feltételes összegzés pedig a második metódust használja a "where" záradék. Ezek ezután össze lehet kapcsolni és az eredeti tábla (elsődleges kulcs oszlopok) mellett az eredeti adatok száma szolgáltatásokat.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>A dobozolás szolgáltatás létrehozása
A következő példa bemutatja, hogyan által (5 bins használatával) a dobozolás binned szolgáltatások létrehozásához egy numerikus oszlopot, amely a ehelyett szolgáltatásként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>A szolgáltatások csak egy oszlop működés közbeni
Ebben a szakaszban a bemutatjuk, hogyan arra csak egy oszlop a tábla létrehozásához további szolgáltatásokat. A példa feltételezi, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből szolgáltatások létrehozni kívánt.

Íme egy rövid ismertetése a szélesség/hosszúság helyadatok (a stackoverflow forrásokat `http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Ez az hogy featurizing előtt tájékozódjon a location mező:

* A bejelentkezési be van állítva, hogy dolgozunk Észak vagy Dél, keleti vagy nyugati a világ.
* Egy nem nulla több száz számjegy közli a gép hosszúsági és szélességi nem használunk!
* A több számjegyet körülbelül 1000 kilométerben helyzetben biztosít. Milyen kontinensen vagy óceáni dolgozunk a hasznos információkat nyújt nekünk.
* Az egységek számjegy (egy decimális fok) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri miles, körülbelül 69 miles). Azt is adja meg, nagyjából milyen nagy állapot vagy a dolgozunk ország.
* Akár 11.1 km-érdemes van tizedes jegyre: azt is különbözteti meg a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes van akár az 1.1-es km-érdemes: azt is egy falu eltérő, külön a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi egyetemi is meghatározhatja.
* A negyedik tizedes ér legfeljebb 11 m: egy is meghatározhatja. Már nem zavarja a nem javított GPS egység tipikus pontosságának összehasonlítható.
* Az ötödik tizedes ér legfeljebb 1.1 m: azt fák megkülönböztetni egymástól. A kereskedelmi GPS-egységekhez szintre pontossága különbözeti helyesbítéssel, csak érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: is használhatja ezt a részletes tájak, tervezéséhez struktúrák elrendezése utak létrehozása. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS, például a differentially javított GPS painstaking intézkedéseket.

A Tartózkodásihely-adatok is lehet featurized következőképpen, régió, a hely és a várost elválasztó. Vegye figyelembe, hogy a többször is meghívhatja a REST-végpont például a Bing térképek API érhető el `https://msdn.microsoft.com/library/ff701710.aspx` a régió/körzeti adatainak megszerzése.

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

A fenti helyét alapján szolgáltatása további használható további száma szolgáltatások létrehozásához, a fentebb leírt módon.

> [!TIP]
> A választott nyelv a rekordok programozott módon is beszúrhat. Helyezze be az adatok írási hatékonyság növelése érdekében adattömbök szeretne [látogasson el a példa bemutatja, hogyan ehhez itt használatával pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik lehetőség, hogy az adatbázis használatával adatokat beszúrni [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure gépi tanulás
Az újonnan létrehozott szolgáltatás fel van véve egy oszlop a meglévő tábla vagy tárolható egy új tábla, és az eredeti táblázatban a machine Learning szolgáltatáshoz csatlakozik. Szolgáltatások jön létre, vagy használatával érhető el, ha már létrehozott, a [és adatokat importálhat](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul Azure ml alább látható módon:

![az azureml-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Például a Python-programozási nyelv használatával
Python szolgáltatások létrehozásához, amikor az adatok az SQL Server használata hasonló adatfeldolgozás dokumentált Python használatával az Azure BLOB [Azure Blobadatok folyamat akkor adatok tudományos környezetben](data-blob.md). Az adatok betöltve kell lennie az adatbázisból pandas adatok keretbe, és majd dolgozhatók további. A folyamat az adatbázishoz csatlakozással, és az adatok betöltését az adatok keret ebben a szakaszban a dokumentum azt.

A következő kapcsolati karakterlánc-formátum használható Python pyodbc (csere kiszolgálónév, dbname, felhasználónevet és jelszót az adott értékek) használatával az SQL Server-adatbázis csatlakozni:

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](http://pandas.pydata.org/) Python szolgáltatás széles választékának adatstruktúrák és adatok elemzésére szolgáló eszközöket ad adatkezelési Python programozáshoz. Az alábbi kódot beolvassa az eredményt Pandas adatok keretbe egy SQL Server-adatbázist vissza:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Most a témakörök kiterjed a Pandas adatok keret dolgozhat [létrehozása az Azure blob storage adatok Panda szolgáltatásai](create-features-blob.md).


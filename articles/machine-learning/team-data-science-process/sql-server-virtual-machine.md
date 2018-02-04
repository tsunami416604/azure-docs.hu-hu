---
title: "Az Azure SQL Server virtuális gépen adatokba |} Microsoft Docs"
description: "Adatokba és szolgáltatások létrehozása az Azure SQL Server virtuális gépen"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: 
ms.assetid: 3949fb2c-ffab-49fb-908d-27d5e42f743b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: garye;bradsev
ms.openlocfilehash: 6a97e1afb761191874b7a54b1951cb6ef9c4b07e
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="heading"></a>Az SQL Server virtuális gépet az Azure adatfeldolgozásra
Ez a dokumentum bemutatja, hogyan adatokba, és az SQL Server virtuális gép az Azure-on tárolt adatok szolgáltatásai generálásához. Ezt adatok wrangling SQL használatával, és Python hasonló programozási nyelv használatával is végrehajthatja.

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server kiszolgálón. Ha nem, nézze meg a felhő adatok tudományos folyamat leképezés megtudhatja, hogyan helyezze át az adatokat az SQL Server.
> 
> 

## <a name="SQL"></a>SQL használatával
Azt mutatják be, a következő wrangling feladatok ebben a szakaszban SQL használatával:

1. [Az adatok feltárása](#sql-dataexploration)
2. [Szolgáltatás létrehozása](#sql-featuregen)

### <a name="sql-dataexploration"></a>Az adatok feltárása
Íme néhány példa SQL-parancsfájlok, amelyek segítségével megismerheti az SQL Server adattárolókhoz.

> [!NOTE]
> Gyakorlati például használhatja a [NYC Taxi dataset](http://www.andresmh.com/nyctaxitrips/) , majd tekintse át a című IPNB [NYC adatok wrangling IPython jegyzetfüzet és az SQL Server használatával](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy végpontok közötti segédlet az.
> 
> 

1. Napi megfigyeléseket szám
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek kategorikus oszlopban beolvasása
   
    `select  distinct <column_name> from <databasename>`
3. A szintek számának beolvasása a kombinációja kategorikus kétoszlopos 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. A numerikus oszlopok terjesztése beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>Szolgáltatás létrehozása
Ez a szakasz azt módokat SQL funkciók generálása mutatják be:  

1. [Count alapú szolgáltatás létrehozása](#sql-countfeature)
2. [A dobozolás szolgáltatás létrehozása](#sql-binningfeature)
3. [A szolgáltatások csak egy oszlop működés közbeni](#sql-featurerollout)

> [!NOTE]
> Létrehozhat további szolgáltatásokat, ha oszlopokként vegye fel a meglévő táblázat, vagy hozzon létre egy új táblázat további funkciók és elsődleges kulcs, az eredeti táblázatban lehetne illeszteni. 
> 
> 

### <a name="sql-countfeature"></a>Count alapú szolgáltatás létrehozása
Az alábbi példák bemutatják, kétféle módon száma szolgáltatások létrehozásakor. Az első módszer Feltételes összegzés pedig a második metódust használja a "where" záradék. Ezek ezután össze lehet kapcsolni és az eredeti tábla (elsődleges kulcs oszlopok) mellett az eredeti adatok száma szolgáltatásokat.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>A dobozolás szolgáltatás létrehozása
A következő példa bemutatja, hogyan által (öt bins használatával) a dobozolás binned szolgáltatások létrehozásához egy numerikus oszlopot, amely a ehelyett szolgáltatásként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>A szolgáltatások csak egy oszlop működés közbeni
Ebben a szakaszban a bemutatjuk, hogyan számára egyetlen oszlop a tábla létrehozásához további szolgáltatásokat, hogy. A példa feltételezi, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből szolgáltatások létrehozni kívánt.

Íme egy rövid ismertetése a szélesség/hosszúság helyadatok (a stackoverflow forrásokat [hogyan kell a szélességi és hosszúsági pontosságát?](http://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Ez az hogy featurizing előtt tájékozódjon a location mező:

* A bejelentkezési be van állítva, hogy dolgozunk Észak vagy Dél, keleti vagy nyugati a világ.
* Egy nem nulla több száz számjegy közli velünk, hogy használjuk-e hosszúsági és szélességi nem!
* A több számjegyet körülbelül 1000 kilométerben helyzetben biztosít. Milyen kontinensen vagy óceáni dolgozunk a hasznos információkat nyújt nekünk.
* Az egységek számjegy (egy decimális fok) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri miles, körülbelül 69 miles). Azt is adja meg, nagyjából milyen nagy állapot vagy a dolgozunk ország.
* Akár 11.1 km-érdemes van tizedes jegyre: azt is különbözteti meg a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes van akár az 1.1-es km-érdemes: azt is egy falu eltérő, külön a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi egyetemi is meghatározhatja.
* A negyedik tizedes ér legfeljebb 11 m: egy is meghatározhatja. Már nem zavarja a nem javított GPS egység tipikus pontosságának összehasonlítható.
* Az ötödik tizedes ér legfeljebb 1.1 m: azt fák különbözteti meg egymástól. A kereskedelmi GPS-egységekhez szintre pontossága különbözeti helyesbítéssel, csak érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: is használhatja ezt a részletes tájak, tervezéséhez struktúrák elrendezése utak létrehozása. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS, például a differentially javított GPS painstaking intézkedéseket.

A Tartózkodásihely-adatok is a következők featurized, régió, a hely és a várost elválasztó. Vegye figyelembe, hogy Ön is meghívhatja a REST-végpont például a Bing térképek API érhető el [pont hely keresése](https://msdn.microsoft.com/library/ff701710.aspx) a régió/körzeti adatainak megszerzése.

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

Ezeket a helyalapú szolgáltatásokat további használható korábban leírt további száma szolgáltatások létrehozásához. 

> [!TIP]
> A választott nyelv a rekordok programozott módon is beszúrhat. Szükség lehet az adatokat beszúrni adattömbök írási hatékonyság növelése érdekében (a példa bemutatja, hogyan ehhez pyodbc használatával, lásd: [Python SQLServer eléréséhez A HelloWorld minta](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Egy másik lehetőség, hogy az adatbázis használatával adatokat beszúrni a [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure gépi tanulás
Az újonnan létrehozott szolgáltatás fel van véve egy oszlop a meglévő tábla vagy tárolható egy új tábla, és az eredeti táblázatban a machine Learning szolgáltatáshoz csatlakozik. Szolgáltatások jön létre, vagy használatával érhető el, ha már létrehozott, a [és adatokat importálhat] [ import-data] modul az Azure Machine Learning alább látható módon:

![az azureml-olvasók][1] 

## <a name="python"></a>Például a Python-programozási nyelv használatával
Python adatokba és szolgáltatások létrehozása, amikor az adatok az SQL Server használata esetén hasonló adatfeldolgozás dokumentált Python használatával az Azure BLOB [Azure Blobadatok folyamat adatok tudományos környezetében](data-blob.md). Az adatok betöltve kell lennie az adatbázisból pandas adatok keretbe, és majd dolgozhatók további. A folyamat az adatbázishoz csatlakozással, és az adatok betöltését az adatok keret ebben a szakaszban a dokumentum azt.

A következő kapcsolati karakterlánc-formátum használható Python pyodbc (csere kiszolgálónév, dbname, a felhasználónevet és jelszót az adott értékek) használatával az SQL Server-adatbázis csatlakozni:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](http://pandas.pydata.org/) Python szolgáltatás széles választékának adatstruktúrák és adatok elemzésére szolgáló eszközöket ad adatkezelési Python programozáshoz. Az alábbi kódot beolvassa az eredményt Pandas adatok keretbe egy SQL Server-adatbázist vissza:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

Most már használhatja a Pandas adatok keretet, a cikkben szereplő [Azure Blobadatok folyamat adatok tudományos környezetében](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Az Azure Data tudományos művelet példa
Az Azure tudományos folyamat egy nyilvános adatkészlet végpont bemutató példa: [Azure adatok tudományos folyamat működés közben](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


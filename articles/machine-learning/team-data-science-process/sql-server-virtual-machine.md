---
title: Egy SQL Server virtuális gép – a csoportos adatelemzési folyamat az adatok megismerése
description: Adatok feltárása és szolgáltatások készítése az SQL Server virtuális gépen az Azure-ban
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/23/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7b3b4e0886f561cc66e2c02e4ea354c86b34453c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57904184"
---
# <a name="heading"></a>Dolgozza fel az adatokat az SQL Server virtuális gépen az Azure-ban
Ez a dokumentum bemutatja, hogyan elemezheti adatait, és hozzon létre funkciók az SQL Server virtuális gép az Azure-ban tárolt adatok. Ezt megteheti, adatok konvertálása SQL-lel vagy a programozási nyelvet, például a Python használatával.

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server. Ha nem, tekintse meg a felhőbeli adatok adatelemzési folyamat térkép megtudhatja, hogyan helyezheti át az adatokat az SQL Server.
> 
> 

## <a name="SQL"></a>SQL-lel
A következő adatok wrangling feladatokat SQL-lel ebben a szakaszban ismertetünk:

1. [Az adatok feltárása](#sql-dataexploration)
2. [A szolgáltatás létrehozása](#sql-featuregen)

### <a name="sql-dataexploration"></a>Az adatok feltárása
Az alábbiakban néhány minta SQL-parancsfájlok, amelyek segítségével ismerje meg az SQL Server adattárakban.

> [!NOTE]
> Gyakorlati példa, használhatja a [NYC Taxi adatkészlet](https://www.andresmh.com/nyctaxitrips/) , majd tekintse át a készülékén IPNB [NYC adatok konvertálása, az IPython Notebook és az SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy teljes körű útmutató az.
> 
> 

1. Napi megfigyeléseket megszámlálása
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek egy kategorikus oszlop beolvasása
   
    `select  distinct <column_name> from <databasename>`
3. Lépjen be két kategorikus oszlopok kombinációja szintek száma 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. A numerikus oszlopok terjesztési beolvasása
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="sql-featuregen"></a>A szolgáltatás létrehozása
Ebben a szakaszban ismertetünk módon a létrehozást funkciók az SQL:  

1. [Száma alapján a szolgáltatás létrehozása](#sql-countfeature)
2. [A dobozolás a szolgáltatás létrehozása](#sql-binningfeature)
3. [Egyetlen oszlop az a funkciók bevezetéséről](#sql-featurerollout)

> [!NOTE]
> További funkciók generál, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcs, amely összekapcsolható a az eredeti tábla. 
> 
> 

### <a name="sql-countfeature"></a>Száma alapján a szolgáltatás létrehozása
Az alábbi példák bemutatják, két módon létrehozni bloberőforrásokhoz száma funkciók. Az első módszer feltételes sum és a második módszer használja a "where" záradék. Ezek ezután összekapcsolható a az eredeti tábla (elsődleges kulcs oszlopát használatával), count funkciók mellett az eredeti adatok.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>A dobozolás a szolgáltatás létrehozása
Az alábbi példa bemutatja, hogyan binned szolgáltatások létrehozásához dobozolás (használatával öt bins) által egy numerikus oszlopot inkább funkcióként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Egyetlen oszlop az a funkciók bevezetéséről
Ebben a szakaszban bemutatjuk, hogyan vezethet be csak egy oszlop a tábla létrehozásához további szolgáltatásokat. A példában feltételeztük, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből próbált szolgáltatások készítése.

Íme egy rövid ismertetőt a szélességi és hosszúsági koordinátákkal helyadatok (a stackoverflow forrásokat [hogyan mérheti a szélességi és hosszúsági pontosságát?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Ez az a hely mezőben előtt featurizing megismeréséhez:

* A bejelentkezési azt jelzi, hogy vannak-e Észak vagy Dél-India, keleti vagy nyugati jelöl.
* Egy nem nulla több száz számjegy tudatja velünk, hogy használjuk-e hosszúság, szélesség nem!
* A több számjegy biztosít olyan helyzetben, hogy körülbelül 1000 alapján. Milyen kontinens vagy vagyunk az óceán hasznos információkat biztosít velünk a kapcsolatot.
* Az egységek számjegy (decimális mértékű) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri mérföldre, körülbelül 69 mérföld). Azt is ossza meg velünk nagyjából milyen nagy állama vagy országa tudunk.
* Az első tizedes ér akár 11.1 km-re: azt megkülönböztethesse a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes ér akár 1.1 km-re: azt is egy falu elkülönítése a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi campus segítenek azonosítani.
* A negyedik tizedes ér legfeljebb 11 m: egy segítenek azonosítani. Fontos nem zavarja a nem javított GPS egység tipikus pontossága hasonlítható.
* Az ötödik tizedes ér akár 1.1 m:, fák különbözteti meg egymástól. Erre a szintre a kereskedelmi GPS-egységekhez pontossága csak különbözeti helyesbítéssel érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: ezzel részletes környezetünk, tervezéséhez struktúrák elrendező utak létrehozásához. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS-adatok, például a GPS differentially javított painstaking intézkedéseket.

A helyadatok lehet featurized módon, régió, a hely és a város információk szétválasztása. Vegye figyelembe, hogy Ön is meghívhatja a REST-végpontot, például a Bing térképek API elérhető [keressen egy helyet pont](https://msdn.microsoft.com/library/ff701710.aspx) a régió/kerület adatok lekéréséhez.

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

Ezek a helyalapú szolgáltatások további használható a fentebb leírt módon további száma szolgáltatások létrehozásához. 

> [!TIP]
> Programozott módon szúrhat be a rekordok használatával tetszőleges nyelven. Szükség lehet az adatok beszúrásához írási hatékonyság növelése érdekében (ehhez pyodbc használatával egy példa: [SQLServer eléréséhez a python egy HelloWorld-minta](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Egy másik lehetőség, hogy az adatbázis használatával szúrja be az adatokat a [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx).
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure Machine Learning
Az újonnan létrehozott szolgáltatást meglévő táblához oszlopként hozzáadható vagy egy új tábla tárolja és csatlakozik, a machine Learning szolgáltatáshoz az eredeti tábla. Szolgáltatások jön létre, vagy ha már létrehozta, keresztül érhető a [adatok importálása] [ import-data] modul az Azure Machine Learning alább látható módon:

![az azureml-olvasók][1] 

## <a name="python"></a>Például a Python-programozási nyelv
Python használatával az adatok feltárásához és szolgáltatások készítése, ha az adatokat az SQL Server hasonlít a dokumentált Python használatával Azure blob adatok feldolgozása [Azure Blobadatok folyamat a data science környezetben](data-blob.md). Az adatok betöltve kell lennie az adatbázisból adatokat pandas keretbe, és akkor is feldolgozható további. Az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz, ebben a szakaszban a dokumentum azt.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](https://pandas.pydata.org/) pythonban adatkezelés Python programozási széles választékának datové struktury és az adatok elemzésére szolgáló eszközöket biztosít. Az alábbi kódot olvas, az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már használhatja a Pandas adatok keretet, a cikkben szereplő [Azure Blobadatok folyamat a data science környezetben](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>A művelet a példában az Azure Data Science
Az Azure adatelemzési folyamat használatával egy nyilvános adatkészletet egy végpontok közötti forgatókönyv példa: [Azure adatelemzési folyamat működés](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


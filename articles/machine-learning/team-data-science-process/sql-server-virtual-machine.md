---
title: Egy SQL Server virtuális gép – a csoportos adatelemzési folyamat az adatok megismerése
description: Az Azure-beli SQL Server virtuális gépeken megismerheti az adatfeldolgozást és a szolgáltatások létrehozását a Python vagy az SQL használatával.
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
ms.openlocfilehash: d3eb4d2faf58d1861fda9d04437f9f9530c77672
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718480"
---
# <a name="heading"></a>Az Azure-ban SQL Server virtuális gépen tárolt adatfeldolgozás
Ez a dokumentum bemutatja, hogyan elemezheti adatait, és hozzon létre funkciók az SQL Server virtuális gép az Azure-ban tárolt adatok. Ezt a célt az SQL használatával vagy a Pythonhoz hasonló programozási nyelv használatával lehet elvégezni az huzavona.

> [!NOTE]
> Ebben a dokumentumban a minta SQL-utasítások feltételezik, hogy adatokat az SQL Server. Ha nem, tekintse meg a felhőbeli adatok adatelemzési folyamat térkép megtudhatja, hogyan helyezheti át az adatokat az SQL Server.
> 
> 

## <a name="SQL"></a>SQL használata
A következő adatok wrangling feladatokat SQL-lel ebben a szakaszban ismertetünk:

1. [Adatelemzés](#sql-dataexploration)
2. [Szolgáltatás létrehozása](#sql-featuregen)

### <a name="sql-dataexploration"></a>Adatelemzés
Az alábbiakban néhány minta SQL-parancsfájlok, amelyek segítségével ismerje meg az SQL Server adattárakban.

> [!NOTE]
> Gyakorlati példaként használhatja a New York-i [taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és a [IPython Notebook és a SQL Server segítségével](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) megtekintheti a IPNB című témakört a New York-i huzavona.
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

### <a name="sql-featuregen"></a>Szolgáltatás létrehozása
Ebben a szakaszban ismertetünk módon a létrehozást funkciók az SQL:  

1. [Darabszám-alapú szolgáltatás létrehozása](#sql-countfeature)
2. [Dobozolási szolgáltatás létrehozása](#sql-binningfeature)
3. [A funkciók kimutatása egyetlen oszlopból](#sql-featurerollout)

> [!NOTE]
> További funkciók generál, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcs, amely összekapcsolható a az eredeti tábla. 
> 
> 

### <a name="sql-countfeature"></a>Darabszám-alapú szolgáltatás létrehozása
Az alábbi példák bemutatják, két módon létrehozni bloberőforrásokhoz száma funkciók. Az első módszer feltételes sum és a második módszer használja a "where" záradék. Ezek az eredmények ezután az eredeti táblával (az elsődleges kulcs oszlopainak használatával) is összekapcsolhatók, hogy az eredeti adatmennyiség mellett is meglegyenek a Count funkciók.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="sql-binningfeature"></a>Dobozolási szolgáltatás létrehozása
Az alábbi példa bemutatja, hogyan binned szolgáltatások létrehozásához dobozolás (használatával öt bins) által egy numerikus oszlopot inkább funkcióként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>A funkciók kimutatása egyetlen oszlopból
Ebben a szakaszban bemutatjuk, hogyan vezethet be csak egy oszlop a tábla létrehozásához további szolgáltatásokat. A példában feltételeztük, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből próbált szolgáltatások készítése.

Íme egy rövid ismertető a szélességi és hosszúsági adatokról (a StackOverflow származik, [hogyan mérhető a szélességi és a hosszúsági pontosság?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Ez az útmutató akkor hasznos, ha a helyet egy vagy több szolgáltatásként is meg szeretné érteni:

* A bejelentkezési azt jelzi, hogy vannak-e Észak vagy Dél-India, keleti vagy nyugati jelöl.
* Egy nem nulla több száz számjegy tudatja velünk, hogy használjuk-e hosszúság, szélesség nem!
* A több számjegy biztosít olyan helyzetben, hogy körülbelül 1000 alapján. Milyen kontinens vagy vagyunk az óceán hasznos információkat biztosít velünk a kapcsolatot.
* Az egységek számjegy (decimális mértékű) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri mérföldre, körülbelül 69 mérföld). Megtudhatja, hogy nagyjából milyen államot, országot vagy régiót használ.
* Az első tizedes ér akár 11.1 km-re: azt megkülönböztethesse a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes ér akár 1.1 km-re: azt is egy falu elkülönítése a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi campus segítenek azonosítani.
* A negyedik tizedes ér legfeljebb 11 m: egy segítenek azonosítani. Fontos nem zavarja a nem javított GPS egység tipikus pontossága hasonlítható.
* Az ötödik tizedes ér akár 1.1 m:, fák különbözteti meg egymástól. Erre a szintre a kereskedelmi GPS-egységekhez pontossága csak különbözeti helyesbítéssel érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: ezzel részletes környezetünk, tervezéséhez struktúrák elrendező utak létrehozásához. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS-adatok, például a GPS differentially javított painstaking intézkedéseket.

A helyadatok lehet featurized módon, régió, a hely és a város információk szétválasztása. Meghívhat egy REST-végpontot is, például a Bing Maps API-t, [ahol megkeresheti](https://msdn.microsoft.com/library/ff701710.aspx) a régió/körzet információit.

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
> Programozott módon szúrhat be a rekordok használatával tetszőleges nyelven. Előfordulhat, hogy az írás hatékonyságának javításához be kell szúrnia az adattömbökben lévő adattömböket (például a pyodbc használatával, lásd: [HelloWorld-minta a Pythonhoz való SQLServer eléréséhez](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Egy másik alternatíva az, hogy a [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx)használatával szúrja be az adatbázist az adatbázisba.
> 
> 

### <a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az újonnan létrehozott szolgáltatást meglévő táblához oszlopként hozzáadható vagy egy új tábla tárolja és csatlakozik, a machine Learning szolgáltatáshoz az eredeti tábla. A szolgáltatások létrehozhatók vagy elérhetők, ha már létrejöttek, az [adatimportálási][import-data] modul használatával Azure Machine learning az alábbi ábrán látható módon:

![az azureml-olvasók][1] 

## <a name="python"></a>Programozási nyelv (például Python) használata
A Python használata az adatelemzéshez és a szolgáltatások létrehozásához, amikor az adatai SQL Server hasonlóak az Azure blobban tárolt adatfeldolgozáshoz az [adatelemzési környezet Azure Blob-adatainak](data-blob.md)feldolgozása során dokumentált módon. Az adatok betöltése az adatbázisból egy Panda-adatkeretbe további feldolgozás céljából. Az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz, ebben a szakaszban a dokumentum azt.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A Pythonban található [Panda Library](https://pandas.pydata.org/) számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. Az alábbi kódot olvas, az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már dolgozhat a pandák adatkerettel, ahogyan az az Azure Blob-adatfeldolgozást az [adatelemzési környezetben](data-blob.md)is tárgyalja.

## <a name="azure-data-science-in-action-example"></a>A művelet a példában az Azure Data Science
Az Azure adatelemzési folyamat egy nyilvános adatkészletet használó teljes körű bemutató példáját az [Azure adatelemzési folyamata](sql-walkthrough.md)című témakörben tekintheti meg.

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


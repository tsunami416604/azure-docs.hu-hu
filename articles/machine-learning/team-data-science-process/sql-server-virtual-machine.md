---
title: Adatok feltárása SQL Server virtuális gépen – Csapatadat-elemzési folyamat
description: Fedezze fel + dolgozza fel az adatokat, és hozzon létre funkciókat a Python vagy az SQL használatával egy SQL Server virtuális gép az Azure-ban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718480"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Adatfeldolgozás az Azure-beli SQL Server virtuális gépen
Ez a dokumentum bemutatja, hogyan tárhatja fel az adatokat, és hozzon létre funkciókat az Azure-beli SQL Server virtuális gépeken tárolt adatokhoz. Ez a cél sql-t használó adathuzavsalsal vagy egy programozási nyelv, például a Python használatával is kivégezhető.

> [!NOTE]
> A dokumentumban található SQL-mintautasítások feltételezik, hogy az adatok az SQL Server ben vannak. Ha nem, olvassa el a felhőalapú adatelemzési folyamatleképezést, amelyből megtudhatja, hogyan helyezheti át az adatokat az SQL Server kiszolgálóra.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>Az SQL használata
Ebben a szakaszban a következő adatwrangling feladatokat ismertetjük az SQL használatával:

1. [Adatok feltárása](#sql-dataexploration)
2. [Szolgáltatás generálása](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Adatok feltárása
Íme néhány példa SQL-parancsfájl, amely az SQL Server adattárainak feltárására használható.

> [!NOTE]
> Gyakorlati példa: a NYC [Taxi adatkészletet használhatja,](https://www.andresmh.com/nyctaxitrips/) és az [IPython Notebook és](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) az SQL Server használatával nyc data nevű IPNB-dokumentumban található a végpontok közötti végigjáráshoz.
> 
> 

1. A megfigyelések száma naponta
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. A szintek beszereznie egy kategorikus oszlopban
   
    `select  distinct <column_name> from <databasename>`
3. A szintek számának beszereznie két kategorikus oszlop kombinációjával 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. Numerikus oszlopok eloszlásának beszereznie
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Szolgáltatás generálása
Ebben a szakaszban az SQL használatával a szolgáltatások létrehozásának módjait ismertetjük:  

1. [Számalapú szolgáltatásgenerálás](#sql-countfeature)
2. [Binning funkció létrehozása](#sql-binningfeature)
3. [A funkciók egyetlen oszlopból történő kivonása](#sql-featurerollout)

> [!NOTE]
> Miután további szolgáltatásokat hozott létre, hozzáadhatja őket oszlopként a meglévő táblához, vagy létrehozhat egy új táblát a további szolgáltatásokkal és elsődleges kulccsal, amely az eredeti táblához csatlakoztatható. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Számalapú szolgáltatásgenerálás
Az alábbi példák bemutatják a count features létrehozásának két módját. Az első módszer feltételes összeget, a második pedig a "where" záradékot használja. Ezek az eredmények ezután összekapcsolhatók az eredeti táblával (elsődleges kulcsoszlopok használatával), hogy az eredeti adatok mellett számlálási funkciók is legyenek.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3> 

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename> 
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2> 

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Binning funkció létrehozása
A következő példa bemutatja, hogyan hozhat létre binned funkciók at binning (öt raktárhely) egy numerikus oszlop, amely használható funkció helyett:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>A funkciók egyetlen oszlopból történő kivonása
Ebben a szakaszban bemutatjuk, hogyan lehet egy oszlopot egy táblázatban további funkciók létrehozásához. A példa feltételezi, hogy van egy szélességi vagy hosszúsági oszlop abban a táblázatban, amelyből szolgáltatásokat próbál létrehozni.

Itt van egy rövid alapozó szélességi / hosszúsági helyadatok (erőforrással stackoverflow [Hogyan mérjük a pontosságát szélességi és hosszúsági? ).](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude) Ez az útmutató hasznos, hogy megértsék, mielőtt a hely, mint egy vagy több funkció:

* A tábla azt mondja, hogy északvagy dél, kelet vagy nyugat vagyunk a világon.
* Egy nem nulla száz számjegy azt mutatja, hogy hosszúsági fokot használunk, nem szélességet!
* A tízes számjegy körülbelül 1000 kilométeres pozíciót ad. Ez ad nekünk hasznos információt arról, hogy milyen kontinensen vagy óceán vagyunk.
* Az egységek számjegye (egy tizedesjegy) 111 km-es (60 tengeri mérföld, körülbelül 69 mérföld) pozíciót ad. Nagyjából meg tudja mondani, hogy milyen állapotban, országban vagy régióban van.
* Az első tizedes jegy 11,1 km-t ér: meg tudja különböztetni egy nagyváros helyzetét a szomszédos nagyvárostól.
* A második tizedes jegy 1,1 km-t ér: egyik falut el tudja választani a másiktól.
* A harmadik tizedes jegy 110 m-ig ér: nagy mezőgazdasági területet vagy intézményi kampuszt tud azonosítani.
* A negyedik tizedes jegy 11 m-ig ér: képes azonosítani egy telket. Ez hasonló a tipikus pontossága egy korrigálatlan GPS egység interferencia nélkül.
* Az ötödik tizedesjegy 1,1 m-ig érdemes: megkülönbözteti a fákat egymástól. A kereskedelmi GPS-egységekkel ezt a szintet csak differenciálkorrekcióval lehet elérni.
* A hatodik tizedesjegy 0,11 m-ig érdemes: ezt használhatja a szerkezetek részletes lefektetésére, tájak tervezésére, utak építésére. Több, mint elég jónak kell lennie a gleccserek és folyók mozgásának nyomon követéséhez. Ez úgy érhető el, hogy a GPS-szel aprólékos intézkedéseket tesz, például differenciáltan korrigált GPS-t.

A helyadatok a következőképpen is felszámíthatók, elkülönítve a régiót, a helyet és a városadatait. A régió/körzet adatainak lekérnie kell a REST-végpontot, például a Bing Maps API-t, amely [a Hely keresése pont szerint](https://msdn.microsoft.com/library/ff701710.aspx) elérhető.

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

Ezek a helyalapú funkciók további használható további count funkciók létrehozásához, ahogy azt korábban leírtuk. 

> [!TIP]
> A rekordokat programozott módon is beszúrhatja a választott nyelv használatával. Előfordulhat, hogy az írási hatékonyság növelése érdekében adattömbökbe kell beszúrnia az adatokat (a pyodbc használatával ezt példaként lásd: [A HelloWorld minta az SQLServer pythonnal való eléréséhez).](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python) Egy másik alternatíva az adatok beszúrása az adatbázisba a [BCP segédprogram mal.](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás az Azure Machine Learninghez
Az újonnan létrehozott szolgáltatás hozzáadható oszlopként egy meglévő táblához, vagy egy új táblában tárolható, és az eredeti gépi tanulási táblához illeszthető. A funkciók létrehozhatók vagy elérhetők, ha már létrehozták őket, az Azure Machine Learning [Adatok importálása][import-data] moduljával az alábbiak szerint:

![azureml olvasók][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Programozási nyelv, például Python használata
A Python használatával adatok feltárása és szolgáltatások létrehozása, ha az adatok az SQL Server hasonló az adatok feldolgozása az Azure blob ban a Python dokumentált [Folyamat Azure Blob adatok az adatelemzési környezetben.](data-blob.md) Töltse be az adatokat az adatbázisból egy pandas adatkeretbe további feldolgozáshoz. Dokumentáljuk az adatbázishoz való csatlakozás és az adatok betöltésének folyamatát az ebben a szakaszban.

A következő kapcsolati karakterlánc-formátum mal(OK) pyodbc használatával csatlakozhat egy SQL Server-adatbázishoz (cserélje le a kiszolgálónevet, a dbname-t, a felhasználónevet és a jelszót a megadott értékekre):

    #Set up the SQL Azure connection
    import pyodbc    
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Python Pandas könyvtára](https://pandas.pydata.org/) adatstruktúrák és adatelemző eszközök gazdag készletét biztosítja a Python-programozás adatkezeléséhez. Az alábbi kód beolvassa az SQL Server adatbázisból visszaadott eredményeket egy Pandas adatkeretbe:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már használhatja a Pandas adatkeret, ahogy azt a cikkben [az Azure Blob adatainak feldolgozása az adatelemzési környezetben.](data-blob.md)

## <a name="azure-data-science-in-action-example"></a>Példa az Azure-adatok tudományára
Az Azure Data Science Process nyilvános adatkészletet használó végig- és forgatókönyv-példáját az Azure Data Science Process in Action című [témakörben található.](sql-walkthrough.md)

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/


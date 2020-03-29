---
title: Szolgáltatások létrehozása az SQL Server ben az SQL és a Python használatával – Csapatadatelemzési folyamat
description: Az SQL Server virtuális gépében tárolt adatokhoz az SQL és a Python használatával – a csapatadat-elemzési folyamat részeként – létrehozott funkciókat hozhat létre.
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
ms.openlocfilehash: 58fa98005d7d89e84404d99cf4f55e456fd91f21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721744"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ez a dokumentum bemutatja, hogyan hozhat létre az Azure-beli SQL Server virtuális gépeken tárolt adatok funkcióit, amelyek segítségével az algoritmusok hatékonyabban tanulhatnak az adatokból. A feladat végrehajtásához sql vagy programozási nyelv, például a Python használatával. Itt mindkét megközelítés bizonyításra került.

Ez a feladat egy lépés a [Csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

> [!NOTE]
> Gyakorlati példát a NYC [Taxi adatkészletben](https://www.andresmh.com/nyctaxitrips/) tekinthet meg, és az IPython Notebook és az SQL Server használatával nyc data nevű IPNB-dokumentumban tekinthet [iPython-jegyzetfüzetet és SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) t.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy a következőket feltételezi:

* Létrehozott egy Azure-tárfiókot. Ha utasításokra van szüksége, olvassa [el az Azure Storage-fiók létrehozása című témakört.](../../storage/common/storage-account-create.md)
* Az adatokat az SQL Server ben tárolta. Ha még nem, olvassa el [az Adatok áthelyezése egy Azure-beli Azure Machine Learning hez](move-sql-azure.md) az adatok áthelyezésére vonatkozó utasításokat.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Szolgáltatásgenerálás SQL-rel
Ebben a szakaszban az SQL használatával a szolgáltatások létrehozásának módjait ismertetjük:  

* [Számalapú szolgáltatásgenerálás](#sql-countfeature)
* [Binning funkció létrehozása](#sql-binningfeature)
* [A funkciók egyetlen oszlopból történő kivonása](#sql-featurerollout)

> [!NOTE]
> Miután további szolgáltatásokat hozott létre, hozzáadhatja őket oszlopként a meglévő táblához, vagy létrehozhat egy új táblát a további szolgáltatásokkal és elsődleges kulccsal, amely az eredeti táblához csatlakoztatható.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Számlálásalapú szolgáltatásgenerálás
Ez a dokumentum a számlálási funkciók létrehozásának két módját mutatja be. Az első módszer feltételes összeget, a második pedig a "where" záradékot használja. Ezek az új szolgáltatások ezután összekapcsolhatók az eredeti táblával (az elsődleges kulcsoszlopok használatával), hogy az eredeti adatok mellett számlálási funkciók is legyenek.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Binning funkció létrehozása
A következő példa bemutatja, hogyan hozhat létre binned funkciók at binning (öt raktárhely) egy numerikus oszlop, amely használható funkció helyett:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>A funkciók egyetlen oszlopból történő kivonása
Ebben a szakaszban bemutatjuk, hogyan lehet egy oszlopot egy táblázatban további funkciók létrehozásához. A példa feltételezi, hogy van egy szélességi vagy hosszúsági oszlop abban a táblázatban, amelyből szolgáltatásokat próbál létrehozni.

Itt van egy rövid alapozó szélességi / hosszúsági helyadatok `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`(erőforrással stackoverflow ). Íme néhány hasznos dolog, amelyet meg kell értenie a helyadatokkal kapcsolatban, mielőtt funkciókat hozna létre a mezőből:

* A jel azt jelzi, hogy északvagy dél, kelet vagy nyugat vagyunk a világon.
* A nem nulla száz számjegy hosszúsági tartamot jelez, nem szélességi fokot használ.
* A tízes számjegy körülbelül 1000 kilométeres pozíciót ad. Hasznos információkat ad arról, hogy milyen kontinensen vagy óceánon vagyunk.
* Az egységek számjegye (egy tizedesjegy) 111 km-es (60 tengeri mérföld, körülbelül 69 mérföld) pozíciót ad. Azt jelzi, nagyjából, milyen nagy állam vagy ország / régió vagyunk.
* Az első tizedes jegy 11,1 km-t ér: meg tudja különböztetni egy nagyváros helyzetét a szomszédos nagyvárostól.
* A második tizedes jegy 1,1 km-t ér: egyik falut el tudja választani a másiktól.
* A harmadik tizedes jegy 110 m-ig ér: nagy mezőgazdasági területet vagy intézményi kampuszt tud azonosítani.
* A negyedik tizedes jegy 11 m-ig ér: képes azonosítani egy telket. Ez hasonló a tipikus pontossága egy korrigálatlan GPS egység interferencia nélkül.
* Az ötödik tizedesjegy 1,1 m-ig érdemes: megkülönbözteti a fákat egymástól. A kereskedelmi GPS-egységekkel ezt a szintet csak differenciálkorrekcióval lehet elérni.
* A hatodik tizedes jegy 0,11 m-ig érdemes: ezt a szintet használhatja a szerkezetek részletes lefektetésére, tájak tervezésére, utak építésére. Több, mint elég jónak kell lennie a gleccserek és folyók mozgásának nyomon követéséhez. Ez a cél úgy érhető el, hogy a GPS-szel aprólékos intézkedéseket tesz, például differenciáltan korrigált GPS-t.

A helyadatok a régió, a hely és a város adatainak elkülönítésével is feladhatók. Egyszer is hívhat egy REST-végpont, például `https://msdn.microsoft.com/library/ff701710.aspx` a Bing Maps API-t (lásd a régió/körzet adatait).

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
> A rekordokat programozott módon is beszúrhatja a választott nyelv használatával. Előfordulhat, hogy az írási hatékonyság javítása érdekében be kell szúrnia az adatokat adattömbökbe. [Íme egy példa arra, hogyan kell ezt csinálni pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik alternatíva az adatok beszúrása az adatbázisba a [BCP segédprogrammal](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Csatlakozás az Azure Machine Learninghez
Az újonnan létrehozott szolgáltatás hozzáadható oszlopként egy meglévő táblához, vagy egy új táblában tárolható, és az eredeti gépi tanulási táblához illeszthető. A funkciók létrehozhatók vagy elérhetők, ha már létrehozták őket, az Azure ML [Adatok importálása](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) moduljával az alábbiak szerint:

![Azure ML-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Programozási nyelv, például Python használata
A Python használatával a szolgáltatások létrehozása, ha az adatok az SQL Server hasonló az adatok feldolgozása az Azure blob python használatával. Összehasonlítás: [Folyamat Az Azure Blob-adatok az adatelemzési környezetben.](data-blob.md) Töltse be az adatokat az adatbázisból egy pandas adatkeretbe, hogy tovább dolgozd fel. Ebben a szakaszban dokumentálja az adatbázishoz való csatlakozás és az adatok adatkeretbe való betöltésének folyamatát.

A következő kapcsolati karakterlánc-formátum mal(OK) pyodbc használatával csatlakozhat egy SQL Server-adatbázishoz (cserélje le a kiszolgálónevet, a dbname-t, a felhasználónevet és a jelszót a megadott értékekre):

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Python Pandas könyvtára](https://pandas.pydata.org/) adatstruktúrák és adatelemző eszközök gazdag készletét biztosítja a Python-programozás adatkezeléséhez. A következő kód beolvassa az SQL Server adatbázisból visszaadott eredményeket egy Pandas adatkeretbe:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már a Pandas adatkerettel dolgozhat a Panda [használatával: Az Azure blobtárolási adatok hozása a Panda használatával](create-features-blob.md)funkciók létrehozása az Azure blobtárolási adatokhoz című témakörben.


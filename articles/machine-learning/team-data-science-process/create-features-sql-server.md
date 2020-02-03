---
title: Funkciók létrehozása az SQL Server az SQL és Python - csoportos adatelemzési folyamat
description: Hozzon létre az SQL Server virtuális gép az Azure-ban, SQL és Python - a csoportos adatelemzési folyamat részeként tárolt adatok funkciókat.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721744"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ez a dokumentum bemutatja, hogyan hozhat létre az SQL Server virtuális gép az Azure-ban tárolt adatokat, amelyek segítségével hatékonyabban megismerheti az adatokból algoritmusok szolgáltatásai. Ennek a feladatnak használhatja az SQL és a egy programozási nyelvet, például a Python. Mindkét módszerénél itt találja meg.

Ez a feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

> [!NOTE]
> Gyakorlati példákért tekintse meg a [New York-i taxi-adatkészletet](https://www.andresmh.com/nyctaxitrips/) , és tekintse meg a New York-i HUZAVONA nevű IPNB a [IPython notebook használatával SQL Server és](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) a végpontok közötti átjáráshoz.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha útmutatásra van szüksége, tekintse meg [Az Azure Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című témakört.
* Az SQL Server tárolja az adatokat. Ha nem rendelkezik a szolgáltatással, olvassa el az [adatáthelyezés egy Azure Machine Learning Azure SQL Databasere](move-sql-azure.md) című témakört, amely útmutatást nyújt az adatáthelyezéshez.

## <a name="sql-featuregen"></a>Funkciók létrehozása SQL-sel
Ebben a szakaszban ismertetünk módon a létrehozást funkciók az SQL:  

* [Darabszám-alapú szolgáltatás létrehozása](#sql-countfeature)
* [Dobozolási szolgáltatás létrehozása](#sql-binningfeature)
* [A funkciók kimutatása egyetlen oszlopból](#sql-featurerollout)

> [!NOTE]
> További funkciók generál, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcs, amely összekapcsolható a az eredeti tábla.
> 
> 

### <a name="sql-countfeature"></a>Darabszám-alapú szolgáltatás létrehozása
Ez a dokumentum azt ismerteti, kétféleképpen létrehozni bloberőforrásokhoz száma funkciókat. Az első módszer feltételes sum és a második módszer használja a "where" záradék. Ezek az új funkciók ezután csatlakoztathatók az eredeti táblához (az elsődleges kulcs oszlopainak használatával), hogy az eredeti adatértékekkel együtt is megszámolják a szolgáltatásokat.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>Dobozolási szolgáltatás létrehozása
Az alábbi példa bemutatja, hogyan binned szolgáltatások létrehozásához dobozolás (használatával öt bins) által egy numerikus oszlopot inkább funkcióként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>A funkciók kimutatása egyetlen oszlopból
Ebben a szakaszban bemutatjuk, hogyan vezethet be csak egy oszlop a tábla létrehozásához további szolgáltatásokat. A példában feltételeztük, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből próbált szolgáltatások készítése.

Íme egy rövid ismertető a szélességi és a hosszúsági hely adatainak áttekintéséről (a StackOverflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`ból származnak). Íme néhány hasznos lépése, hogy a helyadatok kapcsolatos mezőjéből funkciók létrehozása előtt:

* A bejelentkezési azt jelzi, hogy vannak-e Észak vagy Dél-India, keleti vagy nyugati jelöl.
* Egy nem nulla több száz számjegy azt jelzi, hogy hosszúság, szélesség nincs használatban van.
* A több számjegy biztosít olyan helyzetben, hogy körülbelül 1000 alapján. Milyen kontinens vagy vagyunk az óceán hasznos információkat biztosít.
* Az egységek számjegy (decimális mértékű) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri mérföldre, körülbelül 69 mérföld). Ez nagyjából azt jelzi, hogy milyen nagy az állam vagy ország/régió.
* Az első tizedes ér akár 11.1 km-re: azt megkülönböztethesse a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes ér akár 1.1 km-re: azt is egy falu elkülönítése a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi campus segítenek azonosítani.
* A negyedik tizedes ér legfeljebb 11 m: egy segítenek azonosítani. Fontos nem zavarja a nem javított GPS egység tipikus pontossága hasonlítható.
* Az ötödik tizedes ér akár 1.1 m:, fák különbözteti meg egymástól. Erre a szintre a kereskedelmi GPS-egységekhez pontossága csak különbözeti helyesbítéssel érhető el.
* A hatodik tizedes tört érték 0,11 m-re van korlátozva: ezt a szintet részletesen, a Tájképek kialakításához, az utak létrehozásához használhatja. Több mint elég jó glaciers és folyókat követési kell lennie. Ezt a célt úgy érheti el, ha a GPS-szel, például a differentially korrigált GPS-sel kapcsolatos fáradságos mértékeket használ.

A helyadatok lehet featurized régió, a hely és a várost elválasztva. Ha egyszer is meghívhat egy REST-végpontot, például a Bing Maps API-t (lásd: `https://msdn.microsoft.com/library/ff701710.aspx` a régió/kerület információinak beszerzéséhez).

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
> Programozott módon szúrhat be a rekordok használatával tetszőleges nyelven. Szükség lehet az adatok beszúrásához írási hatékonyság növelése érdekében. [Íme egy példa arra, hogyan teheti ezt meg a pyodbc használatával](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik alternatíva az, hogy az adatbázisban a [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx) használatával szúr be adatbevitelt
> 
> 

### <a name="sql-aml"></a>Csatlakozás a Azure Machine Learninghoz
Az újonnan létrehozott szolgáltatást meglévő táblához oszlopként hozzáadható vagy egy új tábla tárolja és csatlakozik, a machine Learning szolgáltatáshoz az eredeti tábla. A szolgáltatások létrehozhatók vagy elérhetők, ha már létrejöttek, az [adatimportálási](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul használata az Azure ml-ben az alábbi ábrán látható módon:

![Az Azure Machine Learning-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Programozási nyelv (például Python) használata
Python használata a szolgáltatások létrehozásához, amikor az adatok az SQL Server hasonlít a Python használatával Azure blob adatok feldolgozása. Összehasonlításért lásd: az [Azure Blob-adatfeldolgozás adatelemzési környezetben](data-blob.md). További feldolgozási pandas adatok keretbe betölteni az adatokat az adatbázisból. Ez a szakasz az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz ismertetését.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A Pythonban található [Panda Library](https://pandas.pydata.org/) számos adatstruktúrát és adatelemzési eszközt biztosít a Python programozási funkciók adatkezeléséhez. Az alábbi kód beolvassa az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már dolgozhat a Panda adatkerettel, ahogy az az [Azure Blob Storage-hoz készült szolgáltatások létrehozása a Panda használatával](create-features-blob.md)című témakörben leírtak szerint.


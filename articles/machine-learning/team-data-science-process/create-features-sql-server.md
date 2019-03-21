---
title: Funkciók létrehozása az SQL Server az SQL és Python - csoportos adatelemzési folyamat
description: Hozzon létre az SQL Server virtuális gép az Azure-ban, SQL és Python - a csoportos adatelemzési folyamat részeként tárolt adatok funkciókat.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2d01b74e7db275f4b2e3933415bbae40911b114b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57854891"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Funkciók létrehozása az adatokhoz az SQL Serveren SQL és Python használatával
Ez a dokumentum bemutatja, hogyan hozhat létre az SQL Server virtuális gép az Azure-ban tárolt adatokat, amelyek segítségével hatékonyabban megismerheti az adatokból algoritmusok szolgáltatásai. Ennek a feladatnak használhatja az SQL és a egy programozási nyelvet, például a Python. Mindkét módszerénél itt találja meg.

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

> [!NOTE]
> Gyakorlati például tanulmányozza a [NYC Taxi adatkészlet](https://www.andresmh.com/nyctaxitrips/) , majd tekintse át a készülékén IPNB [NYC adatok konvertálása, az IPython Notebook és az SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) egy teljes körű útmutató az.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik:

* Létrehozott egy Azure storage-fiókot. Ha utasításokat van szüksége, tekintse meg [Azure Storage-fiók létrehozása](../../storage/common/storage-quickstart-create-account.md)
* Az SQL Server tárolja az adatokat. Ha nem, olvassa el [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md) van az adatok áthelyezéséhez létrehozásával kapcsolatos útmutatást.

## <a name="sql-featuregen"></a>SQL-szolgáltatás létrehozása
Ebben a szakaszban ismertetünk módon a létrehozást funkciók az SQL:  

1. [Száma alapján a szolgáltatás létrehozása](#sql-countfeature)
2. [A dobozolás a szolgáltatás létrehozása](#sql-binningfeature)
3. [Egyetlen oszlop az a funkciók bevezetéséről](#sql-featurerollout)

> [!NOTE]
> További funkciók generál, ha oszlopként, azokat hozzá a meglévő tábla, vagy hozzon létre egy új táblát a további funkciók és az elsődleges kulcs, amely összekapcsolható a az eredeti tábla.
> 
> 

### <a name="sql-countfeature"></a>Alapú száma a szolgáltatás létrehozása
Ez a dokumentum azt ismerteti, kétféleképpen létrehozni bloberőforrásokhoz száma funkciókat. Az első módszer feltételes sum és a második módszer használja a "where" záradék. Ezek ezután összekapcsolható a az eredeti tábla (elsődleges kulcs oszlopát használatával), count funkciók mellett az eredeti adatok.

    select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

    select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
    where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>

### <a name="sql-binningfeature"></a>A dobozolás a szolgáltatás létrehozása
Az alábbi példa bemutatja, hogyan binned szolgáltatások létrehozásához dobozolás (használatával 5 bins) által egy numerikus oszlopot inkább funkcióként használható:

    `SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>`


### <a name="sql-featurerollout"></a>Egyetlen oszlop az a funkciók bevezetéséről
Ebben a szakaszban bemutatjuk, hogyan vezethet be csak egy oszlop a tábla létrehozásához további szolgáltatásokat. A példában feltételeztük, hogy nincs-e a szélességi és hosszúsági oszlop a tábla, amelyből próbált szolgáltatások készítése.

Íme egy rövid ismertetőt a szélességi és hosszúsági koordinátákkal helyadatok (a stackoverflow forrásokat `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude`). Íme néhány hasznos lépése, hogy a helyadatok kapcsolatos mezőjéből funkciók létrehozása előtt:

* A bejelentkezési azt jelzi, hogy vannak-e Észak vagy Dél-India, keleti vagy nyugati jelöl.
* Egy nem nulla több száz számjegy azt jelzi, hogy hosszúság, szélesség nincs használatban van.
* A több számjegy biztosít olyan helyzetben, hogy körülbelül 1000 alapján. Milyen kontinens vagy vagyunk az óceán hasznos információkat biztosít.
* Az egységek számjegy (decimális mértékű) biztosít egy helyen legfeljebb 111 kilométerben (60 tengeri mérföldre, körülbelül 69 mérföld). Azt jelzi, nagyjából, milyen nagy állam vagy ország tudunk.
* Az első tizedes ér akár 11.1 km-re: azt megkülönböztethesse a szomszédos nagy város egy nagy város pozícióját.
* A második tizedes ér akár 1.1 km-re: azt is egy falu elkülönítése a Tovább gombra.
* A harmadik tizedes ér legfeljebb 110 m: nagy mezőgazdasági mező vagy intézményi campus segítenek azonosítani.
* A negyedik tizedes ér legfeljebb 11 m: egy segítenek azonosítani. Fontos nem zavarja a nem javított GPS egység tipikus pontossága hasonlítható.
* Az ötödik tizedes ér akár 1.1 m:, fák különbözteti meg egymástól. Erre a szintre a kereskedelmi GPS-egységekhez pontossága csak különbözeti helyesbítéssel érhető el.
* A hatodik tizedes ér értéke legfeljebb 0,11 m: ezzel részletes környezetünk, tervezéséhez struktúrák elrendező utak létrehozásához. Több mint elég jó glaciers és folyókat követési kell lennie. Ez megvalósítható a GPS-adatok, például a GPS differentially javított painstaking intézkedéseket.

A helyadatok lehet featurized régió, a hely és a várost elválasztva. Vegye figyelembe, hogy többször is meghívhat egy REST-végpontot, például a Bing térképek API elérhető `https://msdn.microsoft.com/library/ff701710.aspx` a régió/kerület adatok lekéréséhez.

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

Ezek a helyalapú szolgáltatások további használható további száma szolgáltatások létrehozásához, a fentebb leírt módon.

> [!TIP]
> Programozott módon szúrhat be a rekordok használatával tetszőleges nyelven. Szükség lehet az adatok beszúrásához írási hatékonyság növelése érdekében. [Íme egy példa bemutatja, hogyan ehhez pyodbc használatával](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Egy másik lehetőség, hogy az adatbázis használatával szúrja be az adatokat [BCP segédprogram](https://msdn.microsoft.com/library/ms162802.aspx)
> 
> 

### <a name="sql-aml"></a>Csatlakozás az Azure Machine Learning
Az újonnan létrehozott szolgáltatást meglévő táblához oszlopként hozzáadható vagy egy új tábla tárolja és csatlakozik, a machine Learning szolgáltatáshoz az eredeti tábla. Szolgáltatások jön létre, vagy ha már létrehozta, keresztül érhető a [adatok importálása](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) modul az Azure ML alább látható módon:

![Az Azure Machine Learning-olvasók](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="python"></a>Például a Python-programozási nyelv
Python használata a szolgáltatások létrehozásához, amikor az adatok az SQL Server hasonlít a Python használatával Azure blob adatok feldolgozása. Összehasonlításáért lásd [Azure Blobadatok folyamat a data science környezetben](data-blob.md). További feldolgozási pandas adatok keretbe betölteni az adatokat az adatbázisból. Ez a szakasz az adatbázishoz csatlakozással, és az adatok betöltését az adathalmaz ismertetését.

Csatlakozás SQL Server-adatbázis a Pythonnal pyodbc (cserélje le a kiszolgálónév, adatbázisnév, felhasználónév és jelszó az adott értékek) használatával a következő kapcsolati karakterlánc-formátum használható:

    #Set up the SQL Azure connection
    import pyodbc
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

A [Pandas könyvtár](https://pandas.pydata.org/) pythonban adatkezelés Python programozási széles választékának datové struktury és az adatok elemzésére szolgáló eszközöket biztosít. Az alábbi kód beolvassa az eredményeket az SQL Server-adatbázisból egy Pandas adatkeretbe küldött:

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)

Most már használhatja a Pandas adatkeretbe témakörökben ismertetett [funkciók létrehozása az Azure blob storage adatokból a Pandas használatával](create-features-blob.md).


---
title: Mintaadatok a tervező számára
titleSuffix: Azure Machine Learning
description: További információ a Azure Machine Learning Designerben elérhető minta adatkészletekről.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: b19c1ed5aa556a2cfb633ac6fbc0cf9f95d0337f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81256977"
---
# <a name="sample-datasets-in-azure-machine-learning-designer-preview"></a>Minta adatkészletek a Azure Machine Learning Designerben (előzetes verzió)

Amikor új folyamatot hoz létre Azure Machine Learning Designerben (előzetes verzió), a rendszer alapértelmezés szerint számos minta adatkészletet tartalmaz. Ezeket a minta adatkészleteket a tervező kezdőlapján található mintavételi folyamatok használják. 

A minta adatkészletek az **adatkészletek**-**mintáinak** kategóriájában érhetők el. Ez a modul paletta bal oldalán található a tervezőben. Ezeket az adatkészleteket a saját folyamatában is használhatja, ha a vászonra húzza azt.

## <a name="datasets"></a>Adathalmazok


| Adatkészlet&nbsp;neve&nbsp;&nbsp;&nbsp;&nbsp;| Adatkészlet leírása |
|-------------|:--------------------|
| Felnőtt népszámlálás jövedelme bináris besorolási adatkészlet | Az 1994-es népszámlálási adatbázis egy részhalmaza, amely a 16. korban dolgozó felnőtteket használja > 100 korrigált bevételi indexszel.<br/>**Használat**: a demográfiai adatokat használó személyek besorolása annak előrejelzéséhez, hogy egy személy évente több mint 50 000-et keres.<br/> **Kapcsolódó kutatás**: Kohavi, R., Becker, B., (1996). [UCI Machine learning adattár](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Az autók árát (nyers)|Információk a márka és a modell alapján, beleértve a díjszabást, az olyan szolgáltatásokat, mint például a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.<br/> A kockázati pontszám kezdetben az automatikus árral van társítva. Ezt követően a rendszer a tényleges kockázatokat egy, a biztosítási matematikusok ismert folyamat során helyesbíti. A + 3 érték azt jelzi, hogy az automatikus kockázatos, a-3 érték pedig valószínűleg biztonságos.<br/>**Használat**:</b> a funkciók kockázati pontszámának előrejelzése regresszió vagy többváltozós besorolás használatával.<br/>**Kapcsolódó kutatás**:</b> Schlimmer, J.C. (1987). [UCI Machine learning adattár](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Megosztott CRM szolgáltatóváltást-Címkék |A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)) címkéi.|
|Megosztott CRM-adatforgalom-Címkék|A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)) címkéi.|
|Megosztott CRM-adatkészlet | Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásról ([orange_small_train. adat. zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)) származnak. <br/>Az adatkészlet 50 000 ügyfelet tartalmaz a francia távközlési cég Orange-től. Minden ügyfél 230-es anonim funkciókkal rendelkezik, amelyek 190 a numerikus és a 40-as számú adat kategorikus. A funkciók nagyon ritkák. |
|Megosztott CRM-Címkék|A KDD Cup 2009 ügyfélkapcsolat-előrejelzési Challenge ([orange_large_train_upselling. labels)](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) címkéi|
|Repülési késések adatvédelme|Az Amerikai Egyesült államokbeli Közlekedési Minisztérium TranStats adatgyűjtési szolgáltatásában[(az időpontnál](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)) üzemelő személyszállító repülési adatok.<br/>Az adatkészlet a 2013. április-október időszakra terjed ki. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva: <br/>– Az adatkészlet úgy lett szűrve, hogy csak a 70-es legforgalmasabb repülőtérre vonatkozzon az USA kontinentális területén <br/>– A megszakított járatok több mint 15 perc késéssel lettek megjelölve <br/>– A kiszűrt járatok kiszűrése megtörtént <br/>-A következő oszlopok lettek kiválasztva: év, hónap, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, megszakítva|
|Német hitelkártya UCI-adatkészlet|Az UCI statlog (német bankkártya) adatkészlete ([statlog + német + kredit + adat](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) a német. adatfájl használatával.<br/>Az adatkészlet az attribútumok egy halmaza által leírtak szerint osztályozza a személyeket, alacsony vagy magas hitelezési kockázatként. Mindegyik példa egy személyt jelöl. A rendszer 20 funkciót használ: számszerű és kategorikus, valamint bináris címkét (a kredit kockázati értékét). A magas kockázatú kreditek címkéje = 2, az alacsony kreditek esetében pedig a Label = 1. Az alacsony kockázatú, például a magas kockázatú példa nem megfelelő besorolásának díja 1, míg a magas kockázatú példa alacsony értékkel való megsértésének díja 5.|
|IMDB-mozgóképek címei|Az adatkészlet a Twitter tweetekben értékelt filmek információit tartalmazza: IMDB-film azonosítója, film neve, műfaj és termelési év. 17K-filmek vannak az adatkészletben. Az adatkészlet az "S" dokumentumban lett bevezetve. Dooms, T. de Pessemier és L. Martens. MovieTweetings: a Twitteren gyűjtött film minősítési adatkészlet. Workshop a közösségi-on és az emberi számításban az ajánló rendszerekhez, CrowdRec a RecSys 2013-kor. "|
|Film minősítése|Az adatkészlet a Movie tweetek adatkészlet kiterjesztett verziója. Az adatkészlet 170K-minősítésekkel rendelkezik, amelyek a Twitteren jól strukturált tweetekkel lettek kinyerve. Minden példány egy tweetet jelöl, és egy rekord: felhasználói azonosító, IMDB-film azonosítója, minősítés, időbélyeg, kedvencek száma ehhez a tweethez és a tweetek száma. Az adatkészlet elérhetővé tétele a. Said, S. dooms, B. Loni és D. tikk for ajánló Systems Challenge 2014.|
|Időjárási adatkészlet|A NOAA-ből származó, óránkénti szárazföld-alapú időjárási megfigyelések ([201304 és 201310 közötti egyesített adatok](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Az időjárási adatok a repülőtéri meteorológiai állomások megfigyeléseit fedik le, amely az 2013. április-október időszakra vonatkozik. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:    <br/> – A meteorológiai állomások azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve    <br/> – A 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások kiszűrése megtörtént    <br/> -A Date oszlop külön évre, hónapra és napi oszlopokra van bontva    <br/> – A következő oszlopok lettek kiválasztva: AirportID, év, hónap, nap, idő, időzóna, SkyCondition, láthatóság, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, rekordtípus, HourlyPrecip, magasságmérő|
|Wikipedia SP 500 adatkészlet|Az adatok a wikipedia-ből származnak (https://www.wikipedia.org/) az egyes S&P 500-es vállalatok cikkei alapján, amelyek XML-adatként vannak tárolva.    <br/>A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:    <br/> -Szöveges tartalom kinyerése minden egyes vállalatnál    <br/> – Wiki formázásának eltávolítása    <br/> – Nem alfanumerikus karakterek eltávolítása    <br/> – Az összes szöveg konvertálása kisbetűsre    <br/> – Ismert vállalati kategóriák lettek hozzáadva    <br/>Vegye figyelembe, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.|

## <a name="next-steps"></a>További lépések

* A prediktív elemzés és a gépi tanulás alapjai a [bemutatóban: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)

* Megtudhatja, hogyan módosíthatja a meglévő [tervezői mintákat](samples-designer.md) az igényeinek megfelelően.

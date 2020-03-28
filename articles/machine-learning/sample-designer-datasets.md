---
title: Mintaadatkészletek használata az Azure Machine Learning tervezőjében
titleSuffix: Azure Machine Learning
description: Tudjon meg többet az Azure Machine Learning-tervezőben található mintaadatkészletekről.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 03/03/2020
ms.openlocfilehash: 943e23aa9d83df33bbe636ce7aee62e6e5a2950e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79037301"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>A mintaadatkészletek használata az Azure Machine Learning designerben (előzetes verzió)

Amikor új folyamatot hoz létre az Azure Machine Learning tervezőjében (előzetes verzió), alapértelmezés szerint számos mintaadatkészlet szerepel. Ezeket a mintaadatkészleteket a tervező kezdőlapján lévő mintafolyamatok használják. 

A mintaadatkészletek az-**Adatkészletminták** kategóriában érhetők el. **Datasets** Ezt a tervező vászontól balra található modulpalettán találja. Ezen adatkészletek bármelyikét használhatja a saját folyamatában, ha a vászonra húzza.

## <a name="datasets"></a>Adathalmazok


| Adatkészlet&nbsp;neve&nbsp;&nbsp;&nbsp;&nbsp;| Adatkészlet leírása |
|-------------|:--------------------|
| Felnőtt census income bináris osztályozási adatkészlet | Az 1994-es népszámlálási adatbázis egy részhalmaza, amely 16 év feletti dolgozó felnőtteket használ, kiigazított jövedelmi indexe > 100.<br/>**Használat**: Osztályozza az embereket a demográfiai adatok használatával annak előrejelzésére, hogy egy személy évente 50 ezer nél több keres-e.<br/> **Kapcsolódó kutatás**: Kohavi, R., Becker, B., (1996). [UCI gépi tanulási adattár](https://archive.ics.uci.edu/ml). Irvine, CA: Kaliforniai Egyetem, Információs és Számítástechnikai Iskola|
|Gépjármű áradatai (Nyers)|Információk az autók ról gyártmány és modell szerint, beleértve az árat, olyan jellemzőket, mint a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.<br/> A kockázati pontszám kezdetben az automatikus árhoz kapcsolódik. Ezt követően a biztosításmatematikusok által szimbólumként ismert folyamat tényleges kockázatához igazítják. A +3 érték azt jelzi, hogy az automatikus kockázatos, és értéke -3, hogy valószínűleg biztonságos.<br/>**Használat:**</b> A kockázati pontszám előrejelzése funkciók szerint, regressziós vagy többváltozós besorolás használatával.<br/>**Kapcsolódó**kutatás</b> : Schlimmer, J.C. (1987). [UCI gépi tanulási adattár](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| CRM előlegcímkék megosztva |Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzés kihívás ([orange_small_train_appetency.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)).|
|CRM churn címkék megosztva|Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzéskihívás ([orange_small_train_churn.labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)).|
|CRM-adatkészlet megosztva | Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásból származnak ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)). <br/>Az adatkészlet 50 Ezer ügyfelet tartalmaz a francia telecomcégtől, az Orange-tól. Minden ügyfél 230 anonimizált funkcióval rendelkezik, amelyek közül 190 numerikus és 40 kategorikus. A funkciók nagyon ritkák. |
|CRM upselling címkék megosztott|Címkék a KDD Cup 2009 ügyfélkapcsolat-előrejelzés kihívás ([orange_large_train_upselling.labels](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels)|
|Járatkésések adatai|Az Egyesült Államok Közlekedési Minisztériumának TranStats adatgyűjtéséből származó utasrepülési teljesítményadatok ([On-Time](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)).<br/>Az adatkészlet a 2013 április-októberi időszakot fedi le. A tervezőnek történő feltöltés előtt az adatkészlet feldolgozása a következőképpen történt: <br/>- Az adatkészletet úgy szűrték, hogy csak az USA 70 legforgalmasabb repülőterére terjedjen ki <br/>- A törölt járatokat több mint 15 perces késéssel látták el <br/>- Az átirányított járatokat kiszűrték <br/>- A következő oszlopok at választották ki: Év, Hónap, DayofWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Canceled|
|Német hitelkártya UCI adatkészlet|Az UCI Statlog (német hitelkártya) adatkészlet ([Statlog+German+Credit+Data](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) a german.data fájl használatával.<br/>Az adatkészlet az attribútumok készletével leírva alacsony vagy magas hitelkockázatként sorolja be a személyeket. Minden példa egy személyt jelöl. 20 funkció létezik, mind numerikus, mind kategorikus, és egy bináris címke (a hitelkockázati érték). A magas hitelkockázati tételek címkével rendelkeznek = 2, az alacsony hitelkockázati tételek címkével rendelkeznek = 1. Az alacsony kockázatú példák magasként való téves besorolásának költsége 1, míg a magas kockázatú példák alacsonyként való téves besorolásának költsége 5.|
|IMDB filmcímek|Az adatkészlet információkat tartalmaz a Twitter-tweetekben értékelt filmekről: IMDB filmazonosító, filmnév, műfaj és gyártási év. Az adatkészletben 17 K-s filmek találhatók. Az adatkészlet et az "S. Dooms, T. De Pessemier és L. Martens. MovieTweetings: a Movie Rating dataset gyűjtött Twitter. Workshop on Crowdsourcing and Human Computation for Recommender Systems (CrowdRec at RecSys 2013) workshop on Crowdsourcing and Human Computation for Recommender Systems (CrowdRec at RecSys 2013."|
|Film értékelések|Az adatkészlet a Movie Tweetings adatkészlet kibővített verziója. Az adatkészlet 170K minősítést tartalmaz a filmekhez, amelyeket jól strukturált tweetekből nyernek ki a Twitteren. Minden példány egy tweetet jelöl, és egy tuple: felhasználói azonosító, IMDB filmazonosító, értékelés, időbélyeg, a tweet kedvencek száma és a tweet retweetjeinek száma. Az adatkészletet Az A. Said, S. Dooms, B. Loni és D. Tikk bocsátotta rendelkezésre az Recommender Systems Challenge 2014 számára.|
|Időjárási adatkészlet|A NOAA óránkénti szárazföldi időjárási megfigyelései ([201304 és 201310](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)között egyesített adatok ).<br/>Az időjárási adatok a repülőtéri meteorológiai állomásokról származó, 2013 április-októberi időszakra vonatkozó megfigyeléseket fedik le. A tervezőnek történő feltöltés előtt az adatkészlet feldolgozása a következőképpen történt:    <br/> - A meteorológiai állomás azonosítóit a megfelelő repülőtéri azonosítókhoz    <br/> - A 70 legforgalmasabb repülőtérhez nem kapcsolódó meteorológiai állomásokat kiszűrték    <br/> - A Dátum oszlop külön Év, Hónap és Nap oszlopokra van felosztva    <br/> - A következő oszlopokat választották ki: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, Visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, Altimeter|
|Wikipedia SP 500 adatkészlet|Az adatok ahttps://www.wikipedia.org/) Wikipédiából származnak (az egyes S&P 500 vállalatok cikkei alapján, XML-adatként tárolva.    <br/>A tervezőnek történő feltöltés előtt az adatkészlet feldolgozása a következőképpen történt:    <br/> - Kivonat szöveges tartalom minden egyes vállalat    <br/> - Wiki formázás eltávolítása    <br/> - Nem alfanumerikus karakterek eltávolítása    <br/> - Konvertálja az összes szöveget kisbetűs    <br/> - Ismert vállalati kategóriákkal bővült    <br/>Ne feledje, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.|

## <a name="next-steps"></a>További lépések

* Ismerje meg a prediktív elemzés és a gépi tanulás alapjait [az Oktatóanyaggal: Az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)

* Ismerje meg, hogyan módosíthatja a meglévő [tervezőmintákat,](samples-designer.md) hogy azok az ön igényeihez igazodjanak.

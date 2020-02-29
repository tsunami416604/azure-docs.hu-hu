---
title: A minta adatkészletek használata
titleSuffix: Azure Machine Learning
description: A Machine Learning Designerben található minta modellekben használt adatkészletek leírása. Ezeket a minta adatkészleteket használhatja a folyamatokhoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 14e514f0025d474b3afb45524753583b7c2e8a7d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78165061"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-designer-preview"></a>A minta adatkészletek használata a Azure Machine Learning Designerben (előzetes verzió)

Amikor új folyamatot hoz létre Azure Machine Learning Designerben (előzetes verzió), a rendszer alapértelmezés szerint számos minta adatkészletet tartalmaz. A minta-adathalmazok nagy részét a tervező kezdőlapján lévő minták használják. Mások például a gépi tanulásban jellemzően használt különböző típusú adattípusokra mutatnak.

Ezen adatkészletek némelyike elérhető az Azure Blob Storage-ban. Ezen adatkészletek esetében a következő táblázat egy közvetlen hivatkozást tartalmaz. Ezeket az adatkészleteket a folyamatokban az [adat importálása](./algorithm-module-reference/import-data.md) modul használatával használhatja.

Ezen adathalmazok további része az **Adatkészletek**-a **Samples** kategóriában érhető el. Ez a modul paletta bal oldalán található a tervezőben. Ezeket az adatkészleteket a saját folyamatában is használhatja, ha a vászonra húzza azt.

## <a name="datasets"></a>Adatkészletek

<table>

<tr>
  <th>Adatkészlet neve</th>
  <th>Adatkészlet leírása</th>
</tr>

<tr>
  <td>Felnőtt népszámlálás jövedelme bináris besorolási adatkészlet</td>
  <td>
Az 1994-es népszámlálási adatbázis egy részhalmaza, amely a 16. korban dolgozó felnőtteket használja > 100 korrigált bevételi indexszel.
<p></p>
<b>Használat:</b> A demográfiai adatokkal rendelkező személyek besorolása annak előrejelzéséhez, hogy egy személy évente több mint 50 000-et keres.
<p></p>
<b>Kapcsolódó kutatás:</b> Kohavi, R., Becker, B., (1996). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>

<tr>
  <td>Az autók árát (nyers)</td>
  <td>
Információk a márka és a modell alapján, beleértve a díjszabást, az olyan szolgáltatásokat, mint például a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.
<p></p>
A kockázati pontszám kezdetben az automatikus árral van társítva. Ezt követően a rendszer a tényleges kockázatokat egy, a biztosítási matematikusok ismert folyamat során helyesbíti. A + 3 érték azt jelzi, hogy az automatikus kockázatos, a-3 érték pedig valószínűleg biztonságos.
<p></p>
<b>Használat:</b> A funkciók kockázati pontszámának előrejelzése regresszió vagy többváltozós besorolás használatával. 
<p></p>
<b>Kapcsolódó kutatás:</b> Schlimmer, J.C. (1987). UCI Machine Learning adattár <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: University of California, School of Information and Computer Science </td>
</tr>


<tr>
  <td>Megosztott CRM szolgáltatóváltást-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-adatforgalom-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-adatkészlet</td>
  <td>
Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásról (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. adat. zip</a>) származnak.
<p></p>
Az adatkészlet 50 000 ügyfelet tartalmaz a francia távközlési cég Orange-től. Minden ügyfél 230-es anonim funkciókkal rendelkezik, amelyek 190 a numerikus és a 40-as számú adat kategorikus. A funkciók nagyon ritkák.
  </td>
</tr>

<tr>
  <td>Megosztott CRM-Címkék</td>
  <td>
A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>) címkéi.
  </td>
</tr>

<tr>
  <td>Repülési késések adatvédelme</td>
  <td>
Az Amerikai Egyesült államokbeli Közlekedési Minisztérium TranStats adatgyűjtési szolgáltatásában<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">(az időpontnál</a>) üzemelő személyszállító repülési adatok.
<p></p>
Az adatkészlet a 2013. április-október időszakra terjed ki. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>Az adatkészlet úgy lett szűrve, hogy csak a 70-es legforgalmasabb repülőtérre vonatkozzon az USA kontinentális területén</li>
  <li>A megszakított járatok több mint 15 perc késéssel lettek megjelölve</li>
  <li>A rendszer kiszűrte a lefordított járatok kiszűrését</li>
  <li>A következő oszlopok lettek kiválasztva: év, hónap, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, megszakítva</li>
</ul>
</td>
</tr>

<tr>
  <td>Német hitelkártya UCI-adatkészlet</td>
  <td>
Az UCI statlog (német bankkártya) adatkészlete (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">statlog + német + kredit + adat</a>) a német. adatfájl használatával.
<p></p>
Az adatkészlet az attribútumok egy halmaza által leírtak szerint osztályozza a személyeket, alacsony vagy magas hitelezési kockázatként. Mindegyik példa egy személyt jelöl. A rendszer 20 funkciót használ: számszerű és kategorikus, valamint bináris címkét (a kredit kockázati értékét). A magas kockázatú kreditek címkéje = 2, az alacsony kreditek esetében pedig a Label = 1. Az alacsony kockázatú, például a magas kockázatú példa nem megfelelő besorolásának díja 1, míg a magas kockázatú példa alacsony értékkel való megsértésének díja 5.
  </td>
</tr>

<tr>
  <td>IMDB-mozgóképek címei</td>
  <td>
Az adatkészlet a Twitter tweetekben értékelt filmek információit tartalmazza: IMDB-film azonosítója, film neve, műfaj és termelési év. 17K-filmek vannak az adatkészletben. Az adatkészlet az "S" dokumentumban lett bevezetve. Dooms, T. de Pessemier és L. Martens. MovieTweetings: a Twitteren gyűjtött film minősítési adatkészlet. Workshop a közösségi-on és az emberi számításban az ajánló rendszerekhez, CrowdRec a RecSys 2013-kor. "
  </td>
</tr>

<tr>
  <td>Film minősítése</td>
  <td>
Az adatkészlet a Movie tweetek adatkészlet kiterjesztett verziója. Az adatkészlet 170K-minősítésekkel rendelkezik, amelyek a Twitteren jól strukturált tweetekkel lettek kinyerve. Minden példány egy tweetet jelöl, és egy rekord: felhasználói azonosító, IMDB-film azonosítója, minősítés, időbélyeg, kedvencek száma ehhez a tweethez és a tweetek száma. Az adatkészlet elérhetővé tétele a. Said, S. dooms, B. Loni és D. tikk for ajánló Systems Challenge 2014.
  </td>
</tr>


<tr>
  <td>Időjárási adatkészlet</td>
  <td>
A NOAA-ből származó, óránkénti szárazföld-alapú időjárási megfigyelések (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">201304 és 201310 közötti egyesített adatok</a>).
<p></p>
Az időjárási adatok a repülőtéri meteorológiai állomások megfigyeléseit fedik le, amely az 2013. április-október időszakra vonatkozik. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>A meteorológiai állomás azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve</li>
  <li>Az 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások kiszűrése megtörtént</li>
  <li>A Date oszlop külön év, hónap és nap oszlopokra van bontva</li>
  <li>A következő oszlopok lettek kiválasztva: AirportID, év, hónap, nap, idő, időzóna, SkyCondition, láthatóság, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, Szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, rekordtípus, HourlyPrecip, magasságmérő</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500 adatkészlet</td>
  <td>
Az adatok forrása a Wikipedia (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>), amely az egyes S & P 500 vállalat cikkei alapján, XML-adatként tárolva.
<p></p>
A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:
<ul>
  <li>Szöveges tartalom kinyerése minden egyes vállalatnál</li>
  <li>Wiki formázásának eltávolítása</li>
  <li>Nem alfanumerikus karakterek eltávolítása</li>
  <li>Az összes szöveg konvertálása kisbetűsre</li>
  <li>Ismert vállalati kategóriák lettek hozzáadva</li>
</ul>
<p></p>
Vegye figyelembe, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Következő lépések

* A prediktív elemzés és a gépi tanulás alapjai a [bemutatóban: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)

* Az [adatimportálási](./algorithm-module-reference/import-data.md) modul használata minta adatkészletek importálásához

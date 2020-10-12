---
title: Példa folyamatokra & adatkészletek a tervező számára
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning Designerben mintákat használhat a gépi tanulási folyamatok elindításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.date: 09/09/2020
ms.custom: designer
ms.openlocfilehash: 15393a0e618c14fbaff46a51a300284d8afb4b77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "90889741"
---
# <a name="example-pipelines--datasets-for-azure-machine-learning-designer"></a>Példa folyamatokra & adatkészletek Azure Machine Learning Designer számára

A Azure Machine Learning Designer beépített példái segítségével gyorsan megkezdheti saját gépi tanulási folyamatainak kialakítását. A Azure Machine Learning Designer [GitHub-tárház](https://github.com/Azure/MachineLearningDesigner) részletes dokumentációt tartalmaz, amely megkönnyíti a gyakori gépi tanulási forgatókönyvek megismerését.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree)
* Egy Azure Machine Learning-munkaterület 

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="use-sample-pipelines"></a>Mintavételezési folyamatok használata

A tervező elmenti a mintavételi folyamatok egy példányát a Studio-munkaterületre. A folyamat szerkesztésével módosíthatja az igényeinek megfelelően, és mentheti a folyamatot. A projektek kiindulási pontként használhatók.

A következő módon használhat tervezői mintát:

1. Jelentkezzen be a <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>-be, és válassza ki a munkaterületet, amellyel dolgozni szeretne.

1. Válassza a **tervező**lehetőséget.

1. Válasszon egy minta folyamatot az **új folyamat** szakaszban.

    Válassza a **további minták megjelenítése** lehetőséget a minták teljes listájához.

1. A folyamat futtatásához először be kell állítania az alapértelmezett számítási célt a folyamat futtatásához.

   1. A vászontól jobbra található **Beállítások** ablaktáblán válassza a **számítási cél kiválasztása**lehetőséget.

   1. A megjelenő párbeszédpanelen válasszon ki egy meglévő számítási célt, vagy hozzon létre újat. Kattintson a **Mentés** gombra.

   1. Válassza a **beküldés** lehetőséget a vászon tetején a folyamat futtatásának elküldéséhez.

   A mintavételezési folyamattól és a számítási beállításoktól függően a futtatások végrehajtása hosszabb időt is igénybe vehet. Az alapértelmezett számítási beállításokhoz a csomópont minimális mérete 0, ami azt jelenti, hogy a tervezőnek üresjárat után le kell foglalnia az erőforrásokat. Az ismétlődő folyamat-futtatások kevesebb időt vesznek igénybe, mivel a számítási erőforrások már le vannak foglalva. Emellett a tervező az egyes modulok gyorsítótárazott eredményeit használja a hatékonyság növelése érdekében.


1. A folyamat futásának befejeződése után áttekintheti a folyamatot, és megtekintheti az egyes modulok kimenetét, és további információkat jeleníthet meg. A modulok kimeneteit a következő lépésekkel tekintheti meg:

   1. Kattintson a jobb gombbal arra a vászonra, amelynek kimenetét látni szeretné.
   1. Válassza a **Megjelenítés**lehetőséget.


   A minták kiindulási pontként használhatók a leggyakoribb gépi tanulási forgatókönyvekhez.

## <a name="regression"></a>Regresszió

Ismerkedjen meg ezekkel a beépített regressziós mintákkal.

| Mintacím | Leírás | 
| --- | --- |
| [Regresszió – autó árának előrejelzése (alapszintű)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Az autó árának előrejelzése lineáris regresszióval. |
| [Regresszió – autó árának előrejelzése (speciális)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Előre jelezheti az autók árát a döntési erdőben és a döntési fa regresszort. Hasonlítsa össze a modelleket a legjobb algoritmus megkereséséhez.

## <a name="classification"></a>Osztályozás

Ismerkedjen meg ezekkel a beépített besorolási mintákkal. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| [Bináris besorolás a szolgáltatás kiválasztásával – bevétel előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | A bevételt magas vagy alacsony értékűre becsülheti, kétosztályos kibővített döntési fában. A Pearson korreláció használatával válassza ki a funkciókat.
| [Bináris besorolás egyéni Python-parancsfájllal – hitelkockázat-előrejelzés](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Magas vagy alacsony kockázatú kredit alkalmazások besorolása. Az adatai súlyozásához használja a Python-szkript végrehajtása modult.
| [Bináris besorolás – ügyfélkapcsolatok előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | A kétosztályos, megnövelt döntési fák használatával megjósolhatja az ügyfelek adatforgalmát. A ARCUL ütötte használata az elfogult adatértékek megkóstolására.
| [Szöveg besorolása – wikipedia SP 500 adatkészlet](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Vállalati típusok besorolása a wikipedia-cikkekből többosztályos logisztikai regresszióval. |
| Többosztályos besorolás – levél felismerése | Hozzon létre a bináris osztályozók együttesét írásos betűk besorolásához. |

## <a name="computer-vision"></a>Számítógépes látástechnológia

Ismerkedjen meg ezekkel a beépített számítógépes jövőkép mintákkal. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| Széles & részletes ajánlás – éttermi értékelés előrejelzése | Hozzon létre egy étterem ajánló motort az étterem/felhasználó funkciók és értékelések közül.|

## <a name="recommender"></a>Ajánló

Ismerkedjen meg ezekkel a beépített Ajánlói mintákkal. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| Képbesorolás a DenseNet használatával | A Computer látási modulok használatával PyTorch-DenseNet alapuló képbesorolási modellt hozhat létre.| 

## <a name="utility"></a>Segédprogram

További információ a Machine learning segédprogramokat és szolgáltatásokat bemutató mintákról. A mintákról a minták megnyitásával és a modulok megjegyzésének megtekintésével többet is megtudhat.

| Mintacím | Leírás | 
| --- | --- |
| Bináris besorolás a Vowpal Wabbit Model használatával – felnőtt jövedelmek előrejelzése | A Vowpal Wabbit egy gépi tanulási rendszer, amely leküldi a gépi tanulás határát olyan technikákkal, mint az online, a kivonatolás, a allreduce, a csökkenés, a learning2search, az aktív és az interaktív tanulás. Ez a minta bemutatja, hogyan használható a Vowpal Wabbit-modell bináris besorolási modell létrehozásához. 
| [Egyéni R-szkript használata – repülési késleltetés előrejelzése](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) | A testreszabott R-szkripttel előre jelezheti, hogy egy ütemezett utasszállító járat 15 percnél hosszabb ideig késleltetve lesz-e.
| Több ellenőrzés a bináris besorolásnál – felnőtt bevétel előrejelzése | Használjon kereszt-érvényesítést a felnőtt jövedelmek bináris besorolásának kiépítéséhez.
| A permutáció funkció fontossága | Az adatkészlethez tartozó számítási fontossági pontszámok kiszámításához használja a permutáció funkció fontosságát. 
| Paraméterek hangolása bináris besoroláshoz – felnőtt bevétel előrejelzése | A Hiperparaméterek beállítása hangolása segítségével optimális hiperparaméterek beállítása találhat a bináris osztályozók létrehozásához. |

## <a name="datasets"></a>Adathalmazok

Amikor új folyamatot hoz létre Azure Machine Learning Designerben, a rendszer alapértelmezés szerint számos minta adatkészletet tartalmaz. Ezeket a minta adatkészleteket a tervező kezdőlapján található mintavételi folyamatok használják. 

A minta adatkészletek az **adatkészletek** - **mintáinak** kategóriájában érhetők el. Ez a modul paletta bal oldalán található a tervezőben. Ezeket az adatkészleteket a saját folyamatában is használhatja, ha a vászonra húzza azt.

| Adatkészlet &nbsp; neve&nbsp;&nbsp;&nbsp;&nbsp;| Adatkészlet leírása |
|-------------|:--------------------|
| Felnőtt népszámlálás jövedelme bináris besorolási adatkészlet | Az 1994-es népszámlálási adatbázis egy részhalmaza, amely a 16. korban dolgozó felnőtteket használja > 100 korrigált bevételi indexszel.<br/>**Használat**: a demográfiai adatokat használó személyek besorolása annak előrejelzéséhez, hogy egy személy évente több mint 50 000-et keres.<br/> **Kapcsolódó kutatás**: Kohavi, R., Becker, B., (1996). [UCI Machine learning adattár](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science|
|Az autók árát (nyers)|Információk a márka és a modell alapján, beleértve a díjszabást, az olyan szolgáltatásokat, mint például a hengerek száma és az MPG, valamint a biztosítási kockázati pontszám.<br/> A kockázati pontszám kezdetben az automatikus árral van társítva. Ezt követően a rendszer a tényleges kockázatokat egy, a biztosítási matematikusok ismert folyamat során helyesbíti. A + 3 érték azt jelzi, hogy az automatikus kockázatos, a-3 érték pedig valószínűleg biztonságos.<br/>**Használat**: </b> a funkciók kockázati pontszámának előrejelzése regresszió vagy többváltozós besorolás használatával.<br/>**Kapcsolódó kutatás**: </b> Schlimmer, J.C. (1987). [UCI Machine learning adattár](https://archive.ics.uci.edu/ml). Irvine, CA: University of California, School of Information and Computer Science. |
| Megosztott CRM szolgáltatóváltást-Címkék |A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás ([orange_small_train_appetency. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels)) címkéi.|
|Megosztott CRM-adatforgalom-Címkék|A KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívás ([orange_small_train_churn. labels](http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels)) címkéi.|
|Megosztott CRM-adatkészlet | Ezek az adatok a KDD Cup 2009 ügyfélkapcsolat-előrejelzési kihívásról ([orange_small_train.data.zip](http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip)) származnak. <br/>Az adatkészlet 50 000 ügyfelet tartalmaz a francia távközlési cég Orange-től. Minden ügyfél 230-es anonim funkciókkal rendelkezik, amelyek 190 a numerikus és a 40-as számú adat kategorikus. A funkciók nagyon ritkák. |
|Megosztott CRM-Címkék|A KDD Cup 2009 ügyfélkapcsolat-előrejelzési Challenge ([orange_large_train_upselling. labels)](http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels) címkéi|
|Repülési késések adatvédelme|Az Amerikai Egyesült államokbeli Közlekedési Minisztérium TranStats adatgyűjtési szolgáltatásában[(az időpontnál](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time)) üzemelő személyszállító repülési adatok.<br/>Az adatkészlet April-October 2013 időszakra vonatkozik. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva: <br/>– Az adatkészlet úgy lett szűrve, hogy csak a 70-es legforgalmasabb repülőtérre vonatkozzon az USA kontinentális területén <br/>– A megszakított járatok több mint 15 perc késéssel lettek megjelölve <br/>– A kiszűrt járatok kiszűrése megtörtént <br/>-A következő oszlopok lettek kiválasztva: év, hónap, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, megszakítva|
|Német hitelkártya UCI-adatkészlet|Az UCI statlog (német bankkártya) adatkészlete ([statlog + német + kredit + adat](https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data))) a német. adatfájl használatával.<br/>Az adatkészlet az attribútumok egy halmaza által leírtak szerint osztályozza a személyeket, alacsony vagy magas hitelezési kockázatként. Mindegyik példa egy személyt jelöl. A rendszer 20 funkciót használ: számszerű és kategorikus, valamint bináris címkét (a kredit kockázati értékét). A magas kockázatú kreditek címkéje = 2, az alacsony kreditek esetében pedig a Label = 1. Az alacsony kockázatú, például a magas kockázatú példa nem megfelelő besorolásának díja 1, míg a magas kockázatú példa alacsony értékkel való megsértésének díja 5.|
|IMDB-mozgóképek címei|Az adatkészlet a Twitter tweetekben értékelt filmek információit tartalmazza: IMDB-film azonosítója, film neve, műfaj és termelési év. 17K-filmek vannak az adatkészletben. Az adatkészlet az "S" dokumentumban lett bevezetve. Dooms, T. de Pessemier és L. Martens. MovieTweetings: a Twitteren gyűjtött film minősítési adatkészlet. Workshop a közösségi-on és az emberi számításban az ajánló rendszerekhez, CrowdRec a RecSys 2013-kor. "|
|Film minősítése|Az adatkészlet a Movie tweetek adatkészlet kiterjesztett verziója. Az adatkészlet 170K-minősítésekkel rendelkezik, amelyek a Twitteren jól strukturált tweetekkel lettek kinyerve. Minden példány egy tweetet jelöl, és egy rekord: felhasználói azonosító, IMDB-film azonosítója, minősítés, időbélyeg, kedvencek száma ehhez a tweethez és a tweetek száma. Az adatkészlet elérhetővé tétele a. Said, S. dooms, B. Loni és D. tikk for ajánló Systems Challenge 2014.|
|Időjárási adatkészlet|A NOAA-ből származó, óránkénti szárazföld-alapú időjárási megfigyelések ([201304 és 201310 közötti egyesített adatok](https://az754797.vo.msecnd.net/data/WeatherDataset.csv)).<br/>Az időjárási adatok a repülőtéri meteorológiai állomások megfigyeléseit fedik le, az 2013-as April-October időszakra vonatkozóan. A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:    <br/> – A meteorológiai állomások azonosítói a megfelelő repülőtéri azonosítóra vannak leképezve    <br/> – A 70-es legforgalmasabb repülőtérhez nem társított meteorológiai állomások kiszűrése megtörtént    <br/> -A Date oszlop külön évre, hónapra és napi oszlopokra van bontva    <br/> – A következő oszlopok lettek kiválasztva: AirportID, év, hónap, nap, idő, időzóna, SkyCondition, láthatóság, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, szélsebesség, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, rekordtípus, HourlyPrecip, magasságmérő|
|Wikipedia SP 500 adatkészlet|Az adatok a wikipedia-ből származnak (az https://www.wikipedia.org/) egyes S&P 500-es vállalatok cikkei alapján, amelyek XML-adatként vannak tárolva.    <br/>A tervezőbe való feltöltés előtt az adatkészlet a következőképpen lett feldolgozva:    <br/> -Szöveges tartalom kinyerése minden egyes vállalatnál    <br/> – Wiki formázásának eltávolítása    <br/> – Nem alfanumerikus karakterek eltávolítása    <br/> – Az összes szöveg konvertálása kisbetűsre    <br/> – Ismert vállalati kategóriák lettek hozzáadva    <br/>Vegye figyelembe, hogy egyes vállalatok esetében nem található cikk, így a rekordok száma kevesebb, mint 500.|


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg a prediktív elemzés és a gépi tanulás alapjait az [oktatóanyaggal: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md)


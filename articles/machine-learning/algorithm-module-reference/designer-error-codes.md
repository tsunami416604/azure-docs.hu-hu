---
title: Modulhibák elhárítása
titleSuffix: Azure Machine Learning
description: Modulkivételek elhárítása az Azure Machine Learning tervezőjében hibakódok használatával
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 12/03/2019
ms.openlocfilehash: cda499b81a61a5b78ca86a96372640e368f90357
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364195"
---
# <a name="exceptions-and-error-codes-for-the-designer-preview"></a>Kivételek és hibakódok a tervezőhöz (előzetes verzió)

Ez a cikk ismerteti a hibaüzenetek és a kivételkódok az Azure Machine Learning designer (előzetes verzió), hogy segítsen a gépi tanulási folyamatok hibaelhárítása.

A hibaüzenet teljes szövegét kétféleképpen kaphatja meg a tervezőben:  

- Kattintson a jobb oldali ablaktáblában található **Kimeneti napló megtekintése**hivatkozásra, és görgessen az aljára. A részletes hibaüzenet az ablak utolsó két sorában jelenik meg.  
  
- Jelölje ki a hibát tartalmazó modult, és kattintson a piros X gombra. Csak a megfelelő hibaszöveg jelenik meg.

## <a name="error-0001"></a>0001-es hiba  
 Kivétel, ha egy vagy több megadott oszlop az adatkészlet nem található.  

 Ez a hibaüzenet akkor jelenik meg, ha egy modulhoz oszlopkijelölés készül, de a kijelölt oszlop(ok) nem léteznek a bemeneti adatkészletben. Ez a hiba akkor fordulhat elő, ha manuálisan írt be egy oszlopnevet, vagy ha az oszlopkijelölő olyan javasolt oszlopot adott meg, amely nem létezett az adatkészletben a folyamat futtatásakor.  

**Felbontás:** Vizsgálja meg újra a kivételt tartalmazó modult, és ellenőrizze, hogy az oszlopnév vagy -nevek helyesek-e, és hogy létezik-e az összes hivatkozott oszlop.  

|Kivételüzenetek|
|------------------------|
|Egy vagy több megadott oszlop nem található.|
|A(z) "{column_id}" nevű vagy indexes oszlop nem található.|
|A(z) "{column_id}" nevű vagy indexes oszlop nem létezik a következőben: "{arg_name_missing_column}".|
|A(z) "{column_id}" nevű vagy indexes oszlop nem létezik a következőben: "{arg_name_missing_column}", de létezik a következőben: "{arg_name_has_column}".|
|A(z) "{column_names}" nevű vagy indexes oszlopok nem találhatók.|
|A(z) "{column_names}" nevű vagy indexes oszlopok nem léteznek a következőben: "{arg_name_missing_column}".|
|A(z) "{column_names}" nevű vagy indexes oszlopok nem léteznek a következőben: "{arg_name_missing_column}", de létezik a következőben: "{arg_name_has_column}".|


## <a name="error-0002"></a>0002-es hiba  
 Kivétel akkor fordul elő, ha egy vagy több paraméter nem elemezhető vagy nem konvertálható a megadott típusból a célmetódus típusához szükségesre.  

 Ez a hiba akkor fordul elő az Azure Machine Learningben, ha egy paramétert ad meg bemenetként, és az értéktípusa eltér a várt típustól, és az implicit konverzió nem hajtható végre.  

**Felbontás:** Ellenőrizze a modulkövetelményeket, és határozza meg, hogy melyik értéktípusra van szükség (karakterlánc, egész szám, dupla stb.)  

|Kivételüzenetek|
|------------------------|
|Nem sikerült elemezni a paramétert.|
|Nem sikerült elemezni a(z) "{arg_name_or_column}" paramétert.|
|Nem sikerült a(z) "{arg_name_or_column}" paramétert "{to_type}" paraméterre konvertálni.|
|Nem sikerült a(z) "{arg_name_or_column}" paramétert "{from_type}" paraméterből "{to_type}"-ra konvertálni.|
|Nem sikerült a(z) "{arg_name_or_column}" paraméter "{arg_value}" paraméterértékét "{from_type}" paraméterből "{to_type}"-ra konvertálni.|
|Nem sikerült a(z) "{arg_value}" értéket a(z) "{arg_name_or_column from_type}" oszlopban "{from_type}" értékről "{to_type}" értékre konvertálni a megadott "{fmt}" formátum használatával.|


## <a name="error-0003"></a>0003-as hiba  
 Kivétel akkor fordul elő, ha egy vagy több bemenet null értékű vagy üres.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha egy modul bármely bemenete vagy paramétere null értékű vagy üres.  Ez a hiba például akkor fordulhat elő, ha nem adott meg egyetlen paraméter értékét sem. Ez akkor is előfordulhat, ha olyan adatkészletet választott, amelynek hiányzó értékei vannak, vagy egy üres adatkészletet.  

**Felbontás:**

+ Nyissa meg a kivételt előállító modult, és ellenőrizze, hogy az összes bemenet meg van-e adva. Győződjön meg arról, hogy minden szükséges bemenet meg van adva. 
+ Győződjön meg arról, hogy az Azure storage-ból betöltött adatok elérhetők, és hogy a fiók neve vagy kulcsa nem változott.  
+ Ellenőrizze, hogy a bemeneti adatok nem tartalmaznak-e hiányzó értékeket vagy null értékeket.
+ Ha egy lekérdezést adatforráson használ, ellenőrizze, hogy az adatok a várt formátumban kerülnek-e visszaadásra. 
+ Ellenőrizze, hogy vannak-e elírások vagy egyéb változások az adatok specifikációjában.
  
|Kivételüzenetek|
|------------------------|
|Egy vagy több bemenet null értékű vagy üres.|
|A(z) "{name}" bevitele null értékű vagy üres.|


## <a name="error-0004"></a>0004-es hiba  
 Kivétel akkor fordul elő, ha a paraméter kisebb vagy egyenlő, mint egy adott érték.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha az üzenetben szereplő paraméter az adatok feldolgozásához szükséges határérték alatt van.  

**Felbontás:** Újra a modul dobott a kivétel, és módosítsa a paraméter, hogy nagyobb, mint a megadott értéket.  

|Kivételüzenetek|
|------------------------|
|A paraméternek nagyobbnak kell lennie a határértéknél.|
|A(z) "{arg_name}" paraméter értékének {lower_boundary} értéknél nagyobbnak kell lennie.|
|A(z) "{arg_name}" paraméter értéke "{actual_value}", amelynek {lower_boundary} értéknél nagyobbnak kell lennie.|


## <a name="error-0005"></a>0005-ös hiba  
 Kivétel akkor fordul elő, ha a paraméter kisebb, mint egy adott érték.  

 Ez a hiba az Azure Machine Learningben jelenik meg, ha az üzenetben szereplő paraméter az adatok feldolgozásához szükséges határérték alatt vagy azzal egyenlő.  

**Felbontás:** Újra a modul dobott a kivétel, és módosítsa a paramétert, hogy nagyobb vagy egyenlő a megadott értéket.  

|Kivételüzenetek|
|------------------------|
|A paraméternek határértéknél nagyobbnak vagy azzal egyenlőnek kell lennie.|
|A(z) "{arg_name}" paraméter értékének {lower_boundary} értéknél nagyobbnak vagy azzal egyenlőnek kell lennie.|
|A(z) "{arg_name}" paraméter értéke "{value}", amelynek nagyobbnak vagy egyenlőnek kell lennie a következővel: {lower_boundary}.|


## <a name="error-0006"></a>0006-os hiba  
 Kivétel akkor fordul elő, ha a paraméter nagyobb vagy egyenlő a megadott értékkel.  

 Ez a hiba az Azure Machine Learningben jelenik meg, ha az üzenetben szereplő paraméter nagyobb vagy egyenlő, mint a modul az adatok feldolgozásához szükséges határérték.  

**Felbontás:** Újra a modul dobott a kivétel, és módosítsa a paraméter, hogy kisebb, mint a megadott értéket.  

|Kivételüzenetek|
|------------------------|
|A paraméterek nem egyeznek. Az egyik paraméternek kisebbnek kell lennie, mint a másiknak.|
|A(z) "{arg_name}" paraméter értékének kisebbnek kell lennie a(z) "{upper_boundary_parameter_name}" paraméterértéknél.|
|A(z) "{arg_name}" paraméter értéke "{value}", amelynek {upper_boundary_parameter_name} értéknél kisebbnek kell lennie.|


## <a name="error-0007"></a>0007-es hiba  
 Kivétel akkor fordul elő, ha a paraméter nagyobb, mint egy adott érték.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha a modul tulajdonságaiban megadott egy nagyobb értéket, mint az engedélyezett. Megadhat például olyan adatokat, amelyek kívül esnek a támogatott dátumok tartományán, vagy azt is jelezheti, hogy öt oszlop ot használ, ha csak három oszlop áll rendelkezésre. 

 Ez a hiba akkor is megjelenhet, ha két olyan adathalmazt ad meg, amelyeknek valamilyen módon egyeztetniük kell. Ha például oszlopokat nevez át, és index szerint adja meg az oszlopokat, a megadott nevek számának meg kell egyeznie az oszlopindexek számával. Egy másik példa lehet egy matematikai művelet, amely két oszlopot használ, ahol az oszlopoknak azonos számú sorral kell rendelkezniük. 

**Felbontás:**

 + Nyissa meg a kérdéses modult, és tekintse át a numerikus tulajdonságbeállításokat.
 + Győződjön meg arról, hogy a paraméterértékek az adott tulajdonság támogatott értéktartományán belül vannak.
 + Ha a modul több bemenetet vesz igénybe, győződjön meg arról, hogy a bemenetek azonos méretűek.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Ellenőrizze, hogy az adatkészlet vagy az adatforrás megváltozott-e. Előfordulhat, hogy az adatok egy korábbi verziójával működött egy érték, miután az oszlopok száma, az oszlop adattípusai vagy az adatok mérete megváltozott.  

|Kivételüzenetek|
|------------------------|
|A paraméterek nem egyeznek. Az egyik paraméternek kisebbnek vagy egyenlőnek kell lennie egy másikparaméterrel.|
|A(z) "{arg_name}" paraméter értékének kisebbnek vagy egyenlőnek kell lennie a(z) "{upper_boundary_parameter_name}" paraméter értékén.|
|A(z) "{arg_name}" paraméter értéke "{actual_value}", amelynek kisebbnek vagy egyenlőnek kell lennie a következővel: {upper_boundary}.|
|A(z) "{arg_name}" {actual_value} paraméter értékének kisebbnek vagy egyenlőnek kell lennie a(z) {upper_boundary_parameter_name}} paraméter ({upper_boundary_parameter_name}) {upper_boundary} paraméterértéknél.|


## <a name="error-0008"></a>0008-as hiba  
 Kivétel akkor fordul elő, ha a paraméter nincs a tartományban.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha az üzenetben szereplő paraméter kívül esik az adatok feldolgozásához szükséges határokon.  

 Ez a hiba például akkor jelenik meg, ha [a Sorok hozzáadása](add-rows.md) segítségével két, eltérő számú oszlopot tartalmazó adatkészletet próbál kombinálni.  

**Felbontás:** Újra a modul dobott a kivétel, és módosítsa a paramétert, hogy a megadott tartományon belül.  

|Kivételüzenetek|
|------------------------|
|A paraméter értéke nincs a megadott tartományban.|
|A(z) "{arg_name}" paraméter értéke nincs a tartományban.|
|A(z) "{arg_name}" paraméter értékének a(z) [{lower_boundary}, {upper_boundary}] tartományban kell lennie.|
|A(z) "{arg_name}" paraméter értéke nincs a tartományban. {ok}|


## <a name="error-0009"></a>0009-es hiba  
 Kivétel akkor fordul elő, ha az Azure storage-fiók vagy a tároló neve helytelenül van megadva.  

Ez a hiba akkor fordul elő az Azure Machine Learning tervezőjében, ha paramétereket ad meg egy Azure-tárfiókhoz, de a név vagy a jelszó nem oldható fel. A jelszavakkal vagy fióknevekkel kapcsolatos hibák több okból is előfordulhatnak:

 + A fiók típusa nem megfelelő. Egyes új fióktípusok nem támogatottak a Machine Learning-tervezővel való használatra. A részleteket az [Adatok importálása](import-data.md) mezőben találja.
 + Helytelen fióknevet adott meg
 + A fiók már nem létezik
 + A tárfiók jelszava hibás vagy megváltozott
 + Nem adta meg a tároló nevét, vagy a tároló nem létezik
 + Nem adta meg teljesen a fájl elérési útját (a blob elérési útját)
   

**Felbontás:**

Ilyen problémák gyakran előfordulnak, amikor megpróbálja manuálisan megadni a fiók nevét, jelszavát vagy a tároló elérési útját. Javasoljuk, hogy az új varázslót használja az [Adatok importálása](import-data.md) modulhoz, amely segít a nevek keresésével és ellenőrzésével.

Azt is ellenőrizze, hogy a fiók, tároló vagy blob törölve lett-e. Használjon egy másik Azure storage-segédprogramot annak ellenőrzéséhez, hogy a fiók nevét és jelszavát helyesen adta meg, és hogy a tároló létezik-e. 

Néhány újabb fióktípusok nem támogatja az Azure Machine Learning. Például az új "meleg" vagy "hideg" tárolási típusok nem használhatók gépi tanuláshoz. Mind a klasszikus tárfiókok, mind az "Általános célú" néven létrehozott tárfiókok jól működnek.

Ha egy blob teljes elérési útja meg van adva, ellenőrizze, hogy az elérési út **tároló/blobnév**ként van-e megadva, és hogy a tároló és a blob is létezik-e a fiókban.  

 Az elérési út nem tartalmazhat sorközt. Például **a /container/blob** helytelen, és meg kell adni **a tároló/blob.**  


|Kivételüzenetek|
|------------------------|
|Az Azure storage-fiók neve vagy a tároló neve helytelen.|
|Az Azure storage-fiók neve "{account_name}" vagy a tárolóneve "{container_name}" helytelen; a formátumtároló/blob tárolónevének várt.|


## <a name="error-0010"></a>0010-es hiba  
 Kivétel akkor fordul elő, ha a bemeneti adatkészletek oszlopnevei megegyeznek, de nem.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha az üzenetoszlop-indexe különböző oszlopnevekkel rendelkezik a két bemeneti adatkészletben.  

**Felbontás:** A [Metaadatok szerkesztése](edit-metadata.md) vagy az eredeti adatkészlet módosítása segítségével a megadott oszlopindexhez azonos oszlopnév vel rendelkezik.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészletekben a megfelelő indexet tartalmazó oszlopok neve eltérő.|
|Az oszlopnevek nem azonosak a bemeneti adatkészletek {col_index} (nulla alapú) oszlopában ({dataset1} és {dataset2} ).|


## <a name="error-0011"></a>0011-es hiba  
 Kivétel, ha az átadott oszlopkészlet argumentum nem vonatkozik az adatkészlet egyik oszlopára sem.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha a megadott oszlopkijelölés nem egyezik meg az adott adatkészlet egyik oszlopával sem.  

 Ezt a hibaüzenetet akkor is megkaphatja, ha még nem választott ki oszlopot, és legalább egy oszlop szükséges a modul működéséhez.  

**Felbontás:** Módosítsa a modul oszlopkiválasztását úgy, hogy az az adatkészlet oszlopaira is vonatkozzon.  

 Ha a modul megköveteli, hogy jelöljön ki egy adott oszlopot, például egy címkeoszlopot, ellenőrizze, hogy a jobb oldali oszlop ki van-e jelölve.  

 Ha nem megfelelő oszlopok vannak kijelölve, távolítsa el őket, és futtassa újra a folyamatot.  

|Kivételüzenetek|
|------------------------|
|A megadott oszlopkészlet egyik adatkészletoszlopra sem vonatkozik.|
|A megadott "{column_set}" oszlopkészlet nem vonatkozik az adatkészlet egyik oszlopára sem.|


## <a name="error-0012"></a>0012-es hiba  
 Kivétel akkor fordul elő, ha az osztály példánya nem hozható létre átadott argumentumokkal.  

**Felbontás:** Ez a hiba nem használható a felhasználó által, és egy későbbi kiadásban elavult lesz.  

|Kivételüzenetek|
|------------------------|
|Képzetlen modell, először a modell betanításával.|
|Képzetlen modell ({arg_name}), betanított modell használata.|


## <a name="error-0013"></a>0013-as hiba  
 Kivétel akkor fordul elő, ha a modulnak átadott tanuló érvénytelen típus.  

 Ez a hiba akkor fordul elő, ha egy betanított modell nem kompatibilis a csatlakoztatott pontozási modullal. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the pipeline is run.  -->

**Felbontás:**

Határozza meg a tanuló típusát, amelyet a képzési modul állít elő, és határozza meg a tanuló számára megfelelő pontozási modult. 

Ha a modell betanítása a speciális betanítási modulok bármelyikével lett betanítva, csatlakoztassa a betanított modellt csak a megfelelő speciális pontozási modulhoz. 


|Modell típusa|Képzési modul| Pontozási modul|
|----|----|----|
|bármilyen osztályozó|[Vonat modell](train-model.md) |[Pontszám modell](score-model.md)|
|bármely regressziós modell|[Vonat modell](train-model.md) |[Pontszám modell](score-model.md)|

<!--| clustering models| [Train Clustering Model](train-clustering-model.md) or [Sweep Clustering](sweep-clustering.md)| [Assign Data to Clusters](assign-data-to-clusters.md)|
| anomaly detection - One-Class SVM | [Train Anomaly Detection Model](train-anomaly-detection-model.md) |[Score Model](score-model.md)|
| anomaly detection - PCA |[Train Model](train-model.md) |[Score Model](score-model.md) </br> Some additional steps are required to evaluate the model. |
| anomaly detection - time series|  [Time Series Anomaly Detection](time-series-anomaly-detection.md) |Model trains from data and generates scores. The module does not create a trained learner and no additional scoring is required. |
| recommendation model| [Train Matchbox Recommender](train-matchbox-recommender.md) | [Score Matchbox Recommender](score-matchbox-recommender.md) |
| image classification | [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md) | [Score Model](score-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) | [Score Vowpal Wabbit Version 7-4 Model](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 7-10 Model](train-vowpal-wabbit-version-7-10-model.md) | [Score Vowpal Wabbit Version 7-10 Model](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit models| [Train Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) | [Score Vowpal Wabbit Version 8 Model](score-vowpal-wabbit-version-8-model.md) |-->

|Kivételüzenetek|
|------------------------|
|Az érvénytelen típusú tanuló átkerül.|
|A(z) "{arg_name}" tanuló típusa érvénytelen.|
|A(z) "{arg_name}" tanuló típusa érvénytelen: "{learner_type}".|


## <a name="error-0014"></a>0014-es hiba  
 Kivétel, ha az oszlop egyedi értékeinek száma nagyobb a megengedettnél.  

 Ez a hiba akkor fordul elő, ha egy oszlop túl sok egyedi értéket tartalmaz.  Előfordulhat például, hogy ez a hiba akkor jelenik meg, ha azt adja meg, hogy egy oszlopot kategorikus adatként kezeljen, de túl sok egyedi érték van az oszlopban ahhoz, hogy a feldolgozás befejeződjen. Ez a hiba akkor is megjelenhet, ha eltérés van az egyedi értékek száma között két bemeneten.   

**Felbontás:**

Nyissa meg a hibát generáló modult, és azonosítsa a bemenetként használt oszlopokat. Egyes modulok esetében a jobb gombbal az adatkészlet bemenetére kattinthat, és a **Visualize parancsra** kattintva statisztikákat kaphat az egyes oszlopokról, beleértve az egyedi értékek számát és eloszlásukat.

A csoportosításhoz vagy kategorizáláshoz használni kívánt oszlopok esetében tegyen lépéseket az oszlopokban lévő egyedi értékek számának csökkentésére. Az oszlop adattípusától függően különböző módokon csökkentheti a költségeket. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nem talál olyan megoldást, amely megfelel a forgatókönyvnek? Visszajelzést adhat erről a témakörről, amely tartalmazza a hibát okozó modul nevét, valamint az oszlop adattípusát és számosságát. Az információkat arra használjuk, hogy célzottabb hibaelhárítási lépéseket biztosítsunk a gyakori forgatókönyvekhez.   

|Kivételüzenetek|
|------------------------|
|Az oszlop egyedi értékeinek mennyisége nagyobb a megengedettnél.|
|Az oszlopban lévő egyedi értékek száma: A(z) "{column_name}" nagyobb a megengedettnél.|
|Az oszlopban lévő egyedi értékek száma: A(z) "{column_name}" meghaladja a {limitation} rekordok számát.|


## <a name="error-0015"></a>0015-ös hiba  
 Kivétel, ha az adatbázis-kapcsolat meghibásodott.  

 Ez a hibaüzenet akkor jelenik meg, ha helytelen SQL-fióknevet, jelszót, adatbázis-kiszolgálót vagy adatbázisnevet ad meg, vagy ha az adatbázissal való kapcsolat az adatbázissal vagy a kiszolgálóval kapcsolatos problémák miatt nem hozható létre.  

**Felbontás:** Ellenőrizze, hogy a fióknév, a jelszó, az adatbázis-kiszolgáló és az adatbázis helyesen lett-e megadva, és hogy a megadott fiók rendelkezik-e a megfelelő szintű engedélyekkel. Ellenőrizze, hogy az adatbázis jelenleg elérhető-e.  

|Kivételüzenetek|
|------------------------|
|Hiba az adatbázis-kapcsolat létrehozásakor.|
|Hiba az adatbázis-kapcsolat létrehozásakor: {connection_str}.|


## <a name="error-0016"></a>0016-os hiba  
 Kivétel tanusít, ha a modulnak átadott bemeneti adatkészletek kompatibilis oszloptípusokkal rendelkeznek, de nem.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha a két vagy több adatkészletben átadott oszlopok típusai nem kompatibilisek egymással.  

**Felbontás:** [Metaadatok szerkesztése](edit-metadata.md) vagy az eredeti bemeneti adatkészlet módosítása<!--, or use [Convert to Dataset](convert-to-dataset.md)--> annak érdekében, hogy az oszlopok típusai kompatibilisek legyenek.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészletekben a megfelelő indexet tartalmazó oszlopok nem kompatibilis típusokkal rendelkeznek.|
|A(z) "{first_col_names}" oszlopok nem kompatibilisek a betanítási és a tesztadatokkal.|
|A(z) "{first_col_names}" és a "{second_col_names}" oszlopok nem kompatibilisek.|
|Az oszlopelem-típusok nem kompatibilisek a bemeneti adatkészletek ({first_dataset_names} és {second_dataset_names} "first_col_names nulla alapú" oszlopával.|


## <a name="error-0017"></a>0017-es hiba  
 Kivétel akkor fordul elő, ha a kijelölt oszlop olyan adattípust használ, amelyet az aktuális modul nem támogat.  

 Előfordulhat például, hogy ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha az oszlopkiválasztása tartalmaz egy olyan oszlopot, amelynek adattípusát a modul nem tudja feldolgozni, például egy matematikai művelet karakterláncoszlopát, vagy egy pontszámoszlopot, ahol egy kategorikus szolgáltatásoszlop Szükséges.  

**Felbontás:**
 1. Azonosítsa a problémát okozó oszlopot.
 2. Tekintse át a modul követelményeit.
 3. Módosítsa az oszlopot úgy, hogy megfeleljen a követelményeknek. Előfordulhat, hogy az oszloptól és a megkísérelt konverziótól függően az alábbi modulok közül többet kell használnia a módosításokhoz:
    + A [Metaadatok szerkesztése funkcióval módosíthatja](edit-metadata.md) az oszlopok adattípusát, vagy módosíthatja az oszlophasználatot szolgáltatásról numerikusra, kategorikusról nem kategorikusra stb.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Végső megoldásként előfordulhat, hogy módosítania kell az eredeti bemeneti adatkészletet.

> [!TIP]
> Nem talál olyan megoldást, amely megfelel a forgatókönyvnek? Visszajelzést adhat erről a témakörről, amely tartalmazza a hibát okozó modul nevét, valamint az oszlop adattípusát és számosságát. Az információkat arra használjuk, hogy célzottabb hibaelhárítási lépéseket biztosítsunk a gyakori forgatókönyvekhez. 

|Kivételüzenetek|
|------------------------|
|Az aktuális típusú oszlop nem dolgozható fel. A modul nem támogatja a típust.|
|A(z) {col_type} típusú oszlop nem dolgozható fel. A modul nem támogatja a típust.|
|A(z) {col_type} típusú "{col_name}" oszlop nem használható fel. A modul nem támogatja a típust.|
|A(z) {col_type} típusú "{col_name}" oszlop nem használható fel. A modul nem támogatja a típust. Paraméter neve: {arg_name}.|


## <a name="error-0018"></a>0018-as hiba  
 Kivétel, ha a bemeneti adatkészlet érvénytelen.  

**Felbontás:** Ez a hiba az Azure Machine Learning ben számos környezetben jelenhet meg, így nincs egyetlen megoldás. A hiba általában azt jelzi, hogy a modulbemenetként megadott adatok nem megfelelő számú oszlopot tartalmaznak, vagy hogy az adattípus nem felel meg a modul követelményeinek. Példa:  

-   A modulhoz szükség van egy címkeoszlopra, de nincs címkeként megjelölt oszlop, vagy még nem jelölt ki címkeoszlopot.  
  
-   A modul megköveteli, hogy az adatok kategorikusak legyenek, de az adatok numerikusak.  

<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->

-   Az adatok formátuma nem megfelelő.  
  
-   Az importált adatok érvénytelen karaktereket, hibás értékeket vagy tartományon kívüli értékeket tartalmaznak.  
-   Az oszlop üres, vagy túl sok hiányzó értéket tartalmaz.  

 A követelmények meghatározásához, és hogyan az adatok is, tekintse át a súgótémakört a modul, amely felfogja az adatkészletet bemenetként.  

 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  

|Kivételüzenetek|
|------------------------|
|Az adatkészlet érvénytelen.|
|A(z) {dataset1} érvénytelen adatokat tartalmaz.|
|A(z) {dataset1} és a(z) {dataset2} oszloponként konzisztensnek kell lennie.|
|A(z) {dataset1} érvénytelen adatokat tartalmaz: {reason}.|
|A(z) {dataset1} {invalid_data_category} elemet tartalmaz. {troubleshoot_hint}|
|A(z) {dataset1} érvénytelen, {reason}. {troubleshoot_hint}|


## <a name="error-0019"></a>0019-es hiba  
 Kivétel, ha az oszlop várhatóan rendezett értékeket tartalmaz, de nem.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha a megadott oszlopértékek nem sorrendben vannak.  

**Felbontás:** Rendezze az oszlopértékeket a bemeneti adatkészlet manuális módosításával és a modul újrafuttatásával.  

|Kivételüzenetek|
|------------------------|
|Az oszlopban lévő értékek nincsenek rendezve.|
|A(z) "{col_index}" oszlop értékei nincsenek rendezve.|
|A(z) "{dataset}" adatkészlet "{col_index}" oszlopában lévő értékek nincsenek rendezve.|


## <a name="error-0020"></a>0020-as hiba  
 Kivétel, ha a modulnak átadott adatkészletek egyes oszlopainak száma túl kicsi.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha nincs elegendő oszlop kiválasztva egy modulhoz.  

**Felbontás:** Keresse fel újra a modult, és győződjön meg arról, hogy az oszlopválasztó megfelelő számú oszlopot jelölt ki.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészlet oszlopainak száma kisebb, mint a megengedett minimum.|
|A(z) "{arg_name}" bemeneti adatkészlet oszlopainak száma kisebb, mint a megengedett minimum.|
|A bemeneti adatkészlet oszlopainak száma kisebb, mint a megengedett {required_columns_count} oszlop(ok) száma.|
|A(z) "{arg_name}" bemeneti adatkészlet oszlopainak száma kisebb, mint a megengedett {required_columns_count} oszlop(ok) száma.|


## <a name="error-0021"></a>0021-es hiba  
 Kivétel, ha a sorok száma egyes adatkészletek átadott a modul túl kicsi.  

 Ez a hiba az Azure Machine Learningben látható, ha nincs elég sor az adatkészletben a megadott művelet végrehajtásához. Előfordulhat például, hogy ez a hiba akkor jelenik meg, ha a bemeneti adatkészlet üres, vagy ha olyan műveletet próbál végrehajtani, amelyhez legalább néhány sor érvényessége szükséges. Az ilyen műveletek magukban foglalhatják (de nem kizárólagosan) a statisztikai módszereken alapuló csoportosítást vagy osztályozást, bizonyos típusú binningeket és a számolással történő tanulást.  

**Felbontás:**

 + Nyissa meg a hibát visszaadó modult, és ellenőrizze a bemeneti adatkészlet et és a modul tulajdonságait. 
 + Ellenőrizze, hogy a bemeneti adatkészlet nem üres-e, és hogy elegendő adatsor van-e a modulsúgóban leírt követelmények teljesítéséhez.  
 + Ha az adatok külső forrásból töltődnek be, győződjön meg arról, hogy az adatforrás elérhető, és nincs olyan hiba vagy változás az adatdefinícióban, amely az importálási folyamat kevesebb sort eredményezne.
 + Ha olyan műveletet hajt végre a modul előtt lévő adatokon, amely hatással lehet az adatok típusára vagy az értékek számára, például a tisztítási, felosztási vagy illesztési műveletekre, ellenőrizze a visszaadott sorok számát.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészlet sorainak száma kisebb, mint a megengedett minimum.|
|A bemeneti adatkészlet sorainak száma kisebb, mint a megengedett minimális {required_rows_count} sor(ok).|
|A bemeneti adatkészlet sorainak száma kisebb, mint a megengedett minimális {required_rows_count} sor(ok). {ok}|
|A(z) "{arg_name}" bemeneti adatkészlet sorainak száma kisebb, mint a megengedett minimális {required_rows_count} sor(ok).|
|A(z) "{arg_name}" bemeneti adatkészlet sorainak száma {actual_rows_count}, ami kevesebb, mint a megengedett {required_rows_count} sor(ok) száma.|
|A(z) "{arg_name}" bemeneti adatkészlet "{row_type}" sorainak száma {actual_rows_count}, ami kevesebb, mint a megengedett {required_rows_count} sor(ok) száma.|


## <a name="error-0022"></a>0022-es hiba  
 Kivétel, ha a bemeneti adatkészlet kijelölt oszlopainak száma nem egyezik meg a várt számmal.  

 Ez a hiba az Azure Machine Learning akkor fordulhat elő, ha az alsóbb rétegbeli modul vagy művelet igényel egy adott számú oszlopok vagy bemenetek, és túl kevés vagy túl sok oszlopot vagy bemenetet biztosított. Példa:  

-   Egyetlen címkeoszlopot vagy kulcsoszlopot adhat meg, és véletlenül több oszlopot jelöl ki.  
  
-   Átnevezi az oszlopokat, de több vagy kevesebb nevet ad meg, mint amennyi oszlop van.  
  
-   A forrás vagy a cél oszlopainak száma megváltozott, vagy nem egyezik meg a modul által használt oszlopok számával.  
  
-   A bemenetek értékeinek vesszővel tagolt listáját adta meg, de az értékek száma nem egyezik, vagy több bemenet nem támogatott.  

**Felbontás:** Tekintse meg újra a modult, és ellenőrizze az oszlopkiválasztását, és győződjön meg arról, hogy a megfelelő számú oszlop van kiválasztva. Ellenőrizze az upstream modulok kimeneteit és az alsóbb rétegbeli műveletek követelményeit.  

 Ha az oszlopkijelölési beállítások egyikét használta, amely több oszlopot is kijelölhet (oszlopindexek, összes jellemző, minden numerikus stb.), ellenőrizze a kijelölés által visszaadott oszlopok pontos számát.  

 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->

 Ellenőrizze, hogy a felső sávban lévő oszlopok száma vagy típusa nem változott-e.  

 Ha egy modell betanításához ajánlott adatkészletet használ, ne feledje, hogy az ajánló korlátozott számú oszlopot vár, amelyek a felhasználói elem pároknak vagy a felhasználói elem rangsorának felelnek meg. További oszlopok eltávolítása a modell betanítása vagy a javaslati adatkészletek felosztása előtt. További információ: [Split Data](split-data.md).  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészlet kijelölt oszlopainak száma nem egyezik meg a várt számmal.|
|A bemeneti adatkészlet kijelölt oszlopainak száma nem egyenlő {expected_col_count} számmal.|
|A(z) "{selection_pattern_friendly_name}" oszlopkijelölési minta a bemeneti adatkészletben a kijelölt oszlopok számát nem egyenlő a(z) {expected_col_count}-mal.|
|A(z) "{selection_pattern_friendly_name}" oszlopkijelölési minta várhatóan {expected_col_count} oszlop(ok)at ad meg a bemeneti adatkészletben, de a{selected_col_count} oszlop(ok) ténylegesen rendelkezésre állnak.|


## <a name="error-0023"></a>0023-as hiba  

Kivétel akkor fordul elő, ha a bemeneti adatkészlet céloszlopa nem érvényes az aktuális oktatómodulra.  

Ez a hiba az Azure Machine Learning akkor fordul elő, ha a céloszlop (a modul paraméterek ben kiválasztott) nem az érvényes adattípus, tartalmazza az összes hiányzó értékeket, vagy nem volt a várt kategorikus.  

**Felbontás:** A címke/cél oszlop tartalmának vizsgálatához vizsgálja meg újra a modul bemenetét. Győződjön meg arról, hogy nem rendelkezik az összes hiányzó értékekkel. Ha a modul azt várja, hogy a céloszlop kategorikus legyen, győződjön meg arról, hogy a céloszlopban egynél több különböző érték van.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészlet nem támogatott céloszlopot rendelkezik.|
|A bemeneti adatkészlet nem támogatott céloszlopa :{column_index}".|
|A bemeneti adatkészlet nem támogatott "{column_index}" céloszloptal rendelkezik a(z) {learner_type} típusú tanuló számára.|


## <a name="error-0024"></a>0024-es hiba  
Kivétel, ha az adatkészlet nem tartalmaz feliratoszlopot.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a modul igényel egy címke oszlopot, és az adatkészlet nem rendelkezik címke oszlop. Például egy pontozott adatkészlet kiértékelése általában megköveteli, hogy egy címke oszlop jelen van a számítási pontossági metrikák.  

Azt is előfordulhat, hogy egy címke oszlop található az adatkészletben, de nem megfelelően észlelt az Azure Machine Learning.

**Felbontás:**

+ Nyissa meg a hibát generáló modult, és állapítsa meg, hogy van-e jelen címkeoszlop. Az oszlop neve vagy adattípusa nem számít, mindaddig, amíg az oszlop egyetlen eredményt (vagy függő változót) tartalmaz, amelyet előre ad. Ha nem biztos abban, hogy melyik oszlopban található a címke, keressen egy általános nevet, például *osztály* vagy *cél*nevet. 
+  Ha az adatkészlet nem tartalmaz címkeoszlopot, lehetséges, hogy a címkeoszlopot explicit módon vagy véletlenül eltávolították a felső bb szintre. Az is lehet, hogy az adatkészlet nem egy upstream pontozási modul kimenete.
+ Ha az oszlopot kifejezetten címkeoszlopként szeretné megjelölni, adja hozzá a [Metaadat szerkesztése](edit-metadata.md) modult, és csatlakoztassa az adatkészletet. Csak a címkeoszlopot jelölje ki, és válassza a **Címkék** elemet a **Mezők** legördülő listából. 
+ Ha nem a megfelelő oszlopot választja címkeként, a **Mezők** **felirattörlése** lehetőséget választva javíthatja az oszlop metaadatait. 
  
|Kivételüzenetek|
|------------------------|
|Az adatkészletben nincs címkeoszlop.|
|A(z) "{dataset_name}" nem tartalmaz címkeoszlopot.|


## <a name="error-0025"></a>0025-ös hiba  
 Kivétel akkor fordul elő, ha az adatkészlet nem tartalmaz pontszámoszlopot.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a bemeneta kiértékelés modell nem tartalmaz érvényes pontszám oszlopok. Például a felhasználó megpróbálja kiértékelni egy adatkészletet, mielőtt a megfelelő betanított modell, vagy a pontszám oszlop explicit módon esett upstream. Ez a kivétel akkor is előfordul, ha a két adatkészlet pontszámoszlopai nem kompatibilisek. Előfordulhat például, hogy egy lineáris regresszor pontosságát próbálja összehasonlítani egy bináris osztályozóval.  

**Felbontás:** Keresse fel újra a kiértékelt modell bemenetét, és vizsgálja meg, hogy tartalmaz-e egy vagy több pontszámoszlopot. Ha nem, az adatkészlet nem lett pontozva, vagy a pontszám oszlopok egy upstream modulban esett.  

|Kivételüzenetek|
|------------------------|
|Nincs pontszám oszlop az adatkészletben.|
|A(z) "{dataset_name}" nem tartalmaz pontszámoszlopot.|
|A(z) "{dataset_name}" nem tartalmaz "{learner_type}" által létrehozott pontszámoszlopot. Pontzítsa az adatkészletet a megfelelő tanulótípus használatával.|


## <a name="error-0026"></a>0026-os hiba  
 Kivétel, ha az azonos nevű oszlopok nem engedélyezettek.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha több oszlop ugyanazt a nevet. Ennek a hibának az egyik módja, ha az adatkészletnem rendelkezik fejlécsorral, és az oszlopnevek automatikusan hozzá vannak rendelve: Col0, Col1 stb.  

**Felbontás:** Ha az oszlopok nak ugyanaz a neve, szúrjon be egy [Metaadat-szerkesztési](edit-metadata.md) modult a bemeneti adatkészlet és a modul közé. A [Metaadatok szerkesztése](edit-metadata.md) párbeszédpanel oszlopkijelölőjével jelölje ki az átnevezendő oszlopokat, és írja be az új neveket az **Új oszlopnevek** mezőbe.  

|Kivételüzenetek|
|------------------------|
|Az argumentumokban azonos oszlopnevek vannak megadva. A modul nem engedélyezi az azonos oszlopneveket.|
|A(z) "{arg_name_1}" és "{arg_name_2}" argumentumokban nem lehet egyenlő oszlopneveket meghagyni. Adjon meg különböző neveket.|


## <a name="error-0027"></a>0027-es hiba  
 Kivétel akkor fordul elő, ha két objektumnak azonos méretűnek kell lennie, de nem.  

 Ez egy gyakori hiba az Azure Machine Learningben, és számos feltétel okozhatja.  

**Felbontás:** Nincs konkrét állásfoglalás. A következő feltételeket azonban ellenőrizheti:  

-   Ha oszlopokat nevez át, győződjön meg arról, hogy minden lista (a bemeneti oszlopok és az új nevek listája) azonos számú elcikkel rendelkezik.  
  
-   Ha két adatkészlethez csatlakozik vagy összefűz, győződjön meg arról, hogy azok azonos sémával rendelkeznek.  
  
-   Ha két, több oszlopot tartalmazó adatkészlethez csatlakozik, győződjön meg arról, hogy a kulcsoszlopok adattípusa megegyezik, és válassza az **Ismétlődések engedélyezése és az oszlopsorrend megőrzése lehetőséget a kijelölésben.**  

|Kivételüzenetek|
|------------------------|
|Az átadott objektumok mérete inkonzisztens.|
|A(z) "{friendly_name1}" mérete nem egyezik a(z) "{friendly_name2}" méretével.|


## <a name="error-0028"></a>0028-as hiba  
 Kivétel akkor fordul elő, ha az oszlopkészlet duplikált oszlopneveket tartalmaz, és ez nem engedélyezett.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, amikor oszlopnevek duplikált; azaz, nem egyedi.  

**Felbontás:** Ha bármelyik oszlopnak azonos a neve, adja hozzá a [Metaadatok szerkesztése példányát](edit-metadata.md) a bemeneti adatkészlet és a hibát kivető modul között. A [Metaadatok szerkesztése](edit-metadata.md) párbeszédpanel Oszlopkijelölőjével jelölje ki az átnevezendő oszlopokat, és írja be az új oszlopok nevét az **Új oszlopnevek** mezőbe. Ha több oszlopot nevez át, győződjön meg arról, hogy az **Új oszlopnevekben** beírt értékek egyediek.  

|Kivételüzenetek|
|------------------------|
|Az oszlopkészlet duplikált oszlopnevet (neveket) tartalmaz.|
|A(z) "{duplicated_name}" név duplikált.|
|A(z) "{duplicated_name}" név a következőben is megkettőződik: "{arg_name}".|
|A(z) "{duplicated_name}" név duplikált. Részletek: {details}|


## <a name="error-0029"></a>0029-es hiba  
 Kivétel akkor fordul elő, ha érvénytelen URI-t adnak át.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha érvénytelen URI átlett halad.  Ez a hibaüzenet akkor jelenik meg, ha az alábbi feltételek bármelyike teljesül:  

-   Az Azure Blob Storage olvasási vagy írási tárolási nyilvános vagy SAS URI-ja hibát tartalmaz.  
  
-   A SAS időablaka lejárt.  
  
-   A HTTP-forráson keresztüli webes URL-cím egy fájlt vagy egy visszacsatolási URI-t jelöl.  
  
-   A HTTP-n keresztüli webes URL-cím helytelenül formázott URL-t tartalmaz.  
  
-   Az URL-címet a távoli forrás nem tudja feloldani.  

**Felbontás:** Keresse fel újra a modult, és ellenőrizze az URI formátumát. Ha az adatforrás egy webes URL HTTP-n keresztül, ellenőrizze, hogy a tervezett forrás nem fájl vagy visszacsatolási URI (localhost).  

|Kivételüzenetek|
|------------------------|
|Érvénytelen Uri átlett haladva.|
|A(z) "{invalid_url}" Uri érvénytelen.|


## <a name="error-0030"></a>0030-as hiba  
 Kivétel akkor fordul elő, ha nem lehet letölteni egy fájlt.  

 Ez a kivétel az Azure Machine Learning akkor fordul elő, ha nem lehet letölteni egy fájlt. Ez a kivétel akkor jelenik meg, ha egy HTTP-forrásból való olvasási kísérlet három (3) újrapróbálkozási kísérlet után sikertelen volt.  

**Felbontás:** Ellenőrizze, hogy a HTTP-forrás URI-ja helyes-e, és hogy a hely jelenleg elérhető-e az interneten keresztül.  

|Kivételüzenetek|
|------------------------|
|Nem lehet letölteni egy fájlt.|
|Hiba történt a következő fájl letöltéseke közben: {file_url}.|


## <a name="error-0031"></a>0031-es hiba  
 Kivétel, ha az oszlopkészletoszlop-oszlopok száma a szükségesnél kisebb.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a kiválasztott oszlopok száma kevesebb, mint szükséges.  Ez a hibaüzenet akkor jelenik meg, ha nincs kiválasztva a minimálisan szükséges számú oszlop.  

**Felbontás:** Adjon további oszlopokat az oszlopkijelöléshez az **Oszlopkijelölő**segítségével.  

|Kivételüzenetek|
|------------------------|
|Az oszlopkészletoszlop-oszlopok száma a szükségesnél kisebb.|
|Legalább {required_columns_count} oszlopot kell megadni a(z) "{arg_name}" bemeneti argumentumhoz.|
|Legalább {required_columns_count} oszlopot kell megadni a(z) "{arg_name}" bemeneti argumentumhoz. A megadott oszlopok tényleges száma {input_columns_count}.|


## <a name="error-0032"></a>0032-es hiba  
 Kivétel akkor fordul elő, ha az argumentum nem szám.  

 Ez a hibaüzenet az Azure Machine Learningben jelenik meg, ha az argumentum kettős vagy NaN.  

**Felbontás:** Módosítsa a megadott argumentumot érvényes érték használatára.  

|Kivételüzenetek|
|------------------------|
|Az argumentum nem szám.|
|A(z) "{arg_name}" nem szám.|


## <a name="error-0033"></a>0033-as hiba  
 Kivétel akkor fordul elő, ha az argumentum Infinity.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az argumentum végtelen. Ez a hibaüzenet akkor jelenik `double.NegativeInfinity` `double.PositiveInfinity`meg, ha az argumentum vagy a .  

**Felbontás:** Módosítsa a megadott argumentumot érvényes értékre.  

|Kivételüzenetek|
|------------------------|
|Az érvelésnek végesnek kell lennie.|
|A(z) "{arg_name}" nem véges.|


## <a name="error-0034"></a>0034-es hiba  
 Kivétel akkor fordul elő, ha egy adott felhasználói elempárhoz egynél több minősítés létezik.  

 Ez a hiba az Azure Machine Learning ben történik javaslat, ha egy felhasználói elem pár egynél több minősítési érték.  

**Felbontás:** Győződjön meg arról, hogy a felhasználó-elem pár csak egy minősítési értékkel rendelkezik.  

|Kivételüzenetek|
|------------------------|
|Az adatkészlet értékéhez egynél több minősítés létezik.|
|A(z) {user} és {item} elem több minősítése a minősítési előrejelzési adattáblában.|
|A(z) {user} és {item} elem több minősítése a(z) {dataset} alkalmazásban.|


## <a name="error-0035"></a>0035-ös hiba  
 Kivétel akkor fordul elő, ha egy adott felhasználóhoz vagy elemhez nem biztosítottak szolgáltatásokat.  

 Ez a hiba az Azure Machine Learning ben történik, amikor egy javaslatmodell t szeretne használni a pontozáshoz, de a szolgáltatás vektor nem található.  

**Felbontás:**

A Matchbox ajánlónak vannak bizonyos követelményei, amelyeknek meg kell felelniük az elemfunkciók vagy a felhasználói funkciók használatakor.  Ez a hiba azt jelzi, hogy egy bemenetként megadott felhasználó vagy elem szolgáltatásvektora hiányzik. Győződjön meg arról, hogy az egyes felhasználók vagy elemek adatai között számos szolgáltatás áll rendelkezésre.  

 Ha például egy ajánlási modellt olyan funkciókkal tanított be, mint a felhasználó életkora, helye vagy jövedelme, de most olyan új felhasználók számára szeretne pontszámokat létrehozni, akik nem voltak láthatók a képzés során, meg kell adnia néhány egyenértékű funkciókészletet (nevezetesen az életkort, a helyet és a az új felhasználók számára, hogy megfelelő előrejelzéseket tehessenek róluk. 

 Ha nem rendelkezik ilyen szolgáltatásokkal ezekhez a felhasználókhoz, fontolja meg a szolgáltatástervezést a megfelelő funkciók létrehozásához.  Ha például nem rendelkezik egyéni felhasználói életkori vagy jövedelmi értékekkel, előfordulhat, hogy hozzávetőleges értékeket hoz létre a felhasználók egy csoportjához. 

<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).

For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->

 > [!TIP]
 > A megoldás nem vonatkozik az Ön ügyére? Szívesen küld visszajelzést erről a cikkről, és tájékoztatást nyújt a forgatókönyvről, beleértve a modult és az oszlopban lévő sorok számát. Ezeket az információkat arra használjuk fel, hogy a jövőben részletesebb hibaelhárítási lépéseket tegyünk.

|Kivételüzenetek|
|------------------------|
|A szükséges felhasználóvagy elem nem rendelkezett szolgáltatásokkal.|
|A(z) {required_feature_name} szolgáltatásai kötelezőek, de nincsenek megadva.|


## <a name="error-0036"></a>0036-os hiba  
 Kivétel akkor fordul elő, ha egy adott felhasználóhoz vagy elemhez több szolgáltatásvektot adott meg.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy szolgáltatás vektor van definiálva többször.  

**Felbontás:** Győződjön meg arról, hogy a szolgáltatásvektor nincs definiálva egynél többször.  

|Kivételüzenetek|
|------------------------|
|Felhasználó vagy elem ismétlődő szolgáltatásdefiníciója.|


## <a name="error-0037"></a>0037-es hiba  
 Kivétel akkor fordul elő, ha több címkeoszlop van megadva, és csak egy engedélyezett.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egynél több oszlop van kiválasztva, hogy az új címke oszlop. A legtöbb felügyelt tanulási algoritmusok megköveteli, hogy egyetlen oszlop kell jelölni a cél vagy címke.  

**Felbontás:** Ügyeljen arra, hogy egyetlen oszlopot jelöljön ki új címkeoszlopként.  

|Kivételüzenetek|
|------------------------|
|Több címkeoszlop van megadva.|
|A(z) "{dataset_name}" több címkeoszlopot is megad.|


## <a name="error-0039"></a>0039-es hiba  
 Kivétel, ha egy művelet sikertelen.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy belső művelet nem hajtható végre.  

**Felbontás:** Ezt a hibát számos feltétel okozza, és nincs konkrét megoldás.  
 Az alábbi táblázat a hibához tartozó általános üzeneteket tartalmazza, amelyeket a feltétel konkrét leírása követ. 

 Ha nem állnak rendelkezésre részletek, [küldjön visszajelzést,](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) és adjon tájékoztatást a hibát és a kapcsolódó feltételeket generáló modulokról.

|Kivételüzenetek|
|------------------------|
|A művelet nem sikerült.|
|Hiba történt a művelet végrehajtása közben: "{failed_operation}".|
|Hiba történt a művelet végrehajtása közben: "{failed_operation}". Indok: "{reason}".|


## <a name="error-0042"></a>0042-es hiba  
 Kivétel akkor fordul elő, ha az oszlopot nem lehet másik típussá konvertálni.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet konvertálni oszlop a megadott típusú.  Ez a hibaüzenet akkor jelenik meg, ha egy modulhoz egy adott adattípusra van szükség, például datetime, szöveg, lebegőpontos szám vagy egész szám, de egy meglévő oszlopot nem lehet a szükséges típusra konvertálni.  

Kijelölhet például egy oszlopot, és megpróbálhatja numerikus adattípussá konvertálni egy matematikai művelethez, és ezt a hibaüzenetet kaphatja, ha az oszlop érvénytelen adatokat tartalmaz. 

Egy másik ok, amiért ez a hibaüzenet akkor jelenhet meg, ha lebegőpontos számokat vagy számos egyedi értéket tartalmazó oszlopot próbál kategorikus oszlopként használni. 

**Felbontás:**

+ Nyissa meg a hibát okozó modul súgólapját, és ellenőrizze az adattípusra vonatkozó követelményeket.
+ Tekintse át a bemeneti adatkészlet oszlopainak adattípusait.
+ Az úgynevezett séma nélküli adatforrásokból származó adatok vizsgálata.
+ Ellenőrizze, hogy nincs-e az adatkészletben hiányzó értékek vagy speciális karakterek, amelyek megakadályozhatják a kívánt adattípusra való átalakítást. 
    + A numerikus adattípusoknak konzisztensnek kell lenniük: például ellenőrizze, hogy vannak-e lebegőpontos számok egy egész oszlopban.
    + Szöveges karakterláncokat vagy NA-értékeket kereshet egy számoszlopban. 
    + A logikai értékek a szükséges adattípustól függően megfelelő ábrázolássá konvertálhatók.
    + Nem unicode-os karakterek, tabulátorkarakterek vagy vezérlőkarakterek szöveges oszlopainak vizsgálata
    + A modellezési hibák elkerülése érdekében a dátumidő-adatoknak konzisztensnek kell lenniük, de a törlés összetett lehet a számos formátum miatt. Fontolja meg a <!--the [Execute R Script](execute-r-script.md) or -->[Python-parancsfájl-modulok végrehajtása](execute-python-script.md) a karbantartás végrehajtásához.  
+ Szükség esetén módosítsa a bemeneti adatkészlet értékeit, hogy az oszlop konvertálható legyen. A módosítás magában foglalhatja a binning, a csonkolási vagy kerekítési műveleteket, a kiugró értékek kiküszöbölését vagy a hiányzó értékek imputálását. Az alábbi cikkekben a gépi tanulás néhány gyakori adatátalakítási forgatókönyvét tartalmaz:
    + [Hiányzó adatok törlése](clean-missing-data.md)
    + [Adatok normalizálása](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->

> [!TIP]
> A megoldás nem egyértelmű, vagy nem alkalmazható az Ön esetében? Szívesen küld visszajelzést erről a cikkről, és tájékoztatást nyújt a forgatókönyvről, beleértve a modult és az oszlop adattípusát. Ezeket az információkat arra használjuk fel, hogy a jövőben részletesebb hibaelhárítási lépéseket tegyünk.  

|Kivételüzenetek|
|------------------------|
|Nem engedélyezett átalakítás.|
|A(z) {type1} típusú oszlop nem konvertálható {type2} típusú oszlopra.|
|A(z) {type1} típusú "{col_name1}" oszlop nem konvertálható {type2} típusú oszlopra.|
|A(z) {col_name1}" {type1} típusú oszlop nem konvertálható {col_name2}" típusú {col_name2}" oszlopra.|


## <a name="error-0044"></a>0044-es hiba  
 Kivétel akkor fordul elő, ha nem lehet az oszlop elemtípusát a meglévő értékekből levezetni.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet következtetni egy oszlop vagy oszlopok egy adatkészletben. Ez általában akkor fordul elő, ha két vagy több adatkészletet fűz össze különböző elemtípusokkal. Ha az Azure Machine Learning nem tudja meghatározni a közös típus, amely képes egy oszlopban vagy oszlopban lévő összes értéket egy adatvesztés nélkül, akkor ezt a hibát generálja.  

**Felbontás:** Győződjön meg arról, hogy mindkét adatkészlet összes értéke azonos típusú (numerikus, logikai, kategorikus, karakterlánc, dátum stb.), vagy ugyanarra a típusra kényszeríthető.  

|Kivételüzenetek|
|------------------------|
|Az oszlop elemtípusa nem származtatható.|
|A(z) "{column_name}" oszlop elemtípusát nem lehet levezetni - az összes elem null hivatkozás.|
|A(z) "{dataset_name}" adatkészlet "{column_name}" oszlopának elemtípusa nem származtatható - az összes elem null hivatkozás.|


## <a name="error-0045"></a>0045-ös hiba  
 Kivétel akkor fordul elő, ha a forrásban lévő vegyes elemtípusok miatt nem lehet oszlopot létrehozni.  

 Ez a hiba az Azure Machine Learning akkor keletkezik, ha a két adatkészletek kombinálása eltérő.  

**Felbontás:** Győződjön meg arról, hogy mindkét adatkészlet összes értéke azonos típusú (numerikus, logikai, kategorikus, karakterlánc, dátum stb.).  

|Kivételüzenetek|
|------------------------|
|Nem hozható létre vegyes elemtípussal rendelkező oszlop.|
|Nem hozható létre vegyes elemtípusok "{column_id}" azonosítójú oszlopa:<br />Az adatok típusa[{row_1}, {column_id}] a következő: "{type_1}". <br />Az adatok típusa[{row_2}, {column_id}] a következő: "{type_2}".|
|Nem hozható létre vegyes elemtípusok "{column_id}" azonosítójú oszlopa:<br />A(z) {chunk_id_1} adattömbben a következő a következő: "{type_1}". <br />A(z) {chunk_id_2} adattömbben a következő típus: "{type_2}" a következő adattömbmérettel: {chunk_size}.|


## <a name="error-0046"></a>0046-os hiba  
 Kivétel akkor fordul elő, ha nem lehet könyvtárat létrehozni a megadott elérési úton.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet létrehozni egy könyvtárat a megadott elérési úton. Ez a hibaüzenet akkor jelenik meg, ha a Hive-lekérdezés kimeneti könyvtárának elérési útjának bármely része helytelen vagy nem érhető el.  

**Felbontás:** Keresse fel újra a modult, és ellenőrizze, hogy a címtár elérési útja megfelelően van-e formázva, és hogy az elérhető-e az aktuális hitelesítő adatokkal.  

|Kivételüzenetek|
|------------------------|
|Adjon meg érvényes kimeneti könyvtárat.|
|Könyvtár: A(z) {path} nem hozható létre. Adjon meg érvényes elérési utat.|


## <a name="error-0047"></a>0047-es hiba  
 Kivétel, ha a modulnak átadott adatkészletek egyes adatkészleteinek szolgáltatásoszlopainak száma túl kicsi.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a bemeneti adatkészlet betanítási nem tartalmazza az algoritmus által megkövetelt minimális számú oszlop. Általában vagy üres az adatkészlet, vagy csak képzési oszlopokat tartalmaz.  

**Felbontás:** A bemeneti adatkészlet et újra felkell keresni, hogy a címkeoszlopon kívül egy vagy több oszlop is található-e.  

|Kivételüzenetek|
|------------------------|
|A bemeneti adatkészlet szolgáltatásoszlopainak száma kisebb, mint a megengedett minimum.|
|A bemeneti adatkészlet jellemzőoszlopainak száma kisebb, mint a megengedett {required_columns_count} oszlop(ok) száma.|
|A(z) "{arg_name}" bemeneti adatkészlet szolgáltatásoszlopainak száma kisebb, mint a megengedett {required_columns_count} oszlop(ok) száma.|


## <a name="error-0048"></a>0048-as hiba  
 Kivétel akkor fordul elő, ha nem lehet megnyitni egy fájlt.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet megnyitni egy fájlt olvasásra vagy írásra. A következő okok miatt jelenhet meg ez a hiba:  

-   A tároló vagy a fájl (blob) nem létezik  
  
-   A fájl vagy tároló hozzáférési szintje nem teszi lehetővé a fájl elérését  
  
-   A fájl túl nagy az olvasáshoz, vagy nem megfelelő formátumú  

**Felbontás:** Keresse fel újra a modult és az olvasni kívánt fájlt.  

 Ellenőrizze, hogy a tároló és a fájl neve helyes-e.  

 Használja az Azure klasszikus portálon, vagy egy Azure storage-eszköz, ellenőrizze, hogy rendelkezik-e engedéllyel a fájl eléréséhez.  

  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->

|Kivételüzenetek|
|------------------------|
|Nem lehet megnyitni egy fájlt.|
|Hiba történt a fájl megnyitásakor: {file_name}.|
|Hiba történt a fájl megnyitásakor: {file_name}. Tárolási kivételüzenet: {exception}.|


## <a name="error-0049"></a>0049-es hiba  
 Kivétel akkor fordul elő, ha nem lehet elemezni egy fájlt.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet elemezni egy fájlt. Ez a hibaüzenet akkor jelenik meg, ha az [Adatok importálása](import-data.md) modulban kiválasztott fájlformátum nem egyezik meg a fájl tényleges formátumával, vagy ha a fájl felismerhetetlen karaktert tartalmaz.  

**Felbontás:** Keresse fel újra a modult, és javítsa ki a fájlformátum-kiválasztást, ha az nem egyezik meg a fájl formátumával. Ha lehetséges, ellenőrizze, hogy nem tartalmaz-e érvénytelen karaktereket a fájlban.  

|Kivételüzenetek|
|------------------------|
|Nem lehet elemezni egy fájlt.|
|Hiba történt a(z) {file_format} fájl elemzése közben.|
|Hiba történt a(z) {file_format} fájl elemzése közben: {file_name}.|
|Hiba történt a(z) {file_format} fájl elemzése közben. Indok: {failure_reason}.|
|Hiba történt a(z) {file_format} fájl elemzése közben: {file_name}. Indok: {failure_reason}.|


## <a name="error-0052"></a>0052-es hiba  
 Kivétel akkor fordul elő, ha az Azure storage-fiók kulcs helytelenül van megadva.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az Azure storage-fiók eléréséhez használt kulcs helytelen. Előfordulhat például, hogy ez a hiba akkor jelenik meg, ha az Azure storage kulcs a másolás és beillesztés során csonkolt, vagy ha nem a megfelelő kulcsot használták.  

 Az Azure-tárfiók kulcsának bekésezéséről a [Tárolási hozzáférési kulcsok megtekintése, másolása és újragenerálása](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/)című témakörben talál további információt.  

**Felbontás:** Keresse fel újra a modult, és ellenőrizze, hogy az Azure storage kulcs a fiók megfelelő-e; szükség esetén másolja újra a kulcsot a klasszikus Azure-portálról.  

|Kivételüzenetek|
|------------------------|
|Az Azure storage-fiók kulcshelytelen.|


## <a name="error-0053"></a>0053-as hiba  
 Kivétel akkor fordul elő, ha nincsenek felhasználói funkciók vagy elemek a matchbox-javaslatokhoz.  

 Ez a hiba az Azure Machine Learning akkor keletkezik, ha egy szolgáltatás vektor nem található.  

**Felbontás:** Győződjön meg arról, hogy a bemeneti adatkészletben egy szolgáltatásvektor található.  

|Kivételüzenetek|
|------------------------|
|A felhasználói funkciók és/és elemek szükségesek, de nem állnak rendelkezésre.|


## <a name="error-0056"></a>0056-os hiba  
 Kivétel, ha a művelethez kiválasztott oszlopok megsértik a követelményeket.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, amikor olyan művelet oszlopokat választ, amely megköveteli, hogy az oszlop egy adott adattípus. 

 Ez a hiba akkor is előfordulhat, ha az oszlop a megfelelő adattípus, de a használt modul megköveteli, hogy az oszlop is meg legyen jelölve szolgáltatásként, címkeként vagy kategorikus oszlopként.  

  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->

**Felbontás:**

1.  Tekintse át az aktuálisan kijelölt oszlopok adattípusát. 

2. Annak megállapítása, hogy a kijelölt oszlopok kategorikus, címke vagy jellemző oszlopok-e.  
  
3.  Tekintse át annak a modulnak a súgótémakörét, amelyben az oszlopot kiválasztotta, és állapítsa meg, hogy vannak-e konkrét követelmények az adattípus vagy az oszlophasználat számára.  
  
3.  A [Metaadatok szerkesztése](edit-metadata.md) segítségével módosíthatja a művelet időtartama alatt az oszloptípusát. Ügyeljen arra, hogy az oszloptípusát visszamódosítsa az eredeti értékére, a [Metaadatok szerkesztése](edit-metadata.md)egy másik példányát használva, ha az alsóbb rétegbeli műveletekhez szüksége van rá.  

|Kivételüzenetek|
|------------------------|
|Egy vagy több kijelölt oszlop nem volt engedélyezett kategóriában.|
|A(z) "{col_name}" nevű oszlop nem engedélyezett kategóriában található.|


## <a name="error-0057"></a>0057-es hiba  
 Kivétel akkor fordul elő, amikor olyan fájlt vagy blobot próbál létrehozni, amely már létezik.  

 Ez a kivétel akkor fordul elő, ha az [Adatok exportálása](export-data.md) modult vagy más modult használja egy folyamat eredményeinek mentéséhez az Azure Machine Learningben az Azure blob storage-ba, de megkísérli létrehozni egy fájlt vagy blobot, amely már létezik.   

**Felbontás:**

 Ez a hibaüzenet csak akkor jelenik meg, ha korábban az **Azure blob storage írási módját** hiba állapotba **állította.** Ez a modul szándékosan hibát okoz, ha olyan blobba próbál adatkészletet írni, amely már létezik.

 - Nyissa meg a modul tulajdonságait, és módosítsa a **tulajdonságot Az Azure blob storage írási módjában** **felülírásra.**
 - Másik lehetőségként beírhatja egy másik célblob vagy -fájl nevét, és biztoslehet, hogy olyan blobot ad meg, amely még nem létezik.  

|Kivételüzenetek|
|------------------------|
|A fájl vagy a Blob már létezik.|
|A(z) "{file_path}" fájl vagy blob már létezik.|


## <a name="error-0058"></a>0058-as hiba  
 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az adatkészlet nem tartalmazza a várt címke oszlop.  

 Ez a kivétel akkor is előfordulhat, ha a megadott címkeoszlop nem egyezik meg a tanuló által várt adatokkal vagy adattípussal, vagy nem megfelelő értékekkel rendelkezik. Ez a kivétel például akkor keletkezik, ha egy bináris osztályozó betanításakor valós értékű címkeoszlopot használ.  

**Felbontás:** A felbontás a használt tanulótól vagy trénertől, valamint az adatkészlet oszlopainak adattípusaitól függ. Először ellenőrizze a gépi tanulási algoritmus vagy a betanítási modul követelményeit.  

 Keresse fel újra a bemeneti adatkészletet. Ellenőrizze, hogy a címkeként várhatóan kezelt oszlop rendelkezik-e a létrehozandó modellhez megfelelő adattípussal.  

 Ellenőrizze a bemeneteken a hiányzó értékeket, és szükség esetén szüntesse meg vagy cserélje ki azokat.  

 Ha szükséges, adja hozzá a [Metaadat szerkesztése modult,](edit-metadata.md) és győződjön meg arról, hogy a címkeoszlop címkeként van megjelölve.  

|Kivételüzenetek|
|------------------------|
|A címkeoszlop-értékek és a pontozott címkeoszlop-értékek nem hasonlíthatók össze.|
|A címkeoszlop nem a várt módon történik a(z) "{dataset_name}" fájlban.|
|A címkeoszlop nem a várt módon történik a(z) {dataset_name}, {reason}.|
|A(z) "{column_name}" címkeoszlop nem várt a(z) "{dataset_name}" fájlban.|
|A(z) "{column_name}" címkeoszlop nem várt a(z) "{dataset_name}" ({reason}) feliratában.|


## <a name="error-0059"></a>0059-es hiba  
 Kivétel, ha az oszlopválasztóban megadott oszlopindex nem elemezhető.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az oszlopindex az oszlopválasztó használatakor megadott oszlopindex nem elemezhető.  Ez a hibaüzenet akkor jelenik meg, ha az oszlopindex érvénytelen formátumú, és nem elemezhető.  

**Felbontás:** Módosítsa az oszlopindexet érvényes indexérték használatára.  

|Kivételüzenetek|
|------------------------|
|Egy vagy több megadott oszlopindex vagy indextartomány nem elemezhető.|
|A(z) "{column_index_or_range}" oszlopindex vagy -tartomány nem elemezhető.|


## <a name="error-0060"></a>0060-as hiba  
 Kivétel akkor fordul elő, ha egy oszlopválasztóban tartományon kívüli oszloptartomány van megadva.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy tartományon kívüli oszlop tartományban van megadva az oszlopválasztó. Ez a hibaüzenet akkor jelenik meg, ha az oszlopválasztó oszloptartománya nem felel meg az adatkészlet oszlopainak.  

**Felbontás:** Módosítsa az oszlopválasztó oszloptartományát úgy, hogy az megfeleljen az adatkészlet oszlopainak.  

|Kivételüzenetek|
|------------------------|
|Érvénytelen vagy kívül esik a megadott tartományoszlop-indextartományon.|
|A(z) "{column_range}" oszloptartomány érvénytelen vagy kívül esik a tartományon.|


## <a name="error-0061"></a>0061-es hiba  
 Kivétel akkor fordul elő, amikor megpróbál hozzáadni egy sort a DataTable, amely más számú oszlopot, mint a táblázat.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha megpróbál hozzáadni egy sort egy adatkészlet, amely az adatkészlet különböző számú oszlopot.  Ez a hibaüzenet akkor jelenik meg, ha az adatkészlethez hozzáadott sor a bemeneti adatkészlettől eltérő számú oszlopot tartalmaz.  A sor nem fűzhető az adatkészlethez, ha az oszlopok száma eltérő.  

**Felbontás:** Módosítsa úgy a bemeneti adatkészletet, hogy ugyanannyi oszlop legyen, mint a megadott sor, vagy módosítsa a hozzáadta a sort, hogy ugyanannyi oszlop legyen, mint az adatkészlet.  

|Kivételüzenetek|
|------------------------|
|Minden táblának azonos számú oszloppal kell rendelkeznie.|
|A(z) "{chunk_id_1}" adattömb oszlopai eltérőek a(z) "{chunk_id_2}" adattömbök {chunk_size} méretű adattömbjében.|
|A(z) "{filename_1}" (count={column_count_1}) oszlopszáma a(z) "{filename_2}" fájl (count={column_count_2}) esetében eltérő.|


## <a name="error-0062"></a>0062-es hiba  
 Kivétel akkor fordul elő, amikor két különböző tanulótípussal rendelkező modellt próbálunk összehasonlítani.  

 Ez a hiba az Azure Machine Learning ben akkor történik, ha két különböző pontozott adatkészletek kiértékelési metrikák nem lehet összehasonlítani. Ebben az esetben nem lehet összehasonlítani a két pontozott adatkészlet előállításához használt modellek hatékonyságát.  

**Felbontás:** Ellenőrizze, hogy a pontozott eredményeket azonos típusú gépi tanulási modell (bináris osztályozás, regresszió, többosztályos besorolás, ajánlás, fürtözés, anomáliadetektálás stb.) állítja-e elő. Minden összehasonlított modellnek azonos tanulótípussal kell rendelkeznie.  

|Kivételüzenetek|
|------------------------|
|Minden modellnek azonos tanulótípussal kell rendelkeznie.|
|Nem kompatibilis tanulótípus: "{actual_learner_type}". A várt tanulótípusok a következők: "{expected_learner_type_list}".|


## <a name="error-0064"></a>0064-es hiba  
 Kivétel akkor fordul elő, ha az Azure storage-fiók neve vagy tárolási kulcshelytelenül van megadva.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az Azure storage-fiók neve vagy tárolási kulcs helytelenül van megadva. Ez a hibaüzenet akkor jelenik meg, ha helytelen fióknevet vagy jelszót ad meg a tárfiókhoz. Ez akkor fordulhat elő, ha manuálisan adja meg a fiók nevét vagy jelszavát. Ez akkor is előfordulhat, ha a fiókot törölték.  

**Felbontás:** Ellenőrizze, hogy helyesen adta-e meg a fióknevét és a jelszavát, és hogy létezik-e a fiók.  

|Kivételüzenetek|
|------------------------|
|Az Azure storage-fiók neve vagy a tárolási kulcs helytelen.|
|Az Azure storage-fiók neve "{account_name}" vagy a fióknév tárolási kulcsa helytelen.|


## <a name="error-0065"></a>0065-ös hiba  
 Kivétel akkor fordul elő, ha az Azure blob neve helytelenül van megadva.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az Azure blob neve helytelenül van megadva.  A hibaüzenet akkor jelenik meg, ha:  

-   A blob nem található a megadott tárolóban.  

 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->

-   Csak a tároló volt megadva forrásként az [Import Data](import-data.md) kérelemben, ha a formátum Excel vagy CSV kódolással; a tárolón belüli összes blob tartalmának összefűzése nem engedélyezett ezekkel a formátumokkal.  
  
-   A SAS URI nem tartalmazza egy érvényes blob nevét.  

**Felbontás:** Újra a modul dobott a kivétel. Ellenőrizze, hogy a megadott blob létezik-e a tárolóban a tárfiókban, és hogy az engedélyek lehetővé teszik a blob megtekintéséhez. Ellenőrizze, hogy a bemenet **űrlaptárolónév-/fájlnévből** van-e, ha excel vagy CSV kódolási formátummal rendelkezik. Ellenőrizze, hogy a SAS URI-azonosító tartalmazza-e egy érvényes blob nevét.  

|Kivételüzenetek|
|------------------------|
|Az Azure storage blob neve helytelen.|
|Az Azure storage blob neve "{blob_name}" helytelen.|
|Az Azure storage blob neve a"{blob_name_prefix}" előtaggal nem létezik.|
|Nem található az Azure storage blobok tároló "{container_name}" tárolóban.|
|Nem található a(z) "{blob_wildcard_path}" helyettesítő elérésiúttal rendelkező Azure storage-blobok.|


## <a name="error-0066"></a>0066-os hiba  
 Kivétel akkor fordul elő, ha egy erőforrás nem tölthető fel egy Azure Blobba.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy erőforrás nem tölthető fel egy Azure Blob.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Mindkettő ugyanabba az Azure storage-fiókba kerül, mint a bemeneti fájlt tartalmazó fiók.  

**Felbontás:** Újra a modult. Ellenőrizze, hogy az Azure-fiók neve, a tárolási kulcs és a tároló helyes-e, és hogy a fiók rendelkezik-e engedéllyel a tárolóba való íráshoz.  

|Kivételüzenetek|
|------------------------|
|Az erőforrás nem tölthető fel az Azure storage-ba.|
|A(z) "{source_path}" fájlt nem lehetett "{dest_path}" néven feltölteni az Azure storage-ba.|


## <a name="error-0067"></a>0067-es hiba  
 Kivétel, ha egy adatkészlet a várttól eltérő számú oszlopot tartalmaz.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy adatkészlet a várttól eltérő számú oszlopot tartalmaz.  Ez a hibaüzenet akkor jelenik meg, ha az adatkészlet oszlopainak száma eltér a modul által a végrehajtás során várt oszlopok számától.  

**Felbontás:** Módosítsa a bemeneti adatkészletet vagy a paramétereket.  

|Kivételüzenetek|
|------------------------|
|Az adattábla oszlopainak váratlan száma.|
|A(z) "{dataset_name}" adatkészlet oszlopainak váratlan száma.|
|A várt "{expected_column_count}" oszlop(ok), de helyette "{actual_column_count}" oszlop(ok) találhatók.|
|A(z) "{dataset_name}" bemeneti adatkészletben a várt "{expected_column_count}" oszlop(ok), de helyette "{actual_column_count}" oszlop(ok) találhatók.|


## <a name="error-0068"></a>0068-as hiba  
 Kivétel akkor fordul elő, ha a megadott Hive-parancsfájl nem megfelelő.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a Hive QL-parancsfájl szintaktikai hibák, vagy ha a Hive-értelmező hibát észlel a lekérdezés vagy parancsfájl végrehajtása közben.  

**Felbontás:**

A Hive hibaüzenete általában a hibanaplóban jelenik meg, így az adott hiba alapján műveleteket végrehajthat. 

+ Nyissa meg a modult, és vizsgálja meg a lekérdezés hibákat.  
+ Ellenőrizze, hogy a lekérdezés megfelelően működik-e az Azure Machine Learningen kívül, ha bejelentkezik a Hadoop-fürt Hive-konzoljára, és futtatja a lekérdezést.  
+ Próbáljon meg megjegyzéseket elhelyezni a Hive-parancsfájlban egy külön sorban, szemben a végrehajtható utasítások és megjegyzések egyetlen sorban való keverésével.  

### <a name="resources"></a>Források

A gépi tanuláshoz kapcsolódó Hive-lekérdezésekkel kapcsolatos segítségért tekintse meg az alábbi cikkeket:

+ [Hive-táblák létrehozása és adatok betöltése az Azure Blob Storage-ból](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Adatok feltárása a táblázatokban Hive-lekérdezésekkel](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Funkciók létrehozása az adatokhoz egy Hadoop-fürtben Hive-lekérdezések segítségével](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive az SQL felhasználók Cheat Sheet (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Kivételüzenetek|
|------------------------|
|A Hive-parancsfájl helytelen.|


## <a name="error-0069"></a>0069-es hiba  
 Kivétel, ha a megadott SQL-parancsfájl nem megfelelő.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha a megadott SQL-parancsfájl szintaktikai problémák, vagy ha a parancsfájlban megadott oszlopok vagy tábla érvénytelen. 

 Ez a hibaüzenet akkor jelenik meg, ha az SQL-motor bármilyen hibát észlel a lekérdezés vagy parancsfájl végrehajtása közben. Az SQL hibaüzenet általában a hibanaplóban jelenik meg, így az adott hiba alapján műveleteket tehet.  

**Felbontás:** Keresse fel újra a modult, és vizsgálja meg az SQL-lekérdezéshibákat.  

 Ellenőrizze, hogy a lekérdezés megfelelően működik-e az Azure ML-en kívül, ha közvetlenül bejelentkezik az adatbázis-kiszolgálóra, és futtatja a lekérdezést.  

 Ha a modulkivétel által jelentett SQL-üzenet jelenik meg, hajtsa végre a jelentett hiba alapján. A hibaüzenetek például néha konkrét útmutatást tartalmaznak a valószínű hibára vonatkozóan:
+ *Nincs ilyen oszlop vagy hiányzó adatbázis,* ami azt jelzi, hogy lehet, hogy hibás oszlopnevet írtunk be. Ha biztos benne, hogy az oszlopnév helyes, próbálja meg szögletes zárójelek vagy idézőjelek használatával csatolni az oszlopazonosítót.
+ *SQL logikai \<hiba\>az SQL kulcsszó közelében,* ami azt jelzi, hogy szintaktikai hiba léphet fel a megadott kulcsszó előtt

  
|Kivételüzenetek|
|------------------------|
|Az SQL-parancsfájl helytelen.|
|A(z) "{sql_query}" SQL-lekérdezés nem megfelelő.|
|A(z) "{sql_query}" SQL-lekérdezés nem megfelelő. Kivételüzenet: {exception}.|


## <a name="error-0070"></a>0070-es hiba  
 Kivétel akkor fordul elő, amikor nem létező Azure-tábla elérésére próbál hozzáférni.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, amikor megpróbál hozzáférni egy nem létező Azure-tábla. Ez a hibaüzenet akkor jelenik meg, ha megad egy táblát az Azure Storage-ban, amely nem létezik az Azure Table Storage olvasásakor vagy írásakor. Ez akkor fordulhat elő, ha elgépeli a kívánt tábla nevét, vagy ha nem egyezik meg a célnév és a tárolótípusa. Például egy táblából kívánt olvasni, de helyette egy blob nevét adta meg.  

**Felbontás:** A tábla helyességének ellenőrzéséhez látogasson el újra a modulra.  

|Kivételüzenetek|
|------------------------|
|Az Azure-tábla nem létezik.|
|A(z) "{table_name}" Azure-tábla nem létezik.|


## <a name="error-0072"></a>0072-es hiba  
 Kivétel történik a kapcsolat időtúllépése esetén.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, amikor egy kapcsolat időtúllépés. Ez a hibaüzenet akkor jelenik meg, ha jelenleg csatlakozási problémák vannak az adatforrással vagy a célállomással, például lassú az internetkapcsolat, vagy ha az adatkészlet nagy, és/vagy az adatokban olvasandó SQL-lekérdezés bonyolult feldolgozást hajt végre.  

**Felbontás:** Határozza meg, hogy jelenleg vannak-e problémák az Azure storage-hoz vagy az internethez való lassú csatlakozással.  

|Kivételüzenetek|
|------------------------|
|A kapcsolat időtúllépéstörtént.|


## <a name="error-0073"></a>0073-as hiba  
 Kivétel akkor fordul elő, ha hiba történik az oszlop másik típussá konvertálása közben.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem lehet konvertálni oszlop másik típusú.  Ez a hibaüzenet akkor jelenik meg, ha egy modulhoz egy adott típusra van szükség, és az oszlopot nem lehet új típusra konvertálni.  

**Felbontás:** Módosítsa a bemeneti adatkészletet úgy, hogy az oszlop a belső kivétel alapján konvertálható legyen.  

|Kivételüzenetek|
|------------------------|
|Nem sikerült konvertálni az oszlopot.|
|Nem sikerült az oszlopot {target_type} konvertálni.|


## <a name="error-0075"></a>0075-ös hiba  
Kivétel akkor fordul elő, ha egy adatkészlet kvantálásakor érvénytelen binning függvényt használnak.  

Ez a hiba az Azure Machine Learning akkor fordul elő, ha nem támogatott metódussal próbál adatokat binnó, vagy ha a paraméterkombinációk érvénytelenek.  

**Felbontás:**

Hibakezelés ehhez az eseményhez az Azure Machine Learning egy korábbi verziójában került bevezetésre, amely lehetővé tette a binning módszerek több testreszabását. Jelenleg minden binning módszer egy legördülő listából való kiválasztáson alapul, így technikailag már nem lehet ezt a hibát beszerezni.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->

|Kivételüzenetek|
|------------------------|
|Érvénytelen binning függvény használatban.|


## <a name="error-0077"></a>0077-es hiba  
 Kivétel akkor fordul elő, ha ismeretlen blob fájl írási mód telt el.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy érvénytelen argumentumot a blob fájl cél vagy forrás specifikációi.  

**Felbontás:** Az Azure blob storage-ba és onnan adatokat importáló vagy exportáló szinte összes modulban az írási módot vezérlő paraméterértékek legördülő lista használatával vannak hozzárendelve; ezért érvénytelen értéket nem lehet átadni, és ez a hiba nem jelenhet meg. Ez a hiba egy későbbi kiadásban elavult.  

|Kivételüzenetek|
|------------------------|
|Nem támogatott blob írási mód.|
|Nem támogatott blob írási mód: {blob_write_mode}.|


## <a name="error-0078"></a>0078-as hiba  
 Kivétel akkor fordul elő, ha az [Adatok importálása](import-data.md) HTTP-beállítás átirányítást jelző 3xx állapotkódot kap.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, amikor a HTTP-beállítás [adatok importálása](import-data.md) kap egy 3xx (301, 302, 304, stb) állapotkód jelzi átirányítást. Ez a hibaüzenet akkor jelenik meg, ha olyan HTTP-forráshoz próbál csatlakozni, amely átirányítja a böngészőt egy másik oldalra. Biztonsági okokból a webhelyek átirányítása nem engedélyezett adatforrásként az Azure Machine Learning számára.  

**Felbontás:** Ha a webhely megbízható webhely, adja meg közvetlenül az átirányított URL-címet.  

|Kivételüzenetek|
|------------------------|
|Http átirányítás nem engedélyezett.|


## <a name="error-0079"></a>0079-es hiba  
 Kivétel akkor fordul elő, ha az Azure storage tároló neve helytelenül van megadva.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha az Azure storage tároló neve helytelenül van megadva. Ez a hibaüzenet akkor jelenik meg, ha nem adta meg mind a tárolót, mind a blob (fájl) nevét **a Blob elérési útja használatával, amely a tárolóbeállítással kezdődik** az Azure Blob Storage-ba íráskor.  

**Felbontás:** Keresse fel újra az [Adatok exportálása](export-data.md) modult, és ellenőrizze, hogy a blob megadott elérési útja tartalmazza-e a tárolót és a fájlnevet a **formátumtárolóban/fájlnévben.**  

|Kivételüzenetek|
|------------------------|
|Az Azure storage tároló neve helytelen.|
|Az Azure storage tárolóneve "{container_name}" helytelen; a formátumtároló/blob tárolónevének várt.|


## <a name="error-0080"></a>0080-as hiba  
 Kivétel akkor fordul elő, ha a modul nem engedélyezi az összes hiányzó értéket tartalmazó oszlopot.  

 Ez a hiba az Azure Machine Learning akkor keletkezik, ha egy vagy több oszlop a modul által felhasznált tartalmazza az összes hiányzó értékeket. Ha például egy modul az egyes oszlopok összesített statisztikáit számolja, nem működhet olyan oszlopban, amely nem tartalmaz adatokat. Ilyen esetekben a modul végrehajtása ezzel a kivétellel leáll.  

**Felbontás:** Keresse fel újra a bemeneti adatkészletet, és távolítsa el az összes hiányzó értéket tartalmazó oszlopokat.  

|Kivételüzenetek|
|------------------------|
|Az összes hiányzó értékkel rendelkező oszlopok nem engedélyezettek.|
|A(z) {col_index_or_name} oszlopban minden érték hiányzik.|


## <a name="error-0081"></a>0081-es hiba  
 Kivétel történik a PCA modulban, ha a csökkentendő dimenziók száma megegyezik a bemeneti adatkészlet jellemzőoszlopainak számával, amely legalább egy ritka jellemzőoszlopot tartalmaz.  

 Ez a hiba az Azure Machine Learning ben a következő feltételek teljesülnek: (a) a bemeneti adatkészlet rendelkezik legalább egy ritka oszlop, és (b) a kért dimenziók végleges száma megegyezik a bemeneti dimenziók száma.  

**Felbontás:** Fontolja meg a dimenziók számának csökkentését a kimenetben, hogy kevesebb legyen, mint a bemeneti dimenziók száma. Ez jellemző a PCA alkalmazásaira.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->

|Kivételüzenetek|
|------------------------|
|A ritka jellemzőoszlopokat tartalmazó dimenziók számát tartalmazó adatkészletek számának kisebbnek kell lennie, mint a jellemzőoszlopok száma.|


## <a name="error-0082"></a>0082-es hiba  
 Kivétel akkor fordul elő, ha egy modell nem lehet sikeresen deszerializálódott.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha egy mentett gépi tanulási modell vagy átalakítás nem tölthető be egy újabb verziója az Azure Machine Learning futásidejű eredményeként egy törés változás.  

**Felbontás:** A modellt vagy átalakítást előállító betanítási folyamatot újra kell futtatni, és a modellt vagy átalakítást újra kell menteni.  

|Kivételüzenetek|
|------------------------|
|A modell nem deszerializálható, mert valószínűleg szerializált egy régebbi szerializálási formátummal. Próbálkozzon újra a modellel, majd mentse újra.|


## <a name="error-0083"></a>0083-as hiba  
 Kivétel tanusít, ha a képzéshez használt adatkészlet nem használható konkrét tanulótípushoz.  

 Ez a hiba az Azure Machine Learning akkor keletkezik, ha az adatkészlet nem kompatibilis a tanuló betanítása. Előfordulhat például, hogy az adatkészlet minden sorban legalább egy hiányzó értéket tartalmaz, és ennek eredményeképpen a teljes adatkészletet kihagyja a betanítás során. Más esetekben egyes gépi tanulási algoritmusok, például az anomáliadetektálás nem várható címkék jelen, és eldobhatja ezt a kivételt, ha címkék vannak jelen az adatkészletben.  

**Felbontás:** Tekintse meg a bemeneti adatkészlet követelményeinek ellenőrzésére használt tanuló dokumentációját. Vizsgálja meg az oszlopokat, hogy az összes szükséges oszlop jelen van-e.  

|Kivételüzenetek|
|------------------------|
|A betanításhoz használt adatkészlet érvénytelen.|
|A(z) {data_name} érvénytelen adatokat tartalmaz a betanításhoz.|
|A(z) {data_name} érvénytelen adatokat tartalmaz a betanításhoz. Tanuló típusa: {learner_type}.|
|A(z) {data_name} érvénytelen adatokat tartalmaz a betanításhoz. Tanuló típusa: {learner_type}. Indok: {reason}.|
|Nem sikerült alkalmazni a(z) "{action_name}" műveletet a(z) {data_name} betanítási adatokra. Indok: {reason}.|


## <a name="error-0084"></a>0084-es hiba  
 Kivétel akkor fordul elő, amikor egy R-parancsfájlból készített pontszámok kiértékelése. Ez jelenleg nem támogatott.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha megpróbálja használni a modulok egyikének kiértékelésére egy modell kimeneti egy R-parancsfájl, amely pontszámokat tartalmaz.  

**Felbontás:**

|Kivételüzenetek|
|------------------------|
|Az egyéni modell által készített pontszámok kiértékelése jelenleg nem támogatott.|


## <a name="error-0085"></a>0085-ös hiba  
 Kivétel akkor fordul elő, ha a parancsfájl kiértékelése hiba miatt sikertelen.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha olyan egyéni parancsfájlt futtat, amely szintaktikai hibákat tartalmaz.  

**Felbontás:** Tekintse át a kódot egy külső szerkesztőben, és ellenőrizze a hibákat.  

|Kivételüzenetek|
|------------------------|
|Hiba a parancsfájl kiértékelése során.|
|A parancsfájlkiértékelése során a következő hiba történt, további információért tekintse meg a kimeneti naplót:<br />---------- Hibaüzenet indítása {script_language} értelmező ----------<br />{üzenet}<br />---------- Hibaüzenet vége a(z) {script_language} értelmező ----------|


## <a name="error-0090"></a>0090-es hiba  
 Kivétel akkor fordul elő, amikor a Hive-tábla létrehozása sikertelen.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha [az Adatok exportálása](export-data.md) vagy egy másik lehetőség az adatok mentése egy HDInsight-fürtbe, és a megadott Hive-tábla nem hozható létre.  

**Felbontás:** Ellenőrizze a fürthöz társított Azure storage-fiók nevét, és ellenőrizze, hogy ugyanazt a fiókot használja-e a modul tulajdonságaiban.  

|Kivételüzenetek|
|------------------------|
|A Hive-tábla nem hozható létre. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure storage-fiók neve megegyezik a modulparaméteren keresztül átadott névvel.|
|A(z) "{table_name}" Hive-tábla nem hozható létre. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure storage-fiók neve megegyezik a modulparaméteren keresztül átadott névvel.|
|A(z) "{table_name}" Hive-tábla nem hozható létre. HDInsight-fürt esetén győződjön meg arról, hogy a fürthöz társított Azure-tárfiók neve "{cluster_name}".|


## <a name="error-0102"></a>0102-es hiba  
 Akkor dobva, ha egy ZIP-fájl nem bontható ki.  

 Ez a hiba az Azure Machine Learning ben akkor fordul elő, ha importálja a tömörített csomagot a .zip kiterjesztés, de a csomag vagy nem egy zip fájlt, vagy a fájl nem használ támogatott zip formátumban.  

**Felbontás:** Győződjön meg arról, hogy a kijelölt fájl érvényes .zip fájl, és hogy a támogatott tömörítési algoritmusok egyikével tömörítették.  

 Ha ez a hibaüzenet jelenik meg az adatkészletek tömörített formátumban történő importálásakor, ellenőrizze, hogy az összes tartalmazott fájl a támogatott fájlformátumok egyikét használja-e, és Unicode formátumú-e.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->

 Próbálja meg elolvasni a kívánt fájlokat egy új tömörített tömörített mappába, és próbálja meg újra hozzáadni az egyéni modult.  

|Kivételüzenetek|
|------------------------|
|A megadott ZIP-fájl formátuma nem megfelelő.|


## <a name="error-0105"></a>0105-ös hiba  
 Ez a hiba akkor jelenik meg, ha egy moduldefiníciós fájl nem támogatott paramétertípust tartalmaz  
  
 Ez a hiba az Azure Machine Learning akkor keletkezik, amikor a létrehozott egy egyéni modul XML-definíciót, és a típusú paraméter vagy argumentum a definícióban nem egyezik meg a támogatott típusú.  
  
**Felbontás:** Győződjön meg arról, hogy az egyéni modul XML-definíciós fájljában lévő **arg** elemek típustulajdonsága támogatott típus.  
  
|Kivételüzenetek|  
|------------------------|  
|Nem támogatott paramétertípus.|  
|Nem támogatott paramétertípus '{0}' megadva.|  


## <a name="error-0107"></a>0107-es hiba  
 Akkor dobva, ha egy moduldefiníciós fájl nem támogatott kimeneti típust határoz meg  
  
 Ez a hiba az Azure Machine Learning akkor keletkezik, ha egy kimeneti port típusa egy egyéni modul XML-definíció nem felel meg a támogatott típusú.  
  
**Felbontás:** Győződjön meg arról, hogy az egyéni modul XML-definíciós fájljában lévő Kimeneti elem típustulajdonsága támogatott típus.  
  
|Kivételüzenetek|  
|------------------------|  
|Nem támogatott kimeneti típus.|  
|Nem támogatott kimeneti típus: "{output_type}" megadva.|  


## <a name="error-0125"></a>0125-ös hiba  
 Ha több adatkészlet sémája nem egyezik.  

**Felbontás:**

|Kivételüzenetek|
|------------------------|
|Az adatkészletséma nem egyezik.|


## <a name="error-0127"></a>0127-es hiba  
 A képképpont mérete meghaladja az engedélyezett korlátot  

 Ez a hiba akkor fordul elő, ha a képeket egy kép adatkészlet besorolásés a képek nagyobbak, mint a modell képes kezelni.  

 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  

-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->

|Kivételüzenetek|
|------------------------|
|A képképpont mérete meghaladja az engedélyezett korlátot.|
|A(z) "{file_path}" fájlképképképképmérete meghaladja a megengedett korlátot: "{size_limit}".|


## <a name="error-0128"></a>0128-as hiba  
 A kategorikus oszlopok feltételes valószínűségei száma meghaladja a korlátot.  

**Felbontás:**

|Kivételüzenetek|
|------------------------|
|A kategorikus oszlopok feltételes valószínűségei száma meghaladja a korlátot.|
|A kategorikus oszlopok feltételes valószínűségei száma meghaladja a korlátot. A problémás "{column_name_or_index_1}" és "{column_name_or_index_2}" oszlopok a problémás pár.|


## <a name="error-0129"></a>0129-es hiba  
 Az adatkészlet oszlopainak száma meghaladja az engedélyezett korlátot.  

**Felbontás:**

|Kivételüzenetek|
|------------------------|
|Az adatkészlet oszlopainak száma meghaladja az engedélyezett korlátot.|
|A(z) "{dataset_name}" adatkészletében az oszlopok száma meghaladja az engedélyezettértéket.|
|A(z) "{dataset_name}" adatkészletében lévő oszlopok száma meghaladja a(z) "{component_name}" megengedett korlátot.|
|A(z) "{dataset_name}" adatkészletében lévő oszlopok száma meghaladja a(z) "{limit_columns_count}" engedélyezett "{component_name limit_columns_count}" korlátot.|


## <a name="error-0134"></a>0134-es hiba
Kivétel akkor fordul elő, ha a feliratoszlop hiányzik, vagy nincs elegendő számú címkézett sor.  

Ez a hiba akkor fordul elő, ha a modulnak feliratoszlopra van szüksége, de nem vett fel egyet az oszlopkijelölésben, vagy a címkeoszlop túl sok értéket tartalmaz.

Ez a hiba akkor is előfordulhat, ha egy korábbi művelet úgy módosítja az adatkészletet, hogy nincs elegendő sor egy alsóbb rétegbeli művelethez. Tegyük fel például, hogy a Partíció és a **Minta** modul ban egy kifejezést használ az adatkészlet értékek szerint történő felosztásához. Ha nem talál egyezést a kifejezéshez, a partícióból származó adatkészletek egyike üres lesz.

Megoldás: 

 Ha az oszlopkijelölésben szerepel egy címkeoszlop, de a program nem ismeri fel, a [Metaadatok szerkesztése](edit-metadata.md) modul segítségével jelölje meg feliratoszlopként.

  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->
  Ezután a [Hiányzó adatok tisztítása](clean-missing-data.md) modullal eltávolíthatja a hiányzó értékekkel rendelkező sorokat a címkeoszlopban. 

 Ellenőrizze a bemeneti adatkészletek, hogy azok érvényes adatokat tartalmaznak, és elegendő sorokat, hogy megfeleljen a művelet követelményeinek. Sok algoritmus hibaüzenetet hoz létre, ha minimális számú adatsort igényel, de az adatok csak néhány sort vagy csak fejlécet tartalmaznak.

|Kivételüzenetek|
|------------------------|
|Kivétel akkor fordul elő, ha a feliratoszlop hiányzik, vagy nincs elegendő számú címkézett sor.|
|Kivétel akkor fordul elő, ha a címkeoszlop hiányzik, vagy a(z) {required_rows_count} feliratos sornál kisebb.|
|Kivétel akkor fordul elő, ha a(z) {dataset_name} adatkészlet címkeoszlopa hiányzik, vagy a(z) {required_rows_count} feliratozott sornál kisebb.|


## <a name="error-0138"></a>0138-as hiba  
 A memória kimerül, nem tudja befejezni a modul futtatását. Az adatkészlet felbontáscsökkentése segíthet a probléma enyhítésében.  

 Ez a hiba akkor fordul elő, ha a futó modul több memóriát igényel, mint az Azure-tárolóban elérhető. Ez akkor fordulhat elő, ha nagy adatkészlettel dolgozik, és az aktuális művelet nem fér bele a memóriába.  

**Felbontás:** Ha nagy adatkészletet próbál olvasni, és a művelet nem hajtható végre, az adatkészlet felbontáscsökkentési segíthet.  

  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  

 Sometimes transient loads can lead to this error. Machine support also changes over time. 

 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->

|Kivételüzenetek|
|------------------------|
|A memória kimerül, nem tudja befejezni a modul futtatását.|
|A memória kimerül, nem tudja befejezni a modul futtatását. Részletek: {details}|


## <a name="error-0141"></a>0141-es hiba  
 Kivétel, ha a kijelölt numerikus oszlopok száma és a kategorikus és karakterláncoszlopok ban lévő egyedi értékek túl kicsik.  

 Ez a hiba az Azure Machine Learning akkor fordul elő, ha nincs elég egyedi értékek a kiválasztott oszlopban a művelet végrehajtásához.  

**Felbontás:** Egyes műveletek statisztikai műveleteket hajtanak végre a jellemző- és kategorikus oszlopokon, és ha nincs elég érték, előfordulhat, hogy a művelet sikertelen vagy érvénytelen eredményt ad vissza. Ellenőrizze az adatkészletet, hogy hány érték van a szolgáltatás- és címkeoszlopokban, és állapítsa meg, hogy a végrehajtani kívánt művelet statisztikailag érvényes-e.  

 Ha a forrásadatkészlet érvényes, azt is ellenőrizheti, hogy néhány upstream adatkezelés vagy metaadat-művelet módosította-e az adatokat, és eltávolított-e néhány értéket.  

 Ha a termelési-értékesítési láncban feljebb lévő műveletek közé tartozik a felosztás, a mintavételezés vagy az újramintavételezés, ellenőrizze, hogy a kimenetek tartalmazzák-e a várt számú sort és értéket.  

|Kivételüzenetek|
|------------------------|
|A kijelölt numerikus oszlopok és a kategorikus és karakterláncoszlopok egyedi értékei túl kicsik.|
|A kijelölt numerikus oszlopok és a categorikus és karakterláncoszlopok (jelenleg {actual_num}) egyedi értékeinek teljes számának legalább {lower_boundary} kell lennie.|


## <a name="error-0154"></a>0154-es hiba  
 Kivétel akkor fordul elő, amikor a felhasználó nem kompatibilis oszloptípussal rendelkező kulcsoszlopok adatait próbálja összeilleszteni.

|Kivételüzenetek|
|------------------------|
|A kulcsoszlopelem-típusok nem kompatibilisek.|
|A kulcsoszlopelem-típusok nem kompatibilisek. (balra: {keys_left}; jobbra: {keys_right})|


## <a name="error-0155"></a>0155-ös hiba  
 Kivétel akkor fordul elő, ha az adatkészlet oszlopnevei nem karakterláncok.

|Kivételüzenetek|
|------------------------|
|A dataframe oszlopnevének karakterlánctípusúnak kell lennie. Az oszlopnevek nem karakterláncok.|
|A dataframe oszlopnevének karakterlánctípusúnak kell lennie. A(z) {column_names} oszlopnevek nem karakterláncok.|


## <a name="error-0156"></a>0156-os hiba  
 Kivétel akkor fordul elő, ha nem sikerült adatokat olvasni az Azure SQL Database.Exception occurs when failed to read data from Azure SQL Database.

|Kivételüzenetek|
|------------------------|
|Nem sikerült adatokat olvasni az Azure SQL Database-ből.|
|Nem sikerült adatokat olvasni az Azure SQL Database-ből: {detailed_message} DB: {database_server_name}:{database_name} Lekérdezés: {sql_statement}|


## <a name="error-0157"></a>0157-es hiba  
 Az adattár nem található.

|Kivételüzenetek|
|------------------------|
|Az adattár adatai érvénytelenek.|
|Az adattár adatai érvénytelenek. Nem sikerült leadni a(z) "{datastore_name}" AzureML-adattaboltot a(z) "{workspace_name}" munkaterületen.|


## <a name="error-0158"></a>0158-as hiba
 Ha egy átalakítási könyvtár érvénytelen, az adatlap rakoncátusa érvénytelen.

|Kivételüzenetek|
|------------------------------------------------------------|
|A given TransformationDirectory érvénytelen.|
|A(z) "{arg_name}" TransformationDirectory érvénytelen. Indok: {reason}. Futtassa újra a betanítási kísérletet, amely létrehozza az Átalakítás fájlt. Ha a betanítási kísérletet törölték, hozza létre újra és mentse az Átalakítás fájlt.|


## <a name="error-1000"></a>1000-es hiba  
Belső könyvtárkivétel.  

Ez a hiba az egyébként nem kezelt belső motorhibák rögzítésére szolgál. Ezért a hiba oka a hibát létrehozó modultól függően eltérő lehet.  

További segítségért azt javasoljuk, hogy tegye közzé a hibát kísérő részletes üzenetet az Azure Machine Learning fórum, a forgatókönyv leírásával együtt, beleértve a bemenetként használt adatokat. Ez a visszajelzés segít nekünk a hibák rangsorolásában és a további munka legfontosabb kérdéseinek azonosításában.  

|Kivételüzenetek|
|------------------------|
|Könyvtárkivétel.|
|Könyvtár kivétel: {exception}.|
|Ismeretlen könyvtárkivétel: {exception}. {customer_support_guidance}.|


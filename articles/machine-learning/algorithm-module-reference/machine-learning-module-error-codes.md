---
title: A modul kapcsolatos hibák elhárítása
titleSuffix: Azure Machine Learning service
description: Az Azure Machine Learning Studiót hibakódok modul-kivételek elhárításához
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09a2b616e2bba93be86241c64d37daec7d6dea3b
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029760"
---
# <a name="exceptions-and-error-codes-for-algorithm--module-reference"></a>Kivételek és Algoritmusokra és modulokra vonatkozó referenciák hibakód alapján

További tudnivalók a hibaüzenetek és a modulok használata az Azure Machine Learning Studióban esetleg felmerülő kivétel kódokat. 

A probléma megoldásához keresse meg a hiba gyakori okai olvashat ebben a cikkben. Beolvasni a hibaüzenetet a teljes szöveg a Studióban két módja van:  
 
- Kattintson a hivatkozásra **kimeneti napló megtekintése**, a jobb oldali ablaktáblán, és görgessen le. A részletes hibaüzenetet az utolsó két sor az ablak jelenik meg.  
  
- Válassza ki a modul, amely rendelkezik a hiba, és kattintson a piros X. Csak a megfelelő hibaüzenet jelenik meg.  
  
Ha a hiba üzenet szövege nem hasznos, küldjön nekünk a környezet és a kívánt hozzáadást, illetve módosítások kapcsolatos információkat. Akkor a hiba a témakör a visszajelzés elküldése, vagy keresse fel a [Azure Machine Learning STUDIO-fórumon](https://aka.ms/aml-forum-studio) és új kérdést tenne fel.  


## <a name="error-0001"></a>Hiba történt a 0001  
 Kivétel lép fel, ha az adatkészlet egy vagy több megadott oszlop nem található.  
  
 Ez a hiba fog kapni, ha egy oszlop kiválasztása a modul történik, de a kijelölt oszlop nem szerepel a bemeneti adatkészlet. Ez a hiba akkor fordulhat elő, ha manuálisan adta egy oszlop nevében, vagy ha az Oszlopválasztó biztosított javasolt oszlop, amely a kísérlet futtatásakor az adatkészlet nem létezik.  
  
**Megoldás:** Nyissa meg újra a modult, ehhez a kivételhez értesítő, és ellenőrizze, hogy az oszlop neve vagy nevei megfelelőek, és hogy létezik-e a hivatkozott oszlopok.  
  
|Kivétel üzenetek|  
|------------------------|  
|Egy vagy több megadott oszlopok nem találhatók.|  
|Oszlop neve vagy indexe "{0}" nem található|  
|Oszlop neve vagy indexe "{0}"nem létezik a"{1}"|  
 

## <a name="error-0002"></a>0002 hiba  
 Kivétel lép fel, ha egy vagy több paraméter nem sikerült az elemzett vagy átváltott a megadott típus által előírt cél metódus típusa.  
  
 Ez a hiba akkor fordul elő, az Azure Machine Learning adja meg a paramétert a bemeneti adatként, és az érték típusa eltér a várt típus, és implicit konverziója nem hajtható végre.  
  
**Megoldás:** A modul követelményeinek ellenőrzése és döntse el, melyik érték típusát kötelező megadni (string, integer, double, stb.)  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült elemezni a paraméter|  
|Nem sikerült elemezni a "{0}" paraméter|  
|Nem sikerült elemezni a (átalakítás) "{0}"paraméter"{1}"|  
|Nem sikerült konvertálni "{0}"paraméter a"{1}"záró"{2}"|  
|Nem sikerült konvertálni "{0}"paraméter értéke"{1}"feladó"{2}"záró"{3}"|  
|Érték nem konvertálható "{0}"oszlopában"{1}"feladó"{2}"záró"{3}"a használati formátum"{4}" megadott|  
  

## <a name="error-0003"></a>Hiba történt a 0003  
 Kivétel lép fel, ha egy vagy több bemenetei null értékű vagy üres.  
  
 Az Azure Machine Learning ezt a hibaüzenetet fog kapni, ha bármely bemeneti vagy a modul paraméterei null értékű vagy üres.  Ez a hiba fordulhatnak elő, például, ha nem adta meg minden paraméter értéke. Azt is történhet, ha egy adatkészletet, amely értékek hiányoznak, vagy egy üres adatkészlet választása.  
  
**Megoldás:**
 
+ Nyissa meg a modul, amely a kivétel előállított, és győződjön meg arról, hogy az összes bemeneti lett megadva. Győződjön meg arról, hogy minden kötelező bemeneti vannak megadva. 
+ Győződjön meg arról, hogy az Azure storage-ból betöltött adatokat, és elérhető, valamint, hogy a fiók neve vagy a kulcs nem változott.  
+ Ellenőrizze a bemeneti adatok hiányzó értékeket, vagy null értékeket.
+ Ha egy lekérdezést használ adatforrásként, ellenőrizze a várt formátum a visszaadott adat. 
+ Ellenőrizze az elgépelések vagy egyéb adatok specifikace változásait.
  
|Kivétel üzenetek|  
|------------------------|  
|Egy vagy több bemeneti null értékű vagy üres|  
|Bemeneti "{0}" NULL értékű vagy üres|  
  

## <a name="error-0004"></a>0004 hiba  
 Kivétel lép fel, ha a paraméter értéke kisebb vagy egyenlő a megadott értéket.  
  
 Ezt a hibaüzenetet fog kapni az Azure Machine Learning, ha a paraméter az üzenetben a modul az adatok feldolgozásához szükséges határ érték alá.  
  
**Megoldás:** Nyissa meg újra a modult, a kivétel értesítő, és módosítsa a paramétert, a megadott értéknek nagyobbnak kell lennie.  
  
|Kivétel üzenetek|  
|------------------------|  
|A paraméter határ értékének nagyobbnak kell lennie.|  
|A paraméter "{0}" értéknek nagyobbnak kell lennie {1}.|  
|A paraméter "{0}"értéke"{1}" amely nagyobbnak kell lennie {2}|  
  


## <a name="error-0005"></a>0005 hiba  
 Kivétel lép fel, ha a paraméter kisebb, mint egy adott érték.  
  
 Az Azure Machine Learning ezt a hibaüzenetet kapja, ha a paraméter az üzenet nem éri el, vagy más, mint a határérték, a modul az adatok feldolgozásához szükséges lesz.  
  
**Megoldás:** Nyissa meg újra a modult, a kivétel értesítő, és módosítsa a paramétert nagyobb vagy azzal egyenlő a megadott érték.  
  
|Kivétel üzenetek|  
|------------------------|  
|A paraméter kisebb értéket a határhoz kell lennie.|  
|A paraméter "{0}" értéknek nagyobbnak vagy azzal egyenlőnek kell lennie. {1}.|  
|A paraméter "{0}"értéke"{1}" amely nagyobb vagy azzal egyenlőnek kell lennie {2}.|  
  

## <a name="error-0006"></a>0006 hiba  
 Kivétel lép fel, ha a paraméter nagyobb vagy egyenlő a megadott érték.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a paraméter az üzenetben nagyobb vagy egyenlő a határérték, a modul az adatok feldolgozásához szükséges.  
  
**Megoldás:** Nyissa meg újra a modult, a kivétel értesítő, és módosítsa a paramétert, a megadott értéknek kisebbnek kell lennie.  
  
|Kivétel üzenetek|  
|------------------------|  
|Paraméterek eltérés. Paraméterek egyike lehet nagyobb, mint a többi.|  
|A paraméter "{0}"értéknek kisebbnek kell lennie a paraméter"{1}" értéket.|  
|A paraméter "{0}"értéke"{1}" kell lennie, amely kisebb, mint {2}.|  
  

## <a name="error-0007"></a>0007 hiba  
 Kivétel lép fel, ha a paraméter értéke nagyobb, mint egy adott érték.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a modul a tulajdonságok között megadott nagyobb, mint az engedélyezett értéket. Például előfordulhat, hogy megad egy támogatott dátumok tartományán kívüli adatokat, vagy jelezheti, hogy öt oszlopnak használható-e, ha csak három oszlopot érhetők el. 
 
 Ez a hiba is meg kell egyeznie a valamilyen módon két adatkészletek megadása esetén előfordulhat, hogy megjelenik. Ha vannak oszlopok átnevezése, és adja meg az oszlopok index által, például a neveket adhat meg számának egyeznie kell a szám oszlop indexek. Egy másik példa lehet egy matematikai művelet által használt két oszlop, amelyben az oszlopok rendelkeznie kell azonos számú sort. 
  
**Megoldás:**
 
 + Nyissa meg a szóban forgó modul, és áttekintheti azokat numerikusra változtatja a beállításokat.
 + Győződjön meg arról, hogy minden olyan paraméterértékeket címtartományba támogatott érték az adott tulajdonságnál.
 + Ha a modul több bemenet tart, győződjön meg arról, hogy bemenetek az azonos méretű.
<!-- + If the module has multiple properties that can be set, ensure that related properties have appropriate values. For example, when using [Group Data into Bins](group-data-into-bins.md), if you use the option to specify custom bin edges, the number of bins must match the number of values you provide as bin boundaries.-->
 + Ellenőrizze, hogy módosultak-e az adatkészlethez vagy adatforrást. Néha egy értéket, amely működött az adatok egy korábbi verziójával meghiúsul után, az oszlopok száma, az oszlop adattípusait vagy az adatok mérete módosult.  
  
|Kivétel üzenetek|  
|------------------------|  
|Paraméterek eltérés. Kisebb vagy egyenlő a másikra a paraméterek egyikét kell lennie.|  
|A paraméter "{0}"értéknek kisebbnek kell lennie, mint vagy egyenlő, paraméterben"{1}" értéket.|  
|A paraméter "{0}"értéke"{1}" kell lennie, amely kisebb vagy egyenlő {2}.|  
  

## <a name="error-0008"></a>0008 hiba  
 Kivétel lép fel, ha a paraméter nem tartományban.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a paraméter az üzenetben kívül esik a határokon, a modul az adatok feldolgozásához szükséges.  
  
 Például ez a hiba jelenik meg, ha megpróbálja használni [sorok hozzáadása](add-rows.md) úgy, hogy két adatkészletet, amely az oszlopok száma eltér.  
  
**Megoldás:** Nyissa meg újra a modult, a kivétel értesítő, és módosítsa a paraméterként megadott tartományon belül.  
  
|Kivétel üzenetek|  
|------------------------|  
|Paraméter értéke nem a megadott tartományban.|  
|A paraméter "{0}" érték nem szerepel a tartományban.|  
|A paraméter "{0}" értéket kell tartományán [{1}, {2}].|  
  

## <a name="error-0009"></a>0009 hiba  
 Kivétel lép fel, ha az Azure storage-fiók neve vagy a tároló neve helytelenül van megadva.  
  
Ez a hiba akkor fordul elő, az Azure Machine Learning Studióban az Azure storage-fiókhoz tartozó paramétert is meghatároz, de a neve vagy a jelszó nem lehet feloldani. Hibákat a jelszavak vagy fiókneveket fordulhat elő, ennek számos oka lehet:
 
 + A fiók nem megfelelő típusú. Néhány új fióktípusok nem támogatottak a Machine Learning Studio segítségével. Lásd: [adatok importálása](import-data.md) részleteiről.
 + Helytelen fiók nevet meg
 + A fiók már nem létezik.
 + A storage-fiók jelszava helytelen, vagy megváltozott
 + A tároló neve nem adott meg, vagy a tároló nem létezik.
 + Teljes mértékben nem adott meg a fájl elérési útját (a blob elérési útja)
   
**Megoldás:**

Ilyen jellegű problémák gyakran fordul elő, amikor megpróbálja manuálisan adja meg a fiók neve, a jelszó és a tároló elérési útja. Azt javasoljuk, hogy az új varázslót az használja a [adatok importálása](import-data.md) modult, amely segítségével kereshet meg és nevek ellenőrzése.

Is ellenőrizheti, hogy a fiók, tárolót vagy blobot törölve lett. Egy másik az Azure storage-segédprogram használatával győződjön meg arról, hogy a fiók nevét és jelszavát megadva, és arról, hogy a tároló létezik. 

Néhány újabb fióktípusok nem támogatottak az Azure Machine Learning. Például az új "Forró" vagy "hidegnek" nevezett tárolási típusok nem használhatók a machine Learning szolgáltatáshoz. Klasszikus tárfiókokat és a storage-fiókok létrehozni, mert az "Általános célú" jól működnek.

Ha megadott egy blob teljes elérési útja, győződjön meg arról, hogy az elérési út van megadva **tároló/blobname**, és hogy a tároló, mind a blob létezik a fiókban.  
  
 Az elérési út nem tartalmazhat perjelet vezető. Például **blob/tároló/** helytelen, és meg kell adni **tárolót vagy blobot**.  

  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-fiók neve vagy a tároló neve nem megfelelő.|  
|Az Azure storage-fiók neve "{0}"vagy a tároló neve"{1}" nem megfelelő; a tárolót a blob nevét, formátumú tároló/várt.|  
  

## <a name="error-0010"></a>0010 hiba  
 Kivétel lép fel, ha a bemeneti adatkészletek oszlopnevek egyező, de nem kell rendelkeznie.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha az üzenet a oszlopindex eltérő oszlopneveket szerepel a két bemeneti adatkészletet.  
  
**Megoldás:** Használat [metaadatainak szerkesztése](edit-metadata.md) vagy módosíthatja az oszlop neve megegyezik a megadott oszlopindex az eredeti adathalmazból.  
  
|Kivétel üzenetek|  
|------------------------|  
|A bemeneti adatkészletek megfelelő indexszel rendelkező oszlopok neve nem lehet.|  
|Oszlopnevek nem azonosak oszlop {0} adatpontjához bemeneti adatkészletek ({1} és {2} jelölik).|  
  

## <a name="error-0011"></a>0011 hiba  
 Kivétel lép fel, ha az átadott oszlop argumentum nem vonatkozik egyik adatkészlet oszlop.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a megadott oszlop kiválasztása nem egyezik meg az adott adatkészlet oszlopok.  
  
 Ez a hiba is kaphat, ha egy oszlop nincs kiválasztva, és a modul működéséhez szükség legalább egy oszlopot.  
  
**Megoldás:** Módosítsa az oszlop kiválasztása a modulban, hogy a, érvénybe lépnek az oszlopok az adatkészletben.  
  
 Ha a modul megköveteli, hogy egy adott oszlop, például egy felirat oszlopot válasszon, ellenőrizze, hogy a jobb oldali oszlop van kijelölve.  
  
 Ha a nem megfelelő oszlop van kijelölve, távolítsa el őket, és futtassa újra a kísérletet.  
  
|Kivétel üzenetek|  
|------------------------|  
|Megadott oszlopkészlet nem vonatkozik egyik adatkészlet oszlop.|  
|A megadott oszlopkészlet "{0}" adatkészletoszlopok valamelyik nem vonatkozik.|  
  

## <a name="error-0012"></a>0012 hiba  
 Kivétel lép fel, ha az osztály példánya nem hozható létre átadott argumentumok együtt.  
  
**Megoldás:** Ez a hiba nem a felhasználó által döntéstámogató, és a egy későbbi kiadástól kezdve elavulttá válik.  
  
|Kivétel üzenetek|  
|------------------------|  
|Kellő modellezheti, először a modell betanításához.|  
|Kellő modell ({0}), betanított modellt használja.|  
  

## <a name="error-0013"></a>0013 hiba  
 Kivétel lép fel, ha a learner a modulba átadott érvénytelen típusú.  
  
 Ez a hiba a betanított modell nem kompatibilis a csatlakoztatott pontozási modul következik be. <!--For example, connecting the output of [Train Matchbox Recommender](train-matchbox-recommender.md) to [Score Model](score-model.md) (instead of [Score Matchbox Recommender](score-matchbox-recommender.md)) will generate this error when the experiment is run.  -->
  
**Megoldás:**

A képzési modul által előállított learner típusának meghatározása, és adja meg a pontozási modul, amely megfelelő a tanuló számára. 

Ha a modell volt képzett, speciális képzési modulok bármelyikével, csatlakozhat a betanított modell csak a megfelelő speciális pontozási modul. 


|Modell típusa|Képzési modul| Pontozó modul|
|----|----|----|
|minden besorolás|[Tanítási modell](train-model.md) |[Pontszám modell](score-model.md)|
|minden olyan regressziós modell|[Tanítási modell](train-model.md) |[Pontszám modell](score-model.md)|
<!--| Fürtszolgáltatás modellek| [Fürtözési modelljét train](train-clustering-model.md) vagy [Szögtartomány fürtszolgáltatás](sweep-clustering.md)| [Adatok hozzárendelése fürtökhöz](assign-data-to-clusters.md)|
| rendellenességek észlelése – egy szintű SVM | [Rendellenességek észlelése tanítási](train-anomaly-detection-model.md) |[Pontszám modell](score-model.md)|
| anomáliadetektálás - PEM |[Tanítási modell](train-model.md) |[Pontszám modell](score-model.md) </br> A modell kiértékelésére további lépésekre van szükség. |
| anomáliadetektálás - idősor|  [Time Series Anomáliadetektálás](time-series-anomaly-detection.md) |Modell betanítja adatokból, és értékeket állít elő. A modul nem hoz létre egy betanított learner, és nincs további pontozási nem szükséges. |
| javaslatok modell| [Matchbox ajánló betanítása](train-matchbox-recommender.md) | [Pontszám Matchbox ajánló](score-matchbox-recommender.md) |
| képek besorolása | [Lépcsőzetes elrendezés imagenet képek besorolása](pretrained-cascade-image-classification.md) | [Pontszám modell](score-model.md) |
|Vowpal Wabbit modellek| [7 – 4 modell betanításához a Vowpal Wabbit verzió](train-vowpal-wabbit-version-7-4-model.md) | [Vowpal Wabbit verzió 7 – 4 modell pontozása](score-vowpal-wabbit-version-7-4-model.md) |   
|Vowpal Wabbit modellek| [7 – 10 modell betanításához a Vowpal Wabbit verzió](train-vowpal-wabbit-version-7-10-model.md) | [Vowpal Wabbit verzió 7 – 10 modell pontozása](score-vowpal-wabbit-version-7-10-model.md) |
|Vowpal Wabbit modellek| [Vowpal Wabbit 8-as verzió modell betanítása](score-vowpal-wabbit-version-8-model.md) | [Pontszám Vowpal Wabbit 8-as verzió modell](score-vowpal-wabbit-version-8-model.md) |-->
  
|Kivétel üzenetek|  
|------------------------|  
|Érvénytelen típusú learner lett átadva.|  
|Learner "{0}" kifejezésben érvénytelen típusú.|  


## <a name="error-0014"></a>0014 hiba  
 Kivétel lép fel, ha az oszlop egyedi értékek száma nagyobb a megengedettnél.  
  
 Ez a hiba akkor fordul elő, ha egy oszlop túl sok egyedi értékeket tartalmaz.  Például előfordulhat, hogy ezt a hibaüzenetet Ha adja meg, hogy egy oszlop kategorikus adatként kell kezelni, de az oszlop, befejeződik a feldolgozás, hogy túl sok egyedi értékek vannak. Is láthatja ezt a hibát, ha a két bemenet egyedi értékek számának között eltérés van.   
  
**Megoldás:**

Nyissa meg a modul, amely a hibát okozta, és azonosíthatja a bemenetként használt oszlopok. Egyes modulok, a jobb gombbal az adatkészlet bemeneti és a select **Visualize** az egyes oszlopain, többek között a egyedi értékeket, és azok elosztásának megtekintheti a statisztikákat.

A csoportosítási vagy kategorizálási használni kívánt oszlopok lépéseket oszlopokban szereplő egyedi értékek számának csökkentése érdekében. Az oszlop adatok típusától függően különböző módon csökkentheti. 
<!--
+ For text data, you might be able to use [Preprocess Text](preprocess-text.md) to collapse similar entries. 
+ For numeric data, you can create a smaller number of bins using [Group Data into Bins](group-data-into-bins.md), remove or truncate values using [Clip Values](clip-values.md), or use machine learning methods such as [Principal Component Analysis](principal-component-analysis.md) or [Learning with Counts](data-transformation-learning-with-counts.md) to reduce the dimensionality of the data.  
-->
> [!TIP]
> Nem található a forgatókönyvének megfelelő megoldást? A visszajelzést a témakör, amely tartalmazza a hiba, és az adattípus generáló modul nevét és az oszlop számosságát. Lesz az információk biztosítása érdekében használjuk több megcélzott hibaelhárítási lépések a gyakran használt esetekben.   
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlop egyedi értékek száma, nagyobb a megengedettnél.|  
|Az oszlop egyedi értékek száma: "{0}" rekord száma meghaladja a {1}.|  
  

## <a name="error-0015"></a>0015 hiba  
 Kivétel lép fel, ha az adatbázis-kapcsolat sikertelen volt.  
  
 Ez a hiba fog kapni, ha egy hibás SQL-fiók neve, jelszavát, adatbázis-kiszolgáló vagy adatbázis nevét adja meg, vagy ha az adatbázis vagy a kiszolgáló hibái miatt nem létesíthető kapcsolat az adatbázissal.  
  
**Megoldás:** Ellenőrizze, hogy a fiók neve, a jelszó, az adatbázis-kiszolgáló és a adatbázis van megadva, és hogy a megadott fiók rendelkezik-e a megfelelő szintű engedélyeket. Ellenőrizze, hogy az adatbázis jelenleg elérhető-e.  
  
|Kivétel üzenetek|  
|------------------------|  
|Adatbázis-csatlakozási hiba.|  
|Adatbázis-kapcsolatot: {0}.|  
  


## <a name="error-0016"></a>0016 hiba  
 Kivétel lép fel, ha a bemeneti adatkészletek a modulba átadott kell kompatibilis oszlop típusa, de nem.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a két vagy több adatkészletet átadott oszlopok típusú nem kompatibilis egymással.  
  
**Megoldás:** Használat [metaadatainak szerkesztése](edit-metadata.md) vagy az eredeti bemeneti adatkészlet módosítása<!--, or use [Convert to Dataset](convert-to-dataset.md)--> annak érdekében, hogy az oszlopok milyen típusú kompatibilis.  
  
|Kivétel üzenetek|  
|------------------------|  
|A bemeneti adatkészletek megfelelő indexszel rendelkező oszlopok rendelkezik típusa nem kompatibilis.|  
|Oszlopok {0} és {1} nem kompatibilis.|  
|Elem típusú oszlophoz nem kompatibilisek az oszlop {0} adatpontjához bemeneti adatkészletek ({1} és {2} jelölik).|  
  

## <a name="error-0017"></a>0017 hiba  
 Kivétel lép fel, ha a kiválasztott oszlop adattípusú, amely nem támogatja az aktuális modult használ.  
  
 Például előfordulhat, hogy ez a hibaüzenet az Azure Machine Learning Ha oszlop választását is tartalmaz, amely nem tudja feldolgozni a modult, például egy karakterláncokat tartalmazó matematikai művelet oszlopot vagy pontszám oszlop egy kategorikus funkció oszlop esetén adattípusú oszlop szükséges.  
  
**Megoldás:**
 1. Az oszlop, amely a probléma azonosításához.
 2. Tekintse át a modul.
 3. Módosítsa az oszlop, hogy felel meg a követelményeknek. Számos, a következő modulok segítségével hajtsa végre a módosításokat, attól függően, oszlopot és a kívánt átalakítást lehet szükség:
    + Használat [metaadatainak szerkesztése](edit-metadata.md) az oszlopok adattípusának módosítása, vagy módosítsa az oszlophasználat funkcióval numerikus, nem kategorikus a kategorikus és így tovább.
<!--    + Use [Convert to Dataset](convert-to-dataset.md) to ensure that all included columns use data types that are supported by Azure Machine Learning.  If you cannot convert the columns, consider removing them from the input dataset.
    + Use the [Apply SQL Transformation](apply-sql-transformation.md) or [Execute R Script](execute-r-script.md) modules to cast or convert any columns that cannot be modified using [Edit Metadata](edit-metadata.md). These modules provide more flexibility for working with datetime data types.
    + For numeric data types, you can use the [Apply Math Operation](apply-math-operation.md) module to round or truncate values, or use the [Clip Values](clip-values.md) module to remove out of range values.  -->
 4. Végső megoldásként szüksége lehet az eredeti bemeneti adatkészlet módosítása.

> [!TIP]
> Nem található a forgatókönyvének megfelelő megoldást? A visszajelzést a témakör, amely tartalmazza a hiba, és az adattípus generáló modul nevét és az oszlop számosságát. Lesz az információk biztosítása érdekében használjuk több megcélzott hibaelhárítási lépések a gyakran használt esetekben. 
  
|Kivétel üzenetek|  
|------------------------|  
|Nem tudja feldolgozni a jelenlegi típusú oszlop. A modul által a típusa nem támogatott.|  
|Nem tudja feldolgozni a típusú oszlop {0}. A modul által a típusa nem támogatott.|  
|Nem tudja feldolgozni az oszlop "{1}" típusú {0}. A modul által a típusa nem támogatott.|  
|Nem tudja feldolgozni az oszlop "{1}" típusú {0}. A modul által a típusa nem támogatott. Paraméter neve: {2}|  
  

## <a name="error-0018"></a>0018 hiba  
 Kivétel lép fel, ha a bemeneti adatkészlet nem érvényes.  
  
**Megoldás:** Ez a hiba az Azure Machine Learning is megjelenhetnek számos környezetben, így nem egyetlen felbontása. Általában a hiba azt jelzi, hogy rendelkezik-e az adatok modulra bemenetként a helytelen számú oszlopot, vagy az adattípus nem egyezik meg a követelményeket a modult. Példa:  
  
-   A modul egy címke oszlopot igényel, de egyetlen oszlop sincs címke van megjelölve, vagy még nincs kiválasztva egy címke oszlopot.  
  
-   A modul megköveteli, hogy adatokat kell kategorikus azonban az adatok numerikus.  
  
<!---   The module requires a specific data type. For example, ratings provided to [Train Matchbox Recommender](train-matchbox-recommender.md) can be either numeric or categorical, but cannot be floating point numbers.  -->
  
-   Az adatok a megfelelő formátumú.  
  
-   Importált adatok obsahuje neplatné znaky, helytelen értékeket, vagy onnan máshová az identitástartomány-értékeknek.  
-   Az oszlop üres, vagy túl sok a hiányzó értékeket tartalmaz.  
  
 A követelményekkel és az adatok előfordulhat, hogy hogyan határozza meg, tekintse át a modult, amely bemenetként az adatkészletet használó fog tartozó súgó-témakör.  
  
 <!--We also recommend that you use [Summarize Data](summarize-data.md) or [Compute Elementary Statistics](compute-elementary-statistics.md) to profile your data, and use these modules to fix metadata and clean values: [Edit Metadata](edit-metadata.md) and [Clean Missing Data](clean-missing-data.md), [Clip Values](clip-values.md)-->.  
  
|Kivétel üzenetek|  
|------------------------|  
|Adatkészlet nem érvényes.|  
|{0} érvénytelen adatokat tartalmaz.|  
|{0} és {1} konzisztens oszlop szerinti legyen.|  
  

## <a name="error-0019"></a>0019 hiba  
 Kivétel lép fel, ha a rendezett értékeket tartalmazó oszlop várt, de nem létezik.  
  
 Az Azure Machine Learning a hibaüzenetet fog kapni, ha a megadott oszlop értékei sorrendben.  
  
**Megoldás:** Az oszlop értékeit rendezni a bemeneti adatkészlet manuális módosításával, majd futtassa újra a modult.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlop értékei nem rendezi a rendszer.|  
|Oszlop értékeinek "{0}" nincsenek rendezve.|  
|Oszlop értékeinek "{0}", "adatkészlet{1}" nincsenek rendezve.|  
  

## <a name="error-0020"></a>0020 hiba  
 Kivétel lép fel, amennyiben az adatkészletek a modulba átadott némelyike szereplő oszlopok száma túl kicsi.  
  
 Az Azure Machine Learning fogja ezt a hibaüzenetet kapja, ellenkező modul kijelölt elegendő számú oszloppal.  
  
**Megoldás:** Nyissa meg újra a modult, és győződjön meg arról, hogy Oszlopválasztó rendelkezik kiválasztott oszlopainak helyes számát.  
  
|Kivétel üzenetek|  
|------------------------|  
|Bemeneti adatkészlet szereplő oszlopok száma kisebb, mint a minimális.|  
|Bemeneti adatkészlet szereplő oszlopok száma kisebb, mint a minimálisan megengedett {0} oszlopot.|  
|Bemeneti adatkészlet szereplő oszlopok száma "{0}" kisebb, mint a minimálisan megengedett {1} oszlopot.|

## <a name="error-0021"></a>0021 hiba  
 Kivétel lép fel, amennyiben az adatkészletek a modulba átadott egyes sorainak száma túl kicsi.  
  
 Ez a hiba a látható az Azure Machine Learning elő az adatkészletben, a megadott művelet végrehajtásához nincs elég sort. Például előfordulhat, hogy ezt a hibaüzenetet a bemeneti adatkészlet Ha üres, vagy ha próbált néhány érvényes sorok minimális száma igénylő műveletet hajtanak végre. Az ilyen műveletek tartalmazhat (de nem korlátozódnak) grouping vagy besorolás alapján statisztikai módszerekkel, bizonyos típusú dobozolás, és a tanulás számokkal.  
  
**Megoldás:**
 
 + Nyissa meg a modul, amely a hibát, és ellenőrizze a bemeneti adatkészlet és a modul tulajdonságait. 
 + Győződjön meg arról, hogy a bemeneti adatkészlet nem üres és, hogy nincsenek-e elegendő sornyi adatot modul súgójában ismertetett követelményeknek.  
 + Ha az adatok betöltése külső forrásból, győződjön meg arról, hogy az adatforrás érhető el, és nincs-e hiba, vagy módosítsa a data-definíciót, amely miatt az importálási folyamat kevesebb sort beolvasni a.
 + A modul, amely hatással legyen az adatok típusát vagy értékek, például a tisztítás, száma, a felosztás vagy join műveletek felső az adatokat egy olyan műveletet hajt végre, ellenőrizze a visszaadott sorok számának meghatározásához műveletekhez kimenetek.  



## <a name="error-0022"></a>0022 hiba  
 Kivétel lép fel, ha a bemeneti adatkészlet a kijelölt oszlopok száma nem egyezik a várt számú.  
  
 Ez a hiba az Azure Machine Learning akkor fordulhat elő, amikor az alsóbb rétegbeli modul vagy a művelet a szükséges oszlopokat vagy bemenetek bizonyos számú, és túl kevés vagy túl sok oszlop, vagy bemenetei között megadott. Példa:  
  
-   Adjon meg egy egycímkés oszlop vagy egy kulcsoszlop, és véletlenül egyszerre választott ki több oszlopot.  
  
-   Oszlopok vannak átnevezése, de több vagy kevesebb nevek megadott oszlopok számánál.  
  
-   A forrás vagy cél oszlopok száma megváltozott, vagy a modul által használt oszlopok száma nem egyezik.  
  
-   Bemenetek, a megadott értékek egy vesszővel tagolt listája, de az értékek száma nem egyezik, vagy több bemenet nem támogatottak.  
  
**Megoldás:** Nyissa meg újra a modult, és ellenőrizze a kijelölés, győződjön meg arról, hogy a megfelelő számú oszlop van kiválasztva. Ellenőrizze a felsőbb rétegbeli modulok kimenetek, és alsóbb rétegbeli műveletek követelményeinek.  
  
 Ha használta a egyikét oszlop kiválasztása, kiválaszthat több oszlop (oszlop indexek, összes funkciót, az összes numerikus, stb.), ellenőrizze a pontos szám visszaadott a kijelölt oszlopok.  
  
 <!--If you are trying to specify a comma-separated list of datasets as inputs to [Unpack Zipped Datasets](unpack-zipped-datasets.md), unpack only one dataset at a time. Multiple inputs are not supported.  -->
  
 Győződjön meg arról, hogy a számát vagy a fölérendelt oszlop típusa nem változott.  
  
 Ha a modell betanításához egy javaslat adatkészletet használ, ne feledje, hogy a ajánló vár oszlopok, csak korlátozott számú felhasználói-elem párokból vagy a felhasználó-elem-módosítják a rangsort megfelelő. A modell tanítása vagy javaslat adatkészletek felosztás előtt további oszlopok eltávolítása. További információkért lásd: [Split Data](split-data.md).  
  
|Kivétel üzenetek|  
|------------------------|  
|A bemeneti adatkészlet a kijelölt oszlopok száma nem egyezik a várt számú.|  
|A bemeneti adatkészlet a kijelölt oszlopok száma nem egyenlő {0}.|  
|Oszlop kiválasztása a minta "{0}" bemeneti adatkészlet nem egyenlő a kiválasztott oszlopok száma biztosít {1}.|  
|Oszlop kiválasztása a minta "{0}" adja meg a várt {1} bemeneti adatkészlethez, a kiválasztott oszlopok, de {2} oszlop(ok) van/biztosított.|  



## <a name="error-0023"></a>0023 hiba  
 Kivétel lép fel, ha a bemeneti adatkészlet a céloszlop nem érvényes az aktuális trainer modulhoz.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a céloszlop (mint kiválasztott modul paraméterei) nem az érvényes adattípus, az összes hiányzó értékeket tartalmazott, vagy nem az elvárt módon kategorikus volt.  
  
**Megoldás:** Nyissa meg újra a modult, bemeneti címke vagy a céloldali oszlop tartalmának vizsgálata. Győződjön meg arról, hogy nem rendelkezik az összes hiányzó értékeket. Ha a modul, a várt lehet kategorikus a céloszlop, ellenőrizze, hogy, hogy nincsenek egynél több különböző értékeket a céloszlop található.  
  
|Kivétel üzenetek|  
|------------------------|  
|Bemeneti adatkészlet céloszlop nem támogatott.|  
|Bemeneti adatkészlet nem támogatott a céloszlop "{0}".|  
|Bemeneti adatkészlet nem támogatott a céloszlop "{0}" típusú learner az {1}.|  
 

## <a name="error-0024"></a>0024 hiba  
Kivétel lép fel, ha az adatkészlet nem tartalmaz egy címkét oszlopot.  

 Az Azure Machine Learning a hiba akkor fordul elő, ha a modul egy címke oszlopot igényel, és az adatkészlet nem rendelkezik egy olyan címke oszlop. Például egy pontozott adatkészlet értékelése gyakran van szükség, hogy megtalálható-e egy címke oszlop pontosságát metrikák számítási.  
 
Emellett előfordulhat, hogy egy felirat oszlop található az adatkészletben, de nem észleli az Azure Machine Learning megfelelően.
  
**Megoldás:**

+ Nyissa meg a modul, amely a hibát okozta, és ha egy címke oszlopban szerepel-e. Az oszlop neve vagy az adatok típusa nem számít, mindaddig, amíg az oszlop egy egyszeri eredmény (vagy függő változó), amely előre jelezni kívánt tartalmaz. Ha nem biztos abban, hogy melyik oszlop a címkével rendelkezik, keressen egy általános nevet például *osztály* vagy *cél*. 
+  Ha az adatkészlet nem tartalmazza a címke oszlop, lehet, hogy a felirat oszlopot kifejezetten vagy véletlenül eltávolították felső. Az is előfordulhat, hogy az adatkészlet ne legyen-e egy felsőbb rétegbeli pontozási modul kimenete.
+ Explicit módon meg az oszlopot a címke oszlopban, adja hozzá a [metaadatainak szerkesztése](edit-metadata.md) modul, és kapcsolódjon az adatkészletet. Csak a címke oszlopban válassza ki és **címke** származó a **mezők** legördülő listából. 
+ Ha a megfelelő oszlopban a címkét választ, kiválaszthat **egyértelmű címke** származó a **mezők** , hárítsa el a metaadatokat az az oszlop. 
  
|Kivétel üzenetek|  
|------------------------|  
|Nincs címke oszlop adatkészletben.|  
|Nincs címke oszlop a "{0}".|  
  

## <a name="error-0025"></a>0025 hiba  
 Kivétel lép fel, ha az adatkészlet nem tartalmaz egy pontszám oszlopot.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a bemenetet a kiértékelés modell nem tartalmaz érvényes pontszám oszlopokat. Ha például a felhasználó megpróbál egy adatkészlet kiértékelése előtt, egy megfelelő betanított modell volt pontozott, vagy a pontszám oszlopot explicit módon elvetése felső. Ez a kivétel akkor is előfordul, ha a két adatkészlet pontszám oszlopai nem kompatibilisek. Például akkor lehet összehasonlítani kívánt egy lineáris regressor erre az adott bináris osztályozó pontosságát.  
  
**Megoldás:** Nyissa meg újra a bemenetet a evaluate-model, és vizsgálja meg, ha egy vagy több pontszám oszlopot tartalmaz. Ha nem, az adatkészlet nem volt pontozott, vagy a pontszám oszlopokat egy felsőbb rétegbeli modul el lettek dobva.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nincs pontszám oszlop adatkészletben.|  
|Nincs a pontszám oszlop "{0}".|  
|Nincs a pontszám oszlop "{0}" által előállított, amely egy "{1}". Az adatkészlet, használja a megfelelő típusú learner pontozása.|  
  

## <a name="error-0026"></a>0026 hiba  
 Kivétel lép fel, ha ilyen nevű oszlop nem engedélyezett.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha több oszlop rendelkezik ugyanazzal a névvel. Egyik módja, előfordulhat, hogy ez a hibaüzenet akkor, ha a az adatkészlet nem rendelkezik egy fejléc sorra, és automatikusan hozzárendeli az oszlopnevek: Col0, Col1, stb.  
  
**Megoldás:** Ha oszlop rendelkezik ugyanazzal a névvel, beszúrása egy [metaadatainak szerkesztése](edit-metadata.md) modul bemeneti adatkészlet és a modul között. Használja az oszlopot az oszlopválasztóban [metaadatainak szerkesztése](edit-metadata.md) válassza ki az oszlop átnevezéséhez írja be az új nevek a **új oszlopnevek** szövegmezőbe.  
  
|Kivétel üzenetek|  
|------------------------|  
|Egyenlő oszlopnevek argumentumok vannak megadva. Egyenlő oszlopnevek nem engedélyezettek a modul által.|  
|Az oszlopnevek argumentumok egyenlő "{0}"és"{1}" használata nem engedélyezett. Adja meg a különböző neveket.|  
  

## <a name="error-0027"></a>0027 hiba  
 Kivétel lép fel, abban az esetben, ha a két objektum kell lennie az azonos méretű, azonban nem.  
  
 Ez gyakori hiba az Azure Machine Learning, és sok feltétel okozhatja.  
  
**Megoldás:** Nincs konkrét megoldás. Azonban úgy ellenőrizheti, például a következő feltételeket:  
  
-   Ha oszlopok vannak átnevezése, ügyeljen arra, hogy rendelkezik-e az azonos számú elemet (a bemeneti oszlopok és az új nevek listája) a listák.  
  
-   Ha való csatlakozás vagy összefűzi a két adatkészletet, győződjön meg arról, hogy ugyanazzal a sémával rendelkezik.  
  
-   Ha, amelyhez csatlakozik, két adatkészletet, amely több oszlop van, ellenőrizze, hogy a Kulcsoszlopok ugyanazokat az adatokat írja be, és válassza a **engedélyezi az ismétlődő elemeket, és őrizze meg a kijelölt oszlopok sorrendjét**.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az átadott objektumok mérete nem konzisztens.|  
|Mérete "{0}"není konzistentní s mérete"{1}".|  
  

## <a name="error-0028"></a>0028 hiba  
 Kivétel történt abban az esetben, amikor oszlopkészlet ismétlődő oszlopneveket tartalmaz, és nem engedélyezett.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor oszlopnevek ismétlődik; Ez azt jelenti, hogy nem egyedi.  
  
**Megoldás:** Az összes oszlop rendelkezik-e ugyanaz a név, adjon hozzá egy [metaadatainak szerkesztése](edit-metadata.md) a bemeneti adatkészlet és a modul a hiba előléptetése között. Használja az oszlopot az Oszlopválasztóban [metaadatainak szerkesztése](edit-metadata.md) nevezze át, és írja be az új oszlopok nevét, az oszlopok kiválasztásához a **új oszlopnevek** szövegmezőbe. Ha több oszlopot, ellenőrizze, hogy a beírt értékeket az **új oszlopnevek** egyediek.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlop ismétlődő oszlop neve tartalmazza.|  
|A név "{0}" je duplicitní.|  
|A név "{0}"duplikálódnak a"{1}".|  
  

## <a name="error-0029"></a>0029 hiba  
 Kivétel lép fel, abban az esetben, amikor átadott URI érvénytelen.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, abban az esetben, amikor átadott URI érvénytelen.  Ez a hiba fog kapni, ha az alábbi feltételek bármelyike igaz:, vagy a.  
  
-   A nyilvános vagy a SAS URI-t az Azure Blob Storage olvasási vagy megadott írási hibát tartalmaz.  
  
-   A SAS időtartományából lejárt.  
  
-   A webes URL-cím via HTTP-forrás egy fájl vagy egy visszacsatolási URI-t jelöli.  
  
-   A webes URL-cím a HTTP Protokollon keresztül egy nem megfelelően formázott URL-címet tartalmaz.  
  
-   Az URL-cím nem oldható fel a távoli forrás.  
  
**Megoldás:** Nyissa meg újra a modult, és ellenőrizze az URI formátumát. Ha az adatforrás egy webes URL-címet a HTTP Protokollon keresztül, ellenőrizze, hogy a tervezett forrás nem egy fájl vagy egy visszacsatolási URI (localhost).  
  
|Kivétel üzenetek|  
|------------------------|  
|Érvénytelen Uri lett átadva.|  
  

## <a name="error-0030"></a>0030 hiba  
 Kivétel történt abban az esetben, ha az nem lehet letölteni a fájlt.  
  
 Ez a kivétel az Azure Machine Learning akkor fordul elő, ha az nem lehet letölteni a fájlt. Ez a kivétel fog kapni, amikor egy HTTP-forrás egy megkísérelt olvasása sikertelen volt a három (3) ismételt próbálkozás után megpróbál.  
  
**Megoldás:** Ellenőrizze, hogy helyesen szerepel-e a HTTP-forrás az URI-t, és hogy a hely jelenleg elérhető az interneten keresztül.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült letölteni a fájlt.|  
|Hiba történt a fájl letöltése: {0}.|  
  

## <a name="error-0031"></a>0031 hiba  
 Kivétel lép fel, ha oszlopkészlet szereplő oszlopok száma kisebb, mint amennyi szükséges.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a kijelölt oszlopok száma kisebb, mint amennyi szükséges.  A hibaüzenetet fog kapni, ha a minimálisan szükséges, hogy nincsenek kijelölve oszlopok számát.  
  
**Megoldás:** A kijelölés használatával további oszlopok hozzáadása a **Oszlopválasztó**.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlopkészlet szereplő oszlopok száma kisebb, mint a szükséges.|  
|{0} oszlopot kell megadni. A megadott oszlopok tényleges száma {1}.|  

## <a name="error-0032"></a>0032 hiba  
 Kivétel lép fel, ha az argumentuma nem szám.  
  
 Ezt a hibaüzenetet fog kapni az Azure Machine Learning, ha az argumentum értéke egy dupla és a NaN.  
  
**Megoldás:** Módosítsa a megadott argumentum egy érvényes értéket használja.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az argumentum nem szám.|  
|"{0}" nem szám.|  
  

## <a name="error-0033"></a>0033 hiba  
 Kivétel lép fel, ha az argumentuma Infinity.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az argumentum végtelen. Ez a hiba fog kapni, ha az argumentum `double.NegativeInfinity` vagy `double.PositiveInfinity`.  
  
**Megoldás:** Módosítsa a megadott argumentum nem egy érvényes értéket.  
  
|Kivétel üzenetek|  
|------------------------|  
|Argumentum véges kell lennie.|  
|"{0}" nem véges.|  
  

## <a name="error-0034"></a>0034 hiba  
 Kivétel lép fel, több minősítés az egy adott felhasználó-cikk pár van-e.  
  
 Ez a hiba az Azure Machine Learning javaslatban akkor jelentkezik, ha egy felhasználó-cikk pár egynél több minősítés értékkel rendelkezik.  
  
**Megoldás:** Győződjön meg arról, hogy a felhasználó-cikk pár rendelkezik-e egy minősítési érték csak.  
  
|Kivétel üzenetek|  
|------------------------|  
|Egynél több minősítés létezik az adatkészletben lévő értékeket.|  
|Felhasználó egynél több minősítés {0} és elemének {1} minősítés előrejelzési adatokat táblában.|  
  

## <a name="error-0035"></a>0035 hiba  
 Kivétel lép fel, ha nem szolgáltatások egy adott felhasználó vagy az elem van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, javaslat modellt alkalmaznak a pontozó próbált, de a szolgáltatás vektor nem található.  
  
**Megoldás:**

A Matchbox ajánló vannak bizonyos feltételei, elem szolgáltatások vagy a felhasználói funkciók használata szükséges.  Ez a hiba azt jelzi, hogy a szolgáltatás vektor egy felhasználó vagy a bemenetként megadott elem hiányzik.  Biztosítania kell, hogy egy vektoros funkciók érhető el minden elem vagy felhasználói adatok.  
  
 Például ha, betanított ajánlás modellkezelési szolgáltatások, például a felhasználó betöltötte, hely vagy jövedelem, de most szeretne létrehozni az új felhasználók, akik nem jelentek-betanítás, során meg kell adnia néhány egyenértékű funkciócsoport pontszámok (azaz, kor, hely, és bevétel értékek) az új felhasználók annak érdekében, hogy a számukra megfelelő előrejelzéseket. 
 
 Ha nem kell ezeknek a felhasználóknak minden olyan funkciók, fontolja meg a funkciófejlesztési létrehozásához szükséges szolgáltatásokat.  Például ha nem rendelkezik egyedi felhasználói kora vagy jövedelem értékeket, előfordulhat, hogy létrehozza a hozzávetőleges értékeket kell használnia a felhasználók egy csoportjánál. 
 
<!--When you are scoring from a recommendation mode, you can use item or user features only if you previously used item or user features during training. For more information, see [Score Matchbox Recommender](score-matchbox-recommender.md).
 
For general information about how the Matchbox recommendation algorithm works, and how to prepare a dataset of item features or user features, see [Train Matchbox Recommender](train-matchbox-recommender.md).  -->
  
 > [!TIP]
 > A megoldás nem alkalmazható az Ön esetében? Üdvözli az ezen a cikken visszajelzés küldésére és a forgatókönyv, beleértve a modul és a sorok száma az az oszlop információ áll. Fog ez az információ biztosítása érdekében használjuk részletesebb hibaelhárítási lépéseket a jövőben.
   
|Kivétel üzenetek|  
|------------------------|  
|Nincsenek szolgáltatások szükséges felhasználói vagy elem van megadva.|  
|A szolgáltatások {0} szükséges, de nincs megadva.|  
  

## <a name="error-0036"></a>0036 hiba  
 Kivétel lép fel, ha több szolgáltatás vektorok egy adott felhasználó vagy az elem van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy szolgáltatás vektor egynél többször van definiálva.  
  
**Megoldás:** Győződjön meg arról, hogy a szolgáltatás vektor nincs definiálva egynél többször.  
  
|Kivétel üzenetek|  
|------------------------|  
|Ismétlődő funkció egy felhasználó vagy egy elem definíciója.|  
|A szolgáltatás definíciója ismétlődő {0}.|  
  

## <a name="error-0037"></a>0037 hiba  
 Kivétel lép fel, ha a címke több oszlop van megadva, és csak egy engedélyezett.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egynél több oszlop van kijelölve, az új címke oszlopot kell. Legtöbb felügyelt tanulási algoritmusok kell megjelölni a cél vagy a címkét, csak egy oszlop van szükség.  
  
**Megoldás:** Ellenőrizze, hogy csak egy oszlop kiválasztása az új címke oszlopként.  
  
|Kivétel üzenetek|  
|------------------------|  
|Több címke oszlop van megadva.|  
  

## <a name="error-0038"></a>0038 hiba  
 Kivétel akkor fordul elő, ha az elemek várt száma pontos szám lehet, de nem.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az elemek várt száma pontos szám lehet, de nem.  A hibaüzenetet fog kapni, ha az elemek száma nem egyenlő a érvényes várt értékkel.  
  
**Megoldás:** Módosítsa a bemeneti rendelkezik a megfelelő elemek száma.  
  
|Kivétel üzenetek|  
|------------------------|  
|Elemek értéke nem érvényes.|  
|Az elemek száma "{0}" nem érvényes.|  
|Az elemek száma "{0}" nem érvényes száma egyenlő {1} elem(ek).|  
  

## <a name="error-0039"></a>0039 hiba  
 Kivétel lép fel, ha egy művelet sikertelen volt.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor egy belső művelet nem hajtható végre.  
  
**Megoldás:** Ezt a hibát sok feltétel okozza, és nem áll rendelkezésre egyedi jogorvoslat.  
 Az alábbi táblázat tartalmazza a hiba általános üzenetekről, amelyeken a rendszer egy adott feltétel leírása követ. 
 
 Részletek nem érhetők el, ha [visszajelzés küldése](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning) és ismertetik a modulok által generált a hibát, és a kapcsolódó feltételeket.
  
|Kivétel üzenetek|  
|------------------------|  
|Sikertelen volt a művelet.|  
|Hiba történt a művelet befejezése: {0}.|  
  

## <a name="error-0040"></a>0040 hiba  
 Kivétel történt egy elavult modul hívása során.  
  
 Ez a hiba az Azure Machine Learning egy elavult modul hívása során jön létre.  
  
**Megoldás:** Cserélje le az elavult modul egy támogatott. A modul kimeneti naplót, mely modul használja helyette a információkat talál.  
  
|Kivétel üzenetek|  
|------------------------|  
|Elavult modul elérése.|  
|A modul "{0}" elavult. Használja a modul "{1}" helyett.|  
 

## <a name="error-0041"></a>0041 hiba  
 Kivétel történt egy elavult modul hívása során.  
  
 Ez a hiba az Azure Machine Learning egy elavult modul hívása során jön létre.  
  
**Megoldás:** Cserélje le az elavult modul támogatott eszközök készletét. Ezt az információt meg fognak jelenni a modul kimeneti naplót.  
  
|Kivétel üzenetek|  
|------------------------|  
|Elavult modul elérése.|  
|A modul "{0}" elavult. A modulok használata "{1}" a kért funkció.|  
 

## <a name="error-0042"></a>0042 hiba  
 Kivétel lép fel, ha az nem lehet átalakítani az oszlop egy másik.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehet átalakítani oszlopot a megadott típusra.  A hibaüzenetet fog kapni, ha egy modulnak szüksége van egy adott adattípus, például a dátum és idő, szöveg, lebegőpontos számot vagy egész szám, de nem lehet átalakítani egy létező oszlop típusának megadása kötelező.  
 
Például előfordulhat, hogy válasszon ki egy oszlopot, és próbálja meg azt a matematikai művelet használható egy numerikus adattípusúra konvertálása, és ezt a hibát kap, ha az oszlop érvénytelen adatokat tartalmazott. 

A másik ok lehet, hogy megjelenik a hibaüzenet, ha megpróbálja használni a kategorikus oszlopként lebegőpontos számok vagy sok egyedi értékeket tartalmazó oszlop. 
  
**Megoldás:**

+ Nyissa meg a következő súgóoldalt a modul, amely a hibát okozta, és ellenőrizze az adattípus-követelményekkel.
+ Tekintse át a bemeneti adatkészlet az oszlopok adattípusát.
+ Vizsgálja meg az úgynevezett séma nélküli adatforrások származó adatokat.
+ Ellenőrizze az adatkészlet a hiányzó értékeket vagy speciális karaktereket, amelyek blokkolhatják a kívánt adattípusra történő átalakítás. 
    + Numerikus adattípusok összhangban kell lennie: például ellenőrizze a lebegőpontos számok egy egész számot tartalmazó oszlop.
    + Keresse meg a szöveges karakterlánc vagy szám oszlop értékeinek NA. 
    + Logikai értékek konvertálható a szükséges adatok típusától függően egy megfelelő választ.
    + Vizsgálja meg a szöveges oszlopok nem unicode-karaktereket, tabulátor karakter vagy vezérlőkaraktereket
    + Dátum és idő adatok modellezése a hibák elkerülése érdekében összhangban kell lennie, de a karbantartás miatt a sok formátumok összetett folyamat lehet. Fontolja meg <!--the [Execute R Script](execute-r-script.md) or -->[Python-szkript végrehajtása](execute-python-script.md) modulok fölösleges elemeket.  
+ Szükség esetén módosítsa az értékeket a bemeneti adatkészletben, hogy az oszlop sikeresen lehet konvertálni. Módosítás tartalmazhat dobozolás, csonkolása vagy kerekítési üzemeltetési és kiugró értékek kiszűrése vagy imputálási érték hiányzik. Machine learning néhány általános adatok átalakítása forgatókönyvhöz a következő cikkekben talál:
    + [Hiányzó adatok törlése](clean-missing-data.md)
    + [Adatok optimalizálása](normalize-data.md)
<!--+ [Clip Values](clip-values.md) 
    + [Group Data Into Bins](group-data-into-bins.md)
  -->
 
> [!TIP]
> Nem egyértelmű vagy nem van megoldás? Üdvözli az ezen a cikken visszajelzés küldésére és a forgatókönyv, beleértve a modul, és az oszlop adattípusát információ áll. Fog ez az információ biztosítása érdekében használjuk részletesebb hibaelhárítási lépéseket a jövőben.  
  
|Kivétel üzenetek|  
|------------------------|  
|Átalakítás nem engedélyezett.|  
|Nem sikerült átalakítani típusú oszlop {0} típusú oszlopra {1}.|  
|Nem sikerült átalakítani az oszlop "{2}" típusú {0} típusú oszlopra {1}.|  
|Nem sikerült átalakítani az oszlop "{2}" típusú {0} oszlopra "{3}" típusú {1}.|  
  

## <a name="error-0043"></a>0043 hiba  
 Typ prvku explicit módon nem implementálja egyenlő kivétel lép fel.  
  
 Ez a hiba az Azure Machine Learning a fel nem használt, és elavulttá válik.  
  
**Megoldás:** Nincs.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nincs elérhető explicit metódus egyenlő található.|  
|Nem lehet két értéket oszlop \\"{0}\\" típusú {1}. Nincs elérhető explicit metódus egyenlő található.|  


## <a name="error-0044"></a>0044 hiba  
 Kivétel lép fel, ha az nem lehetséges a meglévő értékek elem típusú oszlop.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehetséges következtetnek ki egy oszlopot vagy oszlopokat az adatkészlet típusa. Ez általában akkor fordul elő, amikor két vagy több adatkészletet előállítjuk a különböző elem típusát. Ha az Azure Machine Learning nem határozható meg a közös típust, amely képes egy oszlop- vagy információveszteség nélkül oszlop összes értékét képviselik, ezt a hibát generál.  
  
**Megoldás:** Győződjön meg arról, hogy az összes mindkét olyan adatkészlettel, kombinált az adott oszlop értékei azonos egyikét írja be a (numerikus, logikai, kategorikus, string, dátum, stb.), vagy az azonos típusú is kényszeríthető.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült kinyerni az elem típusú oszlop.|  
|Nem sikerült kinyerni az elem típusa "{0}" – az összes az elemei null értékű hivatkozásokat.|  
|Nem sikerült kinyerni az elem típusa "{0}", "adatkészlet{1}" – az összes az elemei null értékű hivatkozásokat.|  
  

## <a name="error-0045"></a>0045 hiba  
 Kivétel lép fel, ha az nem lehet létrehozni egy oszlop miatt a forrás vegyes elem típusát.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha a két adatkészlet kombinált elem típusú különböznek.  
  
**Megoldás:** Győződjön meg arról, hogy az összes mindkét olyan adatkészlettel, kombinált az adott oszlop értékei (numerikus, logikai, kategorikus, string, dátum, stb.) azonos típusú.  
  
|Kivétel üzenetek|  
|------------------------|  
|Vegyes elemtípus oszlop nem hozható létre.|  
|Azonosítójú oszlop nem hozható létre "{0}" elem vegyes típusok: \n\tType az adatok az [{1}, {0}] van {2}\n\tType az adatok [{3}, {0}] van {4}.|  
  

## <a name="error-0046"></a>0046 hiba  
 Kivétel lép fel, ha az nem könyvtár létrehozása a megadott elérési úton lehetséges.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehet létrehozni egy könyvtárat a megadott elérési úton. Ezt a hibaüzenetet fog kapni, ha bármely részét a kimeneti könyvtárba, egy Hive-lekérdezés helytelen vagy nem érhető el.  
  
**Megoldás:** Nyissa meg újra a modult, és győződjön meg arról, hogy a könyvtár elérési útja megfelelően van formázva, valamint, hogy elérhető-e az aktuális hitelesítő adatokkal.  
  
|Kivétel üzenetek|  
|------------------------|  
|Adjon meg egy érvényes kimeneti könyvtár.|  
|Könyvtár: {0} nem hozható létre. Adjon meg érvényes elérési utat.|  
  

## <a name="error-0047"></a>Hiba történt a 0047  
 Kivétel lép fel, amennyiben az adatkészletek a modulba átadott néhány funkció oszlopok száma túl kicsi.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a bemeneti adatkészlet betanítás nem tartalmaz oszlopokat az algoritmus által megkövetelt minimális számát. Általában vagy az adatkészletet üres, vagy csak a betanítási oszlopokat tartalmazza.  
  
**Megoldás:** Nyissa meg újra a bemeneti adatkészletet, győződjön meg arról, hogy egy vagy több további oszlopokat a címke oszlop szereplőkkel.  
  
|Kivétel üzenetek|  
|------------------------|  
|A bemeneti adatkészlet funkció oszlopok száma nem kisebb, mint a minimális.|  
|A bemeneti adatkészlet funkció oszlopok száma kisebb, mint a minimálisan megengedett {0} oszlopot.|  
|A bemeneti adatkészlet funkció oszlopok száma "{0}" kisebb, mint a minimálisan megengedett {1} oszlopot.|  
  

## <a name="error-0048"></a>0048 hiba  
 Kivétel történt abban az esetben, ha az nem lehet megnyitni a fájlt.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehet írni, vagy nyisson meg egy fájlt olvasásra. Ebből kifolyólag előfordulhat, hogy ezt a hibaüzenetet kapja:  
  
-   A tároló vagy a fájlra (blobra) nem létezik  
  
-   A fájl vagy a tároló hozzáférési szintjét nem teszi lehetővé a fájl eléréséhez  
  
-   A fájl mérete túl nagy, olvassa el, vagy formátuma nem megfelelő  
  
**Megoldás:** Nyissa meg újra a modult, és az olvasni kívánt fájlt.  
  
 Győződjön meg arról, hogy helyesek-e a tároló és a fájl nevét.  
  
 A klasszikus Azure portálon vagy az Azure storage eszköz használatával ellenőrizze, hogy rendelkezik-e engedélye a fájl elérésére.  
  
  <!--If you are trying to read an image file, make sure that it meets the requirements for image files in terms of size, number of pixels, and so forth. For more information, see [Import Images](import-images.md).  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Nem lehet megnyitni a fájlt.|  
|Hiba történt a fájl megnyitásakor: {0}.|  


## <a name="error-0049"></a>0049 hiba  
 Kivétel történt abban az esetben, ha az nem lehet elemezni a fájlt.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehet elemezni a fájlt. Ez a hiba fog kapni, ha a fájl formátuma kijelölt a [adatok importálása](import-data.md) modul nem felel meg a fájl tényleges formátumát, vagy ha a fájl egy felismerhetetlen karaktert tartalmaz.  
  
**Megoldás:** Nyissa meg újra a modult, és javítsa ki a fájl formátuma lehetőséget, ha a fájl formátuma nem egyezik. Ha lehetséges vizsgálja meg a fájlt annak ellenőrzéséhez, hogy nem tartalmaz érvénytelen karaktereket.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült elemezni a fájlt.|  
|Hiba történt a fájl elemzésekor: {0}.|  
  

## <a name="error-0050"></a>0050 hiba  
 Kivétel lép fel, abban az esetben, ha a bemeneti és kimeneti fájlok megegyeznek.  
  
**Megoldás:** Ez a hiba az Azure Machine Learning a fel nem használt, és elavulttá válik.  
  
|Kivétel üzenetek|  
|------------------------|  
|A bemeneti és kimeneti megadott fájlokat nem lehet azonos.|


## <a name="error-0051"></a>0051 hiba  
 Kivétel történt abban az esetben, ha több kimeneti fájlok megegyeznek.  
  
**Megoldás:** Ez a hiba az Azure Machine Learning a fel nem használt, és elavulttá válik.  
  
|Kivétel üzenetek|  
|------------------------|  
|A kimenetek megadott fájlokat nem lehet azonos.|


## <a name="error-0052"></a>0052 hiba  
 Kivétel lép fel, ha az Azure storage-fiókkulcs helytelenül van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az Azure-tárfiók eléréséhez használt kulcs helytelen. Például előfordulhat, hogy ezt a hibaüzenetet, ha az Azure storage-kulcs csonkolt másolni és beilleszteni, vagy helytelen kulcsot lett megadva.  
  
 Az Azure storage-fiókhoz tartozó kulcs lekérésével kapcsolatos további információkért lásd: [megtekintése, másolása és újragenerálása storage hozzáférési kulcsok](https://azure.microsoft.com/documentation/articles/storage-create-storage-account-classic-portal/).  
  
**Megoldás:** Nyissa meg újra a modult, és ellenőrizze, hogy az Azure storage-kulcs helyes-e a fiók; másolja a kulcsot újra a klasszikus Azure portálon szükség esetén.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-fiók kulcs helytelen.|  
  

## <a name="error-0053"></a>0053 hiba  
 Kivétel lép fel abban az esetben, ha nem felhasználó funkciók vagy elemek matchbox javaslatokat.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha a szolgáltatás vektor nem található.  
  
**Megoldás:** Győződjön meg arról, hogy a szolgáltatás vektor szerepel a bemeneti adatkészlet.  
  
|Kivétel üzenetek|  
|------------------------|  
|Felhasználó funkciók vagy / és elemek is kötelező, de nincs megadva.|  

## <a name="error-0054"></a>0054 hiba  
 Kivétel lép fel, ha túl kevés eltérő érték szerepel az oszlop a művelet végrehajtásához.  
  
**Megoldás:** Ez a hiba az Azure Machine Learning a fel nem használt, és elavulttá válik.  
  
|Kivétel üzenetek|  
|------------------------|  
|Adatok a művelet végrehajtásához a megadott oszlop túl kevés eltérő érték van.|  
|Adatok a művelet végrehajtásához a megadott oszlop túl kevés eltérő érték van. A szükséges minimális érték {0} elemeket.|  
|Adatok túl kevés eltérő érték szerepel az oszlop "{1}" művelet végrehajtásához. A szükséges minimális érték {0} elemeket.|  
  

## <a name="error-0055"></a>0055 hiba  
 Kivétel történt egy elavult modul hívása során.  Az Azure Machine Learning a hiba akkor jelenik meg, ha megpróbálja meghívni a modul, amely már elavult.
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Elavult modul elérése.|  
|A modul "{0}" elavult.|  

## <a name="error-0056"></a>0056 hiba  
 Kivétel lép fel, ha egy művelet a kijelölt oszlopok sérti szemben támasztott követelményeit.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, az oszlop egy adott adattípusú lehet igénylő művelet oszlopok kiválasztásakor. 
 
 Ez a hiba is fordulhat elő, ha az oszlop a megfelelő adattípust, de a modul megköveteli, hogy az oszlop be kell jelölni egy funkció, a címke vagy a kategorikus oszlopok.  
  
  <!--For example, the [Convert to Indicator Values](convert-to-indicator-values.md) module requires that columns be categorical, and will raise this error if you select a feature column or label column.  -->
  
**Megoldás:**
  
1.  Tekintse át a az aktuálisan kiválasztott oszlopok adattípusát. 

2. Annak megállapítása, hogy kategorikus,-e a kijelölt oszlopok címkét, vagy a szolgáltatás oszlopokat.  
  
3.  Tekintse át a modult, amelyben a kijelölés, hogy vannak-e típus vagy oszlop adathasználati adatokat konkrét követelmények végzett tartozó súgó-témakör.  
  
3.  Használat [metaadatainak szerkesztése](edit-metadata.md) az oszlop típusának módosítása a művelet időtartama. Ügyeljen arra, hogy az oszlop típusa állítsa vissza az eredeti értékére, egy másik példányát [metaadatainak szerkesztése](edit-metadata.md), alsóbb rétegbeli műveletekhez szükség esetén.  
  
|Kivétel üzenetek|  
|------------------------|  
|Egy vagy több kijelölt oszlopokat a rendszer nem egy engedélyezett kategóriában.|  
|Nevű oszlop "{0}" nem szerepel az engedélyezett kategória.|  
  

## <a name="error-0057"></a>0057 hiba  
 Kivétel történt a fájl vagy már létező blobot létrehozására tett kísérlet során.  
  
 A kivétel lép fel, ha használja a [adatok exportálása](export-data.md) modul vagy más, egy kísérlet az eredmények mentése az Azure Machine Learning az Azure blob storage-ba, de a modul megpróbál létrehozni egy fájl vagy blob, amelyet már létezik.   
  
**Megoldás:**
 
 Ez a hiba fog kapni, csak ha korábban a tulajdonság értéke **az Azure blob-tároló írási mód** a **hiba**. A kialakításból fakadóan Ez a modul hibát jelez, ha egy blobot, amely már létezik egy adatkészlet írni.
 
 - Nyissa meg a modul tulajdonságait, és módosítsa a tulajdonság **az Azure blob-tároló írási mód** való **felülírás**.
 - Azt is megteheti írja be egy másik cél blob vagy a fájl nevét, és ügyeljen arra, hogy adjon meg egy blobot, amely már nem létezik.  
  
|Kivétel üzenetek|  
|------------------------|  
|Fájl- vagy Blob már létezik.|  
|Fájl- vagy Blob "{0}" már létezik.|  
  

## <a name="error-0058"></a>0058 hiba  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az adatkészlet nem tartalmaz a várt címkét oszlop.  
  
 Ez a kivétel is előfordulhatnak, ha a címke oszlopban megadott adatok vagy a learner által várt adattípus nem egyezik, vagy a megfelelő értékkel rendelkezik. Ehhez a kivételhez például akkor, ha egy bináris osztályozó által igénybe vett képzés használ a címke valós értékű oszlop jön létre.  
  
**Megoldás:** A megoldás a learner vagy trainer, amelyet használ, és az adatkészlet az oszlopok adattípusai függ. Első lépésként ellenőrizze a gépi tanulási algoritmus vagy képzési modul követelményeinek.  
  
 Nyissa meg újra az adatkészletet. Győződjön meg arról, hogy az oszlop várt kell kezelni, mint a címke rendelkezik a megfelelő adatokat, írja be a modell létrehozásakor.  
  
 Ellenőrizze a bemeneteket hiányzó értékekkel kapcsolatos és számára, vagy szükség esetén cserélje le őket.  
  
 Ha szükséges, adja hozzá a [metaadatainak szerkesztése](edit-metadata.md) modul, és győződjön meg arról, hogy a felirat oszlop címke van megjelölve.  
  
|Kivétel üzenetek|  
|------------------------|  
|A címke oszlop van, nem a várt|  
|A címke oszlop van, nem a várt módon "{0}".|  
|A címke oszlop "{0}"nem várt egy"{1}".|  
  

## <a name="error-0059"></a>0059 hiba  
 Kivétel lép fel, ha egy olyan Oszlopválasztó megadott oszlopindex nem elemezhető.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy oszlopot az Oszlopválasztóban használatakor a megadott oszlopindex nem elemezhető.  Ez a hiba fog kapni, ha a oszlopindex nem elemezhető formátuma érvénytelen.  
  
**Megoldás:** Módosítsa az oszlop index használatához egy érvényes értéket.  
  
|Kivétel üzenetek|  
|------------------------|  
|Egy vagy több megadott oszlophoz index vagy index tartományokat nem elemezhető.|  
|Az oszlopindex vagy tartomány "{0}" nem elemezhető.|  
  

## <a name="error-0060"></a>0060 hiba  
 Egy Oszlopválasztó megadott címtartomány oszlop tartományon kívüli kivétel lép fel.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha meg van adva egy tartományon kívüli oszlop címtartományt oszlopot az oszlopválasztóban. A hibaüzenetet fog kapni, ha az oszlop az Oszlopválasztó a tartomány nem felel meg az oszlopok az adatkészletben.  
  
**Megoldás:** Módosítsa az oszlop a tartomány az Oszlopválasztó, hogy az oszlopok az adatkészletben.  
  
|Kivétel üzenetek|  
|------------------------|  
|Érvénytelen vagy a megadott tartomány oszlop index tartományon kívül esik.|  
|Oszlop tartomány "{0}" érvénytelen, vagy a tartományon kívül esik.|  
  

## <a name="error-0061"></a>0061 hiba  
 Kivétel történt egy sor hozzáadása, amely rendelkezik, mint a táblában az oszlopok különböző számú DataTable megkísérlése során.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor megpróbálja egy sort ad hozzá egy adatkészlethez, amelynek eltérő számú oszlop, mint az adatkészlet.  Ezt a hibaüzenetet fog kapni, ha az adatkészlethez hozzáadott sor a bemeneti adatkészlet az oszlopok különböző számú.  A sor nem fűzhető hozzá az adatkészlethez, ha az oszlopok száma nem egyezik.  
  
**Megoldás:** A bemeneti adatkészlet a hozzáadott sor azonos számú oszlopot kell módosítani, vagy módosítsa az adatkészlet azonos számú oszlopot kell hozzáadni a sor.  
  
|Kivétel üzenetek|  
|------------------------|  
|Összes táblának azonos számú oszlopot kell rendelkeznie.|  
  

## <a name="error-0062"></a>0062 hiba  
 Kivétel lép fel, eltérő learner típusú két modell összehasonlítására megkísérlése során.  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor értékelési mérőszámok két másik pontozott adatkészletek esetében nem lehet összehasonlítani. Ebben az esetben nincs lehetőség a két pontozott adatkészlet létrehozásához használt modell hatékonysága összehasonlítására.  
  
**Megoldás:** Győződjön meg arról, hogy a pontozott eredményeket hozzák ugyanezeket a gépi tanulási modell (bináris osztályozás, regressziós, többcsoportos besorolást, javaslat, fürtözés, rendellenességek észlelése stb.) Minden modell, amely összehasonlítja a azonos learner típusúnak kell lennie.  
  
|Kivétel üzenetek|  
|------------------------|  
|Minden modell a azonos learner típusúnak kell lennie.|  
  

 <!--## Error 0063  
 This exception is raised when R script evaluation fails with an error.  
  
 This error occurs when you have provided an R script in one of the [R language modules](r-language-modules.md) in Azure Machine Learning, and the R code contains internal syntax errors. The exception can also occur if you provide the wrong inputs to the R script. 
 
 The error can also occur if the script is too large to execute in the workspace. The maximum script size for the **Execute R Script** module is 1,000 lines or 32 KB of work space, whichever is lesser.
  
**Resolution:**

1. In Azure Machine Learning Studio, right-click the module that has the error, and select **View Log**.
2. Examine the standard error log of the module, which contains the stack trace.
    + Lines beginning with [ModuleOutput] indicate output from R.
    + Messages from R marked as **warnings** typically do not cause the experiment to fail.
3. Resolve script issues.  
    + Check for R syntax errors. Check for variables that are defined but never populated.
    + Review the input data and the script to determine if either the data or variables in the script use characters not supported by Azure Machine Learning.
    + Check whether all package dependencies are installed.
    + Check whether your code loads required libraries that are not loaded by default.
    + Check whether the required packages are the correct version.
    + Make sure that any dataset that you want to output is converted to a data frame.  
4.  Resubmit the experiment.

 <!--
> [!NOTE]
> These topics contains examples of R code that you can use, as well as links to experiments in the [Cortana Intelligence Gallery](https://gallery.cortanaintelligence.com) that use R script.
> + [Execute R Script](execute-r-script.md)
> + [Create R Model](create-r-model.md)
-->  
|Kivétel üzenetek|  
|------------------------|  
|Hiba történt az R-szkriptet a kiértékelés során.|  
|A következő hiba történt az R-parancsfájl kiértékelése során:---R hibaüzenete kezdete--- {0} ---vége a R hibaüzenete---|  
|R-parancsfájl kiértékelése során "{1}" a következő hiba történt:---R hibaüzenete kezdete--- {0} ---vége a R hibaüzenete---|  
  


## <a name="error-0064"></a>0064 hiba  
 Kivétel lép fel, ha az Azure storage-fiók neve vagy a storage-kulcs helytelenül van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az Azure storage-fiók neve vagy a storage-kulcs helytelenül van megadva. Ez a hiba fog kapni, ha helytelen felhasználónév és jelszó ad meg a tárfiók. Ez akkor fordulhat elő, ha be kézzel a fióknév vagy jelszó. Ez akkor is előfordulhat, ha a fiók törölve lett.  
  
**Megoldás:** Ellenőrizze, hogy a fiók nevét és jelszavát megadva, és arról, hogy a fiók létezik-e.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-fiók neve vagy a storage-kulcs helytelen.|  
|Az Azure storage-fiók neve "{0}" vagy a tárfiók kulcsát, a fiók neve helytelen.|  
  

## <a name="error-0065"></a>0065 hiba  
 Kivétel lép fel, ha az Azure blob neve helytelenül van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az Azure blob neve helytelenül van megadva.  A hibaüzenetet fog kapni, ha:  
  
-   A blob a megadott tároló nem található.  
  
 <!---   The fully qualified name of the blob specified for output in one of the [Learning with Counts](data-transformation-learning-with-counts.md) modules is greater than 512 characters.  -->
  
-   Csak a tároló van megadva, az forrás egy [adatok importálása](import-data.md) kérése, amikor formátuma az Excel- vagy CSV kódolással volt; a tárolóban lévő összes BLOB tartalmát összefűzésén nem engedélyezett, ha ezek a formátumok.  
  
-   SAS URI nem tartalmaz érvényes blob neve.  
  
**Megoldás:** Nyissa meg újra a modult, a kivétel kivétel. Győződjön meg arról, hogy a megadott blob létezik-e a tárolóban, a storage-fiókban és az engedélyek lehetővé teszi a blob. Győződjön meg arról, hogy a bemeneti adatok az űrlap **containername/filename** Ha Excel- vagy CSV formátumban kódolással. Győződjön meg arról, hogy a SAS URI érvényes blob nevét tartalmazza.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-blob nem megfelelő.|  
|Az Azure storage blob neve "{0}" nem megfelelő|  
  

## <a name="error-0066"></a>0066 hiba  
 Kivétel lép fel, ha egy erőforrás nem tölthető fel az Azure-blobba.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy erőforrás nem tölthető fel az Azure-blobba.  <!--You will receive this message if [Train Vowpal Wabbit 7-4 Model](train-vowpal-wabbit-version-7-4-model.md) encounters an error attempting to save either the model or the hash created when training the model.--> Mindkét menti, és ugyanazt az Azure storage-fiókot a fiókot, amely tartalmazza a bemeneti fájl.  
  
**Megoldás:** Nyissa meg újra a modult. Győződjön meg arról, hogy az Azure-fiók neve, tárkulcs és tároló helyességéről, valamint, hogy a fiók rendelkezik-e a tároló írási engedéllyel.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-erőforrás nem tölthető fel.|  
|A fájl "{0}" nem lehet feltölteni az Azure storage-ot {1}.|  
  

## <a name="error-0067"></a>0067 hiba  
 Kivétel lép fel, ha egy adatkészlet rendelkezik a vártnál oszlopok különböző számú.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy adatkészlet rendelkezik a vártnál oszlopok különböző számú.  Ezt a hibaüzenetet fog kapni, ha az adatkészletben lévő oszlopok száma, amelyeket a modul végrehajtása során vár oszlopok száma eltér.  
  
**Megoldás:** Módosítsa a bemeneti adatkészlet vagy a paraméterek.  
  
|Kivétel üzenetek|  
|------------------------|  
|Váratlan számú oszlopot a DataTable.|  
|A várt "{0}"de található oszlopok"{1}" oszlopok helyett.|  
  

## <a name="error-0068"></a>0068 hiba  
 Kivétel lép fel, ha a megadott Hive-parancsfájl nem megfelelő.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha QL Hive-parancsfájl szintaktikai hibák, vagy ha a Hive-értelmező hibába ütközik a lekérdezés vagy a parancsfájl végrehajtása közben.  
  
**Megoldás:**

Hive hibaüzenete általában jelentett vissza a hibanapló, hogy az adott hiba általános alapján műveleteket végezheti el. 

+ Nyissa meg a modult, és megvizsgálja a lekérdezést a hibákat.  
+ Győződjön meg arról, hogy a lekérdezés megfelelően működik az Azure Machine Learning-en kívül a Hive konzolt a Hadoop-fürt és a lekérdezés futtatása.  
+ Próbálja ki a megjegyzéseket a Hive-parancsfájl végrehajtható utasítások és a egy sorba megjegyzések keverése figyelésekor külön sorban helyezi el.  

### <a name="resources"></a>További források

A Hive-lekérdezések machine Learning a Súgó a következő cikkekben talál:

+ [Hive táblák létrehozása és az adatok betöltése az Azure Blob Storage-ból](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-move-hive-tables)
+ [Ismerje meg a Hive-lekérdezések a táblázatokban lévő adatok](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-explore-data-hive-tables)
+ [Funkciók létrehozása az adatokhoz a Hive-lekérdezések segítségével egy Hadoop-fürt](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-create-features-hive)
+ [Hive-az SQL-felhasználók – Adatlap (PDF)](http://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf)

  
|Kivétel üzenetek|  
|------------------------|  
|Hive-parancsfájl nem megfelelő.|  
|Hive-parancsprogram {0} nem megfelelő.|  
  

## <a name="error-0069"></a>0069 hiba  
 Kivétel lép fel, ha a megadott SQL-parancsfájl nem megfelelő.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a megadott SQL-szkript szintaktikai problémák, vagy ha a megadott oszlopokat vagy tábla a parancsfájl nem érvényes. 
 
 Ezt a hibaüzenetet fog kapni, ha az SQL-kezelő hibákat észlel, a lekérdezés vagy a parancsfájl végrehajtása közben. Az SQL-hibaüzenet általában jelentett vissza a hibanapló, hogy az adott hiba általános alapján műveleteket végezheti el.  
  
**Megoldás:** Nyissa meg újra a modult, és vizsgálja meg az SQL-lekérdezés hibákkal.  
  
 Győződjön meg arról, hogy a lekérdezés megfelelően működik az Azure Machine Learning-en kívül közvetlenül bejelentkezni az adatbázis-kiszolgáló és a lekérdezés futtatása.  
  
 Ha a modul kivétel által jelentett létrehozott SQL üzenetet, alapján műveleteket a jelzett hibát. A hibaüzenetek néha például a nagy valószínűséggel hiba útmutatást:
+ *Nincs ilyen oszlop vagy hiányzó adatbázis*, jelezve, hogy előfordulhat, hogy írt be helytelen az oszlop nevét. Ha biztos abban, hogy az oszlop neve helyesen, próbálkozzon zárójelben vagy idézőjelek adni az oszlop azonosítóját.
+ *SQL logikai hiba közelében \<SQL kulcsszó\>*, amely azt jelzi, hogy lehetséges, hogy a megadott kulcsszóval előtt szintaktikai hiba

  
|Kivétel üzenetek|  
|------------------------|  
|SQL-szkript nem megfelelő.|  
|SQL-lekérdezés "{0}" nem megfelelő.|  
|SQL-lekérdezés "{0}" nem megfelelő: {1}|  
  

## <a name="error-0070"></a>0070 hiba  
 Kivétel lép fel, amikor megpróbálja elérni az Azure-tábla nem létezik.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha próbál meg hozzáférni egy nem létező Azure table. Ezt a hibaüzenetet fog kapni, ha megad egy táblát az Azure storage, amely nem létezik, amikor az Azure Table Storage írásakor vagy olvasásakor. Ez akkor fordulhat elő, ha írunk, a kívánt tábla nevét, vagy ha a célnév és a tárolás típusát nem egyeznek. Például egy tábla olvasni szánt, de ehelyett a blob nevét a megadott.  
  
**Megoldás:** Nyissa meg újra a modult, ellenőrizze, hogy a tábla neve helyesen-e.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure tábla nem létezik.|  
|Az Azure table "{0}" nem létezik.|  
  
## <a name="error-0071"></a>0071 hiba  
 Kivétel lép fel, ha a megadott hitelesítő adatok helytelenek.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a megadott hitelesítő adatok helytelenek.  
  
 Ez a hiba akkor is megjelenik, ha a modul nem tud kapcsolódni egy HDInsight-fürtön.  
  
**Megoldás:** Tekintse át a bemeneti adatokat a modult, és ellenőrizze a fiók nevét és jelszavát.  
  
 Ellenőrizze, hogy a következő problémákat okozhat, hiba:  
  
-   Az adatkészlet sémája nem egyezik meg a cél datatable sémája.  
  
-   Oszlopnevek hiányzik vagy helytelenül  
  
-   Írás az oszlopok neveiben nem megengedett karaktereket tartalmazó tábla. Általában ilyen oszlopnevek szögletes zárójelben, de nem működik, ha oszlopnevek használata csak betűket és aláhúzásjeleket (_) szerkesztése  
  
-   Írni próbált karakterláncok tartalmazhat szimpla idézőjelek  
  
 Ha egy HDInsight-fürthöz való csatlakozáshoz, győződjön meg arról, hogy a cél fürtnek érhető el a megadott hitelesítő adatokkal.  
  
|Kivétel üzenetek|  
|------------------------|  
|A rendszer átadja a helytelen hitelesítő adatokat.|  
|Helytelen felhasználónév "{0}" vagy a jelszó átadása|  
  

## <a name="error-0072"></a>0072 hiba  
 Kivétel történt a kapcsolat időtúllépése esetén.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a kapcsolat időkorlátja. Ezt a hibaüzenetet fog kapni, ha jelenleg folyamatban van az adatforrás vagy a cél, például az internetkapcsolat lassú, kapcsolat problémái, vagy ha az adatkészlet nagy és/vagy az SQL-lekérdezést az adatok olvasásához bonyolult számításokat végez.  
  
**Megoldás:** Határozza meg, hogy vannak-e jelenleg az Azure storage és az internet lassú kapcsolat problémái.  
  
|Kivétel üzenetek|  
|------------------------|  
|Kapcsolódási időtúllépés történt.|  
  

## <a name="error-0073"></a>0073 hiba  
 Kivétel lép fel, ha hiba történik egy másik oszlop konvertálása közben.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem lehet átalakítani az oszlop egy másik.  A hibaüzenetet fog kapni, ha egy modulnak szüksége van egy adott típusú, és nem lehet átalakítani az oszlop az új típussá.  
  
**Megoldás:** Módosítsa a bemeneti adatkészlet, úgy, hogy az oszlop alapján a belső kivétel konvertálható.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült konvertálni oszlop.|  
|Nem sikerült konvertálni oszlop {0}.|  
  

## <a name="error-0074"></a>0074 hiba  
 Kivétel az, amikor a [metaadatainak szerkesztése](edit-metadata.md) ritka oszlopot a kategorikus megkísérli.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor a [metaadatainak szerkesztése](edit-metadata.md) ritka oszlopot a kategorikus megkísérli.  Ez a hiba fog kapni, ritka oszlopok átalakítása a kategorikus tett kísérlet során a **kategorikus győződjön** lehetőséget.  Az Azure machine Learning nem támogatja a ritka kategorikus tömbök, így sikertelen lesz, a modul.  
  
 <!--**Resolution:**
 Make the column dense by using [Convert to Dataset](convert-to-dataset.md) first or do not convert the column to categorical.  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Kategorikus ritka oszlop nem lehet konvertálni.|  
  

## <a name="error-0075"></a>0075 hiba  
Érvénytelen dobozolási függvény használatos, amikor egy adatkészlet quantizing kivétel lép fel.  
  
Az Azure Machine Learning a hiba akkor fordul elő, ha próbált bin adatokat egy nem támogatott módszerrel, vagy ha a paraméterkombinációk érvénytelenek.  
  
**Megoldás:**

Ezt az eseményt a hibakezelést az Azure Machine Learning, amely további testreszabási módszerek dobozolás, engedélyezett egy korábbi verzióban jelent meg. Jelenleg az összes dobozolási módszer alapul egy kiválasztási lehetőséget a legördülő listából, így technikailag már nem lehet ez a hibaüzenet jelenhet.

 <!--If you get this error when using the [Group Data into Bins](group-data-into-bins.md) module, consider reporting the issue in the [Azure Machine Learning forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=MachineLearning), providing the data types, parameter settings, and the exact error message.  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Érvénytelen dobozolási függvény.|  
  

## <a name="error-0077"></a>0077 hiba  
 Kivétel lép fel, amikor ismeretlen blobfájlra ír átadott mód.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy blob fájlt a cél- vagy leírásainak átadott érvénytelen argumentum.  
  
**Megoldás:** Adatok importálása és exportálása az Azure blob storage szolgáltatásba vagy onnan szinte összes modult a paraméter értéke az írási módban tartásával rendeli hozzá egy legördülő lista; használatával ezért már nem átadandó érték érvénytelen, és ez a hiba nem jelenik meg. Ezt a hibát egy későbbi kiadástól kezdve elavulttá válik.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem támogatott blob mód ír.|  
|Nem támogatott blob ír mód: {0}.|  
  

## <a name="error-0078"></a>0078 hiba  
 Kivétel történt Ha a HTTP lehetőséget a [adatok importálása](import-data.md) átirányítás jelző 3xx állapotkódot kap.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a HTTP lehetőséget a [adatok importálása](import-data.md) kap egy 3xx (301, 302, 304, stb.) átirányítás jelölő állapotkódot. Ez a hiba fog kapni, ha megpróbál csatlakozni egy HTTP-forrás, amely a böngésző átirányítja egy másik lapra. Biztonsági okokból webhelyek átirányítása nem engedélyezettek adatforrásként az Azure Machine Learning.  
  
**Megoldás:** Ha a webhely megbízható webhelyek, adja meg közvetlenül az átirányított URL-címet.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem engedélyezett HTTP-átirányítás|  
  

## <a name="error-0079"></a>0079 hiba  
 Kivétel lép fel, ha az Azure storage-tároló neve helytelenül van megadva.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az Azure storage-tároló neve helytelenül van megadva. Ez a hiba fog kapni, ha nem adta meg a tároló és a blob (fájl) nevének használatával **blob-tároló kezdődő elérési útját** lehetőséget az Azure Blob Storage írásakor.  
  
**Megoldás:** Nyissa meg újra a [adatok exportálása](export-data.md) modul, és ellenőrizze, hogy a blob a megadott elérési útja tartalmazza a tároló és a fájl nevét, a következő formátumban **tároló/filename**.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure storage-tároló neve nem megfelelő.|  
|Az Azure storage-tároló nevének "{0}" nem megfelelő; a tárolót a blob nevét, formátumú tároló/várt.|  
  

## <a name="error-0080"></a>0080 hiba  
 Kivétel lép fel, ha hiányzik az összes értéket tartalmazó oszlop nem engedélyezett a modul által.  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor egy vagy több, a modul által használt az oszlopok az összes hiányzó értékeket tartalmaz. Például ha egy modul van számítási feladatokat az egyes oszlopok összesített statisztikák, azt nem működik egy adatot nem tartalmazó oszlop. Ezekben az esetekben modul végrehajtása a kivétel miatt megáll.  
  
**Megoldás:** Nyissa meg újra az adatkészletet, és távolítsa el az összes hiányzó értéket tartalmazó oszlopokat.  
  
|Kivétel üzenetek|  
|------------------------|  
|Hiányzik az összes értéket tartalmazó oszlopok nem engedélyezettek.|  
|Oszlop {0} hiányzó összes értékkel rendelkezik.|  
  

## <a name="error-0081"></a>0081 hiba  
 Ha próbálják meg csökkenteni a dimenziók száma a szolgáltatás oszlopok száma egyenlő a bemeneti adatkészlethez, legalább egy szolgáltatás ritka oszlopot tartalmazó PEM-modul kivétel lép fel.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha a következő feltételek teljesülnek: (a) a bemeneti adatkészlet tartalmaz legalább egy ritka oszlop, és (b) a dimenziók a kért utolsó száma megegyezik a bemeneti dimenziók száma.  
  
**Megoldás:** Érdemes csökkenteni lehet kevesebb, mint a bemeneti adatok dimenziók száma a kimenetet a dimenziók száma. Ez a jellemző PEM-alkalmazásokban.   <!--For more information, see [Principal Component Analysis](principal-component-analysis.md).  -->
  
|Kivétel üzenetek|  
|------------------------|  
|A szolgáltatás a ritka oszlopokat tartalmazó adatkészlet kisebb, mint a szolgáltatás oszlopok számának csökkentése érdekében a dimenziók száma kell lennie.|  
 

## <a name="error-0082"></a>0082 hiba  
 Kivétel lép fel, amikor egy modell nem lehet sikeresen deszerializált.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor egy mentett gépi tanulási modellt vagy az átalakítás nem tölthető be az Azure Machine Learning modul használhatatlanná tévő változást eredményeként egy újabb verziójával.  
  
**Megoldás:** A tanítási kísérlet, amely a modell vagy az átalakítás előállított újrafuttatása és a modell vagy átalakítási resaved kell lennie.  
  
|Kivétel üzenetek|  
|------------------------|  
|Modell nem lehet deszerializálni, mert azt valószínűleg egy régebbi szerializációs formátum van szerializált. Újratanítás, és mentse el újra a modellt.|  
  

## <a name="error-0083"></a>0083 hiba  
 Kivétel lép fel, ha a használt adatkészlet képzési konkrétní typ delegáta learner nem használható.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha az adatkészlet nem kompatibilis a learner betanítása. Például az adatkészlet az egyes sorokban szereplő legalább egy hiányzó értéket tartalmaz, és ennek eredményeképpen a teljes adatkészlet szeretné hagyni képzés során. Más esetekben egyes gépi tanulási algoritmusok, például a rendellenességek észlelése nem várható, címkék jelen, és ehhez a kivételhez nagyvállalat, ha a címkék szerepelnek az adatkészlet.  
  
**Megoldás:** A bemeneti adatkészlet követelményeinek ellenőrzéséhez használt learner dokumentációjában. Vizsgálja meg az oszlopban láthatja az összes szükséges oszlopok jelen.  
  
|Kivétel üzenetek|  
|------------------------|  
|A betanításhoz használt adatkészlet nem érvényes.|  
|{0} a betanításhoz érvénytelen adatokat tartalmaz.|  
|{0} a betanításhoz érvénytelen adatokat tartalmaz. Learner típusa: {1}.|  
  

## <a name="error-0084"></a>0084 hiba  
 Kivétel lép fel, az R-szkriptek előállított pontszámok kiértékelésekor. Ez a jelenleg nem támogatott.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha megpróbálja használni a modulok egyik R-szkriptet, amely tartalmazza a pontszámok kimenete modell kiértékelése.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|R által előállított pontszámok kiértékelése használata jelenleg nem támogatott.|  
  

## <a name="error-0085"></a>0085 hiba  
 Kivétel lép fel, ha a parancsfájlok értékelésére egy hibaüzenettel meghiúsul.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, szintaktikai hibákat tartalmazó egyéni parancsfájl futtatása során.  
  
**Megoldás:** Tekintse át a kódot egy külső szerkesztő, és ellenőrizze a hibákat.  
  
|Kivétel üzenetek|  
|------------------------|  
|Hiba történt a parancsfájl kiértékelése közben.|  
|A következő hiba történt a parancsfájl kiértékelése, közben megtekintése további információk a kimeneti naplót:---hibaüzenete kezdete {0} interpret--- {1} ---vége hibaüzenete {0} interpret--- ------|  
  

## <a name="error-0086"></a>0086 hiba  
 Kivétel lép fel, amikor a számlálási átalakító érvénytelen.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor kiválaszt egy count táblázat alapján átalakítást, de a kijelölt átalakítás nem kompatibilis, az aktuális adatokat, vagy az új száma tábla.  
  
**Megoldás:** A modul támogatja az számát és a két különböző formátumokban az átalakítás alkotó szabályok mentése. Táblák száma összevonásakor, győződjön meg arról, hogy mindkét tábla egyesíteni szeretné ugyanazt a formátumot használja.  
  
Általában olyan szám-alapú átalakítást csak alkalmazható az átalakítás eredetileg létrehozott adatkészletként ugyanazzal a sémával rendelkező adatkészletekre.  
  
 <!-- For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Kivétel üzenetek|  
|------------------------|  
|A megadott számlálási átalakító érvénytelen.|  
|A bemeneti portját a számlálási átalakítás "{0}" érvénytelen.|  
|A bemeneti portját a számlálási átalakítás "{0}"nem egyesíthető a bemeneti portját a számlálási átalakítás"{1}". Ellenőrizze, hogy ellenőrizze a leltárhoz egyezések használt metaadatok.|  
  

## <a name="error-0087"></a>0087 hiba  
 Kivétel lép fel, ha meg van adva egy érvénytelen számlálóadat táblatípus tanulási modulok számát.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, próbál meg importálni a meglévő száma tábla, de a tábla nem kompatibilis, az aktuális adatokat, vagy az új száma tábla.  
  
**Megoldás:** Számos különböző formátumokban számát és a szabályok az átalakítás alkotó mentéséhez. Száma táblák összevonásakor, győződjön meg arról, hogy mindkettő ugyanazt a formátumot használja.  
  
 Általában olyan szám-alapú átalakítást csak alkalmazható az átalakítás eredetileg létrehozott adatkészletként ugyanazzal a sémával rendelkező adatkészletekre.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). -->
  

## <a name="error-0088"></a>0088 hiba  
 Kivétel lép fel egy érvénytelen számbavételi típus van megadva a tanulási modulok számát.  
  
 Ez a hiba az Azure Machine Learning történik, ha, mint egy másik számlálási módszer használata esetén támogatott featurization száma alapján.  
  
**Megoldás:** Általában számlálási módszerek közül választ egy legördülő listából, így ez a hibaüzenet nem jelenik meg.  
  
  <!--For general information, see [Learning with Counts](data-transformation-learning-with-counts.md). For requirements specific to creating and merging count-based features, see these topics:  
  
-   [Merge Count Transform](merge-count-transform.md)  
  
-   [Import Count Table](import-count-table.md)  
  
-   [Modify Count Table Parameters](modify-count-table-parameters.md)  
  -->
|Kivétel üzenetek|  
|------------------------|  
|Érvénytelen számbavételi típus van megadva.|  
|A megadott számbavételi típus "{0}" nem egy érvényes számbavételi típus.|  
  

## <a name="error-0089"></a>0089 hiba  
 Kivétel lép fel, ha a megadott számú osztályok nem éri el a tényleges száma egy adatkészletben leltárhoz használt osztályok.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor egy count táblát hoz létre, és a felirat oszlop tartalmazza, mint a modul paraméterei az osztályok különböző számú.  
  
**Megoldás:** Ellenőrizze az adatkészlet, és ismerje meg, hogy pontosan eltérő értékek száma (lehetséges osztályok) címke oszlop található. A count tábla létrehozásakor meg kell adnia legalább az osztályok száma.  
  
 A count tábla automatikusan nem tudja megállapítani a rendelkezésre álló leltárosztályok számát.  
  
 A count tábla létrehozásakor nem adható meg a 0, vagy a szám, amely kisebb, mint a tényleges hány osztály szerepel a címke oszlop.  
  
|Kivétel üzenetek|  
|------------------------|  
|Az osztályok száma helytelen. Győződjön meg arról, hogy az osztályok, adja meg a paraméter ablaktábla száma kisebb, mint a címke oszlop osztályok száma egyenlő.|  
|A megadott osztályok száma "{0}"verziójú, amely nem nagyobb, mint egy címke értéke"{1}" leltározza az adatkészlet. Győződjön meg arról, hogy az osztályok, adja meg a paraméter ablaktábla száma kisebb, mint a címke oszlop osztályok száma egyenlő.|  
  

## <a name="error-0090"></a>0090 hiba  
 Kivétel lép fel, ha a Hive-tábla létrehozása meghiúsul.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, használatakor [adatok exportálása](export-data.md) vagy egy másik lehetőség az adatok mentése egy HDInsight-fürt és a megadott Hive-tábla nem hozható létre.  
  
**Megoldás:** Ellenőrizze a fürthöz társított Azure-tárfiók nevére, és győződjön meg arról, hogy a modul tulajdonságai használ ugyanazzal a fiókkal.  
  
|Kivétel üzenetek|  
|------------------------|  
|A Hive-táblában nem hozható létre. Egy HDInsight-fürtöt hogy a fürthöz társított Azure storage-fiók neve megegyezik a mi átadott modul paraméterrel.|  
|A Hive-tábla "{0}" nem sikerült létrehozni. Egy HDInsight-fürtöt hogy a fürthöz társított Azure storage-fiók neve megegyezik a mi átadott modul paraméterrel.|  
|A Hive-tábla "{0}" nem sikerült létrehozni. Egy HDInsight-fürtöt, győződjön meg, hogy a fürthöz társított Azure storage-fiók neve "{1}".|  
 

## <a name="error-0100"></a>0100 hiba  
 Kivétel lép fel, ha egy nem támogatott nyelvet meg van adva egy egyéni modult.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, egy egyéni modult és name tulajdonsága készítése során a **nyelvi** egy egyéni modult xml-definíciós fájljának elem értéke érvénytelen. Az egyetlen érvényes érték a tulajdonság jelenleg `R`. Példa:  
  
 `<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Megoldás:** Ellenőrizze, hogy a név tulajdonsága a **nyelvi** egyéni modul xml-definíciós fájljának elemében van beállítva `R`. Mentse a fájlt, frissítse az egyéni modult zip-csomagját, és próbálja meg újra hozzá az egyéni modult.  
  
|Kivétel üzenetek|  
|------------------------|  
|A megadott nyelv nem támogatott egyéni modul|  
  

## <a name="error-0101"></a>0101 hiba  
 Minden port és a paraméter azonosítóinak egyedinek kell lennie.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, egy vagy több portot vagy a paraméterek hozzárendelése esetén ugyanaz az azonosító egy egyéni XML-definíciós fájljának modulban.  
  
**Megoldás:** Ellenőrizze, hogy a azonosító minden port és a paraméterek értékei egyedi. Mentse az xml-fájlt, frissítse az egyéni modult zip-csomagját, és próbálja meg újra hozzá az egyéni modult.  
  
|Kivétel üzenetek|  
|------------------------|  
|Minden port és azonosítók paraméter egy modul egyedinek kell lennie|  
|A modul "{0}" argumentuma ismétlődő port és azonosítók. Összes port/argumentum-azonosítót a modulhoz egyedinek kell lennie.|  
  

## <a name="error-0102"></a>0102 hiba  
 Fordul elő, amikor nem nyerhetők ki a ZIP-fájlt.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor egy .zip kiterjesztésű tömörített csomagot importál, de a csomag nem érhető egy zip-fájlt, vagy a fájl nem használja a támogatott zip formátumba.  
  
**Megoldás:** Ellenőrizze, hogy a kiválasztott fájl érvényes .zip fájlt, és, hogy lett-e tömörített egyik támogatott tömörítési algoritmus használatával.  
  
 Megjelenik a hibaüzenet, a tömörített formátumban adatkészletek importálásakor, ha győződjön meg az összes abban található fájlokat a támogatott formátumok valamelyikével, Unicode formátumban vannak.  <!--For more information, see [Unpack Zipped Datasets](unpack-zipped-datasets.md).  -->
  
 Próbálja meg újra hozzáadni a kívánt fájlokat egy új tömörített ZIP mappába, majd próbálja újra hozzá az egyéni modult.  
  
|Kivétel üzenetek|  
|------------------------|  
|A megadott ZIP fájl formátuma nem megfelelő formátumú|  


## <a name="error-0103"></a>0103 hiba  
 Ha egy ZIP-fájl nem tartalmaz .xml fájlok lépett fel  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az egyéni modult zip-csomagját nem tartalmaz a modul definition (.xml) fájlok. Ezeket a fájlokat a zip-csomagját (például nem almappa belül.) gyökérmappájában található kell  
  
**Megoldás:** Ellenőrizze, hogy egy vagy több xml modul definíciós fájl a gyökérmappában található azon a zip-csomagját a oly módon, azt egy ideiglenes mappába a meghajtón található. XML-fájlok közvetlenül a mappát, amelybe kibontotta a zip-csomagját, kell lennie. Győződjön meg arról, hogy a zip-csomagját, amely nem választja ki a zip, ezzel létrehoz belül a zip-csomagját almappa neve megegyezik a zip-kijelölt mappa xml-fájlokat tartalmazó mappa létrehozásakor.  
  
|Kivétel üzenetek|  
|------------------------|  
|A megadott ZIP-fájlja nem tartalmaz a bármely modul definíciós fájlok (.xml)|  


## <a name="error-0104"></a>Hiba történt a 0104  
 Fordul elő, ha egy modul csomagdefiníciós fájl hivatkozik egy parancsfájlt, amely nem található  
  
 Ez a hiba az Azure Machine Learning fordul elő, amikor egy egyéni modult xml-definíciós fájljának hivatkozik egy parancsfájlt a **nyelvi** elem, amely nem szerepel a zip-csomagját. A parancsfájl-fájl elérési útja van megadva a **sourceFile** tulajdonságát a **nyelvi** elemet. A forrásfájl elérési útja a zip-csomagját (a modul xml-definíciós fájlokat megegyező helyen) gyökeréhez viszonyítva. Ha a parancsfájl almappa, meg kell adni a parancsfájl relatív elérési útját. Például ha mentette az összes parancsfájl egy **myScripts** mappán belül, a zip-csomagját a **nyelvi** elem lenne az elérési út hozzáadása a **sourceFile** tulajdonság alább. Példa:  
  
 `<Language name="R" sourceFile="myScripts/CustomAddRows.R" entryPoint="CustomAddRows" />`  
  
**Megoldás:** Győződjön meg arról, hogy értékét a **sourceFile** tulajdonságot a **nyelvi** az egyéni modul xml-definíció elem helyességéről, valamint, hogy a forrásfájl megtalálható-e a megfelelő relatív elérési út a zip-csomagját.  
  
|Kivétel üzenetek|  
|------------------------|  
|R-szkript hivatkozott fájl nem létezik.|  
|Hivatkozott R-parancsfájl "{0}" nem található. Győződjön meg arról, hogy a fájl relatív elérési útja helyes-e a definíciók helyről.|  


## <a name="error-0105"></a>0105 hiba  
 Ez a hibaüzenet jelenik meg, ha egy modul csomagdefiníciós fájl tartalmaz egy nem támogatott paramétertípust  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor a hoz létre egy egyéni modult xml-definíció és a egy paraméter vagy a definícióban argumentum típusa nem egyezik meg a támogatott típusú.  
  
**Megoldás:** Győződjön meg arról, hogy minden tulajdonság **Arg** támogatott típusú elem a modul egyéni xml-definíciós fájlban.  
  
|Kivétel üzenetek|  
|------------------------|  
|A paramétertípus nem támogatott.|  
|Paraméter típusa nem támogatott "{0}" megadott.|  


## <a name="error-0106"></a>0106 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl definiálja a bemeneti típusa nem támogatott  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor egy bemeneti portját a egy egyéni modult XML-definíció típusa nem egyezik meg a támogatott típusú.  
  
**Megoldás:** Győződjön meg róla, hogy egy egyéni modult XML-definíciós fájljának bemeneti elem tulajdonság támogatott típusú.  
  
|Kivétel üzenetek|  
|------------------------|  
|Bemenet típusa nem támogatott.|  
|Nem támogatott a bemeneti típus "{0}" megadott.|  


## <a name="error-0107"></a>0107 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl egy nem támogatott kimenet típusa határozza meg.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha a kimeneti portra, a modul egyéni xml-definíció típusa nem egyezik meg a támogatott típusú.  
  
**Megoldás:** Győződjön meg róla, hogy az egyéni modul xml-definíciós fájl kimeneti elem tulajdonság támogatott típusú.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem támogatott kimeneti típus.|  
|Kimenet típusa nem támogatott "{0}" megadott.|  


## <a name="error-0108"></a>0108 hiba  
 Ha egy modul csomagdefiníciós fájl határozza meg a további bemeneti vagy kimeneti portok, mint amennyit lépett fel  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor egy egyéni modult xml-definíció túl sok bemeneti vagy kimeneti portok vannak meghatározva.  
  
**Megoldás:** Ellenőrzi, hogy a bemeneti és kimeneti portok az egyéni modul xml-definícióban meghatározott maximális száma nem haladja meg a támogatott portok maximális számát.  
  
|Kivétel üzenetek|  
|------------------------|  
|Túllépte a bemeneti vagy kimeneti portok támogatott számát.|  
|Támogatott túllépése száma "{0}" portokat. Maximálisan engedélyezett számú "{0}portok van{1}".| 

## <a name="error-0109"></a>0109 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl egy Oszlopválasztó helytelenül meghatározása  
  
 Ez a hiba az Azure Machine Learning jön létre, ha a szintaxis egy oszlop-választó argumentumként egy egyéni modult xml-definíció hibát tartalmaz.  
  
**Megoldás:** Ez a hiba jön létre, ha a szintaxis egy oszlop-választó argumentumként egy egyéni modult xml-definíció hibát tartalmaz.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlopválasztó szintaxis nem támogatott.|  
  

## <a name="error-0110"></a>0110 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl egy oszlop-választó, amely hivatkozik a nem létező bemeneti portját ID határozza meg.  
  
 Ez a hiba az Azure Machine Learning jön létre. Ha a *portId* tulajdonságot a Tulajdonságok elemben, egy Arg ColumnPicker típusa nem egyezik egy bemeneti porthoz azonosító értékét.  
  
**Megoldás:** Ellenőrizze, hogy a portId tulajdonságában megtalálható a bemeneti portját a modul egyéni xml-definícióban meghatározott azonosító értékét.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlopválasztó hivatkozik egy nem létező bemeneti porthoz.|  
|Oszlopválasztó hivatkozik a nem létező bemeneti portját ID "{0}".|  
  

## <a name="error-0111"></a>0111 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl érvénytelen tulajdonság határozza meg.  
  
 Ez a hiba az Azure Machine Learning jön létre, az egyéni modul XML-definíció elem rendelt érvénytelen tulajdonság.  
  
**Megoldás:** Ellenőrizze, hogy a tulajdonság egyéni modult elem által támogatott.  
  
|Kivétel üzenetek|  
|------------------------|  
|Vlastnost definíciója érvénytelen.|  
|Erőforrástulajdonság-definíciót "{0}" értéke érvénytelen.|  
  

## <a name="error-0112"></a>0112 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl nem elemezhető.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha egy hiba megakadályozza, hogy az egyéni XML-definíció érvényes XML-fájlként elemezhető modul xml formátumban.  
  
**Megoldás:** Győződjön meg arról, hogy minden elem megnyitása és megfelelően lezárva. Győződjön meg arról, hogy nincsenek-e hibák az XML-formázáshoz.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült elemezni a modul csomagdefiníciós fájl.|  
|Nem sikerült elemezni a modul csomagdefiníciós fájl "{0}".|  
  

## <a name="error-0113"></a>0113 hiba  
 Fordul elő, ha egy modul csomagdefiníciós fájl hibákat tartalmaz.  
  
 Ez a hiba az Azure Machine Learning jön létre, amikor az egyéni modul XML-definíciós fájljának elemezhető, de hibák, például az egyéni modulok által nem támogatott elemeket definícióját tartalmazza.  
  
**Megoldás:** Győződjön meg arról, hogy az egyéni modult definíciós fájl határozza meg az elemek és egyéni modulok által támogatott tulajdonságok.  
  
|Kivétel üzenetek|  
|------------------------|  
|A modul csomagdefiníciós fájl hibákat tartalmaz.|  
|A modul csomagdefiníciós fájl "{0}" hibákat tartalmaz.|  
|A modul csomagdefiníciós fájl "{0}" hibákat tartalmaz. {1}|  
  

## <a name="error-0114"></a>0114 hiba  
 Egy egyéni modult sikertelen készítése során lépett fel.  
  
 Ez a hiba az Azure Machine Learning jön létre, ha egy egyéni modult build sikertelen. Ez akkor fordul elő, ha egy vagy több egyéni modul kapcsolatos hiba történt az egyéni modul hozzáadása. A további hibákat jelentett belül ez a hibaüzenet.  
  
**Megoldás:** Oldja meg a kivételre vonatkozó üzenet jelentett hibákat.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült létrehozni az egyéni modult.|  
|A modul egyéni buildek hiba (hibák) miatt sikertelen volt: {0}|  
  

## <a name="error-0115"></a>0115 hiba  
 Fordul elő, amikor egy egyéni modult alapértelmezett parancsfájl kiterjesztése nem támogatott.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha egy parancsfájl adja meg egy egyéni modul által használt ismeretlen fájlnév-kiterjesztés.  
  
**Megoldás:** Ellenőrizze a formátum és a fájlnév kiterjesztése bármilyen parancsfájl fájlt az egyéni modult is tartalmaz.  
  
|Kivétel üzenetek|  
|------------------------|  
|A bővítmény nem támogatott az alapértelmezett parancsprogramok.|  
|Nem támogatott fájl bővítmény {0} alapértelmezett parancsfájlt.|  
  

## <a name="error-0121"></a>0121 hiba  
 Amikor SQL ír sikertelen, mert a tábla nem lehet írni lépett fel  
  
 Ez a hiba az Azure Machine Learning jön létre, ha használja a [adatok exportálása](export-data.md) modul eredmények mentéséhez és a egy SQL database egyik táblájában a tábla nem lehet írni. Általában ez a hiba Ha láthatók a [adatok exportálása](export-data.md) modul sikeresen kapcsolatot hoz létre az SQL Server-példányhoz, de ezután nem lehet írni a tábla tartalmát az Azure ML-adatkészletet.  
  
**Megoldás:**
 - Nyissa meg a Tulajdonságok panelen, a [adatok exportálása](export-data.md) modul, és ellenőrizze, hogy az adatbázis és tábla neve helyesen lett megadva. 
 - Tekintse át az adatkészlet exportál, és ügyeljen arra, hogy az adatok kompatibilis a céltábla sémája.
 - Győződjön meg arról, hogy az SQL jelentkezzen be társított a felhasználónév és jelszó a tábla írási engedélyekkel rendelkezik. 
 - Ha a kivétel tartalmaz további információ a hibáról az SQL Serverből, adott információk használatával javíthatja a megadottakat.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem lehet írni kiszolgálóhoz csatlakoztatott táblába.|  
|Nem lehet írni az Sql-táblát: {0}|  


## <a name="error-0122"></a>0122 hiba  
 Kivétel lép fel, ha több súly oszlop van megadva, és csak egy engedélyezett.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor súlyozási oszlopként, hogy túl sok oszlop kijelölve.  
  
**Megoldás:** Tekintse át a bemeneti adatkészlet és a metaadatait. Győződjön meg arról, hogy csak egy oszlop súlyok tartalmaz.  
  
|Kivétel üzenetek|  
|------------------------|  
|Súly több oszlop van megadva.|  


## <a name="error-0123"></a>0123 hiba  
 Kivétel lép fel, ha vektorok oszlop van megadva címke oszlopra.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, a címke oszlopként egy vektoros használatakor.  
  
**Megoldás:** Módosítsa az oszlop, ha a szükséges adatok formátumát, vagy válasszon másik oszlopot.  
  
|Kivétel üzenetek|  
|------------------------|  
|Oszlop vektorok címke oszlop van megadva.|  


## <a name="error-0124"></a>0124 hiba  
 Kivétel lép fel, ha nem numerikus oszlop van megadva, a súly oszlopban kell.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Nem numerikus oszlophoz a súly oszlop van megadva.|  
  


## <a name="error-0125"></a>0125 hiba  
 Fordul elő, ha több adatkészlet sémája nem egyezik.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Az adatkészlet sémája nem egyezik.|  


## <a name="error-0126"></a>0126 hiba  
 Kivétel lép fel, ha a felhasználó adja meg, amely nem támogatott az Azure ML SQL tartományhoz.  
  
 Ez a hiba akkor jön létre, amikor a felhasználó adja meg, amely nem támogatott az Azure Machine Learning SQL tartományhoz. A hibaüzenetet fog kapni, ha olyan tartományban, amely nem szerepel az engedélyezési listán egy adatbázis-kiszolgálóhoz való csatlakozáshoz. Az engedélyezett SQL-tartományok jelenleg: ". database.windows.net",". cloudapp.net", vagy a ". database.secure.windows.net". A kiszolgáló, egy Azure SQL-kiszolgálóhoz vagy a kiszolgáló Azure-beli virtuális gépen kell lennie.  
  
**Megoldás:** Nyissa meg újra a modult. Győződjön meg arról, hogy az SQL database-kiszolgáló egyikéhez tartozik az elfogadott tartományok:  
  
-   .database.windows.net  
  
-   .cloudapp.net  
  
-   .database.secure.windows.net  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem támogatott az SQL-tartomány.|  
|Az SQL-tartomány {0} jelenleg nem támogatott az Azure gépi tanulás|  
  

## <a name="error-0127"></a>0127 hiba  
 Kép képpontos mérete meghaladja az engedélyezett korlátot  
  
 Ez a hiba akkor fordul elő, ha egy kép adatkészletből besoroláshoz tartozó képek olvas, és a képek nagyobb, mint amit a modell képes kezelni.  
  
 <!--**Resolution:**
 For more information about the image size and other requirements, see these topics:  
  
-   [Import Images](import-images.md)  
  
-   [Pretrained Cascade Image Classification](pretrained-cascade-image-classification.md)  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Kép képpontos mérete meghaladja az engedélyezett korlátot.|  
|A fájl képpontos képméret "{0}" meghaladja az engedélyezett korlátot: "{1}"|  


## <a name="error-0128"></a>0128 hiba  
 Feltételes valószínűségek kategorikus oszlopok száma meghaladja a korlátot.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Feltételes valószínűségek kategorikus oszlopok száma meghaladja a korlátot.|  
|Feltételes valószínűségek kategorikus oszlopok száma meghaladja a korlátot. Oszlopok{0}"és"{1}"a problémás pár vannak.|  


## <a name="error-0129"></a>0129 hiba  
 Az adatkészlet szereplő oszlopok száma meghaladja az engedélyezett korlátot.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Az adatkészlet szereplő oszlopok száma meghaladja az engedélyezett korlátot.|  
|Az adatkészletben szereplő oszlopok száma "{0}"meghaladja a megengedett."|  
|Az adatkészletben szereplő oszlopok száma "{0}"meghaladja az engedélyezett korlátot"{1}". "|  
|Az adatkészletben szereplő oszlopok száma "{0}engedélyezett meghaladja{1}"korlátozza az"{2}". "|  
## <a name="error-0130"></a>0130 hiba  
 Kivétel lép fel, amikor az összes sort a betanítási adatkészletet a hiányzó értékeket tartalmaznak.  
  
 Ez akkor fordul elő, ha a betanítási adatkészletet néhány oszlopa üres.  
  
**Megoldás:** Használja a [Clean Missing Data](clean-missing-data.md) modul összes hiányzó értékekkel rendelkező oszlopok eltávolításával.  
  
|Kivétel üzenetek|  
|------------------------|  
|Betanítási adatkészletet szereplő összes sor a hiányzó értékeket tartalmaznak.  Fontolja meg az Clean Missing Data modullal távolítsa el a hiányzó értékeket.|  
 

## <a name="error-0131"></a>0131 hiba  
 Kivétel lép fel, ha egy vagy több adatkészletet egy zip-fájlban nem tud kicsomagolt és megfelelően regisztrálva  
  
 Ez a hiba akkor jön létre, amikor egy vagy több adatkészletet egy zip-fájlt a kicsomagolt és megfelelően olvasása sikertelen. Ez a hiba fog kapni, ha a kicsomagolás sikertelen, mert maga a zip-fájlt vagy fájlokat az egyik sérült, vagy rendszerhiba kicsomagolása, és a egy fájl kibontása közben.  
  
**Megoldás:** A hibaüzenetben megadott adatok segítségével meghatározhatja a folytatásról.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült tömörített adathalmazok feltöltése|  
|Adatkészlet zip {0} a következő üzenettel meghiúsult: {1}|  
|Adatkészlet zip {0} sikertelen volt egy {1} üzenettel kivétel: {2}|  
  

## <a name="error-0132"></a>0132 hiba  
 Fájlnév nélkül lett megadva a kicsomagolás; több fájl zip-fájlban találhatók.  
  
 Ez a hiba jön létre, ha a fájlnév nélkül lett megadva a kicsomagolás; több fájl zip-fájlban találhatók. Ez a hiba fog kapni, ha a .zip-fájlban csak egy tömörített fájl található, de nem adta meg egy fájlt a a kivonási a **Unpack adatkészlet** szöveg mezőben az a **tulajdonság** modul panel. Jelenleg csak egy fájlt kinyerhetők a modul minden egyes futtatásakor.  
  
**Megoldás:** A hibaüzenet a .zip-fájlban található fájlok listáját tartalmazza. Másolja ki a kívánt fájl nevét, és illessze be azt a **Unpack adatkészlet** szövegmezőben.  
  
|Kivétel üzenetek|  
|------------------------|  
|Zip-fájl több fájlokat; tartalmazza. Bontsa ki a fájlt kell megadnia.|  
|A fájl egynél több fájlt tartalmazza. Adja meg a fájl kibontásához. A következő fájlok találhatók: {0}|  
  

## <a name="error-0133"></a>0133 hiba  
 A megadott fájl nem található a zip-fájl  
  
 Ez a hiba jön létre, amikor a megadott fájlnév a **Unpack adatkészlet** mezőjében a **tulajdonság** ablaktáblában nem egyezik meg a minden fájlhoz a .zip-fájlban található nevét. Ennek a hibának a leggyakoribb okok a következők: egy írja be a hiba, vagy bontsa ki a fájlhoz a megfelelő archív fájl keresése.  
  
**Megoldás:** Nyissa meg újra a modult. Ha a kibontani kívánt fájl neve megjelenik a található fájlok listáját, másolja ki a fájl nevét, és illessze be azt a **Unpack adatkészlet** tulajdonságnál. Ha nem látja a kívánt fájl nevét a listában, ellenőrizze, hogy a megfelelő zip-fájlt, és a helyes-e a kívánt fájl nevét.  
  
|Kivétel üzenetek|  
|------------------------|  
|A megadott fájl nem volt található int a zip-fájlt.|  
|A megadott fájl nem található. A következő fájlok találhatók: {0}|  
  

## <a name="error-0134"></a>0134 hiba
Kivétel lép fel, ha a címke oszlop hiányzik, vagy címkézett sorok száma nem elegendő.  
  
Ez akkor fordul elő, ha a modulnak szüksége van egy olyan címke oszlop, de Ön nem tartalmazza az egyik oszlop kiválasztása, vagy a címke oszlop túl sok érték hiányzik.

Ez a hiba akkor is előfordulhat, ha egy korábbi művelet módosítja az adatkészletet úgy, hogy a megfelelő sorok érhetők el egy alsóbb rétegbeli művelet. Tegyük fel például, a kifejezés használata a **partíciót és minta** modul adatkészlet elosztandó szám értékeket. Nem található egyezés a kifejezés, ha az egyik partíció eredő adatkészlet lesz üres.

Megoldás: 

 Ha egy címke oszlop szerepel a kijelölés, de nem ismerhető fel, használja a [metaadatainak szerkesztése](edit-metadata.md) modult, jelölje meg a címke oszlopként.
  
  <!--Use the [Summarize Data](summarize-data.md) module to generate a report that shows how many values are missing in each column. -->Ezután használhatja a [Clean Missing Data](clean-missing-data.md) modul hiányzó értékekkel sorok eltávolítása a címke oszlopban. 

 Ellenőrizze a bemeneti adatkészletek, győződjön meg arról, hogy érvényes adatokat, és megfelelnek a művelet elegendő sorok tartalmazzák. Számos algoritmusok hibaüzenetet hoz létre, ha néhány minimális száma adatsor van szükségük, de az adatok csak néhány sort, vagy csak egy fejlécet tartalmaz.
  
|Kivétel üzenetek|
|------------------------|
|Kivétel lép fel, ha a címke oszlop hiányzik, vagy címkézett sorok száma nem elegendő.|  
|Kivétel lép fel, ha a címke oszlop hiányzik, vagy kevesebb, mint {0} sorok címkével|  
  

## <a name="error-0135"></a>0135 hiba  
 Csak a középpontját-alapú fürt támogatott.  
  
**Megoldás:** Ez a hibaüzenet fürtözési modell, amely egy egyéni fürtözési algoritmust, amely nem használja a fürt inicializálása centroids alapján kiértékelheti, hogy próbált léphetnek fel.  
  
  <!--You can use [Evaluate Model](evaluate-model.md) to evaluate clustering models that are based on the  [K-Means Clustering](k-means-clustering.md) module. For custom algorithms, use the [Execute R Script](execute-r-script.md) module to create a custom evaluation script.  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Csak a középpontját-alapú fürt támogatott.|  
  

## <a name="error-0136"></a>0136 hiba  
 Nincs fájlnév érkezett; nem sikerült a fájl eredményeképpen feldolgozni.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Nincs fájlnév érkezett; nem sikerült a fájl eredményeképpen feldolgozni.|  
  

## <a name="error-0137"></a>0137 hiba  
 Az Azure Storage SDK táblázat tulajdonságai és adatkészletoszlopok konvertálása olvasási vagy írási során hibába ütközött.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Az Azure table storage tulajdonság és az adatkészlet oszlopok közötti konverziós hiba.|  
|Az Azure table storage tulajdonság és az adatkészlet oszlopok közötti konverziós hiba. További információ: {0}|  

## <a name="error-0138"></a>0138 hiba  
 Memória már sor került, nem lehet a modul teljes futtatását. Az adatkészlet egyszerűsítés segíthet a probléma enyhítése.  
  
 Ez a hiba akkor fordul elő, ha a modul futtató van szüksége több memóriára érhető el az Azure container. Ez akkor fordulhat elő, ha egy nagy méretű adathalmazt dolgozik, és az aktuális művelet nem fér el a memóriába.  
  
**Megoldás:** Ha próbált egy nagy méretű adathalmazt olvassa el, és a művelet nem hajtható végre, egyszerűsítés az adatkészlet segíthetnek.  
  
  <!--If you use the visualizations on datasets to check the cardinality of columns, only some rows are sampled. To get a full report, use [Summarize Data](summarize-data.md). You can also use the [Apply SQL Transformation](apply-sql-transformation.md) to check for the number of unique values in each column.  
  
 Sometimes transient loads can lead to this error. Machine support also changes over time. 
  
 Try using [Principal Component Analysis](principal-component-analysis.md) or one of the provided feature selection methods to reduce your dataset to a smaller set of more feature-rich columns: [Feature Selection](feature-selection-modules.md)  -->
  
|Kivétel üzenetek|  
|------------------------|  
|Memória már sor került, nem lehet a modul teljes futtatását.|  
  

## <a name="error-0139"></a>0139 hiba  
 Kivétel lép fel, ha az nem lehet átalakítani egy oszlop egy másik.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, próbál meg egy másik adattípusúra konvertálása egy olyan oszlop, de típus vagy a modul által az aktuális művelet nem támogatott.  
  
 A hiba akkor is megjelenhet, ha egy modul megkísérli implicit módon konvertálni az adatokat az aktuális modult követelményeinek, de a konvertálás nem lehetséges.  
  
**Megoldás:**

1. Tekintse át a bemeneti adatokat, és határozza meg a használni kívánt oszlop pontos adattípusa és határozzák meg a hiba az oszlop adattípusát. Egyes esetekben előfordulhat, hogy úgy gondolja, hogy az adatok típusa helyes, de találja, hogy egy felsőbb szintű művelet módosította az adattípus vagy egy oszlop használatát. Használja a [metaadatainak szerkesztése](edit-metadata.md) modul oszlop-metaadatot visszaállítani az eredeti állapotba. 
2. Tekintse meg a modul súgóoldalt a megadott művelet esetében a követelmények ellenőrzéséhez. Határozza meg az aktuális modul által támogatott adattípusokat és az értékek milyen tartomány használatát támogatják. 
 <!--3. If values need to be truncated, rounded, or outliers removed, use the [Apply Math Operation](apply-math-operation.md) or [Clip Values](clip-values.md) modules to make corrections.-->
4. Érdemes lehet, hogy konvertálni, vagy más oszlop alakítható. A következő modulok minden jelentős rugalmasságot és a power adja meg az adatok módosításához: 
 <!--
   + [Apply SQL Transformation](apply-sql-transformation.md)
   + [Execute R Script](execute-r-script.md)
-->   
   + [Python-szkript végrehajtása](execute-python-script.md).  

> [!NOTE]
> Még mindig nem működik? Vegye figyelembe, hogy további visszajelzés küldése a problémáról, és segítsen fejlesztése jobban hibaelhárítási útmutatót. Csak ezen az oldalon a visszajelzés elküldése, és adja meg a hiba, és az adattípus konvertálása sikertelen generáló modul nevét.
  
|Kivétel üzenetek|  
|------------------------|  
|Átalakítás nem engedélyezett.|  
|Nem lehet átalakítani: {0}.|  
|Nem lehet átalakítani: {0}, sorban {1}.|  
|Nem sikerült átalakítani típusú oszlop {0} típusú oszlopra {1} sorban {2}.|  
|Nem sikerült átalakítani az oszlop "{2}" típusú {0} típusú oszlopra {1} sorban {3}.|  
|Nem sikerült átalakítani az oszlop "{2}" típusú {0} oszlopra "{3}" típusú {1} sorban {4}.| 

## <a name="error-0140"></a>0140 hiba  
 Kivétel lép fel, ha az átadott oszlop argumentum nem tartalmaz más oszlopok címke oszlopot kivéve.  
  
 Ez a hiba akkor fordul elő, ha csatlakozott egy adatkészlet a modul, amely több oszlop funkciókat, beleértve a szükséges, de csak a címke oszlopban megadott.  
  
**Megoldás:** Válasszon legalább egy szolgáltatás oszlopot tartalmazza az adatkészletben.  
  
|Kivétel üzenetek|  
|------------------------|  
|Megadott oszlop készlet nem tartalmaz más oszlopok címke oszlopot kivéve.|  
  

## <a name="error-0141"></a>0141 hiba  
 Kivétel lép fel, ha a szám, karakterlánc típusú oszlopokra és a kiválasztott numerikus oszlopok és a kategorikus egyedi értékeket túl kicsi.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha az nem elég egyedi értékek a kiválasztott oszlop végrehajtani a műveletet.  
  
**Megoldás:** Egyes műveletek statisztikai műveletek végrehajtása a szolgáltatás és a kategorikus oszlopok, és ha nem elég érték, a művelet sikertelen lehet, vagy érvénytelen eredményt adja vissza. Ellenőrizze az adatkészlet ahhoz, hogy a szolgáltatás és a felirat oszlopban található értékek száma, és ellenőrizze, hogy a végrehajtani kívánt művelet statisztikailag érvényes.  
  
 Ha a forrás adatkészlet érvényes, is megkereshet e néhány felsőbb rétegbeli adat manipulálásával vagy metaadat-művelet módosította az adatokat, és néhány érték eltávolítása.  
  
 Ha felfelé irányuló műveletek felosztása, mintavételi vagy az Újraszámítás, győződjön meg arról, hogy a kimenetek tartalmaz-e a várt számú sort és értékeket.  
  
|Kivétel üzenetek|  
|------------------------|  
|A kiválasztott numerikus és egyedi értékeket a kategorikus és karakterlánc oszlopok száma nem elég nagy.|  
|A kijelölt numerikus oszlopok és egyedi értékeket a kategorikus és karakterlánc típusú oszlopokra teljes száma (jelenleg {0}) kell lennie, mint {1}|  
  

## <a name="error-0142"></a>0142 hiba  
 Kivétel lép fel, amikor a rendszer nem tölthető be a tanúsítvány hitelesítéséhez.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|A tanúsítvány nem tölthető be.|  
|A tanúsítvány {0} nem tölthető be. Az ujjlenyomat {1}.|  
  

## <a name="error-0143"></a>0143 hiba  
 Nem lehet elemezni a felhasználó által megadott URL-címet, amely kellene lennie a Githubról.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha megad egy URL-cím érvénytelen, és a modulnak szüksége van egy érvényes GitHub URL-CÍMÉT.  
  
**Megoldás:** Győződjön meg arról, hogy az URL-cím vonatkozik-e egy érvényes GitHub-adattárból. Másik hely típusok nem támogatottak.  
  
|Kivétel üzenetek|  
|------------------------|  
|URL-je nem a github.com.|  
|URL-je nem a github.com: {0}|  

## <a name="error-0144"></a>0144 hiba  
 Felhasználó által megadott GitHub URL-címét a várt rész hiányzik.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha megad egy GitHub forrás használatával URL-cím formátuma érvénytelen.  
  
**Megoldás:** Ellenőrizze, hogy a GitHub-adattár URL-cím érvényes-e, és \blob\ vagy \tree végződik\\.  
  
|Kivétel üzenetek|  
|------------------------|  
|Nem lehet elemezni a GitHub URL-CÍMÉT.|  
|GitHub URL-címe nem elemezhető (várt érték "\blob\\" vagy "\tree\\" után a tárház nevének): {0}|  

## <a name="error-0145"></a>0145 hiba  
 A replikációs könyvtár valamilyen okból nem hozható létre.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a modul nem tudta létrehozni a megadott könyvtárban.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Replikációs könyvtár nem hozható létre.|  
  

## <a name="error-0146"></a>0146 hiba  
 Ha a felhasználói fájlok a helyi könyvtárba kicsomagolt, a kombinált elérési útja túl hosszú lehet.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor, kibontására fájlokat, de néhány fájlnév túl hosszú, amikor a kicsomagolt.  
  
**Megoldás:** Szerkesztése a fájlneveket, úgy, hogy a kombinált elérési út és fájlnév nem hosszabb 248 karakternél.  
  
|Kivétel üzenetek|  
|------------------------|  
|Replikációs útvonal 248 karakternél hosszabb, Rövidítse le a szkript neve vagy elérési útja.|  

## <a name="error-0147"></a>0147 hiba  
 Nem sikerült letölteni szolgáltatáshasználatot GitHub-okból  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha nem tudja olvasni vagy a megadott fájlok letöltése a githubról.  
  
**Megoldás:** Lehet, hogy a probléma ideiglenes; Próbálja meg elérni a fájlokat egy másik időpontban. Vagy ellenőrizze, hogy rendelkezik-e a szükséges engedélyekkel, és hogy a forrás érvénytelen.  
  
|Kivétel üzenetek|  
|------------------------|  
|GitHub-hozzáférési hiba.|  
|GitHub-hozzáférési hiba. {0}|  
  

## <a name="error-0148"></a>0148 hiba  
 Jogosulatlan hozzáférési problémák adatok kibontása vagy könyvtár létrehozása közben.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, amikor hozzon létre egy könyvtárat vagy adatok olvasása a storage-ból próbált, de nem rendelkezik a szükséges engedélyekkel.  
  
**Megoldás:**
  
|Kivétel üzenetek|  
|------------------------|  
|Jogosulatlan hozzáférési kivétel adatok kinyerésekor.|  
  

## <a name="error-0149"></a>0149 hiba  
 A felhasználó fájl nem létezik a GitHub csomagot belül.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a megadott fájl nem található.  
  
Megoldás: 
  
|Kivétel üzenetek|  
|------------------------|  
|GitHub-fájl nem található.|  
|GitHub-fájl nem található.: {0}|  
  

## <a name="error-0150"></a>0150 hiba  
 A parancsfájlok, amelyek a felhasználói csomag származnak nem sikerült kicsomagolt, nagy valószínűséggel GitHub fájlok való ütközés miatt.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a parancsfájl nem nyerhetők ki, általában az azonos nevű meglévő fájl esetén.  
  
Megoldás:
  
|Kivétel üzenetek|  
|------------------------|  
|Nem sikerült kicsomagolni a köteg; lehetséges névütközés GitHub-fájlokat.|  
  

## <a name="error-0151"></a>0151 hiba  
 Hiba történt a felhőbe tárolás hibát. Ellenőrizze az URL-címet.  
  
 Az Azure Machine Learning a hiba akkor fordul elő, ha a modul próbál adatokat írni a felhőalapú tárolást, de az URL-cím nem érhető el vagy érvénytelen.  
  
Megoldás: Ellenőrizze az URL-címet, és ellenőrizze, hogy írható.  
  
|Kivétel üzenetek|  
|------------------------|  
|Hiba a felhőalapú tárolás (valószínűleg egy hibás url) írása közben.|  
|Hiba történt a felhőalapú tárolás írásakor: {0}. Ellenőrizze az URL-címet.|  
  
## <a name="error-0152"></a>0152 hiba  
 Az Azure-felhőtípus nem megfelelően van megadva a modul a környezetben.  
  
|Kivétel üzenetek|  
|------------------------|  
|Hibás Azure-Felhőtípus|  
|Hibás Azure-felhő típusa: {0}|  
  
## <a name="error-0153"></a>0153 hiba  
 A tárolási végpontot a megadott érték érvénytelen.  
  
|Kivétel üzenetek|  
|------------------------|  
|Hibás Azure-Felhőtípus|  
|Hibás tárolási végpont: {0}|  

## <a name="error-0154"></a>0154 hiba  
 A megadott kiszolgálónév nem oldható fel.  
  
|Kivétel üzenetek|  
|------------------------|  
|A megadott kiszolgálónév nem oldható fel.|  
|A megadott kiszolgáló {0}. documents.azure.com nem oldható fel|

## <a name="error-0155"></a>0155 hiba  
 A DocDb ügyfél kivételt okozott.  
  
|Kivétel üzenetek|  
|------------------------|  
|A DocDb ügyfél kivételt okozott.|  
|DocDb ügyfél: {0}|

## <a name="error-0156"></a>0156 hiba  
 Helytelen választ HCatalog-kiszolgáló.  
  
|Kivétel üzenetek|  
|------------------------|  
|Helytelen választ HCatalog-kiszolgáló. Ellenőrizze, hogy futnak-e az összes szolgáltatás.|  
|Helytelen választ HCatalog-kiszolgáló. Ellenőrizze, hogy futnak-e az összes szolgáltatás. Hiba részletei: {0}|

## <a name="error-0157"></a>0157 hiba  
 Hiba történt az Azure Cosmos DB miatt inkonzisztens vagy más sémák olvasása. Olvasó szükséges összes dokumentum ugyanazzal a sémával rendelkezik.  
  
|Kivétel üzenetek|  
|------------------------|  
|Különböző sémákkal észlelt dokumentumokat. Győződjön meg arról, hogy minden dokumentum ugyanazzal a sémával rendelkezik.|

## <a name="error-1000"></a>1000 hiba  
Belső könyvtár kivételt.  
  
Ez a hiba rögzítése egyéb nem kezelt belső motor hibák biztosítunk. Ezért ez a hiba okát az modult, amely a hibát okozta függően eltérőek lehetnek.  
  
További segítséget szeretne kérni, azt javasoljuk, hogy a hiba az Azure Machine Learning fórum, és egy leírást a forgatókönyv, beleértve az adatokat használja bemenetként való megnyitásáról a részletes üzenet feladása. A visszajelzés segít számunkra, hogy rangsorolhatja a hibákat, és további munka legfontosabb problémáinak azonosítása.  
  
|Kivétel üzenetek|  
|------------------------|  
|Erőforrástár-kivétel.|  
|Erőforrástár-kivétel: {0}|  
|{0} erőforrástár-kivétel: {1}|  

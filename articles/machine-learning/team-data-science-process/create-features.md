---
title: Szolgáltatástervezés az adattudományban - Csapatadat-elemzési folyamat
description: Ismerteti a szolgáltatástervezés céljait, és példákat mutat be a gépi tanulás adatjavítási folyamatában betöltött szerepére.
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
ms.openlocfilehash: 02f109f250fa9bcd4c77cecd0b1b3e4514ecd8bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721132"
---
# <a name="feature-engineering-in-data-science"></a>Szolgáltatástervezés az adatelemzésben
Ez a cikk ismerteti a szolgáltatástervezés céljait, és példákat mutat be a gépi tanulás adatjavítási folyamatában betöltött szerepére. A folyamat szemléltetésére használt példák az Azure Machine Learning Studio-ból származnak. 

Ez a feladat egy lépés a [Csapat adatelemzési folyamatában (TDSP).](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)

A szolgáltatástervezés megpróbálja növelni a tanulási algoritmusok prediktív erejét olyan nyers adatokból származó funkciók létrehozásával, amelyek megkönnyítik a tanulási folyamatot. A mérnöki és a funkciók kiválasztása egy része a TDSP vázolt [a Mi a csapat adatelemzési folyamat életciklusa?](overview.md) A funkciótervezés és a kiválasztás a TDSP **Funkciók fejlesztése** lépésének részét képezi. 

* **funkciótervezés:** Ez a folyamat további releváns funkciókat próbál létrehozni az adatok meglévő nyers funkcióiból, és növelni a tanulási algoritmus prediktív erejét.
* **funkciókiválasztása**: Ez a folyamat kiválasztja az eredeti adatfunkciók legfontosabb részhalmazát, hogy csökkentse a betanítási probléma méretdimenzionális jellegét.

Általában **a szolgáltatásmérnöki** először további funkciók létrehozásához, majd a **szolgáltatás kiválasztási** lépés történik, hogy megszüntesse irreleváns, redundáns, vagy erősen korrelált funkciók.

A gépi tanulásban használt betanítási adatok gyakran javíthatók a funkciók kinyerésével a nyers adatokból. Egy példa egy mesterséges funkció keretében a tanulás, hogyan kell osztályozni a képeket a kézzel írt karakterek létrehozása egy bit sűrűségtérkép épített a nyers bit terjesztési adatok. Ez a térkép segítségével megtalálhatja a karakterek széleit hatékonyabban, mint egyszerűen a nyers eloszlás közvetlen használata.

Ha adott környezetben szeretne szolgáltatásokat létrehozni az adatokhoz, olvassa el az alábbi cikkeket:

* [Szolgáltatások létrehozása az SQL Server adataihoz](create-features-sql-server.md)
* [Hadoop-fürt adatainak létrehozása Hive-lekérdezésekkel](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Funkciók létrehozása az adatokból - szolgáltatásfejlesztés
A betanítási adatok egy példákból (sorokban tárolt rekordokból vagy megfigyelésekből) álló mátrixból állnak, amely nek számos jellemzője van (oszlopokban tárolt változók vagy mezők). A kísérleti tervben megadott jellemzők várhatóan az adatok mintáit jellemzik. Bár a nyers adatmezők közül sok közvetlenül is beépíthető a kiválasztott, modell betanításához használt szolgáltatáskészletbe, gyakran előfordul, hogy további (mesterséges) funkciókat kell létrehozni a nyers adatok funkcióiból egy továbbfejlesztett betanítási adatkészlet létrehozásához.

Milyen funkciókat kell létrehozni az adatkészlet fejlesztéséhez egy modell betanításakor? A betanítást javító, megtervezett funkciók olyan információkat nyújtanak, amelyek jobban megkülönböztetik az adatok mintáit. Az új funkciók várhatóan további információkat nyújtanak, amelyek nem egyértelműen rögzített, vagy könnyen látható az eredeti vagy meglévő szolgáltatáskészlet. De ez a folyamat egyfajta művészet. A megalapozott és eredményes döntések gyakran igényelnek némi szakértelmet a domainek terén.

Az Azure Machine Learning indításakor a legegyszerűbb, ha ezt a folyamatot a Stúdióban biztosított minták használatával pontosan megértjük. Két példa van itt:

* Regressziós példa A felügyelt kísérletben a [kerékpárkölcsönzések számának előrejelzése,](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) ahol a célértékek ismertek
* Szövegbányászati osztályozási példa [a szolgáltatáskivonatolás használatával](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>1. példa: Temporális jellemzők hozzáadása regressziós modellhez
Használja a "Kerékpárok igény szerinti előrejelzése" című kísérletet az Azure Machine Learning Studio (klasszikus) alkalmazásban, hogy bemutassa, hogyan tervezhet funkciókat egy regressziós feladathoz. A kísérlet célja, hogy megjósolja a kerékpár iránti keresletet, azaz a kerékpárkölcsönzések számát egy adott hónapon /napon /óránként. A "Bike Rental UCI adatkészlet" adatkészlet nyers bemeneti adatokként használatos. Ez az adatkészlet a Capital Bikeshare cég valós adataira épül, amely kerékpárkölcsönző hálózatot tart fenn Az Egyesült Államokban Washington DC-ben. Az adatkészlet a 2011-es és 2012-es év egy adott napi órájában található kerékpárkölcsönzések számát mutatja, és 17379 sort és 17 oszlopot tartalmaz. A nyers funkciókészlet tartalmazza az időjárási viszonyokat (hőmérséklet/páratartalom/szélsebesség) és a nap típusát (ünnep/hétköznap). Az előre jelezni a "cnt" szám, amely egy adott órán belül jelenti a kerékpárkölcsönzést, és amely 1 és 977 között mozog.

Azzal a céllal, hogy a betanítási adatok hatékony funkciók at, négy regressziós modellek épülnek fel ugyanazzal az algoritmussal, de négy különböző betanítási adatkészletek. A négy adatkészlet ugyanazokat a nyers bemeneti adatokat képviseli, de egyre több szolgáltatás van beállítva. Ezek a funkciók négy kategóriába sorolhatók:

1. A = időjárás + nyaralás + hétköznap + hétvégi funkciók az előre jelzett napra
2. B = az előző 12 órában bérelt kerékpárok száma
3. C = az on-t 12 nap mindegyikében ugyanabban az órában bérelt kerékpárok száma
4. D = az előző 12 hét mindegyikében ugyanabban az órában és ugyanazon a napon bérelt kerékpárok száma

Az A szolgáltatáskészlet mellett, amely már létezik az eredeti nyers adatokban, a másik három szolgáltatáskészlet a szolgáltatástervezési folyamaton keresztül jön létre. A B funkciókészlet rögzíti a kerékpárok iránti legutóbbi keresletet. A C szolgáltatáskészlet egy adott órában rögzíti a kerékpárok iránti igényt. A D funkciókészlet a hét adott órájában és napján rögzíti a kerékpárok iránti keresletet. A négy betanítási adatkészlet mindegyike tartalmazza az A, A+B, A+B+C és A+B+C+D szolgáltatáskészletet.

Az Azure Machine Learning-kísérletben ez a négy betanítási adatkészletek az előre feldolgozott bemeneti adatkészlet négy ágain keresztül jönnek létre. A bal szélső ág kivételével ezek az ágak mindegyike tartalmaz egy [R-parancsfájl végrehajtása](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modult, amelyben a származtatott funkciók (B, C és D szolgáltatáskészlet) az importált adatkészlethez vannak kialakítva és hozzáfűzve. Az alábbi ábra bemutatja a B szolgáltatáskészlet létrehozásához használt R-parancsfájlt a bal oldali elágazásban.

![funkciók létrehozása](./media/create-features/addFeature-Rscripts.png)

A négy modell teljesítményeredményeinek összehasonlítását a következő táblázat foglalja össze: 

![eredmény összehasonlítása](./media/create-features/result1.png)

A legjobb eredményeket az A+B+C funkciók mutatják. A hibaarány csökken, ha a betanítási adatok további szolgáltatáskészletet tartalmaznak. Ellenőrzi azt a feltételezést, hogy a B, C szolgáltatáskészlet további releváns információkat nyújt a regressziós feladathoz. De hozzátéve, a D funkció nem úgy tűnik, hogy további csökkentése a hibaarány.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>2. példa: Szolgáltatások létrehozása a szövegbányászatban
A szolgáltatástervezést széles körben alkalmazzák a szövegbányászattal kapcsolatos feladatokban, például a dokumentumosztályozásban és a hangulatelemzésben. Ha például a dokumentumokat több kategóriába szeretné sorolni, egy tipikus feltételezés az, hogy az egyik dokumentumkategóriában szereplő szó/kifejezések kisebb valószínűséggel fordulnak elő egy másik dokumentumkategóriában. Más szóval, a szavak/kifejezések eloszlásának gyakorisága képes jellemezni a különböző dokumentumkategóriákat. A szövegbányászati alkalmazásokban, mivel az egyes szövegtartalmak általában bemeneti adatként szolgálnak, a szolgáltatástervezési folyamat szükséges a szó/kifejezés frekvenciákat tartalmazó funkciók létrehozásához.

A feladat eléréséhez a **rendszer egy szolgáltatáskivonatolásnak** nevezett technikát alkalmaz, amely hatékonyan alakíthatja a tetszőleges szövegjellemzőket indexekké. Ahelyett, hogy minden egyes szövegjellemzőt (szavakat/kifejezéseket) egy adott indexhez társítana, ez a módszer úgy működik, hogy kivonatoló függvényt alkalmaz a jellemzőkre, és kivonatértékeiket közvetlenül indexként használja.

Az Azure Machine Learningben van egy [szolgáltatáskivonatoló](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, amely kényelmesen létrehozza a szó/kifejezés funkciókat. Az alábbi ábra egy példát mutat be a modul használatára. A bemeneti adatkészlet két oszlopot tartalmaz: az 1-től 5-ig terjedő könyvminősítést és a tényleges ellenőrzési tartalmat. Ennek a [funkciókivonat-kivonatoló](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modulnak az a célja, hogy lekérjen egy csomó új funkciót, amelyek a megfelelő szó(k)/kifejezés(ek) előfordulási gyakoriságát mutatják az adott könyvértékelésen belül. A modul használatához hajtsa végre az alábbi lépéseket:

* Először jelölje ki a bemeneti szöveget tartalmazó oszlopot ("Col2" ebben a példában).
* Másodszor állítsa a "Kivonatoló bitméretet" 8-ra, ami azt jelenti, hogy 2^8=256 szolgáltatás jön létre. A szöveg szó/fázisa 256 indexre lesz kivonatolt. A "Kivonatoló bitméret" paraméter 1 és 31 között mozog. Ha nagyobb számra állítja be, akkor a szó(k)/kifejezés(ek) kisebb valószínűséggel kerülnek ugyanabba az indexbe.
* Harmadszor állítsa az "N-gramm" paramétert 2-re. Ez az érték a bemeneti szövegből az unigramok (minden egyes szó hoz a szolgáltatás) és a bigramok (a szomszédos szavak minden párjának jellemzője) előfordulási gyakoriságát kapja meg. Az "N-gramm" paraméter 0 és 10 között mozog, ami azt jelzi, hogy a sorsorok maximális száma szerepel-e egy jellemzőben.  

!["Feature Hashing" modul](./media/create-features/feature-Hashing1.png)

Az alábbi ábra bemutatja, hogyan néznek ki ezek az új funkciók.

!["Feature Hashing" példa](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Összegzés
A megtervezett és kiválasztott funkciók növelik a betanítási folyamat hatékonyságát, amely megpróbálja kinyerni az adatokban található legfontosabb információkat. Emellett javítják ezeknek a modelleknek a hatalmát a bemeneti adatok pontos besorolásához és az érdeklődésre számot tartó eredmények erőteljesebb előrejelzéséhez. A funkciótervezés és a kiválasztás kombinálható, hogy a tanulás takarásban kezelhetőbb legyen. Ezt úgy éri el, hogy javítja, majd csökkenti a modell kalibrálásához vagy betanításához szükséges funkciók számát. Matematikailag szólva a modell betanításához kiválasztott funkciók a független változók minimális készletei, amelyek elmagyarázzák az adatok mintáit, majd sikeresen előrejelzik az eredményeket.

Ez nem mindig feltétlenül hajtsa végre a szolgáltatás mérnöki vagy funkció kiválasztása. Az, hogy szükség van-e rá, a leosztandó vagy összegyűjtött adatoktól, a kiválasztott algoritmustól és a kísérlet céljától függ.


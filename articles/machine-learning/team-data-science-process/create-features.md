---
title: Az adatelemzési funkció az adattudományban – csoportos adatelemzési folyamat
description: Ismerteti a funkció-mérnöki célokat, és példákat tartalmaz a gépi tanulás adatfejlesztési folyamatában betöltött szerepére.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76721132"
---
# <a name="feature-engineering-in-data-science"></a>Az adatelemzés funkcióinak mérnöki funkciója
Ez a cikk ismerteti a funkció-mérnöki célokat, és példákat nyújt a szerepkörére a gépi tanulás adatfejlesztési folyamatában. A folyamat szemléltetésére szolgáló példák a Azure Machine Learning Studioból készülnek. 

Ez a feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

A funkciók mérnöki célja a tanulási algoritmusok prediktív teljesítményének fokozása azáltal, hogy olyan szolgáltatásokat hoz létre a nyers adatokból, amelyek megkönnyítik a tanulási folyamat megkönnyítése érdekében. A szolgáltatások mérnöki és kiválasztási funkciója a [Mi a csoportos adatelemzési folyamat életciklusa](overview.md) című részében ismertetett TDSP egyik része? A funkciók mérnöki és kiválasztási funkciója a TDSP **szolgáltatások fejlesztése** lépésének részei. 

* **szolgáltatások mérnöki**szerepe: Ez a folyamat megkísérli további releváns funkciók létrehozását az adatok meglévő nyers funkcióiról, valamint a tanulási algoritmus prediktív teljesítményének növelését.
* **funkció kiválasztása**: Ez a folyamat kiválasztja az eredeti adatszolgáltatások kulcsának részhalmazát, hogy megpróbálja csökkenteni a dimenzióját.

A rendszer általában a szolgáltatások **fejlesztését** alkalmazza a további funkciók létrehozásához, majd a **funkció kiválasztása** lépéssel megszünteti a lényegtelen, redundáns vagy szorosan korrelált funkciókat.

A gépi tanulásban használt betanítási adatok gyakran javíthatók a begyűjtött nyers adatokból származó funkciók kivonásával. Példa arra, hogy megtanítsa, hogyan osztályozhatja a kézírásos karakterek képeit úgy, hogy az a nyers bites terjesztési adatokból létrehozott, kis sűrűségű leképezést hozza létre. Ez a Térkép segít megkeresni a karakterek szegélyeit hatékonyabban, mint egyszerűen a nyers eloszlás közvetlen használatával.

Bizonyos környezetekben lévő adatszolgáltatások létrehozásához tekintse meg a következő cikkeket:

* [Szolgáltatások létrehozása a SQL Serverban található adatszolgáltatásokhoz](create-features-sql-server.md)
* [Hadoop-fürtben lévő adatszolgáltatások létrehozása struktúra-lekérdezések használatával](create-features-hive.md)

## <a name="create-features-from-your-data---feature-engineering"></a>Funkciók létrehozása az adatokból – szolgáltatás-mérnöki
A betanítási adatok egy példákból (a sorokban tárolt rekordok vagy megjegyzések) álló mátrixokból állnak, amelyek mindegyike rendelkezik szolgáltatásokkal (változókkal vagy oszlopokban tárolt mezőkkel). A kísérleti tervben megadott funkcióknak az adatmintákat kell megadniuk. Bár a nyers adatmezők közül sok közvetlenül belefoglalható a modell betanításához használt kiválasztott szolgáltatáskészletbe, gyakran előfordul, hogy a további (mérnöki) funkciókat a nyers adatok funkciói közül kell kiépíteni egy bővített betanítási adatkészlet létrehozásához.

Milyen szolgáltatásokat kell létrehozni az adatkészlet fejlesztéséhez a modell betanításakor? A képzést javító, megerősített funkciók olyan információkat biztosítanak, amelyek jobban megkülönböztetik az adatokban lévő mintákat. Az új szolgáltatások várhatóan olyan további adatokat biztosítanak, amelyek nem rögzítettek vagy egyszerűen nem láthatók az eredeti vagy meglévő szolgáltatáskészlet esetében. Ez a folyamat azonban a Művészetek egyike. A hang-és produktív döntések gyakran igényelnek némi tartományi szakértelmet.

A Azure Machine Learning-től kezdődően a legegyszerűbben a Studióban megadott minták használatával kell felfognia ezt a folyamatot. A következő két példa jelenik meg:

* Regressziós példa egy felügyelt kísérletben szereplő [kerékpár-kölcsönzések számának előrejelzésére](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) , ahol a célként megadott értékek ismertek
* Egy szöveges adatbányászati besorolás példa a [szolgáltatások kivonatolására](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>1. példa: időbeli funkciók hozzáadása regressziós modellhez
Használjuk a "kerékpárok igény szerinti előrejelzése" kifejezést a Azure Machine Learning Studio (klasszikus) című témakörben, amely bemutatja, hogyan lehet egy regressziós feladat funkcióit megtervezni. A kísérlet célja, hogy előre megjósolja a kerékpárok igényét, azaz a kerékpár-kölcsönzések számát egy adott hónap/nap/óra alatt. A "Bike Rental UCI-adatkészlet" adatkészlet a nyers bemeneti adatokként van használatban. Ez az adatkészlet a Capital Bikeshare Company olyan valós adatán alapul, amely egy Bike Rental Network-t tart fenn a Egyesült Államokban. Az adatkészlet a kerékpár-kölcsönzések számát jelöli egy adott órán belül, a 2011-es és a 2012-os évben, valamint 17379 sort és 17 oszlopot tartalmaz. A nyers szolgáltatáskészlet időjárási körülményeket (hőmérséklet/páratartalom/Szélsebesség) és a nap típusát (ünnepnap/hét) tartalmazza. Az előre jelzett mező a "CNT" szám, amely egy adott órán belül a kerékpár-kölcsönzést jelöli, az 1-től 977-ig terjedő tartományban.

A betanítási adatokat tartalmazó hatékony funkciók kiépítésének céljaként négy regressziós modell készül ugyanazzal az algoritmussal, de négy különböző betanítási adatkészlettel. A négy adatkészlet ugyanazokat a nyers bemeneti adatokat jelöli, de egyre több szolgáltatás van beállítva. Ezek a funkciók négy kategóriába vannak csoportosítva:

1. A = időjárás + nyaralás + hétköznap + hétvégi funkciók az előre jelzett napra
2. B = az előző 12 órában bérelt kerékpárok száma
3. C = az előző 12 nap során bérelt kerékpárok száma ugyanazon az órában
4. D = az előző 12 hétben az adott órában és ugyanazon a napon bérelt kerékpárok száma

Az A szolgáltatáskészlet mellett, amely már létezik az eredeti nyers adatban, a szolgáltatások másik három készletét a szolgáltatás mérnöki folyamata hozza létre. A B szolgáltatáskészlet a legújabb igényeket rögzíti a kerékpárok számára. A C szolgáltatáskészlet egy adott órában rögzíti a kerékpárok igényét. A D szolgáltatáskészlet adott órában és a hét adott napján rögzíti a kerékpárok igényét. A négyféle betanítási adatkészlet az A, A + B, A + B + C és A + B + C + D készleteket tartalmazza.

A Azure Machine Learning kísérletben ez a négy betanítási adatkészlet az előre feldolgozott bemeneti adatkészletből származó négy ág alapján lett létrehozva. A bal szélső ág kivételével mindegyik ág tartalmaz egy [végrehajtási R parancsfájl](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) -modult, amelyben a származtatott funkciókat (B, C és D) a rendszer kiépíti és hozzáfűzi az importált adatkészlethez. Az alábbi ábra bemutatja a B. szolgáltatáskészlet létrehozásához használt R-szkriptet a második bal oldali ágban.

![szolgáltatások létrehozása](./media/create-features/addFeature-Rscripts.png)

A négy modell teljesítmény-eredményeinek összehasonlítását az alábbi táblázat foglalja össze: 

![eredmény-összehasonlítás](./media/create-features/result1.png)

A legjobb eredményeket a + B + C funkció mutatja be. A hiba mértéke csökken, ha további szolgáltatáskészlet szerepel a betanítási adatkészletben. Ellenőrzi azt a feltételezést, hogy a B, C beállított funkció további releváns információkat biztosít a regressziós feladathoz. A D funkció hozzáadása azonban úgy tűnik, hogy nem biztosít további csökkentést a hibák arányában.

## <a name="example-2-creating-features-in-text-mining"></a><a name="example2"></a>2. példa: szolgáltatások létrehozása a szöveges adatbányászatban
A szolgáltatások fejlesztését széles körben alkalmazzák a szöveges adatbányászatgal kapcsolatos feladatokban, például a dokumentumok besorolásával és a hangulat elemzésével. Ha például több kategóriába kívánja minősíteni a dokumentumokat, a rendszer általában feltételezi, hogy az egyik doc kategóriába tartozó szó/kifejezés kisebb valószínűséggel egy másik doc-kategóriában fordul elő. Más szóval a szavak/kifejezések eloszlásának gyakorisága különböző dokumentum-kategóriákat képes jellemezni. A szöveges adatbányászati alkalmazásokban, mivel az egyes szövegrészek tartalma általában bemeneti adatokként szolgál, a szolgáltatás mérnöki folyamata szükséges a szó/kifejezés gyakoriságot érintő funkciók létrehozásához.

Ennek a feladatnak a megvalósításához a rendszer a **szolgáltatás-kivonatolás** nevű technikát alkalmazza, hogy hatékonyan kapcsolja be a tetszőleges szöveges funkciókat az indexekben. Ahelyett, hogy az egyes szöveges funkciókat (szavakat/kifejezéseket) egy adott indexhez társítsa, ez a módszer egy kivonatoló függvényt alkalmaz a funkciókra, és a kivonatok értékeit közvetlenül a következő módon használja.

Azure Machine Learning-ben egy funkció- [kivonatolási](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, amely a szó/kifejezés funkciók kényelmes létrehozását hozza létre. Az alábbi ábrán egy példa látható a modul használatára. A bemeneti adatkészlet két oszlopot tartalmaz: a könyv minősítése 1-től 5-ig terjed, és a tényleges felülvizsgálati tartalom. A [szolgáltatás-kivonatolási](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul célja, hogy beolvasson egy olyan új funkciót, amely megjeleníti az adott könyv-ellenőrzésen belüli megfelelő szó (ok)/phrase előfordulási gyakoriságát. A modul használatához hajtsa végre a következő lépéseket:

* Először válassza ki a bemeneti szöveget tartalmazó oszlopot (ebben a példában a "Col2").
* Másodszor állítsa a "kivonatolási bitsize" a 8-as értékre, ami azt jelenti, hogy 2 ^ 8 = 256 funkció lesz létrehozva. Az összes szövegben szereplő szó/fázis kivonata 256-es indexbe kerül. A "kivonatolási bitsize" paraméter 1 és 31 közötti tartományba esik. A/phrase (ok) kevésbé valószínű, hogy ugyanabba az indexbe kerül, ha nagyobb számra van beállítva.
* Harmadszor, állítsa az "N-gramm" paramétert 2 értékre. Ez az érték beolvassa a unigrams előfordulási gyakoriságát (minden egyes szó funkcióját) és a bigrams (a szomszédos szavak minden pár funkcióját) a bemeneti szövegből. Az "N-gramm" paraméter 0 és 10 közötti tartományba esik, ami azt jelzi, hogy legfeljebb hány szekvenciális szót kell szerepeltetni egy adott szolgáltatásban.  

!["Szolgáltatások kivonatolása" modul](./media/create-features/feature-Hashing1.png)

Az alábbi ábra az új funkció megjelenését mutatja be.

!["Szolgáltatások kivonatolása" példa](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Összegzés
A tervezett és a kiválasztott funkciók növelhetik a betanítási folyamat hatékonyságát, amely az adatokban található kulcsfontosságú információk kinyerését kísérli meg. Emellett javítják ezeknek a modelleknek a hatékonyságát a bemeneti adatok pontos osztályozása érdekében, valamint a kamatok kiszámításának hatékonyabbá válását. A funkciók mérnöki és kijelölési funkciói is kombinálhatók, így a tanulás több számítási feltételt is igénybe vehet. Ezt a modell kalibrálásához vagy betanításához szükséges szolgáltatások számának növelésével és csökkentésével végzi. Matematikailag a modell betanítására kiválasztott funkciók a független változók minimális készlete, amelyek ismertetik az adatmintázatokat, majd megjósolják az eredmények sikerességét.

Nem mindig feltétlenül kell végrehajtania a szolgáltatások mérnöki vagy szolgáltatásbeli kijelölését. Szükség van-e rá, vagy nem függ a kézzel vagy gyűjtött adatoktól, a kiválasztott algoritmustól és a kísérlet céljától.


---
title: Funkciók tervezése a data science - csoportos adatelemzési folyamat
description: Az alkalmazásában funkciófejlesztési ismerteti, és példákat tartalmaz a szerepét a machine learning adatokat a fejlesztés folyamatát.
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
ms.openlocfilehash: 8b0973007a78b492cff1c5ffc2ce1e43116a0847
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57835084"
---
# <a name="feature-engineering-in-data-science"></a>Funkciók tervezése a adatelemzés
Ez a cikk ismerteti az alkalmazásában funkciófejlesztési és példák a szerepét a machine learning adatokat a fejlesztés folyamatát. A folyamat szemléltetése használt példák az Azure Machine Learning Studio állítják. 

Ez a feladat Ez a lépés a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/).

Ez a funkció mérnöki kísérletek tanulási algoritmus a nyers adatokat, amelyek a tanulási folyamat megkönnyítése érdekében szolgáltatásokat hoz létre prediktív hatékonyságának növelése érdekében. A termékgondozó csoportja és a funkciók köre a leírt TDSP egy részét képezi a [Mi az a csoportos adatelemzési folyamat életciklusa?](overview.md) A szolgáltatás jellemzőkiválasztás és -kiemelés, részből áll a **funkciók fejlesztése** a TDSP. lépését. 

* **jellemzőkiemelés**: Ez a folyamat megpróbálja további kapcsolódó funkciók létrehozása az adatok meglévő nyers funkciókat, valamint a tanulási algoritmus prediktív hatékonyságának növelése érdekében.
* **kijelölés funkció**: Ez a folyamat az eredeti funkciók kulcs részhalmazát csökkentése érdekében a betanítási probléma dimenzióinak kísérlet kijelöli.

Általában **jellemzőkiemelés** további funkciók létrehozásához először alkalmazza, majd a **kijelölés funkció** irreleváns, redundáns vagy magas kapcsolódó funkciók kiküszöbölése érdekében a lépést.

A betanítási adatok, a machine Learning szolgáltatásban használt gyakran lehet bővíteni úgy a nyers adatoktól a gyűjtött funkciók. Megtudhatja, hogyan kell kézzel írt karakterek, a képek osztályozásához kontextusában egy visszafejtett szolgáltatás egyik példája egy kicsit a nyers bit terjesztési adataiból összeállított sűrűségű térkép létrehozása. A térkép segítségével sokkal hatékonyabb, mint egyszerűen használatával közvetlenül a nyers terjesztési keresse meg az élek a karaktereket.

Funkciók létrehozása az adatok adott környezetekben, tekintse meg a következő cikkeket:

* [Funkciók létrehozása az adatok az SQL Server](create-features-sql-server.md)
* [Funkciók létrehozása az adatokhoz a Hive-lekérdezések segítségével Hadoop-fürt](create-features-hive.md)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Funkciók létrehozása az adatokból - funkciók tervezése
A betanítási adatok, amelyek mindegyike rendelkezik (változók vagy oszlopaiban tárolt mezők) szolgáltatások mikroszolgáltatásokból álló, példák (rekordok vagy megfigyeléseket sorok tárolva), mátrix áll. A funkciók a kísérleti tervezési megadott írhatók le a mintát az adatok a várt. Bár számos, a nyers adatok mezőket is közvetlenül foglalandó a kiválasztott szolgáltatást a modell betanításához használja, ez a helyzet gyakran, hogy további (visszafejtett) szolgáltatások kell létrehozni a nyers adatok egy továbbfejlesztett betanítási adatkészletet létrehozása az a funkciók.

Adatkészlet továbbfejlesztése, amikor egy modell tanítása milyen funkciók kell létrehozni? Visszafejtett funkciók, amelyek javítják a képzés jobban különbözteti meg a mintákat, az adatok információkat tartalmaznak. Az új funkciók fejtheti ki bővebben, amely nem egyértelműen rögzített vagy könnyen látható, az eredeti vagy meglévő szolgáltatáskészletére várt. Azonban ez a folyamat valami művészet. Megbízható és hatékony döntések bizonyos tartomány szakértelmet gyakran igényelnek.

Az Azure Machine Learning indításakor a legegyszerűbb bonyolultnak a folyamat, konkrétan a megadott a Studio minták használatával. Két példa itt jelennek meg:

* Egy regressziós példa [kerékpárkölcsönzés számát az előrejelzések](https://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) egy felügyelt kísérletben, ahol a cél értékek ismertek
* Egy szöveges adatbányászati példa használatával végzett adatbesorolás [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>1. példa: Egy regressziós modell historikus szolgáltatások hozzáadása
Hozzunk használatával a kísérlet "kereslet-előrejelzés, kerékpárok" az Azure Machine Learning Studióban bemutatják, hogyan lehet egy regressziós tevékenység funkciókat. Ez a kísérlet célja, kerékpár, azaz kerékpárkölcsönzés belül egy adott hónap/nap/óra száma iránti kereslet előrejelzésére. Az adatkészlet "uci Kerékpárkölcsönzési UCI adatkészlet" szolgál a nyers bemeneti adatként. Ez az adatkészlet a tőke Bikeshare vállalat, amely fenntartja az Egyesült Államokban Washington, D.C. uci kerékpárkölcsönzési hálózat adatokon alapul. Az adatkészlet jelöli az kerékpárkölcsönzés számát az évben 2011 és 2012-es év naponta egy adott órán belül, és 17379 sorok és oszlopok 17 tartalmazza. A nyers funkció időjárási viszonyok (hőmérsékleti és páratartalom/mért legnagyobb szélsebesség) és a típus a nap (szünnap/hét napja) tartalmazza. Előre jelezni a mező a "cnt" száma, amely a kerékpárkölcsönzés jelöli egy adott órán belül, és amely címtartományok 1 977.

A célt hozhat létre, amely hatékony funkciókat a betanítási adatok, négy regressziós modellek létrehozása az azonos algoritmus használatával történik, de a négy különböző képzési adathalmazok alapján. A négy adatkészletek nyers ugyanazt a bemeneti adatokat képviselik, de a szolgáltatások egyre több beállítása. Ezek a funkciók négy kategóriákba vannak csoportosítva:

1. A időjárási + szünnap + hét napja =, + a hétvégi funkciók az előre jelzett napra
2. B, amely az egyes a korábbi 12 órán át is bérelt kerékpárok száma =
3. C =, amely minden, az adott órán az elmúlt 12 napon lettek bérelt kerékpárok számát
4. D =, amely a korábbi 12 hetes, az adott órán és ugyanazon a napon minden is bérelt kerékpárok számát

Mellett már létezik az eredeti nyers adatokat, a szolgáltatás beállítása A a másik három különböző szolgáltatások jönnek létre a mérnöki folyamat szolgáltatáson keresztül. A szolgáltatás beállítása B rögzíti a kerékpárok nagyon friss iránti igény. A szolgáltatás kerékpárok iránti igény beállítása C rögzíti egy adott óránként. A szolgáltatás adott óra és a hét adott napja D rögzíti igény kerékpárok beállítani. A négy képzési adathalmazok egyes rendre tartalmazza a szolgáltatás egy, A + B, A + B + C és A + B + C + D.

Az Azure Machine Learning kísérletben ezek négy képzési adathalmazok keresztül négy ágaiból származó előre feldolgozott bemeneti adatkészlet jöttek létre. A bal szélső ág kivételével ezek ágak mindegyike tartalmaz egy [R-szkript végrehajtása](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modul, amely a származtatott funkciókat, (B, C és D szolgáltatáskészleteket) rendre kialakítani, és hozzáfűzi az importált adatkészlet. A következő ábra bemutatja az R-szkriptet a második bal oldali ágban szolgáltatáskészlet B létrehozásához használt.

![Funkciók létrehozása](./media/create-features/addFeature-Rscripts.png)

A négy modellek teljesítményének eredmények összehasonlítása az alábbi táblázat foglalja össze: 

![eredmények összehasonlítása](./media/create-features/result1.png)

A legjobb eredmények elérése érdekében funkcióihoz jelennek meg A + B + C billentyűkombinációt. Vegye figyelembe, hogy a Hibaarány csökken, ha további funkciókat a betanítási adatok szerepelnek. Ellenőrzi, hogy a B, C szolgáltatáskészletére biztosítani a regressziós feladat kapcsolatos további információt a feltételezés. A D-funkció hozzáadása nem verziónk, de a Hibaarány csökkenése további adja meg.

## <a name="example2"></a> 2. példa: A szöveg adatbányászati funkciók létrehozása
Széles körben alkalmazása funkciófejlesztési szöveg adatbányászati, például a dokumentum besorolási és vélemények elemzése kapcsolatos feladatokat. Például, ha szeretné besorolni a dokumentumokat számos kategóriába sorolhatók, egy tipikus feltételezi, hogy a word/kifejezések egy doc kategóriába kevésbé valószínű, hogy egy másik doc kategória fordulnak elő. Más szóval a szavak és kifejezések terjesztési gyakoriságát is képes írhatók le a dokumentum különböző kategóriák. Szöveg adatbányászati alkalmazásokban szövegtartalmára – egyéni adatokat általában szolgálhat a bemeneti adatokat, mert a szolgáltatás műszaki folyamat létrehozásához szükséges a szolgáltatásokat érintő szó vagy kifejezés gyakoriságot.

Ez a feladat eléréséhez technika nevű **szolgáltatáskivonatolás** hatékonyan kapcsolja be a tetszőleges szöveges funkciók az indexek vonatkozik. Helyett (szavak vagy kifejezések) szöveg alapdokumentációjában társítása adott index, a metódus függvények alkalmazásával a kivonatoló függvényt a szolgáltatásokhoz, és közvetlenül az indexek, a kivonati értékek használatával.

Az Azure Machine Learning, van egy [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, amely hozza létre ezeket a szó vagy kifejezés funkciók terheljük. Következő ábrán látható egy példa a modul használatával. A bemeneti adatkészlet két oszlopot tartalmaz: a beállításnak 1 és 5 közötti könyv besorolása és a tényleges tekintse át a tartalmat. Jelen [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul álló, lemezcsoport típusú új lekéréséhez funkcióit, hogy a megfelelő szavak előfordulását gyakorisága megjelenítése / belül az adott könyv tájékoztatás(ok) tekintse át az. Ez a modul használatához kövesse az alábbi lépéseket:

* Első lépésként válassza ki az oszlop, amely tartalmazza a bemeneti szöveg (ebben a példában "Col2" jelöli).
* Második, állítsa a "Hashing bitsize" 8-ra, ami azt jelenti, hogy 2 ^ 8 = 256 funkciók jön létre. A szövegben, a word/fázis fog kivonatolása 256 indexekkel kellene foglalkoznia. A paraméter "Hashing bitsize" tartomány 1 és 31. A keresett szavakat / tájékoztatás(ok) kevésbé valószínű, hogy az azonos indexbe történő kivonatolása beállítás nagyobb számnak kell lennie, ha.
* Harmadik, a paraméter értéke "N-gramokat" 2. Ez az érték előfordulási gyakoriságát unigrams (minden egyszavas funkció) és bigrams (a szomszédos szavak minden virtuálisgép-pár funkció) a bemeneti szöveg beolvasása. A paraméter "N-gramokat" címtartományok 0 és 10, amely azt jelzi, hogy a funkció foglalandó szekvenciális szavak maximális száma.  

!["Funkció Hashing" modul](./media/create-features/feature-Hashing1.png)

Az alábbi ábra bemutatja, milyen ezek új funkció néz ki.

![A példában "Funkció Hashing"](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Összegzés
Visszafejtett és a kiválasztott funkciók a hatékonyabbá teheti a betanítási folyamat, amely megkísérli az adatok tartalmazzák a legfontosabb információt nyerhet ki. Emellett javíthatják a modellek hatékonyságát a bemeneti adatok besorolására pontosan, és hatékonyabban dolgozhatók több előre jelezni a lényeges eredményeket. A szolgáltatás jellemzőkiválasztás és -kiemelés kombinálhatja is, hogy akár több tractable a tanulást. Ezt úgy valósítja kerülésről, és ezután továbbá bármikor kalibrálhatja vagy a modell betanításához szükséges szolgáltatások számának csökkentése. Matematikai beszéd, a modell betanításához kiválasztott szolgáltatások független változók, amelyek az adatok a minták ismertetik, és majd sikeresen az eredmények előrejelzésére egy minimális számú.

Nincs mindig feltétlenül szolgáltatás mérnöki vagy szolgáltatás kiválasztása végrehajtásához. E vagy sem szükséges kéz függ, hogy az adatokat, vagy az összegyűjtött, a kiválasztott algoritmus és a cél a kísérlet.


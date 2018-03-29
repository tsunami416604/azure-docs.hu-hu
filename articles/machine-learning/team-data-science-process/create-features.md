---
title: Jellemzőkiemelés adatok tudományos |} Microsoft Docs
description: Elmagyarázza, a szolgáltatás műszaki osztály alkalmazásában és példák a szerepét a gépi tanulás adatokat a fejlesztés folyamatán.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 3fde69e8-5e7b-49ad-b3fb-ab8ef6503a4d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2017
ms.author: bradsev
ms.openlocfilehash: 70a78659bc14ce5ff9358ed11ad0af68be31bd17
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="feature-engineering-in-data-science"></a>Jellemzőkiemelés tudományos adatok
Ez a cikk azt ismerteti, a szolgáltatás műszaki osztály alkalmazásában és példák a szerepet a gépi tanulás adatokat a fejlesztés során. A példák ezt a folyamatot mutatja be az Azure Machine Learning Studio állítják. 

[!INCLUDE [cap-create-features-data-selector](../../../includes/cap-create-features-selector.md)]

Ez **menü** hivatkozásokat követve hozhatók létre az adatok szolgáltatások különböző környezetekben. Ez a feladat Ez a lépés a [Team adatok tudományos folyamat (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

Ez a funkció mérnöki kísérletek a tanulási algoritmus a nyers adatok a tanulási folyamat megkönnyítése érdekében a szolgáltatások létrehozásával előrejelzési teljesítményének növelése érdekében. Az ismertetett TDSP a mérnöki és funkciók kiválasztása egy része a [Mi az a csoport az tudományos folyamata életciklus?](overview.md) A szolgáltatás termékgondozó csoportja és a kijelölés részét képezik a **szolgáltatások fejlesztéséhez** a TDSP lépését. 

* **jellemzőkiemelés**: Ez a folyamat megkísérli a meglévő nyers funkciókat az adatok további kapcsolódó szolgáltatások létrehozásához és a tanulási algoritmus előrejelzési teljesítményének növelése érdekében.
* **szolgáltatás kiválasztása**: Ez a folyamat az eredeti adatokkal kapcsolatos funkciókkal kulcs részhalmazát kijelöli a kísérlet dimenzióinak, a képzési probléma csökkentése érdekében.

Általában **jellemzőkiemelés** további szolgáltatások létrehozásához először vonatkozik, majd a **kijelölés funkció** lépést irreleváns, redundáns vagy magas kapcsolódó szolgáltatásokat nyújthatnak.

A gépi tanulás használt betanítási adatok gyakran fejleszthető úgy a nyers adatokat gyűjt a szolgáltatásokat. Példa egy visszafejtett szolgáltatás keretén belül a dokumentum besorolását kézzel karaktereket a képek létrehozása egy kicsit sűrűség leképezés a nyers bit terjesztési adataiból összeállított:. Ez a térkép segítségével keresse meg mint egyszerűen használja közvetlenül a nyers terjesztési hatékonyabban széleit, a karaktereket.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-features-from-your-data---feature-engineering"></a>Az adatokból - funkciók létrehozása jellemzőkiemelés
A betanítási adatok, amelyek mindegyike rendelkezik (változók vagy az oszlopokban tárolt mezők) funkciókat (rekordok vagy megfigyelések egy sor tárolt), például álló mátrix áll. A funkciók a kísérleti tervezési várhatóan írhatók le az adatok kombinációját. A nyers adatok mezők számos közvetlenül szerepelhet a modell betanításához használandó kijelölt szolgáltatáskészlete, bár ez a helyzet gyakran, hogy további (visszafejtett) szolgáltatásokat szeretne-e állítható össze a szolgáltatások egy továbbfejlesztett képzési adatkészlet létrehozásához a nyers adatok a.

Milyen funkciókat javítása érdekében a dataset, amikor egy modell betanítása létre kell hozni? Visszafejtett funkciókat, amelyek javítják a képzés információkkal jobban megkülönbözteti az adatok kombinációját. Az új szolgáltatások várhatóan további információkkal, amely nem egyértelműen rögzített vagy az eredeti vagy meglévő szolgáltatáskészlet könnyen látható. Azonban ez a folyamat történt egy kép. Megfelelő és hatékony döntések gyakran megkövetelik a néhány tartomány szakértői.

Azure Machine Learning segítségével indításakor a legegyszerűbb bonyolultnak Ez a folyamat a Studio biztosított minták konkrétan használatával. Két példa a itt jelenik meg:

* Egy regressziós példa [kerékpárt bérlését száma előrejelzését](http://gallery.cortanaintelligence.com/Experiment/Regression-Demand-estimation-4) egy felügyelt kísérletben, ahol a cél értékek ismertek
* A szöveg adatbányászati besorolás példa használatával [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/)

## <a name="example-1-add-temporal-features-for-a-regression-model"></a>1. példa: Egy regressziós modell historikus szolgáltatások hozzáadása
Most segítségével a kísérlet "igény szerinti előrejelzéséhez kerékpárt" az Azure Machine Learning Studióban bemutatják, hogyan lehet szolgáltatásokat regressziós tevékenység tervezését. Ehhez a kísérlethez célja előre jelezni a kerékpárt, ez azt jelenti, hogy egy adott hónap/nap/órán belül kerékpárt bérlését száma terhelését. A dataset "bérlet kerékpárt UCI dataset" szolgál a nyers bemeneti adatként. Ez az adatkészlet fenntartja az Amerikai Egyesült Államokban Washingtoni kerékpárt bérleti hálózat beruházási Bikeshare vállalattól valós adatokon alapul. A dataset kerékpárt bérlését számát jelenti. a nap-ben 2011 és 2012-ben meghatározott órán belül, és tartalmazza a 17379 sorok és oszlopok 17. A nyers szolgáltatáskészlete időjárási feltételek (hőmérséklet/páratartalom/szél sebesség) és a típus a nap (szünnap/hét napja) tartalmazza. A mező előre jelezni a "számláló" száma, amelyek a kerékpárt bérlését jelöli egy adott órán belül, és amely címtartományok 1 977.

Azzal a céllal, hatékony funkciókat a betanítási adatok, a modellek az azonos algoritmus használatával készített négy regressziós, de négy különböző képzési adatkészletekkel hozhat létre. A négy adatkészletek nyers ugyanazon bemeneti adatait, de a szolgáltatások egyre több beállítása. Ezek a funkciók négy kategóriákba vannak csoportosítva:

1. A időjárási + szünnap + hétköznap = + hétvégi szolgáltatások előre jelezhető napi
2. B = kerékpárt, amely minden az előző 12 óra volt bérelt száma
3. C = kerékpárt, amely minden, az azonos órához az elmúlt 12 nap volt bérelt száma
4. D = kerékpárt, amely minden az előző 12 hetes, az azonos órához és ugyanarra a napra esnek volt bérelt száma

A szolgáltatás egy, már szerepel az eredeti nyers adatok, amelyek mellett a másik három különböző szolgáltatások a műszaki osztály folyamat funkción keresztül jönnek létre. A szolgáltatás nagyon friss iránti igények a kerékpárt beállítva a B rögzíti. A szolgáltatás kerékpárt iránti igény beállítva C rögzíti, egy adott óra. A szolgáltatás adott óra és a hét napját adott D rögzítésekre iránti igények kerékpárt beállítva. A négy tanítási adathalmazt minden rendre tartalmazza a szolgáltatás egy, A + B, A + B + C és A + B + C + D.

Az Azure Machine Learning kísérletben ezen négy képzési adatkészletek hozhatók létre a Windowsban a Előfeldolgozott bemeneti adatkészletből négy ágak keresztül. A bal szélső fiókirodai kivételével minden ilyen ág tartalmaz egy [R-parancsfájl végrehajtása](https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/) modul, amely a származtatott funkciókat, (B, C és D szolgáltatáskészleteket) rendre összeállított és az importált adatkészlet lesz hozzáfűzve. A következő ábra bemutatja az R-parancsfájl, a második bal fiókiroda B készlet létrehozásához használt.

![szolgáltatások létrehozása](./media/create-features/addFeature-Rscripts.png)

A teljesítmény eredmények négy modell összehasonlítása az alábbi táblázat foglalja össze: 

![eredmények összehasonlítása](./media/create-features/result1.png)

A legjobb eredmények elérése érdekében A + B + C látható funkcióihoz. Vegye figyelembe, hogy a Hibaarány csökken, ha további szolgáltatáskészlet a betanítási adatok szerepelnek. Ellenőrzi, hogy a szolgáltatáskészlete B, C biztosítson a regressziós feladat kapcsolatos további információt a feltételezés. De hozzáadása a D szolgáltatás nem úgy tűnik, hogy adja meg a Hibaarány további csökkentését.

## <a name="example2"></a> 2. példa: Szöveg adatbányászati szolgáltatások létrehozása
Szöveg adatbányászati, például a besorolás és a céggel kapcsolatos véleményeket dokumentumelemzés kapcsolatos feladatok széles körben alkalmazása szolgáltatás mérnöki csapathoz. Például, ha szeretné besorolni a dokumentumok számos kategóriába sorolhatók, egy tipikus feltételezi, hogy kevésbé valószínű egy másik doc kategóriába-e a word/kifejezések egy dokumentumot a kategóriába tartozik. Más szóval a szavakat vagy kifejezéseket terjesztési gyakoriságát el tudja írhatók le másik dokumentum kategóriák. Szöveg adatbányászati alkalmazásokban mivel egyes adatra szöveges-tartalom általában szolgál a bemeneti adatként, a folyamat mérnöki szolgáltatás létrehozásához szükséges a szolgáltatásokat érintő szó vagy kifejezés gyakoriságot.

Ez a feladat eléréséhez technika nevű **szolgáltatáskivonatolás** tetszőleges szöveg szolgáltatások hatékonyan ikonná indexek vonatkozik. Ahelyett, hogy egy adott index, a kivonatoló függvényt alkalmazásával a szolgáltatásokhoz, és közvetlenül a kivonati értékek használata indexek metódus funkciók közötti társítás minden szöveges funkciót (szavak vagy kifejezések).

Az Azure Machine Learning van egy [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul, amely hozza létre ezeket a szó vagy kifejezés szolgáltatások kényelmesen. Alábbi ábrán ez a modul használatának példája. A bemeneti adatkészlet két oszlopokat tartalmazza: a beállításnak 1 és 5 közötti könyv besorolása és a tényleges felülvizsgálati tartalmat. Ez a cél [Szolgáltatáskivonatolás](https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/) modul olyan szolgáltatást, hogy megjelenítése a megfelelő szavak előfordulási gyakoriságát álló, lemezcsoport típusú új beolvasása / tájékoztatás(ok) belül az adott könyv tekintse át az. Ez a modul használatához kövesse az alábbi lépéseket:

* Első lépésként válassza ki az oszlop a bemeneti szöveget tartalmazó (ebben a példában "Col2" jelöli).
* Második, állítsa a "Hashing bitsize" 8-ra, ami azt jelenti, hogy 2 ^ 8 = 256 szolgáltatások jön létre. A szöveg, word/fázisában fogja kivonatolása 256 indexet. A paraméter "Hashing bitsize" tartománya 1 és 31. A keresett szavakat / tájékoztatás(ok) kevésbé valószínű, hogy az azonos indexbe kivonatolása, ha a beállítás nagyobb számnak kell lennie.
* Harmadik, a paraméter értéke "N-g" 2. Ez az érték lekérése a bemeneti szöveg előfordulási gyakoriságát unigrams (egy mindegyik egyszavas szolgáltatás) és bigrams (egy szolgáltatás minden pár szomszédos szavak). A paraméter "N-g" címtartományok 0 és 10, amely megadja, hogy a szolgáltatás szereplő szekvenciális szavak maximális száma.  

!["Funkció Hashing" modul](./media/create-features/feature-Hashing1.png)

Az alábbi ábra bemutatja, milyen ezek új szolgáltatás megjelenését hasonlóan.

!["Funkció Hashing" – példa](./media/create-features/feature-Hashing2.png)

## <a name="conclusion"></a>Összegzés
Tervezett és a kiválasztott szolgáltatásokat a hatékonyabbá teheti a képzési folyamat, amely az adatok tartalmazzák a főbb információkat. Is javítják ezek a modellek power pontosan a bemeneti adatok osztályozására szolgáló, és több abroncsnyomásmérők erős előre jelezni az érdeklődési eredményeit. A szolgáltatás termékgondozó csoportja és a kijelölés kombinálhatja is annak a tanulási több számításilag tractable. Igen, javítja, és majd a szolgáltatások szükséges továbbá bármikor kalibrálhatja vagy a modell betanításához számának csökkentése. Matematikailag nyelven, a modell betanításához kijelölt szolgáltatások olyan minimális független változó, amely az adatok kombinációját ismertetik, és majd sikeresen az eredmények előrejelzése.

Nincs mindig feltétlenül mérnöki vagy szolgáltatás szolgáltatásválasztást végrehajtásához. E rá szükség, vagy nem kéz adatoktól függ, vagy az összegyűjtött, a kiválasztott algoritmus és a cél a kísérlet.


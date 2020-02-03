---
title: A csoportos adatelemzési folyamat a szolgáltatás kiválasztása
description: Szolgáltatás kiválasztása célját ismerteti, és a példákat, azok szerepét a machine Learning adatokat a fejlesztés során.
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
ms.openlocfilehash: 1127a470a48660ffffa892d24c9f2991ec64c8e6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76716682"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funkcióválasztás a csoportos adatelemzési folyamatban (TDSP)
Ez a cikk azt ismerteti, szolgáltatás kiválasztása az alkalmazásában, és példákat tartalmaz a szerepét a machine learning adatokat a fejlesztés folyamatát. Ezek a példák az Azure Machine Learning Studio állítják.

A szolgáltatások mérnöki és kiválasztási funkciója a csoportos adatelemzési folyamat (TDSP) egyik része, amely a következő cikkben található: [Mi a csoportos adatelemzési folyamat?](overview.md). A funkciók mérnöki és kiválasztási funkciója a TDSP **szolgáltatások fejlesztése** lépésének részei.

* **szolgáltatás-mérnöki**: Ez a folyamat megkísérli további releváns funkciók létrehozását az adatok meglévő nyers szolgáltatásaiból, és a prediktív teljesítmény növelését a tanulási algoritmusra.
* **funkció kiválasztása**: Ez a folyamat kiválasztja az eredeti adatszolgáltatások kulcsának részhalmazát, hogy megpróbálja csökkenteni a dimenzióját.

A rendszer általában a szolgáltatások **fejlesztését** alkalmazza a további funkciók létrehozásához, majd a **funkció kiválasztása** lépéssel megszünteti a lényegtelen, redundáns vagy szorosan korrelált funkciókat.

## <a name="filter-features-from-your-data---feature-selection"></a>Szűrő előzetes funkciók a data - szolgáltatás kiválasztása
A szolgáltatás kiválasztása besorolási vagy regressziós feladatokhoz is használható. A cél, hogy válassza ki a funkciók egy részét, amely a dimenziók ábrázolásához eltérés az adatok maximális mennyisége minimális funkciók használatával csökkenthető az eredeti adatkészletből. Ezen funkciók részhalmazát szolgál a modell betanításához. Szolgáltatás kiválasztása két fő célja szolgálja ki.

* Először a szolgáltatás kiválasztása gyakran nem számít, redundáns kiküszöbölése révén növeli a besorolás pontossága, vagy funkciók tekinthetők.
* Másodszor így csökken a több funkciót, így még hatékonyabbá teszi a modell betanítási folyamat. A hatékonyság fontos az olyan tanulók számára, akik a támogatási vektoros gépek betanítására költségesek.

Szolgáltatás kiválasztása az adatkészletben, a modell betanításához használt szolgáltatások számának csökkentése szolgálnak, bár ez nem hivatkozik "dimenziócsökkentést" kifejezés. A szolgáltatás kiválasztási módszerek módosítás nélkül ezeket bontsa ki az adatok az eredeti funkciók egy részét.  Dimenziói csökkentésére szolgáló módszerek a alkalmazni visszafejtett funkciók, amelyek az eredeti funkciók átalakíthatja és így módosíthatja őket. Dimenziói csökkentésére szolgáló módszerek közé egyszerű összetevő elemzés, kanonikus korrelációs elemzési és egyetlen értéket felbontása.

Többek között a funkció kiválasztási módszer egy felügyelt környezetben egy széles körben alkalmazott kategória "szűrő-alapú szolgáltatás kiválasztása" nevezzük. Meghatározza az egyes funkciókról és a célattribútum korrelációját, ezek a metódusok a pontszámot rendel minden funkció statisztikai mérték vonatkoznak. A szolgáltatások majd rangsora szerint a pontszám, tartja, vagy távolítsa el egy adott szolgáltatást küszöbértékét beállítása segítségével használható. A statisztikai intézkedéseket, ezek a metódusok a használt közé tartoznak a Chi squared teszt, a személy korrelációs és a kölcsönös információkat.

Az Azure Machine Learning Studióban nincsenek modulok megadott szolgáltatás kiválasztása. Ahogy az az alábbi ábrán is látható, ezek a modulok a [Filter-alapú funkciók kiválasztása][filter-based-feature-selection] és a [Fisher lineáris diszkrimináns elemzése][fisher-linear-discriminant-analysis].

![A szolgáltatás kiválasztása modulok](./media/select-features/feature-Selection.png)

Vegyük például a [Filter-alapú funkciók kiválasztási][filter-based-feature-selection] moduljának használatát. Az egyszerűség kedvéért továbbra is a szöveg adatbányászati példa használatával. Tegyük fel, hogy egy regressziós modellt szeretne létrehozni a 256-szolgáltatások egy készletének létrehozása után a [szolgáltatás kivonatolási][feature-hashing] modulján keresztül, és a válasz változó a "Col1", amely az 1 és 5 közötti könyv-felülvizsgálati minősítéseket tartalmazza. Állítsa a "Funkció pontozási metódus" "Pearson korrelációs", "céloszlop" kell "Col1" és "Kívánt szolgáltatások száma" 50-re. Ezt követően a modul [Filter-alapú funkciója][filter-based-feature-selection] a 50 funkciókat tartalmazó adatkészletet hoz létre, amely a "Col1" célként megadott attribútummal együtt. A következő ábrán látható a folyamat ehhez a kísérlethez, és a bemeneti paramétereket:

![Filter-alapú szolgáltatás kiválasztási moduljának tulajdonságai](./media/select-features/feature-Selection1.png)

A következő ábrán látható, az így kapott adatkészleteket:

![Eredményül kapott adatkészletet modul szűrő-alapú szolgáltatás kiválasztása](./media/select-features/feature-Selection2.png)

Egyes szolgáltatások összességére vonatkozik önmagában, illetve a target attribútum "Col1" Pearson korrelációját. A felső pontszámok szolgáltatásai megmaradnak.

A kiválasztott funkciók a megfelelő eredmények az alábbi ábrán láthatók:

![Pontszámok modul szűrő-alapú szolgáltatás kiválasztása](./media/select-features/feature-Selection3.png)

Ennek a [szűrő-alapú funkció-kiválasztási modulnak a Kiválasztásával][filter-based-feature-selection] 50-ből 256-funkciók közül választhat, mivel azok a leginkább korrelált funkciókkal rendelkeznek a "Col1" megcélzott változóval, a "Pearson korreláció" pontozási módszer alapján.

## <a name="conclusion"></a>Összegzés
A funkciók mérnöki és funkcióinak kiválasztása két gyakran használt és kiválasztott szolgáltatás, így növelheti az adatokban található kulcsfontosságú információk kinyeréséhez szükséges képzési folyamat hatékonyságát. Emellett javíthatják a modellek hatékonyságát a bemeneti adatok besorolására pontosan, és hatékonyabban dolgozhatók több előre jelezni a lényeges eredményeket. A szolgáltatás jellemzőkiválasztás és -kiemelés kombinálhatja is, hogy akár több tractable a tanulást. Ezt úgy valósítja kerülésről, és ezután továbbá bármikor kalibrálhatja vagy a modell betanításához szükséges szolgáltatások számának csökkentése. Matematikai beszéd, a modell betanításához kiválasztott szolgáltatások független változók, amelyek az adatok a minták ismertetik, és majd sikeresen az eredmények előrejelzésére egy minimális számú.

Nincs mindig feltétlenül szolgáltatás mérnöki vagy szolgáltatás kiválasztása végrehajtásához. Szükség van-e vagy sem, az összegyűjtött adatokat, a kiválasztott algoritmus és a cél a kísérlet függ.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/


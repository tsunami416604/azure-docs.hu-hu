---
title: A csoportos adatelemzési folyamat a szolgáltatás kiválasztása
description: Szolgáltatás kiválasztása célját ismerteti, és a példákat, azok szerepét a machine Learning adatokat a fejlesztés során.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/21/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: de8070906f7b2470378fb631f2e94a96b4a2960d
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138653"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funkcióválasztás a csoportos adatelemzési folyamatban (TDSP)
Ez a cikk azt ismerteti, szolgáltatás kiválasztása az alkalmazásában, és példákat tartalmaz a szerepét a machine learning adatokat a fejlesztés folyamatát. Ezek a példák az Azure Machine Learning Studio állítják. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A jellemzőkiválasztás és -kiemelés szolgáltatások része egy, a csoportos adatelemzési folyamat (TDSP) című cikkben ismertetett [Mi az a csoportos adatelemzési folyamat?](overview.md). A szolgáltatás jellemzőkiválasztás és -kiemelés, részből áll a **funkciók fejlesztése** a TDSP. lépését.

* **jellemzőkiemelés**: Ez a folyamat megpróbálja további kapcsolódó funkciók létrehozása az adatok meglévő nyers funkciókat, és a tanulási algoritmus prediktív teljesítmény növelése érdekében.
* **kijelölés funkció**: Ez a folyamat az eredeti funkciók kulcs részhalmazát kijelöli a kísérlet a betanítási probléma dimenzióinak csökkentése érdekében.

Általában **jellemzőkiemelés** további funkciók létrehozásához először alkalmazza, majd a **kijelölés funkció** irreleváns, redundáns vagy magas kapcsolódó funkciók kiküszöbölése érdekében a lépést.

## <a name="filter-features-from-your-data---feature-selection"></a>Szűrő előzetes funkciók a data - szolgáltatás kiválasztása
Szolgáltatás kiválasztása egy olyan folyamat, a prediktív modellezési feladatokhoz, mint az osztályozási vagy regressziós feladatok betanítási adatkészletek építéséhez gyakran alkalmazott. A cél, hogy válassza ki a funkciók egy részét, amely a dimenziók ábrázolásához eltérés az adatok maximális mennyisége minimális funkciók használatával csökkenthető az eredeti adatkészletből. Ezen funkciók részhalmazát szolgál a modell betanításához. Szolgáltatás kiválasztása két fő célja szolgálja ki.

* Először a szolgáltatás kiválasztása gyakran nem számít, redundáns kiküszöbölése révén növeli a besorolás pontossága, vagy funkciók tekinthetők.
* Másodszor így csökken a több funkciót, így még hatékonyabbá teszi a modell betanítási folyamat. Hatékonyság különösen fontos a tanulók, amelyek költséges, például a támogatási vektor gépek betanításához.

Szolgáltatás kiválasztása az adatkészletben, a modell betanításához használt szolgáltatások számának csökkentése szolgálnak, bár ez nem hivatkozik "dimenziócsökkentést" kifejezés. A szolgáltatás kiválasztási módszerek módosítás nélkül ezeket bontsa ki az adatok az eredeti funkciók egy részét.  Dimenziói csökkentésére szolgáló módszerek a alkalmazni visszafejtett funkciók, amelyek az eredeti funkciók átalakíthatja és így módosíthatja őket. Dimenziói csökkentésére szolgáló módszerek közé egyszerű összetevő elemzés, kanonikus korrelációs elemzési és egyetlen értéket felbontása.

Többek között a funkció kiválasztási módszer egy felügyelt környezetben egy széles körben alkalmazott kategória "szűrő-alapú szolgáltatás kiválasztása" nevezzük. Meghatározza az egyes funkciókról és a célattribútum korrelációját, ezek a metódusok a pontszámot rendel minden funkció statisztikai mérték vonatkoznak. A szolgáltatások majd rangsora szerint a pontszám, tartja, vagy távolítsa el egy adott szolgáltatást küszöbértékét beállítása segítségével használható. A statisztikai intézkedéseket, ezek a metódusok a használt közé tartoznak a Chi squared teszt, a személy korrelációs és a kölcsönös információkat.

Az Azure Machine Learning Studióban nincsenek modulok megadott szolgáltatás kiválasztása. Az alábbi ábrán látható, ezek a modulok tartalmaznak [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] és [Fisher lineáris Discriminant elemzési] [ fisher-linear-discriminant-analysis].

![A szolgáltatás kiválasztása modulok](./media/select-features/feature-Selection.png)

Fontolja meg, például a használatát a [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modul. Az egyszerűség kedvéért továbbra is a szöveg adatbányászati példa használatával. Tegyük fel, hogy szeretné-e 256 szolgáltatások keresztül létrehozott regressziós modell létrehozása a [Szolgáltatáskivonatolás] [ feature-hashing] modul, és hogy a válasz változót a "Col1", amely tartalmazza a könyv felülvizsgálati minősítések 1 és 5 közötti. Állítsa a "Funkció pontozási metódus" "Pearson korrelációs", "céloszlop" kell "Col1" és "Kívánt szolgáltatások száma" 50-re. Ezután a modul [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] együtt a target attribútum "Col1" 50 funkciókat tartalmazó adatkészletet hoz létre. A következő ábrán látható a folyamat ehhez a kísérlethez, és a bemeneti paramétereket:

![Szűrő-alapú szolgáltatás kiválasztása modul tulajdonságai](./media/select-features/feature-Selection1.png)

A következő ábrán látható, az így kapott adatkészleteket:

![Eredményül kapott adatkészletet modul szűrő-alapú szolgáltatás kiválasztása](./media/select-features/feature-Selection2.png)

Egyes szolgáltatások összességére vonatkozik önmagában, illetve a target attribútum "Col1" Pearson korrelációját. A felső pontszámok szolgáltatásai megmaradnak.

A kiválasztott funkciók a megfelelő eredmények az alábbi ábrán láthatók:

![Pontszámok modul szűrő-alapú szolgáltatás kiválasztása](./media/select-features/feature-Selection3.png)

Ez alkalmazásával [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modult, 50 kívüli 256 funkciók ki van jelölve, mert a legtöbb korrelált funkcióit a célváltozó "Col1", a pontozó módszer alapján "Pearson-korrelációs".

## <a name="conclusion"></a>Összegzés
Funkció termékgondozó csoportja és a szolgáltatás kiválasztása: a két gyakran fejthetők vissza, és a kijelölt szolgáltatások a betanítási folyamat, amely az adatok tartalmazzák a legfontosabb információt nyerhet ki tett kísérletek hatékonyságának növelése. Emellett javíthatják a modellek hatékonyságát a bemeneti adatok besorolására pontosan, és hatékonyabban dolgozhatók több előre jelezni a lényeges eredményeket. A szolgáltatás jellemzőkiválasztás és -kiemelés kombinálhatja is, hogy akár több tractable a tanulást. Ezt úgy valósítja kerülésről, és ezután továbbá bármikor kalibrálhatja vagy a modell betanításához szükséges szolgáltatások számának csökkentése. Matematikai beszéd, a modell betanításához kiválasztott szolgáltatások független változók, amelyek az adatok a minták ismertetik, és majd sikeresen az eredmények előrejelzésére egy minimális számú.

Nincs mindig feltétlenül szolgáltatás mérnöki vagy szolgáltatás kiválasztása végrehajtásához. Szükség van-e vagy sem, az összegyűjtött adatokat, a kiválasztott algoritmus és a cél a kísérlet függ.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/


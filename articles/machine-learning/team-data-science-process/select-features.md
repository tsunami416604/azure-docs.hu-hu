---
title: "A beállítást, az adatok tudományos folyamatban kiválasztása |} Microsoft Docs"
description: "Szolgáltatás kiválasztása célját, és a gépi tanulás adatok a fejlesztés során szerepkörük példákat."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 878541f5-1df8-4368-889a-ced6852aba47
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: zhangya;bradsev
ms.openlocfilehash: 1d45c8beb78101b04b9e998e3e202477cb5ce6c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Funkcióválasztás a csoportos adatelemzési folyamatban (TDSP)
Ez a cikk azt ismerteti, szolgáltatás kiválasztása alkalmazásában és példák a szerepet a gépi tanulás adatokat a fejlesztés során. Ezekben a példákban az Azure Machine Learning Studio állítják. 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

A mérnöki és funkciók kiválasztása része egy, a csapat adatok tudományos folyamat (TDSP) című témakörben ismertetett [Mi az az Team tudományos folyamat?](overview.md). A szolgáltatás termékgondozó csoportja és a kijelölés részét képezik a **szolgáltatások fejlesztéséhez** a TDSP lépését.

* **jellemzőkiemelés**: Ez a folyamat megkísérli a meglévő nyers funkciókat az adatok további kapcsolódó szolgáltatások létrehozásához és a tanulási algoritmust előrejelzési teljesítményének növelése érdekében.
* **szolgáltatás kiválasztása**: Ez a folyamat az eredeti adatokkal kapcsolatos funkciókkal kulcs részhalmazát kijelöli a kísérlet dimenzióinak, a képzési probléma csökkentése érdekében.

Általában **jellemzőkiemelés** további szolgáltatások létrehozásához először vonatkozik, majd a **kijelölés funkció** lépést irreleváns, redundáns vagy magas kapcsolódó szolgáltatásokat nyújthatnak.

## <a name="filtering-features-from-your-data---feature-selection"></a>Az adatok - szolgáltatás kiválasztása a szűrési funkciók
Szolgáltatás kiválasztása rendkívül gyakran alkalmazott prediktív modellezési feladatokhoz, mint az osztályozási vagy regressziós feladatok képzési adathalmaz építéséhez folyamat. A cél, hogy válassza ki a funkciók egy részét az eredeti adathalmazból, amely a dimenziók csökkentik a legszükségesebb funkciókat használ az eltérés alapján, az adatok maximális száma ábrázolásához. Ezen funkciók részhalmazát, majd a része a modell betanításához csak funkciók. Szolgáltatás kiválasztása a két fő célokra szolgál.

* Szolgáltatás kiválasztása először, gyakran nem számít, redundáns kiküszöbölése révén növeli a besorolás pontossága, vagy szolgáltatások szorosan összefügg.
* Második így csökken a szolgáltatások a számát, ezáltal hatékonyabban modell képzési folyamat. Ez különösen fontos a tanulókkal, amelyek például a támogatási vektoros gépek betanítása drága.

Szolgáltatás kiválasztása a keresési funkciók a modell betanításához használandó adatkészlet számának csökkentése érdekében, bár ez nem általában hivatkozik "granularitása csökkentési" kifejezés. A szolgáltatás kiválasztási módszerek őket módosítása nélkül bontsa ki az adatokat az eredeti funkciók egy részéhez.  Granularitása csökkentési módszereket tervezni a visszafejtett funkciókra átalakíthatja az eredeti funkciók és így módosíthatja azokat. Granularitása csökkentési módszerek például egyszerű összetevő elemzés, kanonikus korrelációs elemzés és egyes érték felbontás ellen.

Többek között szolgáltatás kijelölés módszerek olyan felügyelt környezetben egy széles körben alkalmazott kategória neve "szűrő alapú szolgáltatás kiválasztása". Értékelése az egyes szolgáltatások és a target attribútummal közötti összefüggést, ezek a módszerek pontszámot hozzárendelése az egyes szolgáltatások statisztikai mérték vonatkoznak. A szolgáltatások majd rangsora által a pontszám, megadhatja a küszöbértéket tartására, vagy egy adott funkcióhoz kiküszöbölése érdekében használható. Ezek a módszerek használt statisztikai intézkedések például személy korrelációs, kölcsönös adatokat és a Chi squared vizsgálat.

Az Azure Machine Learning Studióban nincsenek szolgáltatás kiválasztása a megadott modulokat. A következő ábrán látható, tartalmaz-e ezek a modulok [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] és [Fisher lineáris Discriminant elemzés] [ fisher-linear-discriminant-analysis].

![Szolgáltatás kiválasztása – példa](./media/select-features/feature-Selection.png)

Vegye figyelembe például használatát a [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modul. Céljából kényelme érdekében továbbra is használja a fent vázolt szöveg adatbányászati példában. Tegyük fel, hogy szeretnénk egy regressziós modell létrehozása 256 funkciókat keresztül létrehozása után a [Szolgáltatáskivonatolás] [ feature-hashing] modul, és, hogy a válasz változó "Oszlop1", és jelenti a könyv áttekintése a minősítések beállításnak 1 és 5 közötti. "Funkció pontozási metódus" értékre állításával "Pearson korrelációs", "céloszlop" kell "Oszlop1" és "Kívánt szolgáltatások száma" 50 kell lennie. Ezután a modul [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] együtt a target attribútummal "Oszlop1" 50 szolgáltatásokat tartalmazó adatkészletet eredményez. Az alábbi ábra szemlélteti a ehhez a kísérlethez és a bemeneti paraméterek csak ismerteti.

![Szolgáltatás kiválasztása – példa](./media/select-features/feature-Selection1.png)

Az alábbi ábrán láthatók az eredményül kapott adatkészletek. Egyes szolgáltatások program pontozza a mennyiségeket alapján a a Pearson korrelációs és a cél "Oszlop1" attribútum között. A felső pontszámok funkcióinak tartanak.

![Szolgáltatás kiválasztása – példa](./media/select-features/feature-Selection2.png)

A megfelelő eredmények a kiválasztott szolgáltatások az alábbi ábrán látható.

![Szolgáltatás kiválasztása – példa](./media/select-features/feature-Selection3.png)

Ez alkalmazásával [szűrő-alapú szolgáltatás kiválasztása] [ filter-based-feature-selection] modul, 50 kívüli 256 szolgáltatások van kiválasztva, mert a cél változó "Oszlop1", a legtöbb korrelált funkcióinak a pontozási módszer alapján "Korrelációs Pearson".

## <a name="conclusion"></a>Összegzés
A szolgáltatás termékgondozó csoportja és a szolgáltatás kiválasztása két gyakran fejthetők vissza, és a kiválasztott funkciók a képzés folyamattal, amely az adatok tartalmazzák a főbb információkat kísérletek hatékonyságának növelése. Is javítják ezek a modellek power pontosan a bemeneti adatok osztályozására szolgáló, és több abroncsnyomásmérők erős előre jelezni az érdeklődési eredményeit. A szolgáltatás termékgondozó csoportja és a kijelölés kombinálhatja is annak a tanulási több számításilag tractable. Igen, javítja, és majd a szolgáltatások szükséges továbbá bármikor kalibrálhatja vagy a modell betanításához számának csökkentése. Matematikailag nyelven, a modell betanításához kijelölt szolgáltatások olyan minimális független változó, amely az adatok kombinációját ismertetik, és majd sikeresen az eredmények előrejelzése.

Ne feledje, hogy nem mindig feltétlenül mérnöki vagy szolgáltatás szolgáltatásválasztást végrehajtásához. Hogy szükséges-e az adatokat a Microsoft rendelkezik, vagy gyűjtése, az algoritmus kiválasztása a Microsoft és a cél a kísérlet függ.

<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[fisher-linear-discriminant-analysis]: https://msdn.microsoft.com/library/azure/dcaab0b2-59ca-4bec-bb66-79fd23540080/


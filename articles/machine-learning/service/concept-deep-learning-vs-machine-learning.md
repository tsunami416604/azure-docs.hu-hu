---
title: Bevezetés a Deep learning és a Machine learning szolgáltatásba
titleSuffix: Azure Machine Learning service
description: Ismerje meg a Deep learning és a gépi tanulás közötti kapcsolatot, valamint azt, hogy azok hogyan illeszkednek a mesterséges intelligencia kategóriába. Ez a cikk azt ismerteti, Hogyan oldhatók meg a Deep learning olyan forgatókönyvek, mint például a csalások észlelése, a hang-és Arcfelismerés, a hangulat-elemzés és az idősorozat-előrejelzés.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 08/07/2019
ms.openlocfilehash: 186ba03142e01c4b9340fc5b492621cc17575104
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934291"
---
# <a name="introduction-to-deep-learning-vs-machine-learning"></a>A Deep learning és a Machine learning bemutatása

Ez a cikk segít megérteni a Deep learning és a Machine learning közötti kapcsolatot. Megtudhatja, hogyan hasonlítja össze a két fogalmat, és hogyan illeszkednek a mesterséges intelligencia tágabb kategóriába. Végül a cikk leírja, hogyan alkalmazhatók a mély tanulás a valós forgatókönyvekre, például a csalások észlelésére, a hang-és Arcfelismerés, a hangulat-elemzés és az idősorozat-előrejelzés alapján.

## <a name="how-do-deep-learning-machine-learning-and-ai-relate-to-one-another"></a>Hogyan kapcsolódnak egymáshoz a mély tanulás, a gépi tanulás és az AI?

![Kapcsolati diagram: AI vs. Machine learning és Deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

A Deep learning és a Machine learning és a közötti különbség megismerése érdekében AI, ismerkedjen meg a következő tanulmányi mezőkkel:

- A **Deep learning** a gépi tanulás egy részhalmaza, amely lehetővé teszi, hogy a gép betanítsa magát a mesterséges neurális hálózatokon. Ebben az esetben a _tanulási folyamatot_ mélynek nevezzük, mert a mesterséges neurális hálózatok szerkezete több bemeneti, kimeneti és rejtett rétegből áll. Minden réteg olyan egységeket tartalmaz, amelyek átalakítja a bemeneti adatokat olyan adatokba, amelyeket a következő réteg használhat egy bizonyos prediktív feladat végrehajtásához. Ennek a struktúrának köszönhetően a gépek saját adatfeldolgozáson keresztül képesek megtanulni.

- A **gépi tanulás** a mesterséges intelligencia olyan részhalmaza, amely olyan technikákat (például mély tanulást) tartalmaz, amelyek lehetővé teszik a gépek számára, hogy a tapasztalatok alapján javítsák a feladatokat. Ebben az esetben a _tanulási folyamat_ a következő lépésektől függ:

    1. Egy algoritmust az adatokkal több információ megadásával (például a szolgáltatások kinyerésének elindításával) kell megetetni.
    2. Ezekkel az értékekkel betaníthatja a modelleket.
    3. Tesztelje és telepítse a modellt.
    4. Egy adott automatizált prediktív feladat elvégzéséhez használja fel a központilag telepített modellt.

- A **mesterséges intelligencia (AI)** olyan technika, amely lehetővé teszi a számítógépek számára az emberi intelligenciát. A gépi tanulást is magában foglalja. Fontos megérteni az AI és a _Machine learning és a Deep learning_közötti kapcsolatot: A gépi tanulás lehetővé teszi a mesterséges intelligencia elérését, ami a gépi tanulás és a mélyreható tanulási technikák használatával olyan számítógépes rendszerek és alkalmazások készítésére használható, amelyek az emberi intelligenciához gyakran kapcsolódó feladatokat hajthatnak végre, például vizuális észlelés, beszédfelismerés, döntéshozatal és fordítás nyelvek között.

## <a name="comparison-of-deep-learning-vs-machine-learning"></a>A Deep learning és a Machine learning összehasonlítása

Most, hogy áttekintette a Machine learning és a Deep learning áttekintését, hasonlítsa össze a két technikát. Gépi tanulás esetén az algoritmusnak azt kell megadnia, hogyan lehet pontos előrejelzést készíteni további információk megadásával. míg a mély tanulás esetében az algoritmus képes megtanulni, hogy a mesterséges neurális hálózati struktúrának köszönhetően a saját adatfeldolgozása révén.

Az alábbi táblázat részletesebben összehasonlítja a két technikát:

| |Minden gépi tanulás |Csak mély tanulás|
|---|---|---|
|  **Adatpontok száma** | Használhat a felhasználók által biztosított kis adatmennyiségeket | Nagy mennyiségű betanítási adattal kell rendelkeznie a rövid következtetések elvégzéséhez |
|  **Hardver függőségei** | Az alacsony végű gépeken is működhet. Nincs szükség nagy mennyiségű számítási teljesítményre | A magas végú gépektől függ. Természeténél fogva nagy számú mátrix-szorzási műveletet végez. Ezek a műveletek hatékonyan optimalizálható GPU használatával |
|  **Featurization folyamat** | A funkciók pontos azonosítását és létrehozását igénylik a felhasználók számára | Megismerheti az adatok magas szintű funkcióit, és önmagában új szolgáltatásokat hoz létre |
|  **Tanulási megközelítés** | Kis méretűre osztja a tevékenységeket, majd egyetlen következtetésbe egyesítheti a kapott eredményeket. | Feloldja a problémát egy teljes körű alapon |
|  **Végrehajtási idő** | Viszonylag kevesebb időt vesz igénybe a betanítás, néhány másodperctől néhány órára | Szokatlanul hosszú időt vesz igénybe a betanítás során, mert számos paraméter szerepel egy mély tanulási algoritmusban |
|  **Kimenet** | A kimenet általában numerikus érték, például pontszám vagy besorolás | A kimenet bármi lehet egy pontszámból, egy szövegből, egy elemből vagy egy hangból |

## <a name="deep-learning-use-cases-what-problems-does-it-solve"></a>Mély tanulási használati esetek: Milyen problémák megoldására?

A mesterséges neurális hálózati struktúra miatt a Deep learning kiemelkedik a strukturálatlan adatmennyiségek, például képek, hangok, videók és szövegek azonosítására. Emiatt gyorsan át lehet alakítani számos iparágat, például az egészségügyet, az energiát, a fintechnek, a szállítást és másokat a hagyományos üzleti folyamatok újragondolása érdekében. A Deep learning leggyakoribb alkalmazásai a következő bekezdésekben olvashatók.

### <a name="named-entity-recognition"></a>Nevesített entitások felismerése

A mélyreható tanulási hálózatok egyike a nevesített entitások felismerése, amely a strukturálatlan, címkézetlen adatok bizonyos típusai, például személyek, helyek, vállalatok vagy dolgok kinyerésének módja. Ezek az adatok egy strukturált sémában tárolhatók, és a címek listájának összeállítására szolgálnak, illetve az Identity Validation Engine-nek is.

### <a name="object-detection"></a>Objektumészlelés

A Deep learning számos objektum-észlelési használati esettel lett alkalmazva. Az objektumok észlelése tulajdonképpen egy kétrészes folyamat: a képbesorolás, majd a rendszerkép honosítása. A rendszerkép besorolása határozza meg, hogy a képen milyen objektumok láthatók, például egy autó vagy egy személy, míg a képhonosítás megadja az objektumok adott helyét. Az objektumok észlelése már használatban van a játékokban, a kiskereskedelemben, a turizmusban és az önkiszolgáló személygépkocsi-iparban.

### <a name="image-caption-generation"></a>Képfelirat létrehozása

A képfelismerési feladathoz hasonlóan a képfelirat is az a feladat, ahol egy adott rendszerkép esetében a rendszernek egy olyan feliratot kell létrehoznia, amely leírja a rendszerkép tartalmát. Ha a fényképek objektumainak észleléséhez és az objektumok címkéjének létrehozásához azt láthatja, hogy a következő lépés a címkék egy koherens mondat-leírásba való bekapcsolása. A rendszerek általában a nagy mennyiségű, egyidejű neurális hálózatok használatát igénylik az objektumok észleléséhez a fényképekben, majd egy visszatérő neurális hálózatot (RNN), amely egy koherens mondatba kapcsolja a címkéket.

### <a name="machine-translation"></a>Gépi fordítás

A gépi fordítás szavakat, kifejezéseket vagy mondatokat használ az egyik nyelvről, és automatikusan lefordítja őket egy másik nyelvre. Az automatikus gépi fordítás sok időt vett igénybe, de a Deep learning két konkrét területen érhető el: a szöveg automatikus fordítása és a képek automatikus fordítása. A megfelelő adatátalakítással a mély hálózat képes megérteni a szöveg-, hang-és vizuális jeleket. A gépi fordítás használatával azonosíthatók a hangtöredékek a nagyobb hangfájlokban, és szövegként is áthelyezhetők a kimondott szövegből vagy képekből.

### <a name="text-analytics"></a>Szövegelemzés

Az e-Discovery egy fontos feladat, amelyet a Deep learning képes elvégezni. A vállalatok mélyreható, tanuláson alapuló szöveges elemzéseket használnak a bennfentes kereskedelmi észleléshez és a kormányzati előírások betartásához. A fedezeti alapok a Text Analytics használatával részletezik a nagyméretű dokumentumok tárházait, így betekintést nyerhetnek a jövőbeli befektetési teljesítményre és piaci hangulatra. A Deep learning-alapú szöveges elemzések esetében a használati eset a nagy mennyiségű szöveges adat elemzésére és összesítések készítésére való képességére mutat.

## <a name="what-are-artificial-neural-networks"></a>Mik azok a mesterséges neurális hálózatok?

A mesterséges neurális hálózatokat összekapcsolt csomópontok rétegei alkotják. A Deep learning-modellek nagyon nagy számú réteggel rendelkező neurális hálózatokat használnak. A legnépszerűbb mesterséges neurális hálózati tipológiák az alábbiakban tárgyaljuk.

### <a name="feedforward-neural-network"></a>Feedforward neurális hálózat

A feedforward neurális hálózat a mesterséges neurális hálózat legalapvetőbb típusa, amelyben az információ csak egy irányba utazik a bemeneti rétegből a kimeneti rétegbe. A Feedforward neurális hálózatokat a rendszer a rejtett rétegek sorozatán keresztül alakítja át. Minden réteg idegsejtekből tevődik össze, ahol minden réteg teljes mértékben csatlakozik a rétegben lévő összes neuronokhoz. Végül van egy utolsó teljesen csatlakoztatott réteg – a kimeneti réteg, amely a generált előrejelzéseket jelöli.

### <a name="recurrent-neural-network"></a>Visszatérő neurális hálózat

Az ismétlődő neurális hálózatok egy szélesebb körben használt mesterséges neurális hálózat, amely a rétegek kimenetének mentésének és a bemeneti rétegbe való visszaetetésének elvén működik, így segítve a réteg eredményének előrejelzésében. Ezek a neurális hálózatok nagyobb tanulási képességekkel rendelkeznek, és széles körben használják az összetettebb feladatokhoz, például a kézírásos vagy a nyelvi felismeréshez.

### <a name="convolutional-neural-networks"></a>Összetartozó neurális hálózatok

A többrészes neurális hálózat különösen hatékony mesterséges neurális hálózat, amely egyedi architektúrát mutat be. Először a rétegek három dimenzióban vannak rendszerezve: szélesség, magasság és mélység. Ezen túlmenően a neuronok az egyik rétegben nem csatlakoznak a következő rétegben lévő összes neuronokhoz, hanem csak egy kis régióhoz. Végül a végső kimenetet a rendszer a mélységi dimenzió mentén rendezi a valószínűségi pontszámok egyetlen vektorára. Ezek a neurális hálózatok olyan területeken lettek felhasználva, mint a képfelismerés és a besorolás.

## <a name="next-steps"></a>További lépések

A következő cikkek bemutatják, hogyan használhatja a Deep learning-technológiát a [Azure Machine learning szolgáltatásban](/azure/machine-learning/service/):

- [Kézzel írt számjegyek osztályozása TensorFlow-modellel](how-to-train-tensorflow.md)
- [Kézzel írt számjegyek besorolása TensorFlow-becsléssel és kerasz](how-to-train-keras.md)
- [Rendszerképek besorolása Pytorch-modellel](how-to-train-pytorch.md)
- [Kézzel írt számjegyek besorolása egy Chainer-modellel](how-to-train-pytorch.md)

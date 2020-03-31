---
title: Kezelje a gyakori ML-modell buktatóit az automatizált gépi tanulással.
titleSuffix: Azure Machine Learning
description: Azonosítsa és kezelje az ML-modellek gyakori buktatóit az Azure Machine Learning automatizált gépi tanulási megoldásaival.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 03/27/2020
ms.openlocfilehash: e0bc1aa48dfb40ea146fa79fdfd57da841ca1404
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385531"
---
# <a name="manage-ml-pitfalls-with-automated-machine-learning"></a>Ml-es buktatók kezelése automatizált gépi tanulással

A túlillesztett és kiegyensúlyozatlan adatok gyakori buktatókat gépi tanulási modellek létrehozásakor. Alapértelmezés szerint az Azure Machine Learning automatizált gépi tanulás a diagramok és metrikák segítségével azonosítani ezeket a kockázatokat, és megvalósítja az ajánlott eljárásokat, hogy segítsen enyhíteni őket. 

## <a name="identify-over-fitting"></a>A túlillesztés azonosítása

A gépi tanulás túlzott beszerelése akkor következik be, ha egy modell túl jól illeszkedik a betanítási adatokhoz, és ennek eredményeképpen nem tudja pontosan megjósolni a láthatatlan tesztadatokon. Más szóval a modell egyszerűen memorizálta a betanítási adatok ban adott mintákat és zajt, de nem elég rugalmas ahhoz, hogy valós adatokra vonatkozó előrejelzéseket készítsen.

Vegye figyelembe a következő betanított modelleket, valamint a hozzájuk tartozó vonat- és vizsgálati pontosságot.

| Modell | A vonat pontossága | A vizsgálat pontossága |
|-------|----------------|---------------|
| A | 99.9% | 95% |
| B | 87% | 87% |
| C# | 99.9% | 45% |

Figyelembe véve az **A**modellt, általános tévhit, hogy ha a nem látott adatokon a teszt pontossága alacsonyabb, mint a betanításpontosság, a modell túl van szerelve. Azonban a teszt pontosságának mindig kisebbnek kell lennie, mint az edzés pontossága, és a túlilleszkedés és a megfelelő illeszkedés különbsége *sokkal* kevésbé pontos. 

Az **A** és **B**modellek összehasonlításakor az **A** modell jobb modell, mivel nagyobb vizsgálati pontossággal rendelkezik, és bár a vizsgálati pontosság valamivel alacsonyabb, 95%, nem jelent jelentős különbséget, ami arra utal, hogy a túlillesztés jelen van. Nem csak azért választanád a **B** modellt, mert a vonat és a teszt pontossága közelebb van egymáshoz.

A **C.** minta a túlillesztés egyértelmű esetét jelenti; a képzés pontossága nagyon magas, de a teszt pontossága közel sem olyan magas. Ez a különbségtétel szubjektív, de a probléma és az adatok ismeretéből, valamint a hiba nagyságrendjéből származik.

## <a name="prevent-over-fitting"></a>A túlszerelés megakadályozása

A legkirívóbb esetekben a túlfelszerelt modell azt feltételezi, hogy a betanítás során látható jellemzőérték-kombinációk mindig pontosan ugyanazt a kimenetet eredményezik a célhoz.

A legjobb módja annak, hogy megakadályozzák a túlzott felszerelés, hogy kövesse a vk legjobb gyakorlatok, beleértve:

* Több betanítási adat használata és a statisztikai elfogultság kiküszöbölése
* A célszivárgás megelőzése
* Kevesebb funkció használata
* **Szabályosítás és hiperparaméter-optimalizálás**
* **A modell összetettségének korlátai**
* **Keresztellenőrzés**

Az automatizált pénzmosási rendszer összefüggésében a fenti első három elem a **bevált gyakorlatok, amelyeket ön valósít meg.** Az utolsó három félkövér elem a **legjobb gyakorlat az automatikus ml-implements** alapértelmezés szerint a túlillesztés elleni védelem érdekében. Az automatizált ml-től eltérő beállításokban mind a hat bevált gyakorlatot érdemes követni a modellek túlszerelésének elkerülése érdekében.

### <a name="best-practices-you-implement"></a>Az Ön által megvalósított gyakorlati tanácsok

**Több adat** használata a legegyszerűbb és a lehető legjobb módja annak, hogy megakadályozzák a túlilleszkedést, és a hozzáadott bónusz általában növeli a pontosságot. Ha több adatot használ, a modell nehezebben megjegyzi a pontos mintákat, és olyan megoldásokat kell elérnie, amelyek rugalmasabbak a több feltétel hez. Fontos felismerni a **statisztikai elfogultságot**is, hogy a betanítási adatok ne tartalmazzanak olyan elkülönített mintákat, amelyek nem jelennek meg az élő előrejelzési adatokban. Ezt a forgatókönyvet nehéz lehet megoldani, mert lehet, hogy nem lehet túlilleszkedni a vonat és a tesztkészletek között, de az élő vizsgálati adatokhoz képest előfordulhat, hogy túlilleszkedik.

A célszivárgás hasonló probléma, ahol előfordulhat, hogy nem látja a vonat/tesztkészletek közötti túlillesztést, hanem előrejelzési időpontban jelenik meg. Cél szivárgás akkor jelentkezik, amikor a modell "csal" a betanítás során az adatokhoz való hozzáférés, hogy általában nem kell előrejelzési időben. Ha például a probléma az, hogy hétfőn megjósolja, hogy pénteken milyen nyersanyagár lesz, de az egyik funkció véletlenül csütörtökről származó adatokat tartalmazott, akkor az olyan adatok lennének, amelyekkel a modell előrejelzési időben nem lesz, mivel nem lát a jövőbe. Cél szivárgás egy egyszerű hiba, hogy hiányzik, de gyakran jellemzi rendellenesen nagy pontossággal a problémát. Ha megpróbálja megjósolni a készlet árát, és 95%-os pontossággal betanított egy modellt, akkor valószínűleg célszivárgás van valahol a funkciókban.

A funkciók eltávolítása segíthet a túlillesztésben is, mivel megakadályozza, hogy a modell túl sok mezőt használjon az adott minták memorizálásához, így rugalmasabbá teheti azt. Nehéz lehet mennyiségileg mérni, de ha el tudja távolítani a funkciókat, és megtartja ugyanazt a pontosságot, akkor valószínűleg rugalmasabbá tette a modellt, és csökkentette a túlszerelés kockázatát.

### <a name="best-practices-automated-ml-implements"></a>Gyakorlati tanácsok automatizált pénzmosási valósítók

A legalizálás az a folyamat, amelynek során minimalizáljuk a költségfunkciót, hogy szankcionálják az összetett és túlfelszerelt modelleket. A regiszterizálási függvényeknek különböző típusai vannak, de általában mindegyik bünteti a modell együtthatójának méretét, varianciáját és összetettségét. Az automatikus ml az L1 (Lasso), L2 (Ridge) és elasticNet (L1 és L2 egyidejű) különböző kombinációkban, különböző modell hiperparaméter-beállításokkal, amelyek szabályozzák a túlillesztést, l1 (gerinc) és ElasticNet (L1 és L2 egyidejű) különböző kombinációkban használja. Egyszerűen fogalmazva, automatizált ML változik, hogy mennyi a modell szabályozott, és válassza ki a legjobb eredményt.

Az automatikus ml explicit modellösszetettségi korlátozásokat is alkalmaz a túlillesztés megakadályozása érdekében. A legtöbb esetben ez a megvalósítás kifejezetten a döntési fa vagy erdő algoritmusok, ahol az egyes fa max-mélység korlátozott, és az összes használt fák erdő vagy együttes technikák korlátozottak.

Keresztérvényesítés (CV) az a folyamat, amelynek során a teljes betanítási adatok számos részhalmazát, és a modell betanítása minden egyes részhalmaza. Az ötlet az, hogy egy modell is kap "szerencsés", és nagy pontosságú egy részhalmaza, de segítségével sok részhalmazok a modell nem fogja elérni ezt a nagy pontosságminden alkalommal. Cv-vel egy érvényesítési várakoztatási adatkészletet kell megadnia, megadhatja az önéletrajz-redők (részhalmazok száma) és az automatikus ml betanítja a modellt, és behangolja a hiperparamétereket az érvényesítési készlet ben lévő hibák minimalizálása érdekében. Egy CV-hajtás lehet túlilleszkedés, de sok közülük használatával csökkenti annak valószínűségét, hogy a végső modell túlilleszkedik. A kompromisszum az, hogy az önéletrajz hosszabb képzési időt és így nagyobb költséget eredményez, mivel a modell egyszeri betanítása helyett egyszer edzheti azt minden *n* CV részhalmazhoz. 

> [!NOTE]
> A keresztérvényesítés alapértelmezés szerint nincs engedélyezve; automatikus gépelési beállításokban kell konfigurálni. A keresztérvényesítés konfigurálása és az érvényesítési adatkészlet megadása után azonban a folyamat automatikus lesz. Lásd: 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Kiegyensúlyozatlan adatokkal rendelkező modellek azonosítása

A kiegyensúlyozatlan adatok általában a gépi tanulási besorolási forgatókönyvek adataiban találhatók, és olyan adatokra hivatkoznak, amelyek az egyes osztályokban a megfigyelések aránytalan arányát tartalmazzák. Ez az egyensúlyhiány a modell pontosságának tévesen érzékelt pozitív hatásához vezethet, mivel a bemeneti adatok elfogultságot mutatnak egy osztály felé, ami azt eredményezi, hogy a betanított modell utánozza ezt az elfogultságot. 

Mivel a besorolási algoritmusokat általában pontosság alapján értékelik ki, a modell pontossági pontszámának ellenőrzése jó módszer annak azonosítására, hogy a kiegyensúlyozatlan adatok hatással volt-e rá. Vajon tényleg nagy pontosságú vagy nagyon alacsony pontosságú bizonyos osztályok?

Emellett az automatizált ML-futtatások automatikusan generálják a következő diagramokat, amelyek segítségével megismerheti a modell besorolásainak helyességét, és azonosíthatja a kiegyensúlyozatlan adatok által potenciálisan érintett modelleket.

Diagram| Leírás
---|---
[Zavartmátrix](how-to-understand-automated-ml.md#confusion-matrix)| Kiértékeli a helyesen osztályozott címkéket az adatok tényleges címkéivel szemben. 
[Precíziós visszahívás](how-to-understand-automated-ml.md#precision-recall-chart)| Kiértékeli a helyes címkék arányát az adatok talált címkepéldányainak arányával 
[ROC-görbék](how-to-understand-automated-ml.md#roc)| Kiértékeli a helyes címkék arányát a hamis pozitív címkék arányával szemben.

## <a name="handle-imbalanced-data"></a>Kiegyensúlyozatlan adatok kezelése 

A gépi tanulási munkafolyamat egyszerűsítésére irányuló célja részeként az automatizált ml olyan képességeket épített be, amelyek segítenek a kiegyensúlyozatlan adatok kezelésében, 

- **Súlyoszlop:** az automatizált ml egy súlyozott oszlopot támogat bemenetként, így az adatok sorait fel- vagy lekell súlyozni, ami többé-kevésbé "fontossá" teheti az osztályt.

- Az automatikus ml által használt algoritmusok akár 20:1-ig is megfelelően tudják kezelni az egyensúlyhiányt, ami azt jelenti, hogy a leggyakoribb osztály nak 20-szor több sora lehet az adatokban, mint a legkevésbé általános osztálynak.

A következő technikák további lehetőségek a kiegyensúlyozatlan adatok kezelésére az automatizált ml-en kívül. 

- Újraszámítás még az osztály egyensúlyhiánya, akár up-mintavétel a kisebb osztályok vagy lefelé mintavétel ez a nagyobb osztályok. Ezek a módszerek szakértelmet igényelnek a feldolgozáshoz és elemzéshez.

- Használjon olyan teljesítménymérőt, amely jobban kezeli a kiegyensúlyozatlan adatokat. Például az F1 pontszám a pontosság és a visszahívás súlyozott átlaga. A pontosság méri az osztályozó pontosságát- az alacsony pontosság nagy számú hamis pozitív atlétát jelez--, míg a visszahívás az osztályozó teljességét méri- az alacsony visszahívás nagy számú hamis negatívot jelez. 

## <a name="next-steps"></a>További lépések

Tekintse meg a példákat, és ismerje meg, hogyan hozhat létre modelleket automatizált gépi tanulással:

+ Kövesse az [oktatóanyagot: Regressziós modell automatikus betanítása az Azure Machine Learning segítségével](tutorial-auto-train-models.md)

+ Az automatikus betanítási kísérlet beállításainak konfigurálása:
  + Az Azure Machine Learning stúdióban [kövesse ezeket a lépéseket.](how-to-use-automated-ml-for-ml-models.md)
  + A Python SDK-val [kövesse ezeket a lépéseket.](how-to-configure-auto-train.md)



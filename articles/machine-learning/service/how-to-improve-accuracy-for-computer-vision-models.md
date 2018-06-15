---
title: A számítógép stratégiai & besorolási modell Azure Machine Learning jobb pontossága
description: Útmutató a számítógép átfogóan bemutató kép besorolás, objektum észlelését és az Azure Machine Learning csomagot használ a számítógép átfogóan bemutató kép hasonlóság modellek pontosságának javítása érdekében.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: e134e1e544c51d6756d5021fef8c049fe7d8afb0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788773"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Számítógép stratégiai modellek pontosságának javítása

Az a **Azure Machine Learning csomag számítógép stratégiai**, létrehozhatja és lemezkép besorolást, telepítése objektum észlelési és image hasonlóság modellek. További információ a csomag és telepítésének módját.

Ebből a cikkből megismerheti, hogyan annak finomhangolásához, ezek a modellek, a pontosság növeléséhez. 

## <a name="accuracy-of-image-classification-models"></a>Kép besorolási modell pontosságát

A számítógép stratégiai csomag helyes eredményt adnak az adatkészletek számos különböző megjelenik. Azonban, a legtöbb machine learning projektek, a legjobb lehetséges első igaz eredményeket egy új adatkészlet csak gondos paraméter hangolása, valamint értékeli a különböző tervezési döntésekhez. A következő szakaszok útmutatást nyújtanak egy adott adatkészlet pontosságának javítására, ez azt jelenti, hogy milyen paramétereket legtöbb ígéret milyen értékeket a paraméterek egy érdemes kipróbálni, és milyen nehézségek elkerülése érdekében először optimalizálása érdekében.

Általánosságban véve képzési mély tanulási modellek között képzési időt és a modell pontosságát tartalmaz. A számítógép stratégiai csomag rendelkezik előre beállított alapértelmezett paraméterek (lásd az alábbi táblázat első sorában), amelyek során általában pontosságot modellek gyors képzési sebesség összpontosítanak. A pontosság gyakran növelhető a használatával, például magasabb képfelbontás vagy mélyebb modellek azonban ugyan képzési idő legalább 10 x tényezővel növelését.

Javasoljuk, hogy először dolgozni az alapértelmezett paraméterek, a modell betanításához, vizsgálja meg az eredményeket, javítsa ki az alapoktól igazság jegyzeteket, igény szerint, és csak ezután próbálja paraméterek, a művelet lelassíthat képzési idő (lásd a javasolt paraméterértékeket az alábbi táblázatban). Ezek a paraméterek, amíg ez technikai értelemben nem szükséges ismeretét azonban ajánlott.


### <a name="best-practices-and-tips"></a>Ajánlott eljárásokat és tippek

* Az adatminőségi: a tanítási és tesztelési készletek kiváló minőségű legyen. Ez azt jelenti, hogy a képek vannak feliratozva helytelenül, nem egyértelmű lemezképek eltávolítása (például hol egyértelmű emberi szem számára, hogy ha a kép bemutatja a tenisz golyó vagy egy kisfejű), és az attribútumok kölcsönösen kizárják egymást (Ez azt jelenti, hogy egyes lemezképek tartozik pontosan egy attribútuma van).

* A DNN szűkebbé tenni, mielőtt egy SVM osztályozó kell kell képezni egy előre betanított és a rögzített DNN featurizer használatával. Ez a számítógép stratégiai csomag támogatott, és nem igényel hosszú betanítása, mivel a DNN maga nem módosult. Akkor is ezt a egyszerű módszert gyakran éri el a megfelelő pontosság, és ezért jelenti az erős alapkonfigurációt. A következő lépéssel majd pontosítsa a DNN, amely nagyobb pontosságot adjon.

* Ha az objektum--fontos a kép kicsi, kép besorolás megközelítések ismertek nem működik jól. Ilyen esetben fontolja meg az objektum észlelési módszer használatát, mint például a számítógép stratégiai csomag gyorsabban R-CNN Tensorflow alapján.

* A további betanítási adatok minél pontosabban. A szabály-az-görgetőgomb, mint egy rendelkeznie kell legalább 100 példák minden osztályához, ez azt jelenti, hogy 100 képek "kutya", 100 kép "cat", stb. Kevesebb képek modell betanítása lehetséges, de előfordulhat, hogy megfelelő eredményeket sem ad.

* A képzési lemezképek találhatók a GPU-t a gépen, és egy SSD meghajtó nem egy kell. Ellenkező esetben a kép olvasási késése jelentősen csökkentheti képzési sebesség (tényezővel akár 100 x).


### <a name="parameters-to-optimize"></a>Paraméterek optimalizálása

Optimális értékek keresése a következő paraméterek fontos és javítható pontosságának jelentős mértékben:
* Tanulási gyakorisága (`lr_per_mb`): A legfontosabb késései paraméter használatával beolvassa a megfelelő. Ha a képzési pontosságának beállítása után DNN pontosítás ~ 5 % feletti akkor nagy valószínűséggel a tanulási rátát esik, vagy túl nagy vagy túl alacsony képzési alapidőszakkal száma. Különösen kis adatkészletekkel a DNN általában túlzott elférjen a betanítási adatok, azonban a gyakorlatban ez jó modellek a teszteléshez használt vezet be. Általában használjuk 15 alapidőszakkal ahol a kezdeti tanulási van csökkentett kétszer; További alapidőszakkal segítségével betanítási néhány esetben javíthatja a teljesítményt.

* Megoldási bemeneti (`image_dims`): az alapértelmezett lemezkép megoldás lehet 224 x 224 képpontban megadva. Magasabb kép feloldását, például 500 x 500 használatával képpont vagy 1000 x 1000 képpont jelentősen fejleszti a pontosság, de DNN pontosítás lelassul. A számítógép stratégiai csomag vár a bemeneti felbontás (-csatornák, képszélességet, kép-magassága) rekordot kell (3, 224, 224), például ha szín csatornák számát rendelkezik 3 (vörös-zöld-kék sávok) értékűre kell beállítani.

* A modell architektúra (`base_model_name`): például ResNet-34 vagy ResNet-50 mélyebb DNNs helyett az alapértelmezett ResNet-18-modell próbálja. A Resnet-50 modell nem csak mélyebben, de az utolsó előtti réteg kimenetét mérete 2048 úszó (és a a ResNet-18-ra és ResNet-34 modellek 512 úszó). Ez megnövekedett granularitása különösen hasznos lehet, ha a rögzített, és ehelyett betanítása egy SVM osztályozó DNN tartása.

* Minibatch mérete (`mb_size`): magas minibatch méretek irányítja gyorsabb képzési azonban csökkenti a megnövekedett DNN memória-felhasználás. Emiatt mélyebb modellek (például ResNet-50 és ResNet 18.) és/vagy magasabb képfelbontás kiválasztásakor (500\*224 és 500 képpont\*224 képpontban megadva), egy általában kevés a memória hibák elkerülése érdekében minibatch méretének csökkentése érdekében van. Amikor a minibatch méretének módosítása gyakran is a tanulási rátát kell igazítani, mivel az alábbi táblázatban láthatók.
* Közvetlen kibővített gyakorisága (`dropout_rate`) és 2. szintű-regularizer (`l2_reg_weight`): DNN túlzott méretezés jelkiesés árfolyam legalább 0.5 (alapértelmezett érték 0,5 számítógép stratégiai csomagban) használatával, és a regularizer súly növelésével csökkenthető (alapértelmezett érték a számítógép stratégiai 0.0005 A csomag). Vegye figyelembe, hogy különösen adatkészletekkel kis DNN túlzott méretezés rendkívül nehéz és elkerülése érdekében gyakran nem lehetséges.


### <a name="parameter-definitions"></a>Paraméterdefiníciókra

- **Tanulási arány**: lépésben használt színátmenet módszeren tanulás közben. Ha a készlet túl alacsony, majd a modell fog igénybe venni betanításához sok alapidőszakkal, ha a modell be van állítva túl nagy, nem lesz összevonva a jó megoldás. Ütemezés jellemzően akkor alkalmazzák, ahol a tanulási rátát alapidőszakkal bizonyos számú után csökken. E.For példa a tanulási értékelje ütemezés `[0.05]*7 + [0.005]*7 + [0.0005]` megfelel-e az első hét alapidőszakkal egy kezdeti tanulási rátát 0,05 használja, a másik hét alapidőszakkal egy 10 x 0,005 csökkentett tanulásra aránya követ, és végül finombeállításához a a modell egyetlen epoch a 100 x 0.0005 tanulási gyakorisága csökken.

- **Minibatch mérete**: Feldolgozóegységekkel számítási felgyorsítása érdekében párhuzamosan több lemezképet tud feldolgozni. A párhuzamos feldolgozott lemezképek vannak egy minibatch néven. Minél nagyobb a minibatch a gyorsabb képzési lesz, azonban csökkenti a megnövekedett DNN memória-felhasználás.

### <a name="recommended-parameter-values"></a>Ajánlott paraméterértékek

Az alábbi táblázat a kép fájlosztályozási feladatokhoz számos pontosságot modellek létrehozásához megjelenített különböző paraméterkészletei. Az optimális paraméterek az adott DataSet adatkészlet és a pontos gpu-t használja a függ, ezért a táblában kell értékelni az eszközöket csak. Után próbálja meg ezeket a paramétereket, fontolja meg is kép megoldások a több mint 500 x 500 képpont, vagy például Resnet-101 vagy Resnet-152 mélyebb modellek.

A tábla első sorának belül számítógép stratégiai beállított alapértelmezett paraméterek felel meg. Az összes többi sort kell még betanítani (az első oszlopban megjelenített) azonban hosszabb ideig, az az előnye, hogy a megnövekedett pontossága (lásd: a második oszlopában átlagos pontosságának három belső adatkészletek keresztül). Például a paraméterek az utolsó sor lépnek 5-15 x hosszabb betanítása, azonban három belső tesztkészleteket 82.6 % 92.8 % pontosságának megnövekedett (átlagolt) eredményezett.

Mélyebb modellek és magasabb bemeneti feloldási memóriáját DNN eltarthat, és ezért minibatch mérete nagyobb modell összetettsége ki a memória hibák elkerülése érdekében a csökkenteni kell. Amint az alábbi táblázatban is látható, célszerű a tanulási rátát csökkentheti a két tényező, amikor a minibatch csökkenő méretezés a azonos többszöröző által. Minibatch méretének beolvasása csökkenteni kell tovább Feldolgozóegységekkel kisebb mennyiségű memóriával.

| Képzési idő (durva becslést) | Példa pontossága | Minibatch mérete (*mb_size*) | Tanulási gyakorisága (*lr_per_mb*) | Kép felbontás (*image_dims*) | DNN architektúra (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (referencia) | 82.6 % | 32 | [0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 90.2 % | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500, 500) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 87,5 % | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5-15 x        | 92.8 % |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>További lépések

A számítógép stratégiai az Azure Machine Learning csomag kapcsolatos információk:
+ Tekintse meg a referencia-dokumentáció

+ További tudnivalók [egyéb Python-csomagokat, az Azure gépi tanulás](reference-python-package-overview.md)
---
title: Számítógépes látástechnológiai és osztályozás modelljeit az Azure Machine Learning jobb pontossága
description: Útmutató a számítógép vision képbesorolás, objektumdetektálás és lemezkép hasonlóság modellek az Azure Machine Learning csomagot használ a Computer Vision pontosságának javítása.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: netahw
author: nhaiby
ms.date: 04/23/2018
ms.openlocfilehash: 6d6c540cd8711ae89784cdc797ca56d312522a83
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46989311"
---
# <a name="improve-the-accuracy-of-computer-vision-models"></a>Számítógépes látástechnológiai modelljeit pontosságának javítása

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Az a **Azure Machine Learning-csomagja Computer Vision**, alkalmazásfejlesztést és üzembe helyezése, képbesorolás objektum észlelése és a képfájl hasonlósági modellek. További információ a csomag és a telepítésének módjáról.

Ebből a cikkből megismerheti, hogyan finomhangolásához ezek a modellek a pontosság növeléséhez. 

## <a name="accuracy-of-image-classification-models"></a>Képbesorolási modellek pontossága

A számítógép Vision csomag jó eredménnyel adatkészletek számos megjelenik. Azonban, igaz a legtöbb machine learning-projektek, a legjobb lehetséges első egy új adatkészlet eredményei csak gondos finomhangolással, valamint a különböző tervezési döntésekhez kiértékelése. A következő szakaszok útmutatást nyújtanak egy adott adatkészlet pontosságát fejlesztéséhez, azt jelenti, paramétereket legígéretesebb, milyen értékeket a paraméterekhez egy kell próbálja ki, és milyen alkalmazásmegoldásokra elkerülése érdekében először optimalizálása érdekében.

Általánosan fogalmazva képzési Deep Learning-modellek között pontosság és képzési időt tartalmaz. A számítógép Vision csomag rendelkezik előre beállított alapértelmezett paraméterek (lásd az alábbi táblázat első sora), amelyek általában idempotentnek nagy pontosságú modellek gyors képzést sebesség összpontosíthat. A pontosság gyakran javítása érdekében további használja, például magasabb képfelbontás vagy részletesebb modellek azonban képzési idő növelésével legalább 10 x-szorosára cserébe.

Javasoljuk, hogy, először az alapértelmezett paraméterek használata, a modell betanítását, vizsgálja meg az eredményeket, javítsa ki az alapoktól hiteles jegyzetek, igény szerint és csak ezután próbálja meg képzési idő lelassíthatják paraméterek (lásd a javasolt paraméter értékét az alábbi táblázatban). Azonban ajánlott ezeket a paramétereket, bár ez technikai értelemben nem szükséges megismerése.


### <a name="best-practices-and-tips"></a>Ajánlott eljárások és tippek

* Adatminőség: a tanítási és tesztelési eljárások kiváló minőségű kell lennie. Ez azt jelenti, hogy a képek vannak feliratozva megfelelően, nem egyértelmű képek eltávolítása (például ha nem egyértelmű, emberi szem számára, ha a kép mutatja a tenisz golyó vagy egy kisfejű), és az attribútumokat, egymást kölcsönösen kizáró (vagyis minden egyes képe tartozik pontosan egy attribútuma).

* A DNN pontosítását, mielőtt egy SVM osztályozó tájékoztatni kell egy előre betanított és a rögzített DNN featurizer használatával. Ez támogatott a számítógép Vision csomag, és nem igényel hosszú betanításához, mivel a DNN maga nem módosul. Még akkor is ezt a egyszerű módszert gyakran éri el a helyes pontosságú, és ezért jelenti az erős alapkonfigurációt. A következő lépésben majd, hogy finomíthatja a DNN, pontosabb eredményeket adjon.

* Ha az objektum-– fontos kicsi a képen, kép besorolási megközelítések ismertek nem megfelelően működjön. Ezekben az esetekben érdemes lehet egy objektum észlelési módszer például a számítógép Vision csomag gyorsabban R-CNN alapján tensorflow-hoz.

* A további betanítási adatok annál jobbak. Egy szabály-az-bütykének, mint egy rendelkeznie kell legalább 100 példák az osztályra, azaz 100 rendszerképek "kutya", 100 kép "cat" stb. A modell kevesebb képekkel lehetséges, de előfordulhat, hogy megfelelő eredményeket sem ad.

* A betanító kép kell helyileg a gépen a GPU-tartalmazhat, és a egy SSD meghajtón (nem egy HDD-) lehet. Ellenkező esetben a kép olvasási késés jelentősen csökkentheti a betanítási sebessége (-szorosára akár 100-szor).


### <a name="parameters-to-optimize"></a>Paraméterek optimalizálása

Optimális értékek keresése a paraméterek fontos, és javítható pontosságának jelentős mértékben:
* Tanulási ráta (`lr_per_mb`): A legfontosabb késései paraméter kellhet. Ha a pontosságot, a képzési be után DNN pontosítás fent ~ 5 % -os, akkor nagy valószínűséggel megfelelő tanulási rátát értéke túl magas, vagy a betanítási alapidőszakkal túl alacsony száma. Különösen kis méretű adatkészletekkel a DNN általában túlterhelt fér el a betanítási adatok, azonban a gyakorlatban ez a teszt azt eredményezi, jó modellek beállítása. Általában használjuk 15 alapidőszakkal ahol a kezdeti tanulási ráta csökkenti, kétszer; képzést nyújt további alapidőszakkal néhány esetben javíthatja a teljesítményt.

* Feloldási bemeneti (`image_dims`): az alapértelmezett lemezkép 224 x 224 képpont monitorfelbontást. Használata magasabb képfelbontás, például 500 x 500 képpont vagy 1000 x 1000 képpont jelentősen javíthatja a pontosság, de DNN pontosítás lelassul. A számítógép Vision csomag vár a bementi felbontás (szín-csatornák, képszélességet, kép-magasság), egy rekord kell (3, 224, 224), például ahol csatornák száma 3 (vörös-zöld-kék sávok) értéke van.

* Modell architektúra (`base_model_name`): Próbáljon mélyebb dnn-eket például ResNet-34 vagy a ResNet-50 helyett az alapértelmezett ResNet-18-modellt. A Resnet-50-modell nem csak mélyebb, de az utolsó előtti réteg annak kimenete az mérete 2048 úszó (vs. a ResNet-18-ra és a ResNet-34 modellekhez 512 úszó). Ez megnövekedett dimenziói különösen hasznos lehet, ha rögzített, és helyette az egy SVM osztályozó által igénybe vett képzés a DNN tartja.

* Minibatch mérete (`mb_size`): magas minibatch méretek azonban gyorsabb képzési vezet egy DNN nagyobb memóriát rovására. Ezért mélyebb modellek (például a ResNet-50 és a ResNet-18-ra) és/vagy magasabb képfelbontás kiválasztásakor (500\*224 és 500 képpont\*224 képpont), általában adatáthelyezések memóriahiány hibák elkerülése érdekében minibatch méretének csökkentése érdekében. A minibatch méret módosításakor gyakran is megfelelő tanulási rátát kell módosítani az alábbi táblázatban látható módon.
* Kimenő eldobási arány (`dropout_rate`) és L2-regularizer (`l2_reg_weight`): túlzott méretezés DNN csökkenteni lehet egy jelkiesés sebessége legalább 0.5-ös (alapértelmezés szerint a számítógép Vision csomag 0,5) használatával, és növelje a regularizer súly (alapértelmezés szerint a 0.0005 a Computer Vision A csomag). Vegye figyelembe ugyanakkor, hogy különösen kis adatkészletek DNN-túlzott méretezés rendkívül nehéz és gyakran lehetetlen elkerülése érdekében.


### <a name="parameter-definitions"></a>A paraméter-definíciók

- **Tanulási ráta**:. lépés: tanulási gradiens módszeres során használt méret. Ha a modell be van állítva túl alacsony, a készlet túl magas, majd a modell nem lesz összevonva jó megoldás, ha vesz a számos alapidőszakkal betanításához. Általában ütemezés szerint használatos, ahol megfelelő tanulási rátát alapidőszakkal bizonyos számú után csökken. E.For példa a tanulási ráta ütemezés `[0.05]*7 + [0.005]*7 + [0.0005]` felel meg az első hét alapidőszakkal egy kezdeti tanulási ráta 0,05 használatával, a hét egy másik alapidőszakkal egy 10 x 0,005 csökkentett learning aránya követ és végül finomhangoló a modell az egyetlen alapidőpont egy 100-szor kisebb 0.0005 learning aránya.

- **Minibatch mérete**: gpu-kkal számítási felgyorsítása érdekében párhuzamosan több lemezképet tud feldolgozni. A párhuzamos feldolgozás lemezképek vannak egy minibatch néven. Minél nagyobb a minibatch mérete a gyorsabb képzési lesz, azonban egy DNN nagyobb memóriát rovására.

### <a name="recommended-parameter-values"></a>Ajánlott paraméterértékek

Az alábbi táblázat a különböző paraméterkészlettel jelentek meg a lemezkép fájlosztályozási feladatokhoz számos nagy pontosságú modellek létrehozásához biztosít. Az optimális paraméterek az adott adatkészlet és a pontos gpu-alapú használt függ, ezért a tábla csak irányelvként kell értékelni. Után próbálja meg ezeket a paramétereket, fontolja meg is kép megoldások a több mint 500 x 500 képpont, vagy például Resnet-101-es vagy a Resnet-152 mélyebb modellek.

A tábla első sorát felel meg az alapértelmezett paramétereket, hogy a számítógép Vision belül vannak beállítva. Az összes többi sort (az első oszlopban jelzett) betanításához azonban tovább tart, nagyobb pontosság előnyeit (lásd a második oszlop az átlagos pontosak-e a három belső adatkészleteken). Például a paraméterek utolsó sorára igénybe 5 – 15 x hosszabb betanításához 92.8 % 82.6 % három belső teszt beállítása (átlagolt) nagyobb pontosságát azonban eredményezett.

Részletesebb modellek és magasabb bementi felbontás is igénybe vehet a DNN több memóriát, és ezért a minibatch tárolóméretének az out-memória hibák elkerülése érdekében nagyobb modell összetettsége csökkenthető. Ahogy az alábbi táblázatban látható, akkor előnyös, ha csökkenti a learning két tényezővel, amikor az ugyanazon szorzó által a minibatch csökkenő méretezés. Minibatch méretének lekérése csökkenteni lehet szükség további gpu-kkal rendelkező kisebb mennyiségű memória található.

| Képzési idő (hozzávetőleges becslés) | Példa pontossága | Minibatch mérete (*mb_size*) | Tanulási ráta (*lr_per_mb*) | Feloldási kép (*image_dims*) | DNN-architektúra (*base_model_name*) |
|------------- |:-------------:|:-------------:|:-----:|:-----:|:---:|
| 1 x (hivatkozás) | 82.6 % | 32 | [0,05] \*7 + [0,005]\*7 + [0.0005]  | (3, 224, 224) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 90.2 % | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 500-as, 500) | ResNet18_ImageNet_CNTK |
| 2 – 5 x    | 87,5 % | 16 | [0,025] \*7 + [0.0025]\*7 + [0.00025] | (3, 224, 224) | ResNet50_ImageNet_CNTK |
| 5 – 15 x        | 92.8 % |  8 | [0,01] \*7 + [0,001]\*7 + [0,0001]  | (3, 500-as, 500) | ResNet50_ImageNet_CNTK |


## <a name="next-steps"></a>További lépések

Az Azure Machine Learning csomag információ a számítógépes Látástechnológia:
+ Tekintse meg a dokumentációja

+ Ismerje meg [más az Azure Machine Learning Python-csomagok](reference-python-package-overview.md)
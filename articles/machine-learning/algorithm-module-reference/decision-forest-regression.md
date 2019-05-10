---
title: 'Döntési erdő regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a döntési erdő regressziós modul használata az Azure Machine Learning szolgáltatás egy döntési fák algoritmus egy közelmúltra alapuló regressziós modell létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: d372adf75d46fdedb7a6f2b17e47822475d1f155
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442363"
---
# <a name="decision-forest-regression-module"></a>Döntési erdő regressziós modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával a döntési fák algoritmus egy közelmúltra alapuló regressziós modell létrehozásához.

Miután konfigurálta a modellt, akkor kell betanítja a modellt, címkézett adatkészletet használ, és a [Train Model](./train-model.md) modul.  A betanított modell használható, hogy előrejelzéseket végezzen. 

## <a name="how-it-works"></a>A szolgáltatás működése

Döntési fák algoritmus nem számszerű modellek egyszerű tesztek egy sorozatát végre minden egyes előfordulás esetén áthaladó bináris fa adatstruktúra, amíg el nem levélcsomópont (döntés).

Döntési fák rendelkezik, ezeket az előnyöket:

- Azok a számítási és a memóriahasználat hatékony betanítási és Predikciós során.

- Nem lineáris döntési határok jelölnek.

- Ezek hajtsa végre az integrált szolgáltatás kiválasztása és besorolása és ellenálló zajos szolgáltatások folytonosságát.

Itt látható regressziós modell döntési fák részének áll. Egy regressziós döntési erdő fákban Gauss terjesztési jelenít meg, az előrejelzési. Összesítést fák Gauss terjesztési összes fák kombinált eloszlásának legközelebb található a modellben a közelmúltra-n keresztül történik.

Ez az algoritmus és a megvalósítás elméleti keretében kapcsolatos további információkért lásd: Ez a cikk: [Döntési erdő: A besorolás, regressziós, sűrűségű-becslésére, szívócsővezeték tanulási és félig felügyelt tanítás egyesített keretrendszer](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Döntési erdő regressziós modell konfigurálása

1. Adja hozzá a **döntési erdő regressziós** modult a kísérletvászonra. A modul az a kapcsolat alatt található **Machine Learning**, **modell inicializálása**, és **regressziós**.

2. Nyissa meg a modul tulajdonságait, és a **metódus az újraszámítás**, az egyes fák létrehozásához használt módszer kiválasztására szolgál.  Választhat **zsákoló** vagy **replikálása**.

    - **Zsákoló**: Zsákolási néven is ismert *bootstrap összesítésével*. Egy regressziós döntési erdő fákban Gauss disztribúciót előrejelzési de jelenít meg. Az összesítés a egy Gauss, amelynek első két percet egyezik a megadott egyéni fák által visszaadott összes Gaussians kombinálásával Gaussians keverékét pillanat található.

         További információkért tekintse meg a Wikipédia [Bootstrap összesítésével](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replikálása**: Replikáció esetén minden egyes fa be van tanítva, pontosan ugyanazt a bemeneti adatok. A meghatározása, mely split predikátum használt egyes facsomópont véletlenszerű marad, és a fák eltérő lesz.

         További információ a betanítási folyamat, a **replikálása** talál [számítógépes Látástechnológiai és orvosi képelemzés döntési erdő. Criminisi és J. Shotton. Springer 2013. ](https://research.microsoft.com/projects/decisionforests/).

3. Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.

    - **Egyetlen paraméter**

      Ha tudja, hogyan szeretné konfigurálni a modellt, megadhat egy adott értékhalmazt argumentumként. Előfordulhat, hogy megismerte ezeket az értékeket a Kísérletezési vagy útmutatásként kapott őket.



4. A **döntési fák száma**, hozhat létre a ensemble döntési fák teljes számát. Több döntési fák létrehozásával lefedettségét esetlegesen kap, de megnöveli a képzési időt.

    > [!TIP]
    > Ez az érték vezérli jelenik meg, amikor megjelenítik a betanított modell fák számát is. Ha azt szeretné, vagy egy egyetlen fa nyomtatása, az érték 1; megadható azonban ez azt jelenti, hogy csak egy fa lesz (a kezdeti paraméterkészlet fa) előállított és hajt végre semmilyen további ismétlését.

5. A **a döntési fák maximális mélysége**, írja be a maximális bármely döntési fa maximális mélységét. A fa mélysége növelése növelheti a pontosság kockázatára overfitting és megnövelt képzési időt.

6. A **csomópontonként véletlen megszakítások száma**, írja be a minden csomópont a fa készítése során használandó megszakítások számát. A *felosztása* azt jelenti, hogy minden egyes szintjét a fához (node) funkcióit véletlenszerűen vannak osztva.

7. A **minták levél csomópontonként minimális száma**, jelezze a minimális száma, amelyekre szükség van egy fa bármely terminál csomópontot (levél) hozhat létre.

     Ez az érték növelésével növeli az új szabályok létrehozása a küszöbértéket. Az alapértelmezett értéke 1, például egyetlen esetet okozhat egy új szabályt létrehozni. Az érték 5-re növeli, ha a betanítási adatok kellene az azonos feltételeket teljesítő legalább öt esetek tartalmaznak.


9. Címkézett adatkészlet csatlakoztatása, válasszon ki egy egycímkés oszlopot tartalmazó legfeljebb két eredményeket, és csatlakozzon [tanítási modell](./train-model.md).

    - Ha **létrehozási trainer módban** beállítást **egyetlen paramétert**, a modell betanításához használja a [Train Model](./train-model.md) modul.

   

10. Futtassa a kísérletet.

### <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A létrehozott minden egyes ismétléskor a fában, kattintson a jobb gombbal a képzési modul kimenete, és válassza **Visualize**.

+ A szabályokat az egyes csomópontok fákban kattintson, és az elágazást feltárásához.

+ Szeretné menteni a betanított modell pillanatképét, kattintson a jobb gombbal a képzési modul kimenetét, és válassza **betanított modell mentése másként**. A kísérlet futtatásának egymást követő nem frissül a minta ezen példányával. 

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
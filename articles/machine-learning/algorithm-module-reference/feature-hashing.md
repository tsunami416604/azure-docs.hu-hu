---
title: 'Szolgáltatások kivonatolása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban a funkció-kivonatolási modult szöveges adatokat szabadkézi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 78d01cf071faed312773ebf12c75e7e6e5596e71
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210844"
---
# <a name="feature-hashing"></a>Szolgáltatások kivonatolása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

A **szolgáltatás** -kivonatolási modul használatával az angol nyelvű adatfolyamokat egész számokból álló funkciókba alakíthatja át. Ezt követően átadhatja ezt a kivonatoló funkciót egy gépi tanulási algoritmusba egy szöveges elemzési modell betanításához.

Az ebben a modulban megadott funkció-kivonatolási funkció a nimbusml-keretrendszeren alapul. További információ: [NgramHash osztály](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>A funkciók kivonatolása

A szolgáltatások kivonatolása úgy működik, hogy az egyedi tokeneket egész számokra konvertálja. A művelet a bemenetként megadott pontos karakterláncokon működik, és nem végez nyelvi elemzést vagy előfeldolgozást. 

Tegyük fel például, hogy a fentiekhez hasonló egyszerű mondatokat, majd egy hangulati pontszámot követ. Tegyük fel, hogy ezt a szöveget szeretné használni a modell létrehozásához.

|USERTEXT|HANGULATELEMZÉS|
|--------------|---------------|
|Imádtam ezt a könyvet|3|
|Utáltam ezt a könyvet|1|
|Ez a könyv nagyszerű volt|3|
|Szeretem a könyveket|2|

Belsőleg a funkció- **kivonatolási** modul egy n-grammos szótárt hoz létre. Az adatkészlet bigrams listája például a következőhöz hasonló lesz:

|KIFEJEZÉS (bigrams)|FREKVENCIA|
|------------|---------------|
|Ez a könyv|3|
|Szerettem|1|
|Utáltam|1|
|Szeretem|1|

Az n-gramm méretének szabályozására az **n-gramm** tulajdonság használható. Ha a bigrams lehetőséget választja, a rendszer a unigrams is kiszámítja. A szótár a következőhöz hasonló egyetlen kifejezést is tartalmaz:

|Kifejezés (unigrams)|FREKVENCIA|
|------------|---------------|
|könyv|3|
|I|3|
|könyvek|1|
|a következő volt:|1|

A szótár kiépítését követően a **funkció** -kivonatolási modul a szótári kifejezést kivonatoló értékre konvertálja. Ezután kiszámítja, hogy az egyes esetekben használták-e a szolgáltatást. A modul minden egyes sornyi szöveges adatokat tartalmaz, és az egyes kivonatoló funkciók egy oszlopát adja vissza.

Például a kivonatolás után a funkció oszlopai a következőhöz hasonló módon jelenhetnek meg:

|Minősítés|Kivonatolási funkció 1|Kivonatolási funkció 2|3\. kivonatolási funkció|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Ha az oszlopban szereplő érték 0, akkor a sor nem tartalmazta a kivonatoló funkciót.
* Ha az érték 1, akkor a sor tartalmazta a funkciót.

A szolgáltatás-kivonatolás lehetővé teszi, hogy a dimenzióját-csökkentés eléréséhez változó hosszúságú szöveges dokumentumokat jelenítse meg a numerikus szolgáltatásokban. Ha a Text (szöveg) oszlopot a betanításhoz próbálta használni, a rendszer egy kategorikus funkció oszlopként kezeli, amely számos különböző értéket tartalmaz.

A numerikus kimenetek közös gépi tanulási módszereket is használhatnak, beleértve a besorolást, a fürtözést és az információk lekérését. Mivel a keresési műveletek a karakterlánc-összehasonlítás helyett egész kivonatokat használhatnak, a funkciók súlyozásának beszerzése is sokkal gyorsabb.

## <a name="configure-feature-hashing"></a>Szolgáltatások kivonatolásának konfigurálása

1.  Adja hozzá a **szolgáltatás kivonatolási** modulját a kísérlethez a Visual Interface-ben.

1. Az elemezni kívánt szöveget tartalmazó adatkészlet csatlakoztatása.

    > [!TIP]
    > Mivel a funkció-kivonatolás nem hajt végre olyan lexikális műveleteket, mint például a kivonás vagy a csonkítás, a funkciók kivonatolása előtt időnként jobb eredményeket érhet el a szöveg előfeldolgozásával. 

1. Állítsa be a **cél oszlopokat** a kivonatoló funkciókra átalakítani kívánt szöveges oszlopokra. 

    * Az oszlopoknak karakterlánc adattípusúnak kell lenniük.
    
    * Több szöveges oszlop kiválasztásával jelentős hatással lehet a funkciók dimenzióját. Egy 10 bites kivonat oszlopainak száma például 1024, és egyetlen oszlopból 2048 két oszlopra.

1. **Kivonatoló bitsize** használatával adhatja meg, hogy hány bitet kell használni a kivonatoló tábla létrehozásakor.
    
    Az alapértelmezett bit mérete 10. Számos probléma esetén ez az érték megfelelő. Előfordulhat, hogy több helyre van szüksége az ütközések elkerüléséhez az n-grammos tanítási szöveg méretétől függően.
    
1. Az **n-gramm**értéknél adjon meg egy számot, amely meghatározza a képzési szótárhoz hozzáadandó N-g maximális hosszát. Az n-Gram az *n* betűs sorozat, amely egyedi egységként van kezelve.

    Ha például a 3, a unigrams, a bigrams és a Trigrams értéket adja meg, a rendszer létrehozza a következőt:.

1. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után a modul egy átalakított adatkészletet ad ki, amelyben az eredeti szöveges oszlop több oszlopra lett konvertálva, amelyek mindegyike egy funkciót jelképez a szövegben. Attól függően, hogy milyen jelentős a szótár, az eredményül kapott adatkészlet nagyméretű lehet:

|Oszlop neve 1|2-es típusú oszlop|
|-------------------|-------------------|
|USERTEXT|Eredeti adatoszlop|
|HANGULATELEMZÉS|Eredeti adatoszlop|
|USERTEXT-kivonatolási funkció 1|Kivonatoló funkció oszlopa|
|USERTEXT – kivonatoló funkció 2|Kivonatoló funkció oszlopa|
|USERTEXT-kivonatolási funkció n|Kivonatoló funkció oszlopa|
|USERTEXT-kivonatolási funkció 1024|Kivonatoló funkció oszlopa|

Az átalakított adatkészlet létrehozása után használhatja a betanítási modell modul bemenetként.
 
### <a name="best-practices"></a>Ajánlott eljárások

Az alábbi ajánlott eljárások segítséget nyújtanak a **funkció** -kivonatolási modul leghatékonyabban történő kihasználása érdekében:

* A bemeneti szöveg előfeldolgozásához vegyen fel egy **előfeldolgozó szöveges** modult, mielőtt a **szolgáltatás** -kivonatolást használja. 

* A **funkció** -kivonatolási modul után vegyen fel egy **Select Columns** modult, hogy eltávolítsa a szöveges oszlopokat a kimeneti adatkészletből. A kivonatolási funkciók létrehozása után nincs szükség a szöveges oszlopokra.
    
* Ezeket a szöveg-előfeldolgozási beállításokat érdemes használni az eredmények egyszerűsítése és a pontosság javítása érdekében:

    * Szóhatárolás
    * A Word eltávolításának leállítása
    * Kis-és nagybetűk normalizálása
    * Írásjelek és speciális karakterek eltávolítása
    * Eredő  

Az egyes megoldásokban alkalmazandó előfeldolgozási módszerek optimális készlete a tartománytól, a szókincstől és az üzleti igényektől függ. Kísérletezzen az adataival, és nézze meg, mely szöveg-feldolgozási módszerek a leghatékonyabbak.

## <a name="next-steps"></a>További lépések
            
Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

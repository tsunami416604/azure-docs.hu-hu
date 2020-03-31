---
title: Szolgáltatáskivonatoló modul hivatkozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a szolgáltatáskivonat-kivonatoló modult az Azure Machine Learningben szöveges adatok felvázolása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456250"
---
# <a name="feature-hashing-module-reference"></a>Szolgáltatáskivonatoló modul hivatkozása

Ez a cikk az Azure Machine Learning designerben található modult ismerteti (előzetes verzió).

A Feature Hashing modul segítségével az angol szövegfolyamot egész számjellemzőkhalmazává alakíthatja. Ezután adja át ezt a kivonatolt szolgáltatáskészletet egy gépi tanulási algoritmusnak egy szövegelemzési modell betanításához.

A modulban biztosított szolgáltatáskivonatolási funkció a nimbusml keretrendszeren alapul. További információ: [NgramHash class](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>Mi az a funkciókivonatolás?

A funkciókivonatolás az egyedi tokenek egész számokká alakításával működik. A pontos karakterláncok, amelyek bemenetként megadott, és nem végez semmilyen nyelvi elemzés vagy előfeldolgozás. 

Például, hogy egy sor egyszerű mondatokat, mint ezek, majd egy hangulat pontszám. Tegyük fel, hogy ezt a szöveget szeretné használni egy modell létrehozásához.

|Felhasználói szöveg|Hangulat|
|--------------|---------------|
|Imádtam ezt a könyvet|3|
|Utáltam ezt a könyvet.|1|
|Ez a könyv nagyszerű volt.|3|
|Szeretem a könyveket|2|

Belsőleg a Feature Hashing modul létrehoz egy n-gramm szótárt. Például az adatkészlet bigram-ok listája a következőhez hasonló lehet:

|Kifejezés (bigram)|Frequency|
|------------|---------------|
|Ez a könyv|3|
|Szerettem|1|
|Gyűlöltem.|1|
|Szeretem|1|

Az n-grammok méretét az **N-gramm** tulajdonság használatával szabályozhatja. Ha a bigrams-et választja, az unigramok is kiszámításra kerülnek. A szótár is tartalmazna egyetlen kifejezések, mint például:

|Kifejezés (unigramm)|Frequency|
|------------|---------------|
|Könyv|3|
|I|3|
|könyvek|1|
|Volt|1|

A szótár elkészülte után a Szolgáltatáskivonat-kivonatmodul a szótárkifejezéseket kivonatértékekké alakítja. Ezután kiszámítja, hogy minden esetben használtak-e egy funkciót. A modul minden egyes szövegadatsorhoz egy oszlopkészletet ad ki, minden kivonatolt jellemzőhez egy oszlopot.

Kivonatolás után például a jellemzőoszlopok a következővel jelenhetnek meg:

|Minősítés|Kivonatolási funkció 1|Kivonatolási funkció 2|Kivonatolási funkció 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Ha az oszlop értéke 0, a sor nem tartalmazta a kivonatolt funkciót.
* Ha az érték 1, a sor tartalmazta a funkciót.

A szolgáltatáskivonatolás lehetővé teszi, hogy a változó hosszúságú szöveges dokumentumokat azonos hosszúságú numerikus jellemzővektorokként ábrázolja a méretcsökkentés érdekében. Ha a szövegoszlopot a betanításhoz próbálta használni, akkor a program egy több különböző értékkel rendelkező kategorikus jellemzőoszlopként kezeli.

Numerikus kimenetek is lehetővé teszik a közös gépi tanulási módszerek, beleértve a besorolás, fürtözés és információlekérések használatát is. Mivel a kitekintési műveletek karakterlánc-összehasonlítások helyett egész kihágásokat is használhatnak, a jellemzősúlyok lehívása is sokkal gyorsabb.

## <a name="configure-the-feature-hashing-module"></a>A Szolgáltatáskivonat-modul konfigurálása

1.  Adja hozzá a szolgáltatáskivonatoló modult a tervező folyamatához.

1. Kapcsolja össze az elemezni kívánt szöveget tartalmazó adatkészletet.

    > [!TIP]
    > Mivel a szolgáltatáskivonatolás nem hajt végre lexikális műveleteket, például a származtatást vagy a csonkolást, néha jobb eredményeket érhet el a szöveg előfeldolgozásával a szolgáltatáskivonatolás alkalmazása előtt. 

1. Állítsa a **Céloszlopokat** azon szövegoszlopokra, amelyeket kivonatolt szolgáltatásokká szeretne alakítani. Ne feledje, hogy:

    * Az oszlopoknak karakterlánc-adattípusnak kell lenniük.
    
    * Több szövegoszlop kiválasztása jelentős hatással lehet a funkciók méretére. Egy 10 bites kivonat oszlopainak száma például 1024-ről 2048-ra emelkedik két oszlop esetén.

1. **A kivonatoló bitméret** segítségével megadhatja a kivonattábla létrehozásakor használandó bitek számát.
    
    Az alapértelmezett bitméret 10. Sok probléma esetén ez az érték megfelelő. Az ütközések elkerüléséhez több helyre lehet szükség, a betanítási szövegben található n-gramm szókincs méretétől függően.
    
1. **N-gramm**esetén adjon meg egy számot, amely meghatározza az n-gramm maximális hosszát, amelyet hozzá szeretne adni az edzésszótárhoz. Az n-gramm az *n* szavak sorozata, amelyet egyedi egységként kezelnek.

    Ha például 3-at ad meg, akkor egygrammos, bigram és trigramok jönnek létre.

1. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után a modul egy átalakított adatkészletet ad ki, amelyben az eredeti szövegoszlopot több oszlopra konvertálta. Minden oszlop egy jellemzőt jelöl a szövegben. Aszótár jelentősségétől függően az eredményül kapott adatkészlet nagy lehet:

|Oszlop neve 1|2. oszloptípus|
|-------------------|-------------------|
|FELHASZNÁLÓI SZÖVEG|Eredeti adatoszlop|
|Hangulat|Eredeti adatoszlop|
|USERTEXT - Kivonatolási funkció 1|Kivonatolt szolgáltatásoszlop|
|USERTEXT - Kivonatolási funkció 2|Kivonatolt szolgáltatásoszlop|
|USERTEXT - Kivonatolási funkció n|Kivonatolt szolgáltatásoszlop|
|USERTEXT - Kivonatoló funkció 1024|Kivonatolt szolgáltatásoszlop|

Az átalakított adatkészlet létrehozása után használhatja azt a Betanítási modell modul bemeneteként.
 
## <a name="best-practices"></a>Ajánlott eljárások

Az alábbi gyakorlati tanácsok segítségével a legtöbbet hozhatja ki a funkciókivonat-kivonatoló modulból:

* Adjon hozzá egy Előprocess Text modult a szolgáltatáskivonat használata előtt a bemeneti szöveg előfeldolgozásához. 

* Adjon hozzá egy Oszlopok kijelölése modult a Szolgáltatáskivonat-kivonatmodul után, hogy eltávolítsa a szövegoszlopokat a kimeneti adatkészletből. A kivonatolási szolgáltatások létrehozása után nincs szükség a szövegoszlopokra.
    
* Fontolja meg a szöveg előfeldolgozási beállításainak használatát az eredmények egyszerűsítése és a pontosság javítása érdekében:

    * Szótörés
    * A szavak eltávolításának leállítása
    * Eset normalizálása
    * Az írásjelek és a speciális karakterek eltávolítása
    * Eredet  

A bármely megoldásban alkalmazható előfeldolgozási módszerek optimális készlete a tartománytól, a szókincstől és az üzleti szükséglettől függ. folyamataz adatokkal, hogy melyik szövegfeldolgozási módszerek a leghatékonyabbak.

## <a name="next-steps"></a>További lépések
            
Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét](module-reference.md) 

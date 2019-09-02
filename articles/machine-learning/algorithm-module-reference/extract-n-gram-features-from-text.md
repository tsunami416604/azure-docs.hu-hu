---
title: 'N-gramm kibontása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning-szolgáltatásban található N-Gram-modult a szöveges adatok szabadkézi.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 450c205f92fc65cad4e552aef3a1f28157d25ab6
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210870"
---
# <a name="extract-n-gram-features-from-text"></a>N-Gram-funkciók kinyerése szövegből

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

A nem strukturált szöveges adatok *szabadkézi* használja az **N-Gram-funkciók a szöveges** modulból való kinyerését.

Ez a modul az alábbi forgatókönyveket támogatja egy n-Gram szótár használatához:

* [Hozzon létre egy új n-Gram szótárt](#create-a-new-n-gram-dictionary) egy szabad szövegű oszlopból.

* Egy [meglévő szöveges funkciók használatával](#use-an-existing-n-gram-dictionary) szabadkézi egy ingyenes szöveges oszlopot.

* Az n-grammot használó [modellek pontszáma vagy közzététele](#score-or-publish-a-model-that-uses-n-grams) .

## <a name="configure-extract-n-gram-features-from-text"></a>N-Gram-funkciók kinyerésének beállítása a szövegből

### <a name="create-a-new-n-gram-dictionary"></a>Új n-Gram szótár létrehozása

1.  Adja hozzá az **N-Gram-funkciók a szöveges** modulból a kísérlethez és a feldolgozni kívánt szöveget tartalmazó adatkészlet összekapcsolásához.

1.  A **text oszlop** használatával kiválaszthatja a kiolvasni kívánt szöveget tartalmazó **karakterlánc** típusú oszlopot. Mivel az eredmények részletesek, egyszerre csak egyetlen oszlopot dolgozhat fel.

1. A **szókincs mód** beállítása , hogy jelezze, hogy az n-Gram-funkciók új listáját hozza létre. 

1. Állítsa be az **n-gramm méretet** , hogy jelezze a kinyerni és tárolni kívánt n-grammos *maximális* méretet. 

    Ha például a 3, a unigrams, a bigrams és a Trigrams értéket adja meg, a rendszer létrehozza a következőt:.

1. A **súlyozási függvény** meghatározza, hogyan kell felépíteni a dokumentum-szolgáltatás vektort, és hogyan lehet kinyerni a szókincset a dokumentumokból

    * **Bináris súlyozás**: Egy bináris jelenléti értéket rendel a kinyert n-grammhoz. Az egyes n-grammok értéke 1, ha az adott dokumentumban létezik, és 0 más.

    * **TF súlyozás**: Egy kifejezés-gyakorisági pontszámot (**TF**) rendel a kinyert n-grammhoz. Az egyes n-gramok értéke az adott dokumentum előfordulási gyakorisága.

    * Az **IDF súlya**: Egy inverz dokumentum-gyakorisági pontszámot (**IDF**) rendel a kinyert n-grammhoz. Az egyes n-gramok értéke a corpus méretének a teljes corpusban való előfordulási gyakorisága alapján felosztott naplója. `IDF = log of corpus_size / document_frequency`
 
    *  **TF – IDF-súlyozás**: A kinyert n-gramm értékhez rendel egy kifejezési gyakoriságot/inverz dokumentum-gyakorisági pontszámot (**TF/IDF**). Az egyes n-grammok értéke a TF pontszám szorozva az IDF pontszámával.

1. Megadhatja a **minimális szó hosszát** az n-Gram *egyetlen szavában* használható minimális számú betűvel.

1. A **maximális hossz** értékének megadásával beállíthatja, hogy legfeljebb hány betűt használjon egy n-Gram *egyetlen szavában* .

    Alapértelmezés szerint a Word vagy a token esetében legfeljebb 25 karakter adható meg.

1. A **minimális n-Gram-dokumentum abszolút gyakoriságának**használata az n-grammos szótárban található összes n-Gram minimális előfordulásának beállításához. 

    Ha például az alapértelmezett 5 értéket használja, minden n-grammnak legalább ötször szerepelnie kell a corpusban az n-Gram szótárban való felvételhez. 

1.  Adja meg a **maximális n-grammos dokumentum arányát** az adott n-grammot tartalmazó sorok számának maximális arányához a teljes Corpus soraiban.

    Az 1 arány például azt jelzi, hogy ha egy adott n-gramm szerepel az összes sorban, akkor az n-Gram az n-Gram szótárhoz adható. Az egyes sorokban előforduló szavak általában egy zaj szót vesznek, és el lesznek távolítva. A tartománytól függő zajok kiszűréséhez próbálja meg csökkenteni ezt az arányt.

    > [!IMPORTANT]
    > Az egyes szavak előfordulási aránya nem egységes, de a dokumentumtól a dokumentumig változhat. Ha például egy adott termékkel kapcsolatos vásárlói megjegyzéseket elemez, a termék neve nagyon magas gyakorisággal és a zajszinthez közel van, azonban más kontextusban jelentős kifejezésnek kell lennie.

1. Válassza az **n-Gram funkció** -vektorok normalizálása lehetőséget a szolgáltatás-vektorok normalizálása érdekében. Ha engedélyezve van, minden n-Gram-szolgáltatás vektora az L2-normával van elosztva.

1. Futtassa a kísérletet.

### <a name="use-an-existing-n-gram-dictionary"></a>Meglévő n-Gram szótár használata

1.  Adja hozzá az **N-Gram-funkciók a szöveges** modulból a kísérlethez lehetőséget, és kapcsolja össze azt az adatkészletet, amely az **adatkészlet** portjára feldolgozni kívánt szöveggel rendelkezik.

1.  A Text ( **szöveg) oszlop** kiválasztásával válassza ki azt a szöveges oszlopot, amely a szabadkézi szöveget tartalmazza. Alapértelmezés szerint a modul a String típusú összes oszlopot kiválasztja. A legjobb eredmények érdekében egyszerre csak egyetlen oszlopot kell feldolgoznia.

1. Adja hozzá a korábban generált n-Gram-szótárt tartalmazó mentett adatkészletet, és kapcsolja a **bemeneti szókincs** portjához. Az eredményként szolgáló **szókincs** kimenetét is összeállíthatja az **N-Gram-funkciók szöveges** modulból való kinyeréséhez.

1. A **szókincs mód**esetében válassza a következő frissítési lehetőséget a legördülő listából:

   * **ReadOnly**: A bemeneti szókincshez tartozó bemeneti corpusot jelöli. Az új szöveges adatkészletből (a bal oldali bemeneten) a számítástechnikai kifejezés gyakorisága helyett a bemeneti szókincs n-grammos súlyozását is alkalmazza a rendszer.

    > [!TIP]
    > Akkor használja ezt a lehetőséget, ha szöveges osztályozó kiértékelésére van szükség.

1.  Az összes többi beállításnál tekintse meg az [előző szakaszban](#create-a-new-n-gram-dictionary)található tulajdonságok leírását.

1.  Futtassa a kísérletet.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>N-grammot használó modell pontszáma vagy közzététele

1.  Másolja az **N-Gram-funkciók** kinyerése a szöveges modulból a betanítási adatfolyam a pontozási adatfolyam.

1.  Az **eredmény szókincs** kimenetének összekapcsolása a betanítási adatfolyam a pontozási adatfolyam lévő **bemeneti szókincstel** .

1.  A pontozási munkafolyamatban módosítsa az **N-Gram-funkciók** kinyerése a szöveges modulból elemet, és végezze el ezeket a módosításokat, így az összes többi ugyanaz marad:

    - Állítsa a **szókincs mód** paraméterét **readonly**értékre.

1.  A kísérlet közzétételéhez mentse az **eredmény szókincsét** adatkészletként.

1.  Ezután kapcsolja össze a mentett adatkészletet az **N-Gram-funkciók** kinyerésével a pontozási gráfban található Text modulból.

## <a name="results"></a>Results (Eredmények)

A **text modul N-Gram-funkcióinak** kinyerése két típusú kimenetet hoz létre: 

* **Eredmények adatkészlet**: Az elemzett szöveg összegzése a kinyert n-grammtal együtt. A **text (szöveg) oszlopban** nem kiválasztott oszlopok a kimenetre lesznek átadva. Az elemzett szöveg minden egyes oszlopához a modul a következő oszlopokat hozza létre:

  * **N-Gram előfordulások mátrixa**: A modul létrehoz egy oszlopot minden olyan n-grammnál, amely megtalálható a teljes corpusban, és egy pontszámot ad az egyes oszlopokban, hogy jelezze az adott sorhoz tartozó n-gramm súlyozását. 

* **Eredmény szókincse**: A szókincs tartalmazza az aktuális n-Gram-szótárt, valamint az elemzés részeként generált gyakorisági pontszámokat. Az adatkészletet a különböző bemenetekkel való újrafelhasználáshoz, vagy egy későbbi frissítéshez is mentheti. A szókincset a modellezéshez és pontozáshoz is felhasználhatja.

### <a name="result-vocabulary"></a>Eredmény szókincse

A szószedet tartalmazza az n-Gram-szótárt, amely az elemzés részeként generált kifejezés gyakorisági pontszámait tartalmazza. A **DF** és az **IDF** pontszámok a többi lehetőségtől függetlenül jönnek létre.

+ **AZONOSÍTÓ**: Az egyes egyedi n-grammokhoz generált azonosító.
+ **NGram**: Az n-Gram. A szóközöket vagy más Word-elválasztó karaktereket az aláhúzás karakter váltja fel.
+ **DF**: Az n-gramm kifejezés gyakorisági pontszáma az eredeti corpusban.
+ **IDF**: Az n-grammhoz tartozó inverz dokumentum gyakorisági pontszáma az eredeti corpusban.

Manuálisan is frissítheti ezt az adatkészletet; Előfordulhat azonban, hogy hibákat fog bevezetni. Példa:

* A rendszer hibát jelez, ha a modul ismétlődő sorokat talál a bemeneti szókincsben található ugyanazzal a kulccsal. Ügyeljen arra, hogy a szókincsben ne legyen két sor ugyanazzal a Szóval.
* A szókincs-adatkészletek bemeneti sémájának pontosan egyeznie kell, beleértve az oszlopnevek és az oszlopok típusát is. 
* Az **ID** oszlop és a **DF** pontszám oszlopának egész típusúnak kell lennie. 
* Az **IDF** -OSZLOPnak lebegőpontos típusúnak kell lennie.

> [!Note]
> Az adatkimenetet ne kapcsolja közvetlenül a **Train Model** modulhoz. A betanítási modellbe való beetetéselőtt el kell távolítani a szabad szöveges oszlopokat, máskülönben a szabad szöveges oszlopokat kategorikus funkciókként kezeli a rendszer.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 

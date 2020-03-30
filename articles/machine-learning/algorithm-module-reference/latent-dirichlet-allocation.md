---
title: Látens Dirichlet-allokáció
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a látens dirichlet felosztása modult az egyébként nem minősített szöveg több kategóriába sorolja.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/11/2020
ms.openlocfilehash: 1384491489c175ffc338f80a99aa8d5050f835d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109224"
---
# <a name="latent-dirichlet-allocation"></a>Látens Dirichlet-allokáció

Ez a cikk ismerteti, hogyan használhatja a **látens dirichlet-hozzárendelési** modul az Azure Machine Learning designer (előzetes verzió), az egyébként nem minősített szöveg csoportosítása több kategóriába. 

A látens Dirichlet Allocation (LDA) gyakran használják a természetes nyelvi feldolgozásban (NLP) hasonló szövegek keresésére. Egy másik gyakori kifejezés a *témamodellezés*.

Ez a modul egy szövegoszlopot vesz fel, és létrehozza ezeket a kimeneteket:

+ A forrásszöveg, az egyes kategóriák pontszámával együtt

+ Jellemzőmátrix, amely minden kategóriához kivont kifejezéseket és együtthatókat tartalmaz

+ Egy átalakítás, amelyet menthet, és újra alkalmazhat a bemenetként használt új szövegre

Ez a modul a scikit-learn könyvtárat használja. További információ a scikit-learn, lásd a [GitHub tárház, amely oktatóanyagokat és az algoritmus magyarázatát tartalmazza.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>A Látens Dirichlet allocation (LDA) – További információk

Általánosságban elmondható, hogy az LDA önmagában nem osztályozási módszer, hanem generatív megközelítést alkalmaz. Ez azt jelenti, hogy nem kell ismert osztálycímkéket megadnia, majd következtetnia a mintákat.  Ehelyett az algoritmus létrehoz egy valószínűségi modellt, amely a témakörcsoportok azonosítására szolgál. A valószínűségi modell segítségével a meglévő betanítási eseteket vagy a modellnek megadott új eseteket bemenetként osztályozhatja.

A generatív modell előnyös lehet, mert elkerüli, hogy a szöveg és a kategóriák közötti kapcsolatra vonatkozó erős feltételezéseket, és csak a szavak eloszlását használja matematikailag a témakörök modellezéséhez.

+ Az elmélet tárgyalja ezt a papírt, elérhető PDF letöltés: [Látens Dirichlet kiosztás: Blei, Ng, és Jordánia](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf)

+ A modul megvalósítása az LDA [scikit-learn könyvtárán](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) alapul.

További információt a [Műszaki megjegyzések](#technical-notes) című részben talál.

## <a name="how-to-configure-latent-dirichlet-allocation"></a>A látens dirichlet-hozzárendelés beállítása

Ehhez a modulhoz olyan adatkészletszükséges, amely nyers vagy előfeldolgozott szövegoszlopot tartalmaz.

1. Adja hozzá a **látens dirichlet-hozzárendelési** modult a folyamathoz.

2. A modul bemeneteként adjon meg egy vagy több szövegoszlopot tartalmazó adatkészletet.

3. A **Céloszlopok mezőben**válasszon ki egy vagy több elemezni kívánt szöveget tartalmazó oszlopot.

    Több oszlopot is választhat, de azoknak karakterlánc-adattípusúnak kell lenniük.

    Mivel az LDA általában nagy jellemzőmátrixot hoz létre a szövegből, általában egyetlen szövegoszlopot elemez.

4. A **Modellező témakörök számozásához**írjon be egy 1 és 1000 közötti egész számot, amely azt jelzi, hogy hány kategóriát vagy témakört szeretne a bemeneti szövegből származtatni.

    Alapértelmezés szerint 5 témakör jön létre.

5. **N-grammok**esetén adja meg a kivonatolás során keletkezett N-grammok maximális hosszát.

    Az alapértelmezett érték 2, ami azt jelenti, hogy a bigramok és az unigrammok is jönnek létre.

6. Válassza a **Normalizálás** beállítást a kimeneti értékek valószínűségké alakításához. Ezért ahelyett, hogy az átalakított értékeket egész számként képviselné, a kimeneti és jellemzőadatkészlet értékei a következőképpen alakulnak át:

    + Az adatkészlet értékei valószínűségként jelennek `P(topic|document)`meg, ha a .

    + A jellemzőtémakör-mátrix értékei valószínűségként jelennek meg, ha `P(word|topic)`a .

    > [!NOTE] 
    > Az Azure Machine Learning tervezőjében (előzetes verzió), mivel az általunk létrehozott könyvtár, scikit-learn, már nem támogatja a 0.19-es verzió ból származó nem normalizált *doc_topic_distr* kimenetet, ezért ebben a modulban a **Normalize** paraméter csak **a Feature Topic mátrix** kimenetére alkalmazható, az átalakított **adatkészlet** kimenete mindig normalizálódik.

7. Válassza a **(Összes beállítás megjelenítése)** lehetőséget, majd állítsa IGAZ értékre, ha meg szeretné tekinteni, majd adja meg a további speciális paramétereket.

    Ezek a paraméterek az LDA scikit-learn implementációjára jellemzőek. Van néhány jó oktató lda scikit-tanulni, valamint a hivatalos [scikit-tanulni dokumentum](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho paraméter**. Adja meg a témakör-eloszlások ritkaságának előzetes valószínűségét. Megfelel a sklearn `topic_word_prior` paraméterének. Az 1 értéket akkor használja, ha azt szeretné, hogy a szavak eloszlása lapos; azaz minden szó feltételezhetően equiprobable. Ha úgy gondolja, hogy a legtöbb szó gyéren jelenik meg, akkor sokkal alacsonyabb értékre állíthatja.

    + **Alfa paraméter**. Adja meg a dokumentumtémakör-súlyok ritkaságának korábbi valószínűségét.  Megfelel a sklearn `doc_topic_prior` paraméterének.

    + **A dokumentumok becsült száma**. Írjon be egy számot, amely a legjobb becslést adja a feldolgozandó dokumentumok (sorok) számáról. Ez lehetővé teszi, hogy a modul megfelelő méretű kivonatoló táblát foglaljon le.  Megfelel a `total_samples` scikit-learn paraméternek.

    + **A köteg mérete**. Írjon be egy számot, amely azt jelzi, hogy az LDA-modellnek küldött szövegkötegek hány sort kell tartalmaznia. Megfelel a `batch_size` scikit-learn paraméternek.

    + **A tanulási frissítési ütemezésben használt iteráció kezdeti értéke.** Adja meg azt a kezdőértéket, amely lebecsüli az online tanulás korai iterációinak tanulási sebességét. Megfelel a `learning_offset` scikit-learn paraméternek.

    + **A frissítések során az iterációra alkalmazott teljesítmény**. Adja meg az iterációs számra alkalmazott teljesítményszintet az online frissítések során a tanulási arány szabályozása érdekében. Megfelel a `learning_decay` scikit-learn paraméternek.

    + **Az adatok feletti átmegyések száma**. Adja meg, hogy az algoritmus hányszor lépkedhet az adatok között. Megfelel a `max_iter` scikit-learn paraméternek.

8. Válassza a **lehetőséget, Build szótár ngrams** vagy **Build szótár ngrams előtt LDA**, ha azt szeretné, hogy az n-gram lista egy kezdeti lépés, mielőtt a szöveg besorolása.

    Ha előre létrehozza a kezdeti szótárt, később használhatja a szótárt a modell áttekintésekénél. Az eredmények szövegre, nem pedig numerikus indexekbe való leképezése általában könnyebb értelmezést. A szótár mentése azonban hosszabb időt vesz igénybe, és további tárhelyet használ.

9. Az **ngram szótár maximális mérete**mezőbe írja be az n-gram szótárban létrehozható sorok teljes számát.

    Ez a beállítás a szótár méretének szabályozásakor hasznos. Ha azonban a bemeneti ngrammok száma meghaladja ezt a méretet, ütközések fordulhatnak elő.

10. Küldje el a folyamatot. Az LDA modul Bayes-tételt használ annak meghatározására, hogy mely témakörök társíthatók az egyes szavakhoz. A szavak nem kizárólag témákhoz vagy csoportokhoz kapcsolódnak; ehelyett minden n-grammnak van egy tanult valószínűsége, hogy a felfedezett osztályok bármelyikével társítva van.

## <a name="results"></a>Results (Eredmények)

A modul két kimenettel rendelkezik:

+ **Átalakított adatkészlet**: A bemeneti szöveget és a felderített kategóriák megadott számát tartalmazza, az egyes kategóriákra vonatkozó egyes szövegpéldák pontszámaival együtt.

+ **Jellemzőtémakör mátrixa**: A bal szélső oszlop tartalmazza a kibontott szöveg funkciót, és minden kategóriához tartozik egy oszlop, amely az adott funkció pontszámát tartalmazza az adott kategóriában.


### <a name="lda-transformation"></a>LDA-transzformáció

Ez a modul az *LDA-transzformációt* is kiadja, amely az LDA-t az adatkészletre alkalmazza.

Ezt az átalakítást úgy mentheti, hogy regisztrálja az **adatkészletet** a modul jobb oldali ablaktáblájában, és használja fel más adatkészletek hez. Ez akkor lehet hasznos, ha nagy korpuszra van betanítva, és újra fel szeretné használni az együtthatókat vagy kategóriákat.

### <a name="refining-an-lda-model-or-results"></a>LDA modell vagy eredmények finomítása

Általában nem hozhat létre egyetlen LDA-modellt, amely minden igénynek megfelel, és még egy feladathoz tervezett modell is sok iterációt igényelhet a pontosság javítása érdekében. Javasoljuk, hogy próbálja meg ezeket a módszereket a modell fejlesztéséhez:

+ A modell paramétereinek módosítása
+ Vizualizáció használata az eredmények megértéséhez
+ A téma szakértőinek visszajelzései annak megállapítására, hogy a generált témák hasznosak-e.

A minőségi intézkedések az eredmények értékeléséhez is hasznosak lehetnek. A témakörmodellezési eredmények kiértékeléséhez vegye figyelembe a következőket:

+ Pontosság - A hasonló elemek nagyon hasonlóak?
+ Sokszínűség – Különbséget tehet-e a modell a hasonló tételek között, ha az üzleti probléma miatt szükséges?
+ Méretezhetőség – A szövegkategóriák széles skáláján működik, vagy csak egy szűk céltartományon?

Az LDA-n alapuló modellek pontossága gyakran javítható a természetes nyelvi feldolgozással a szöveg tisztításához, összegzéséhez és egyszerűsítéséhez vagy kategorizálásához. Például a következő technikák, amelyek mindegyike támogatja az Azure Machine Learning, javíthatja a besorolás pontosságát:

+ Szó eltávolításának leállítása

+ Eset normalizálása

+ Lemmatizáció vagy szinkálás

+ Nevesített entitások felismerése

További információt a [Szöveg előfeldolgozása című témakörben talál.](preprocess-text.md)

A tervezőben r- vagy Python-kódtárakat is használhat a szövegfeldolgozáshoz: [R-parancsfájl végrehajtása](execute-r-script.md), [Python-parancsfájl végrehajtása](execute-python-script.md)



## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz a megvalósítás részleteit, tippjeit és a gyakran feltett kérdésekre adott válaszokat tartalmazza.

### <a name="implementation-details"></a>A végrehajtás részletei

Alapértelmezés szerint az átalakított adatkészlet és a jellemzőtéma-mátrix kimeneteinek eloszlása valószínűségként normalizálódik.

+ Az átalakított adatkészlet normalizálódik, mint a dokumentumban megadott témakörök feltételes valószínűsége. Ebben az esetben az egyes sorok összege 1.

+ A jellemzőtéma-mátrix a témakörben megadott szavak feltételes valószínűségeként normalizálódik. Ebben az esetben az egyes oszlopok összege 1.

> [!TIP]
> Előfordulhat, hogy a modul egy üres témakört ad vissza, amelyet leggyakrabban az algoritmus pszeudo-véletlenszerű inicializálása okoz.  Ebben az esetben megpróbálhatja módosítani a kapcsolódó paramétereket, például az N-gram szótár maximális méretét vagy a funkciókivonatoláshoz használandó bitek számát.

### <a name="lda-and-topic-modeling"></a>LDA és témamodellezés

A látens dirichlet-kiosztást (LDA) gyakran használják *tartalomalapú témakörmodellezéshez,* ami alapvetően a nem osztályozott szövegből származó kategóriák tanulását jelenti. A tartalomalapú témakörmodellezésben a témakör szavakon keresztüli terjesztés.

Tegyük fel például, hogy számos terméket tartalmazó vásárlói véleményeket adott meg. A sok ügyfél által az idő múlásával benyújtott vélemények szövege sok kifejezést tartalmazna, amelyek közül néhányat több témakörben is használnak.

Az LDA-folyamat által azonosított **témakör** egy adott "A" termék reprezentatot, vagy termékértékelések egy csoportját képviselheti. Az LDA számára maga a témakör csak egy valószínűségi eloszlás egy sor szó hoz.

A kifejezések ritkán kizárólagosak egy termékre, de hivatkozhatnak más termékekre, vagy lehetnek olyan általános kifejezések, amelyek mindenre vonatkoznak ("nagyszerű", "szörnyű"). Más kifejezések lehetnek a zaj szavakat.  Fontos azonban megérteni, hogy az LDA módszer nem célja, hogy rögzítse az összes szót az univerzumban, vagy megérteni, hogyan szavak kapcsolódnak, eltekintve a valószínűsége konvaló előfordulása. Csak a céltartományban használt szavakat csoportosíthatja.

Az indexek kifejezés kiszámítása után az egyes szövegsorokat a program távolságalapú hasonlósági mértékkel hasonlítja össze annak meghatározásához, hogy két szövegdarab hasonlít-e egymáshoz.  Előfordulhat például, hogy a termék több, erősen korreláló névvel rendelkezik. Vagy előfordulhat, hogy az erősen negatív kifejezések általában egy adott termékhez kapcsolódnak. A hasonlósági mérték segítségével azonosíthatja a kapcsolódó kifejezéseket, és javaslatokat is létrehozhat.

###  <a name="module-parameters"></a>Modul paraméterek

|Név|Típus|Tartomány|Optional|Alapértelmezett|Leírás|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Céloszlop(ok)|Oszlopkijelölés||Kötelező|Karakterláncfunkció|Céloszlop neve vagy indexe|  
|A modellező témakörök száma|Egész szám|[1;1000]|Kötelező|5|A dokumentumterjesztés modellezése N témakörökhöz|  
|N-gramm|Egész szám|[1;10]|Kötelező|2|A kivonatolás során keletkező N-grammok sorrendje|  
|Normalizálni|Logikai|Igaz vagy Hamis|Kötelező|igaz|A kimenet normalizálása valószínűségre.  Az átalakított adatkészlet P(témakör&#124;dokumentum) lesz, a jellemzőtémakör-mátrix pedig P(word&#124;témakör) lesz.|  
|Az összes beállítás megjelenítése|Logikai|Igaz vagy Hamis|Kötelező|False (Hamis)|Bemutatja a további paraméterek et scikit-learn online LDA|  
|Rho paraméter|Float|[0.00001;1.0]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|0.01|Témakör szó előzetes eloszlása|  
|Alfa paraméter|Float|[0.00001;1.0]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|0.01|Témakör előzetes terjesztése|  
|Dokumentumok becsült száma|Egész szám|[1;int. MaxValue]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|1000|A dokumentumok becsült száma (megfelel total_samples paraméternek)|  
|A tétel mérete|Egész szám|[1;1024]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|32|A tétel mérete|  
|A tanulási sebesség frissítési ütemezésében használt iteráció kezdeti értéke|Egész szám|[0;int. MaxValue]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|0|Kezdeti érték, amely leértékeli a korai iterációk tanulási sebességét. Megfelel a learning_offset paraméternek|  
|A frissítések során az iterációhoz alkalmazott teljesítmény|Float|[0.0;1.0]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|0,5|A tanulási sebesség szabályozása érdekében az iterációs számra alkalmazott teljesítmény. Megfelel a learning_decay paraméternek |  
|Képzési ismétlések száma|Egész szám|[1;1024]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|25|Képzési ismétlések száma|  
|Ngrams szótárának összeállítása|Logikai|Igaz vagy Hamis|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet *nincs* bejelölve|True (Igaz)|Az LDA számítása előtt létrehoz egy ngramm-szótárt. Hasznos a modell ellenőrzés és értelmezés|  
|Az ngramszótár maximális mérete|Egész szám|[1;int. MaxValue]|Akkor érvényes, ha az **ngrams szótár készítése** jelölőnégyzet igaz|20000|Az ngramszótár maximális mérete. Ha a bemeneti bemeneti tokenek száma meghaladja ezt a méretet, ütközések|  
|A funkciókivonatoláshoz használandó bitek száma|Egész szám|[1;31]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet *nincs* bejelölve, és **az ngrams szótárának összeállítása** hamis|12|A funkciókivonatoláshoz használandó bitek száma| 
|Az Ngrammok szótárának összeállítása az LDA előtt|Logikai|Igaz vagy Hamis|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve|True (Igaz)|Az LDA előtt létrehoz egy ngramm-szótárt. Hasznos a modell ellenőrzés és értelmezés|  
|Ngrammok maximális száma a szótárban|Egész szám|[1;int. MaxValue]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve, és az **ngrams szótár készítése** jelölőnégyzet igaz|20000|A szótár maximális mérete. Ha a bemeneti bemeneti tokenek száma meghaladja ezt a méretet, ütközések|  
|Kivonatbitek száma|Egész szám|[1;31]|Akkor érvényes, ha az **Összes beállítás megjelenítése** jelölőnégyzet be van jelölve, és az **ngrams szótár készítése** parancs hamis.|12|A funkció kivonatolása során használandó bitek száma|   


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md)   
A modulokra jellemző hibák listáját a [tervező kivételei és hibakódjai](designer-error-codes.md)című témakörben található.

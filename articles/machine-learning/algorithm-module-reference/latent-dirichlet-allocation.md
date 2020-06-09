---
title: Rejtett Dirichlet-lefoglalás
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használható a látens Dirichlet-kiosztási modul a nem besorolt szöveg több kategóriába való csoportosításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: a75bf458a1c6735de42349de5d5cb6845e9ae464
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84487967"
---
# <a name="latent-dirichlet-allocation"></a>Rejtett Dirichlet-lefoglalás

Ez a cikk azt ismerteti, hogyan használható a **látens Dirichlet-kiosztási** modul a Azure Machine learning Designerben (előzetes verzió), hogy az egyébként nem besorolt szövegeket csoportosítsa egy adott számú kategóriába. 

A látens Dirichlet-kiosztást (LDA) gyakran használják természetes nyelvi feldolgozással (NLP) a hasonló szövegek kereséséhez. Egy másik gyakori kifejezés a *témakör modellezése*.

Ez a modul egy szöveges oszlopot vesz igénybe, és létrehozza ezeket a kimeneteket:

+ A forrás szövege, valamint az egyes kategóriákhoz tartozó pontszám

+ Egy szolgáltatás mátrixa, amely tartalmazza a kinyert kifejezéseket és az egyes kategóriákhoz tartozó együtthatókat

+ Egy átalakítás, amelyet menthet és újra alkalmazhat a bemenetként használt új szövegre.

Ez a modul a scikit-Learn könyvtárat használja. További információ a scikit-ről: [GitHub-adattár, amely oktatóanyagokat és az algoritmus magyarázatát tartalmazza.

### <a name="more-about-latent-dirichlet-allocation-lda"></a>További információ a látens Dirichlet-kiosztásról (LDA)

Általánosságban elmondható, hogy a LDA nem egy se-besorolási módszer, de a generatív megközelítést használja. Ez azt jelenti, hogy nem kell megadnia ismert osztályok címkéit, majd következtetni a mintázatokra.  Ehelyett az algoritmus létrehoz egy valószínűségi modellt, amely a témakörök csoportjai azonosítására szolgál. A valószínűségi modell segítségével a meglévő betanítási eseteket, illetve a modellbe bemenetként megadott új eseteket is osztályozhatja.

A generatív modell előnyösebb lehet, mivel elkerüli a szöveg és a kategóriák közötti kapcsolatra vonatkozó erős feltételezéseket, és csak a szavak eloszlását használja a matematikai modellekkel kapcsolatos témakörökben.

+ Ebben a dokumentumban a PDF-Letöltés: [látens Dirichlet foglalás: Bleiweiss, ng és Jordan](https://ai.stanford.edu/~ang/papers/nips01-lda.pdf) nyelven érhető el.

+ A modul implementációja a LDA [scikit-Learn könyvtárán](https://github.com/scikit-learn/scikit-learn/blob/master/sklearn/decomposition/_lda.py) alapul.

További információ: [technikai megjegyzések](#technical-notes) szakasz.

## <a name="how-to-configure-latent-dirichlet-allocation"></a>Látens Dirichlet-foglalás konfigurálása

Ehhez a modulhoz olyan adatkészlet szükséges, amely tartalmazza a nyers vagy az előfeldolgozott szöveg egy oszlopát.

1. Adja hozzá a **látens Dirichlet kiosztási** modult a folyamathoz.

2. A modul bemenete adjon meg egy vagy több szöveges oszlopot tartalmazó adatkészletet.

3. A **célobjektum**mezőben válasszon ki egy vagy több olyan oszlopot, amely az elemezni kívánt szöveget tartalmazza.

    Több oszlopot is kiválaszthat, de a karakterlánc adattípusnak kell lennie.

    Általánosságban elmondható, hogy mivel a LDA egy nagyméretű szolgáltatás-mátrixot hoz létre a szövegből, általában egyetlen szöveges oszlopot fog elemezni.

4. A **modellhez tartozó témakörök számának**megadásához írjon be egy 1 és 1000 közötti egész számot, amely azt jelzi, hogy hány kategóriát vagy témakört szeretne a bemeneti szövegből származtatni.

    Alapértelmezés szerint 5 témakör jön létre.

5. **N-gramm**esetén a kivonatolás során generált n-g maximális hosszát határozza meg.

    Az alapértelmezett érték a 2, ami azt jelenti, hogy mind a bigrams, mind a unigrams létrejön.

6. Válassza a **normalizálás** lehetőséget a kimeneti értékek valószínűségekre való átalakításához. Ezért ahelyett, hogy az átalakított értékeket egész számként ábrázolja, a kimenet és a szolgáltatás adatkészletében lévő értékek a következőképpen alakulnak:

    + Az adatkészletben lévő értékek valószínűséggel lesznek megjelenítve `P(topic|document)` .

    + A szolgáltatás témakörének mátrixában szereplő értékek a következő valószínűséggel lesznek megjelenítve: `P(word|topic)` .

    > [!NOTE] 
    > Azure Machine Learning Designerben (előzetes verzió), mert az alapul szolgáló scikit-Learn, a továbbiakban nem támogatja a 0,19-es verzió nem normalizált *doc_topic_distr* kimenetét, ezért ebben a modulban a **normalizálás** paraméter csak a **szolgáltatási témakör mátrix** kimenetére alkalmazható, az **átalakított adatkészlet** kimenete mindig normalizált.

7. Válassza ki a lehetőséget, **mutasson az összes beállítás**elemre, majd állítsa igaz értékre, ha szeretné megtekinteni, majd további speciális paramétereket beállítani.

    Ezek a paraméterek a LDA scikit-Learn megvalósítására vonatkoznak. Van néhány jó oktatóanyag a scikit-Learn LDA, valamint a hivatalos [scikit – Learn dokumentumban](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.LatentDirichletAllocation.html).

    + **Rho paraméter**. Adjon meg egy korábbi valószínűséget a adatelégtelenségetához. Megfelel a sklearn `topic_word_prior` paraméterének. Ha azt szeretné, hogy a szavak eloszlása lapos legyen, használja az 1 értéket. azaz az összes szót feltételezzük equiprobable. Ha úgy gondolja, hogy a legtöbb szó ritkán jelenik meg, lehet, hogy sokkal alacsonyabb értékre van állítva.

    + **Alpha paraméter**. Meg kell adni egy korábbi valószínűséget a adatelégtelenséget témák súlyozásához.  Megfelel a sklearn `doc_topic_prior` paraméterének.

    + **A dokumentumok becsült száma**. Írjon be egy számot, amely a feldolgozásra kerülő dokumentumok (sorok) számának legjobb becslését jelöli. Ez lehetővé teszi, hogy a modul elegendő méretű kivonatoló táblázatot foglaljon le.  Megfelel a `total_samples` scikit-Learn paraméternek.

    + **A köteg mérete**. Írjon be egy számot, amely azt jelzi, hogy hány sort kell belefoglalni az egyes kötegekben a LDA-modellbe küldendő szövegbe. Megfelel a `batch_size` scikit-Learn paraméternek.

    + A **tanulás frissítési ütemtervében használt iteráció kezdeti értéke**. Itt adhatja meg a kezdő értéket, amely downweights tanulási arányt biztosít a korai ismétlésekhez az online tanulásban. Megfelel a `learning_offset` scikit-Learn paraméternek.

    + **A frissítések során a rendszer az iterációra alkalmazza a teljesítményt**. Adja meg az iterációk számának mértékét, hogy az online frissítések során a tanulási arányt vezérelni lehessen. Megfelel a `learning_decay` scikit-Learn paraméternek.

    + **Az adattovábbítások száma**. Adja meg, hogy az algoritmus hány alkalommal fogja átvenni az adatmennyiséget. Megfelel a `max_iter` scikit-Learn paraméternek.

8. Válassza ki a lehetőséget, állítsa össze a **ngrams szótárát** , vagy hozza létre a **ngrams a Lda előtt**, ha az n-Gram-listát egy kezdeti menetben szeretné létrehozni a szöveg besorolása előtt.

    Ha korábban hozza létre a kezdeti szótárt, később a modell áttekintéséhez használhatja a szótárt. Az eredmények szövegre való leképezése a numerikus indexek helyett általában könnyebben értelmezhető. A szótár mentése azonban hosszabb időt vesz igénybe, és további tárhelyet fog használni.

9. A **ngram-szótár maximális méretéhez**írja be az n-Gram-szótárban létrehozható sorok teljes számát.

    Ez a beállítás a szótár méretének szabályozásához hasznos. Ha azonban a bemeneti ngrams száma meghaladja ezt a méretet, ütközések merülhetnek fel.

10. A folyamat elküldése. A LDA modul a Bayes-tétel használatával határozza meg, hogy az egyes szavakat milyen témakörökhöz lehet társítani. A szavak nem kizárólag egyetlen témakörhöz vagy csoporthoz vannak társítva; Ehelyett minden n-Gram megtanulta, hogy a felderített osztályok bármelyikéhez társítva van-e.

## <a name="results"></a>Results (Eredmények)

A modul két kimenettel rendelkezik:

+ **Átalakított adatkészlet**: tartalmazza a bemeneti szöveget és a megadott számú felderített kategóriát, valamint az egyes kategóriákhoz tartozó egyes szöveges példák pontszámait.

+ A **szolgáltatás témakörének mátrixa**: a bal szélső oszlop tartalmazza a kinyert szöveg funkciót, és minden kategóriához tartozik egy oszlop, amely tartalmazza az adott szolgáltatáshoz tartozó pontszámot.


### <a name="lda-transformation"></a>LDA-transzformáció

Ez a modul a *Lda átalakítást* is megjeleníti, amely a Lda alkalmazza az adatkészletre.

Ezt a transzformációt újra felhasználhatja más adatkészletek esetében is. Ez akkor lehet hasznos, ha nagy corpuson van betanítva, és szeretné újra felhasználni az együtthatókat vagy kategóriákat.
Az átalakítás újbóli felhasználásához kattintson a LDA modul jobb oldali paneljén található **adatkészlet regisztrálása** ikonra, hogy a modul a modulok listájában az **adatkészletek** kategóriában maradjon. Ezt a modult akkor is összekapcsolhatjuk, ha [átalakítási](apply-transformation.md) modult alkalmaz az átalakítás újbóli felhasználásához.

### <a name="refining-an-lda-model-or-results"></a>LDA-modell vagy-eredmények finomítása

Általában nem hozható létre egyetlen LDA-modell, amely megfelel az összes szükségletnek, és még egy feladathoz tervezett modellnek is sok iterációra lehet szüksége a pontosság javítása érdekében. Javasoljuk, hogy az összes módszert kipróbálva javítsa a modelljét:

+ A modell paramétereinek módosítása
+ Az eredmények megismerése vizualizáció használatával
+ A témával foglalkozó szakértők visszajelzésének beszerzésével meggyőződhet arról, hogy a létrehozott témakörök hasznosak-e.

A minőségi mértékek az eredmények kiértékeléséhez is hasznosak lehetnek. A témakör modellezési eredményeinek kiértékeléséhez fontolja meg a következőket:

+ Pontosság – hasonló elemek nagyon hasonlóak?
+ Sokféleség – a modell különbséget tesz a hasonló elemek között, ha az üzleti probléma szükséges?
+ Méretezhetőség – számos szöveges kategórián vagy csak keskeny céltartományban működik?

A LDA alapuló modellek pontossága gyakran javítható a természetes nyelvi feldolgozás használatával a szöveg tisztításához, összefoglalásához és egyszerűsítéséhez, illetve kategorizálásához. Például az alábbi, Azure Machine Learning által támogatott módszerek javítják a besorolás pontosságát:

+ A Word eltávolításának leállítása

+ Kis-és nagybetűk normalizálása

+ Morfológiai elemzéshez vagy eredő

+ Nevesített entitások felismerése

További információ: [szöveg előfeldolgozása](preprocess-text.md).

A tervezőben R-vagy Python-kódtárakat is használhat a szöveges feldolgozáshoz: [r-szkript végrehajtása](execute-r-script.md), [Python-szkript végrehajtása](execute-python-script.md)



## <a name="technical-notes"></a>Technikai megjegyzések

Ez a szakasz megvalósítási részleteket, tippeket és válaszokat tartalmaz a gyakori kérdésekre.

### <a name="implementation-details"></a>Megvalósítás részletei

Alapértelmezés szerint a rendszer az átalakított adatkészlet és a szolgáltatás-témakör mátrix kimeneteit valószínűség szerint normalizálja.

+ Az átalakított adatkészlet normalizált, mivel a témakörök feltételes valószínűsége egy dokumentumnak adott. Ebben az esetben az egyes sorok összegének értéke 1.

+ A Feature-topic mátrix normalizált, mivel a szavak egy témakörnek a feltételes valószínűsége. Ebben az esetben az egyes oszlopok összegének értéke 1.

> [!TIP]
> Esetenként előfordulhat, hogy a modul egy üres témakört ad vissza, amelyet leggyakrabban az algoritmus pszeudo-véletlenszerű inicializálása okoz.  Ha ez történik, megpróbálkozhat a kapcsolódó paraméterek módosításával, például az N-Gram szótár maximális méretével vagy a szolgáltatások kivonatolásához használandó bitek számával.

### <a name="lda-and-topic-modeling"></a>LDA és témakör modellezése

A látens Dirichlet-kiosztást (LDA) gyakran használják a *tartalomon alapuló témakör modellezéséhez*, ami alapvetően a nem besorolt szövegből származó tanulási kategóriákat jelenti. A tartalomon alapuló témakör modellezésében a témakör a szavak eloszlása.

Tegyük fel például, hogy a számos, sok terméket tartalmazó vásárlói felülvizsgálatokat kapott. A sok ügyfél által az idő múlásával elküldött felülvizsgálatok szövege számos kifejezést tartalmaz, amelyek némelyike több témakörben is használatos.

A LDA folyamat által azonosított **témakörök** az a termékre vonatkozó felülvizsgálatokat jelezhetik, vagy a termékek egy csoportját is képviselik. Ahhoz, hogy LDA, a témakör önmagában csak egy valószínűségi eloszlást tartalmaz a szavak egy adott halmaza esetében.

A feltételek kizárólag egyetlen termékre vonatkoznak, de más termékekre is hivatkozhatnak, vagy általános feltételek vonatkoznak rá, amelyek mindenre érvényesek ("nagy", "borzasztó"). Más feltételek lehetnek zaj-szavak.  Fontos azonban megérteni, hogy a LDA metódus nem az univerzumban lévő összes szó rögzítésére, vagy a szavak összefüggésének megismerésére szolgál, eltekintve a közös előfordulási valószínűségtől. Csak a céltartományban használt szavak csoportosítására használható.

Az indexek kiszámítását követően az egyes sorok a távolsági hasonlóság mértékével vannak összehasonlítva annak megállapítására, hogy két szöveg hasonló-e egymáshoz.  Előfordulhat például, hogy úgy találja, hogy a termék több, szorosan korrelált névvel rendelkezik. Vagy előfordulhat, hogy az erősen negatív kifejezések általában egy adott termékhez vannak társítva. A hasonlósági mértéket egyaránt használhatja a kapcsolódó kifejezések azonosításához és javaslatok létrehozásához.

###  <a name="module-parameters"></a>Modul paramétereinek

|Name|Típus|Tartomány|Választható|Alapértelmezett|Leírás|  
|----------|----------|-----------|--------------|-------------|-----------------|  
|Cél oszlop (ok)|Oszlop kijelölése||Kötelező|StringFeature|Cél oszlop neve vagy indexe|  
|A modellhez tartozó témakörök száma|Egész szám|[1; 1000]|Kötelező|5|A dokumentumok terjesztésének modellezése N témákkal|  
|N – gramm|Egész szám|[1; 10]|Kötelező|2|A kivonatolás során generált N-gramm sorrend|  
|Normalizálni|Logikai|Igaz vagy hamis|Kötelező|igaz|A kimenet normalizálása valószínűségek szerint.  Az átalakított adatkészlet P (témakör&#124;dokumentum) lesz, és a szolgáltatás témakörének mátrixa a P (Word&#124;témakör) lesz.|  
|Az összes beállítás megjelenítése|Logikai|Igaz vagy hamis|Kötelező|False (Hamis)|További, a scikit-hoz kapcsolódó paramétereket jelenít meg – online LDA|  
|Rho paraméter|Float|[0.00001; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,01|Témakör korábbi eloszlása|  
|Alfa-paraméter|Float|[0.00001; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,01|Dokumentum korábbi eloszlása|  
|A dokumentumok becsült száma|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|1000|A dokumentumok becsült száma (a total_samples paraméternek felel meg)|  
|A köteg mérete|Egész szám|[1; 1024]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|32|A köteg mérete|  
|A tanulási sebesség frissítési ütemtervében használt iteráció kezdeti értéke|Egész szám|[0; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0|Kezdeti érték, amely a korai iterációk downweights képzési arányát. Megfelel a learning_offset paraméternek|  
|A frissítések során az iterációra alkalmazott teljesítmény|Float|[0.0; 1.0]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|0,5|A tanulási arány szabályozása érdekében a rendszer az iterációk számára alkalmazza a szükséges teljesítményt. Megfelel a learning_decay paraméternek |  
|Betanítási Ismétlések száma|Egész szám|[1; 1024]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|25|Betanítási Ismétlések száma|  
|Ngrams-szótár összeállítása|Logikai|Igaz vagy hamis|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet *nincs* bejelölve.|True (Igaz)|Létrehozza a ngrams szótárát a számítástechnikai LDA előtt. Modellek ellenőrzése és értelmezése hasznos|  
|Ngram-szótár maximális mérete|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **ngrams-összeállítási szótár** értéke TRUE (igaz)|20000|A ngrams szótár maximális mérete Ha a bemeneti tokenek száma meghaladja ezt a méretet, ütközések merülhetnek fel|  
|A szolgáltatás-kivonatoláshoz használandó bitek száma|Egész szám|[1; 31]|Akkor érvényes, ha az **összes beállítás megjelenítése** *jelölőnégyzet nincs bejelölve,* és a **ngrams szótárának létrehozása** hamis.|12|A szolgáltatás-kivonatoláshoz használandó bitek száma| 
|Ngrams-szótár létrehozása a LDA előtt|Logikai|Igaz vagy hamis|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve|True (Igaz)|A LDA előtti ngrams-szótárt hoz létre. Modellek ellenőrzése és értelmezése hasznos|  
|Ngrams maximális száma a szótárban|Egész szám|[1; int. MaxValue|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve, és a **ngrams fordítási szótár** értéke TRUE (igaz).|20000|A szótár maximális mérete Ha a bemeneti tokenek száma meghaladja ezt a méretet, ütközések merülhetnek fel|  
|Kivonatoló bitek száma|Egész szám|[1; 31]|Akkor érvényes, ha a **minden beállítás megjelenítése** jelölőnégyzet be van jelölve, és a **ngrams-összeállítás szótárának** beállítása hamis.|12|A szolgáltatás kivonatolásakor használandó bitek száma|   


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .   
A modulokra jellemző hibák listáját a [tervező kivételei és hibakódai](designer-error-codes.md)című témakörben tekintheti meg.

---
title: A dokumentum elemzése – Azure |} A Microsoft Docs
description: Összegzés és elemzése gyűjteménye, dokumentumok, ideértve például a kifejezés, tanulási, a témakör a modellezési és témakör modell elemzése Azure Machine Learning Workbench használatával hogyan.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 29f493449d48df26919a98452fa7f832d653d45e
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861785"
---
# <a name="document-collection-analysis"></a>Dokumentumgyűjtemény elemzése

Ez a forgatókönyv bemutatja, hogyan összesítése, valamint a dokumentumok, ideértve például a kifejezés, tanulási, a témakör a modellezési és témakör modell elemzése Azure Machine Learning Workbench használatával nagyszámú elemzése. Az Azure Machine Learning Workbench könnyen méretezhető nagyon nagy méretű dokumentum egy dokumentumgyűjteményben regisztrálásához biztosít, és betanítása és finomhangolása a különböző számítási környezeteket, és a helyi számítási az adatelemző virtuális gépek Spark-fürthöz modelljeit mechanizmusokat biztosít. A Jupyter notebooks belül az Azure Machine Learning Workbench által biztosított és egyszerű fejlesztését.

## <a name="link-to-the-gallery-github-repository"></a>A katalógus GitHub-adattárra mutató hivatkozás

A nyilvános GitHub-tárházat a valós forgatókönyvekben a tartalmazza az összes anyag – Kódminták ebben a példában a szükséges:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Áttekintés

A nagy mennyiségű (különösen strukturálatlan szöveges adatokat) gyűjtött minden nap komoly nehézségeket jelenthet az rendezhet, keresése és szövegek hatalmas mennyiségű ismertetése. A dokumentum gyűjtemény elemzése forgatókönyv azt mutatja be egy hatékony és automatizált végpontok közötti munkafolyamat nagy dokumentumgyűjtemény elemzése és az nlp-vel feladat engedélyezése.

Ebben a forgatókönyvben kézbesítette fontosabb elemei a következők:

1. Tanulási dokumentumokból mintájára több szavak kifejezés helyett szerepel.

1. A dokumentum egy dokumentumgyűjteményben bemutatott alapul szolgáló témakörök felderítéséhez.

1. Jelölő dokumentumok szerint a helyi terjesztési.

1. Metódusok rendszerezése, keresés és a dokumentumok a helyi tartalom alapján összefoglalójához bemutatására.

Az ebben a forgatókönyvben bemutatott módszerek különböző kritikus ipari számítási feladatok, például a témakör trendek anomáliadetektálási dokumentum gyűjtemény összegzési és hasonló dokumentum keresési felderítése sikerült engedélyezni. A dokumentum elemzés, például kormányzati jogszabályok, hírek, osztályozásával, vásárlói visszajelzések és tudományos kutatás cikkek számos különböző típusú alkalmazhatók.

A machine learning technikák/ebben a forgatókönyvben használt algoritmy:

1. Szöveg feldolgozása és a tisztítás

1. A kifejezés, tanulási

1. A témakör modellezés

1. Forrásgyűjteményébe összegzése

1. Helyi trendek és rendellenességek észlelése

## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

* Győződjön meg arról, hogy megfelelően telepítette, [Azure Machine Learning Workbench](../service/overview-what-is-azure-ml.md) követve a [telepítése és létrehozási rövid útmutató](../service/quickstart-installation.md).

* Ebben a példában minden számítási környezetben is futhat. Azonban javasoljuk, hogy legalább futtatása a Többmagos gépen 16GB memória, és 5GB szabad lemezterület.

## <a name="create-a-new-workbench-project"></a>Egy új Workbench-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning Workbench alkalmazásban
2.  Az a **projektek** lap, kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** panelen adja meg az információkat az új projekt
4.  Az a **projektsablonok keresése** keresőmezőbe, írja be a "Dokumentumgyűjtemény elemzése", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

Ebben a forgatókönyvben használt adatkészlet tartalmaz szöveg összegzéseket és az Egyesült Államok kongresszusa által végrehajtott jogszabályi műveletek társított metaadatok. Az adatgyűjtés [GovTrack.us](https://www.govtrack.us/), amely nyomon követi az Egyesült Államok kongresszusa tevékenységeit, és segít Sportrajongó részt vesznek a nemzeti jogszabályok folyamatait. A tömeges adatok keresztül letölthető [ezt a hivatkozást](https://www.govtrack.us/data/congress/) manuális parancsfájl, amely nem szerepel ebben a forgatókönyvben használatával. Részletesen ismerteti az adatok letöltéséhez található a [GovTrack API-dokumentáció](https://www.govtrack.us/developers).

### <a name="data-source"></a>Adatforrás

Ebben a forgatókönyvben a nyers adatokat gyűjtött több jogszabályi 1973 bevezetni az Egyesült Államok kongresszusa (javasolt számlák és megoldásaik) által a 2017 június (az a 115th kongresszusok 93rd). Az összegyűjtött adatok JSON formátumban vannak, és számos a jogszabályi műveleteivel kapcsolatos információkat tartalmaz. Tekintse meg [a GitHub-hivatkozás](https://github.com/unitedstates/congress/wiki/bills) adatmezőket részletes leírását. Ebben a forgatókönyvben belül a bemutatási célból datová Pole csak egy részét a JSON-fájlokból könyvtárban találhatók. Egy előre lefordított TSV-fájlt `CongressionalDataAll_Jun_2017.tsv` jogszabályi műveletek rekordokat tartalmazó biztosított ebben a forgatókönyvben. A TSV-fájl letölthető automatikusan ehhez a notebookok vagy `1_Preprocess_Text.ipynb` meg a notebook mappában vagy `preprocessText.py` a Python-csomagban.

### <a name="data-structure"></a>Adatszerkezet

Az adatfájl nincsenek kilenc adatmezőket. Az adatok mezőneveket és a leírások módon vannak felsorolva.

| Mezőnév | Típus | Leírás | Hiányzik az értéket tartalmazza |
|------------|------|-------------|---------------|
| `ID` | Sztring | A számlázási/megoldási azonosítója. Ez a mező formátuma [bill_type] [száma]-[kongresszusa]. Például "hconres1-93" azt jelenti, hogy a számlázási típus: "hconres" (összhangban ház párhuzamosan futó feloldási, tekintse meg [ebben a dokumentumban](https://github.com/unitedstates/congress/wiki/bills#basic-information)), a számla értéke "1"és a kongresszusa szám "93". | Nem |
| `Text` | Sztring | A számlázási/megoldási tartalma. | Nem |
| `Date` | Sztring | Először javasolt a számlázási/megoldási dátuma. "Éééé-hh-nn" formátumban. | Nem |
| `SponsorName` | Sztring | Az elsődleges szponzor, amely a számlázási/felbontás javasolt neve. | Igen |
| `Type` | Sztring | Az elsődleges cím típusú szponzoráljon, vagy a "rep" (képviselő), vagy a "feladó" (senator). | Igen |
| `State` | Sztring | Az elsődleges szponzor állapotát. | Igen |
| `District` | Egész szám | Ha a cím a szponzor képviselő elsődleges szponzor kerület száma. | Igen |
| `Party` | Sztring | Az elsődleges szponzor fél. | Igen |
| `Subjects` | Sztring | A tulajdonos használati összesítve hozzáadta a kongresszusa könyvtár a számlán. A feltételek vannak összefűzött elválasztva. Ezeket a feltételeket a kongresszusa könyvtár a egy emberi által írt, és nem általában jelenik meg, ha a számlázási információkat tesszük közzé. Bármikor hozzáadhatók. Ezért a számla élettartama végén, néhány tulajdonos is többé. | Igen |

## <a name="scenario-structure"></a>A forgatókönyv-struktúra

A dokumentum gyűjtemény elemzési példa diagramnézete két kategóriába vannak rendezve. Az első típus egy sorozatát iPython notebookok azt mutatják be, a teljes munkafolyamat részletes leírása. A második pedig egy Python-csomag, valamint néhány kód példa bemutatja, hogyan használhatja a csomagot. A Python-csomag az általános elég sok használati esetek kiszolgálása érdekében.

Ebben a példában a fájlokat a következőképpen vannak rendszerezve.

| Fájlnév | Típus | Leírás |
|-----------|------|-------------|
| `aml_config` | Mappa | Az Azure Machine Learning Workbench konfigurációt tartalmazó mappa, tekintse meg [ebben a dokumentációban](./experimentation-service-configuration-reference.md) részletes kísérlet végrehajtási konfiguráció |
| `Code` | Mappa | A kód mappa a Python-szkriptek és a Python-csomag mentéséhez használt |
| `Data` | Mappa | A köztes fájlok mentéséhez használt adatok mappa |
| `notebooks` | Mappa | A Jupyter notebookok mappa |
| `Code/documentAnalysis/__init__.py` | Soubor Pythonu | A Python Csomagkezelő init-fájlt |
| `Code/documentAnalysis/configs.py` | Soubor Pythonu | A dokumentum elemzés Python-csomag, beleértve az előre definiált konstansokat is használja a konfigurációs fájl |
| `Code/documentAnalysis/preprocessText.py` | Soubor Pythonu | A Python-fájlt az alárendelt feladatok az adatok előfeldolgozása segítségével |
| `Code/documentAnalysis/phraseLearning.py` | Soubor Pythonu | Ismerje meg az adatokat a mondatok és a nyers adatok átalakítása a Python-fájlt |
| `Code/documentAnalysis/topicModeling.py` | Soubor Pythonu | A témakör segít a rejtett Dirichlet foglalási (LDA) modell betanításához használja a Python-fájlt |
| `Code/step1.py` | Soubor Pythonu | Dokumentumgyűjtemény elemzése 1. lépés: szöveg előfeldolgozása |
| `Code/step2.py` | Soubor Pythonu | Dokumentumgyűjtemény elemzése 2. lépés: tanulási kifejezés |
| `Code/step3.py` | Soubor Pythonu | Dokumentumgyűjtemény elemzése 3. lépés: betanítása és kiértékelése LDA témakör modell |
| `Code/runme.py` | Soubor Pythonu | Példa futtatása lépéseket egyetlen fájlban |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Szöveg előfeldolgozása és tisztázása a nyers adataiból |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Ismerje meg a mondatok szöveg adatokból (után adatátalakítás) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | LDA témakör tanítási |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | A dokumentum egy dokumentumgyűjteményben LDA témakör betanított modell alapján tartalmát összefoglalója |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | Szöveges dokumentumok gyűjteményét nyilvánosfelhő tartalmát elemzése, és vesse össze az adatokat más metaadatok, a dokumentum egy dokumentumgyűjteményben társított nyilvánosfelhő |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | Interaktív vizualizációs megismert témakör modell |
| `notebooks/winprocess.py` | Soubor Pythonu | A python-szkriptet a többprocesszoros notebookok által használt |
| `README.md` | Markdown-fájl | Az információs markdown-fájl |

### <a name="data-ingestion-and-transformation"></a>Adatfeldolgozás és -átalakítási

A lefordított adatkészlet `CongressionalDataAll_Jun_2017.tsv` Blobtárolóba menti a rendszer, és elérhető-e mind a jegyzetfüzetek és a Python-szkriptek belül. Az adatfeldolgozás és -átalakítási két lépésből áll: a szöveges adatok előfeldolgozása és kifejezés, tanulási.

#### <a name="preprocess-text-data"></a>Szöveges adatok előfeldolgozása

Az előfeldolgozási lépés érvényes természetes nyelvi feldolgozási technikákat, a nyers szöveg adatok tisztítása és előkészítése. Egy előzetes felügyeletlen kifejezés, tanulási és a témakör segít a rejtett modellezési funkcionál. A részletes, lépésenkénti leírása megtalálható a notebook `1_Preprocess_Text.ipynb`. Emellett van egy Python-szkriptet `step1.py` Ez a jegyzetfüzet felel meg.

A TSV adatok fájl ebben a lépésben az Azure Blob Storage-ból letöltött és importált, Pandas DataFrame. Az adathalmaz minden egyes sor eleme: szöveg vagy egy dokumentum egyetlen javul hosszú karakterlánc. Minden egyes dokumentum majd szöveg adattömböket, amelyek valószínűleg mondatokat, kifejezéseket vagy subphrases van felosztva. A felosztás célja, hogy a kifejezés, tanulási folyamat a kereszt-mondatban vagy a kereszt-kifejezést a word karakterláncok használatát, amikor a tanulási kifejezések tiltása.

Nincsenek definiálva az előfeldolgozási lépés a jegyzetfüzetet, és a Python-csomag is több függvényt. A feladat a legtöbb ezeket két sornyi kód meghívásával érhető el.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>A kifejezés, tanulási

A kifejezés tanulási lépést a kulcsfontosságú kifejezéseket, így többek között a nagy dokumentumok gyűjteménye, további egy alapvető keretrendszert valósítanak meg. A tanulmány jogosult leírtak "[Multiword kifejezések modellezési kifejezések fa korlátozott a továbbfejlesztett témakör modellezés, természetes nyelvi beszédfelismerés](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", amely eredetileg bemutatták az 2012 IEEE Workshop a beszélt nyelv Technológia. A részletes végrehajtását kifejezés, tanulási lépést jelenik meg az iPython Notebook `2_Phrase_Learning.ipynb` és a Python-szkriptet `step2.py`.

Ebben a lépésben a bemeneti szöveg megtisztítva vesz igénybe, és megtanulja a az legtöbb mintájára kifejezések gyűjteménye, dokumentumok szerepel. A kifejezés learning egy iteratív folyamat, amely három feladatok osztható: n-gramokat száma, potenciális kifejezések a súlyozott Pointwise kölcsönös információkat azok megvalósítani a bennük foglalt szó szerint rangsorolja és újraírási szöveges kifejezést. Ezen három feladatok végrehajtásának egymás után több iterációban mindaddig, amíg az megadott kifejezések megismerte lett.

A dokumentum elemzési Python-csomag, a Python osztály a `PhraseLearner` van definiálva a `phraseLearning.py` fájlt. Alább a kódrészletben használt kifejezések további.

```python
# Instantiate a PhraseLearner and run a configuration
phraseLearner = PhraseLearner(cleanedDataFrame, "CleanedText", numPhrase,
                        maxPhrasePerIter, maxPhraseLength, minInstanceCount)

# The chunks of text in a list
textData = list(phraseLearner.textFrame['LowercaseText'])

# Learn most salient phrases present in a large collection of documents
phraseLearner.RunConfiguration(textData,
            phraseLearner.learnedPhrases,
            addSpace=True,
            writeFile=True,
            num_workers=cpu_count()-1)
```

> [!NOTE]
> A kifejezés tanulási lépést az többprocesszoros valósítja meg. Azonban további Processzormagok tegye **nem** jelenti azt, hogy egy gyorsabb végrehajtási idő. A tesztek a teljesítmény nem növelése többprocesszoros járó többletterhelést miatt legfeljebb nyolc processzormaggal rendelkező. 25 000 kifejezések nyolc processzormaggal (3,6 GHz-es) rendelkező gépen további végrehajtásának nagyjából két és fél óra.
>

### <a name="topic-modeling"></a>A témakör modellezés

A témakör segít a rejtett modell használata tanulási LDA harmadik lépése ebben a forgatókönyvben. A [gensim](https://radimrehurek.com/gensim/) Python-csomag további szükséges ebben a lépésben egy [LDA témakör modell](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Ez a lépés a megfelelő jegyzetfüzet `3_Topic_Model_Training.ipynb`. Emellett olvassa el `step3.py` a dokumentum elemzési csomag számára.

Ebben a lépésben a fő tevékenység, ismerje meg, egy LDA témakör modell, és Finomhangolás a hyper-paraméterek. Nincsenek a több paramétert kell beállítani, hogy mikor train-LDA modell, de a legfontosabb paraméter a témakörök számát. Túl kevés témakörök nem rendelkezik a dokumentum egy dokumentumgyűjteményben; alkalmazásüzemeltetés de túl sok eredményez a "túlterhelt fürtözését" egy korpusz, sok kis méretű, magas hasonló témaköröket. Ebben a forgatókönyvben az a célja, hogy megjelenítése a témakörök számát hangolását. Az Azure Machine Learning Workbench különböző paraméter konfigurációval kísérleteket futtathat a különböző számítási környezeteket szabadon biztosít.

A dokumentum elemzési Python-csomag, néhány funkciók lettek definiálva segítségével a felhasználók döntse el, a témakörök ajánlott számát. Az első megközelítés a következő témakör modell koherencia kiértékelése. Nincsenek a támogatott négy értékelési mátrixok: `u_mass`, `c_v`, `c_uci`, és `c_npmi`. Ezen négy metrika részletei tárgyalja [Ez a tanulmány](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A második megközelítéssel, hogy a perplexity egy tárolt kibővített forrásgyűjteményébe az kiértékeléséhez.

Perplexity értékeléséhez a "U" alakzat görbe várhatóan ismerje meg, hogy a témakörök ajánlott számát. És a töréspont elhelyezése a legjobb választás. A különböző véletlenszám-generálás kezdőértéke többször kiértékelése és az átlagos ajánlott. A koherenciát kiértékelése kell lennie egy "n" formázhatja, ami azt jelenti, hogy a témakörök számának növelésével koherencia növekszik, és csökkentse. Egy példa ábrázolása a perplexity és `c_v` koherencia látható módon.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v koherencia](./media/scenario-document-collection-analysis/c_v_Coherence.png)

Ebben a forgatókönyvben a perplexity nő, jelentősen Miután 200 témaköröket, míg után, valamint a 200 témakörök jelentősen csökkenti a koherencia érték. Alapján azokat diagramok és az általános témaköröket és az fürtözött témakörök fölé, válassza ki, 200 témakörök jó választás lehet.

Az egyik LDA témakör modell egy kísérletben futtatja, vagy a modellek betanítása és kiértékelése több LDA futtatása egyetlen kísérletben másik szám konfigurációval betaníthatja. Egy konfiguráció többször is futtatni, és kérje le az átlagos koherenciát és/vagy perplexity értékelések ajánlott. A dokumentum elemzési csomag részleteinek található `step3.py` fájlt. Egy példa a kódrészlet a következőképpen történik.

```python
topicmodeler = TopicModeler(docs,
        stopWordFile=FUNCTION_WORDS_FILE,
        minWordCount=MIN_WORD_COUNT,
        minDocCount=MIN_DOC_COUNT,
        maxDocFreq=MAX_DOC_FREQ,
        workers=cpu_count()-1,
        numTopics=NUM_TOPICS,
        numIterations=NUM_ITERATIONS,
        passes=NUM_PASSES,
        chunksize=CHUNK_SIZE,
        random_state=RANDOM_STATE,
        test_ratio=test_ratio)

# Train an LDA topic model
lda = topicmodeler.TrainLDA(saveModel=saveModel)

# Evaluate coherence metrics
coherence = topicmodeler.EvaluateCoherence(lda, coherence_types)

# Evaluate perplexity on a held-out corpus
perplex = topicmodeler.EvaluatePerplexity(lda)
```

> [!NOTE]
> A végrehajtás ideje egy LDA témakör modell betanításához több tényező befolyásolja, például korpusz, a hyper-paramétereinek konfigurálása, valamint a gép magjainak számát méretétől függ. Gyorsabb modell használatával több processzormag betanítja. Azonban az azonos hyper paraméterrel több maggal beállítás azt jelenti, hogy kevesebb frissítések betanítás során. Azt javasoljuk, hogy **legalább 100 frissítést egy átszervezett LDA modell betanításához**. Frissítések száma és a hyper-paraméterek közötti kapcsolat a következő cikkben [ebben a bejegyzésben](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) és [ebben a bejegyzésben](http://miningthedetails.com/blog/python/lda/GensimLDA/). A tesztek a 200 témakörök segítségével a konfigurálása egy LDA modell betanításához tartottak körülbelül 3 órás `workers=15`, `passes=10`, `chunksize=1000` 16 mag (2,0 GHz-es) rendelkező gépen.
>

### <a name="topic-summarization-and-analysis"></a>A témakör összefoglaló és -elemzés

A témakör összefoglaló és elemzési áll két jegyzetfüzetet, noha a dokumentum elemzési csomag nem megfelelő függvényeket.

A `4_Topic_Model_Summarization.ipynb`, összefoglalva a dokumentum LDA témakör betanított modell alapján mutatja. Az összegzés alkalmazza a rendszer egy LDA témakör modell megtanult a 3. lépésben. Azt bemutatja, hogyan mérheti a fontos vagy a minőség, a témakör a témakör a dokumentum tisztaságát mérték használata. Ez a mérték tisztaságát feltételezi segít a rejtett témakörök, amelyek szerepelnek a dokumentumok uralja szemantikailag több fontos, mint számos dokumentumok helyezkednek el, amelyek kis mértékben segít a rejtett témakörök. A tanulmány a jelent meg. a fogalom "[segít a rejtett témakör modellezése az Audio Forrásgyűjteményébe összegzési](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Notebook `5_Topic_Model_Analysis.ipynb` bemutatja, hogyan elemezheti a dokumentumok gyűjteményét nyilvánosfelhő tartalmát, és vesse össze az adatokat más metaadatok, a dokumentum egy dokumentumgyűjteményben társított nyilvánosfelhő. Néhány grafikon jelennek meg a jegyzetfüzet segítségével a felhasználók jobban megismerheti a megismert témakört és a dokumentum egy dokumentumgyűjteményben.

Notebook `6_Interactive_Visualization.ipynb` bemutatja, hogyan interaktív módon jelenítheti meg a megismert témakör modell. Ez magában foglalja a négy interaktív vizualizációs feladatokhoz.

## <a name="conclusion"></a>Összegzés

A valós forgatókönyvekben emeli ki, hogyan használható a Well-Known text analytics módszereket használjuk (Ez esetben, a kifejezés, tanulási és a LDA témakör modellezés) egy robusztus modell létrehozásához, és hogyan segíthet az Azure Machine Learning Workbench a modellek teljesítményének nyomon, és zökkenőmentesen tanulók nagyobb méretekben. További részleteket:

* A kifejezés, tanulási és témakör modellezési használatával dolgozza fel a dokumentumok gyűjteményét és a egy robusztus modellt. Ha a dokumentumok gyűjteményét hatalmas, Azure Machine Learning Workbench könnyedén méretezheti be és ki. Emellett a felhasználók rendelkeznek futtasson kísérleteket a különböző számítási környezet egyszerűen az Azure Machine Learning Workbench belül szabadon.

* Az Azure Machine Learning Workbench a két lehetőség részletes módon és Python-szkript futtatásához egy teljes kísérlet jegyzetfüzetek futtatásához biztosít.

* Ismerje meg, a Hyper-finomhangolással témakörök ajánlott számát keresése az Azure Machine Learning Workbench használatával szükséges. Az Azure Machine Learning Workbench segítségével nyomon követheti a modell teljesítményét, és zökkenőmentesen nagyobb méretekben más tanulókkal.

* Az Azure Machine Learning Workbench futtatási előzményeket és megismert modellek is kezelheti. Lehetővé teszi az adatszakértők gyorsan azonosíthatja a legjobban teljesítő modelljeit, és keresse meg a parancsfájlok és létrehozhatja őket a használt adatokat.

## <a name="references"></a>Referencia

* **Zak J. Hazen, Fred Richardson**, [ _korlátozott kifejezések fája Multiword kifejezések modellezési továbbfejlesztett témakör modellezés, természetes nyelvi beszédfelismerési_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Használja a beszélt nyelv technológia Workshop (SLT), 2012 IEEE. IEEE, 2012.

* **Zak J. Hazen**, [ _számára audiotartalmak Forrásgyűjteményébe összefoglaló témakör segít a rejtett modellezési_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). a nemzetközi Speech kommunikációs társítás 12 éves konferencia. 2011.

* **Michael Roder, mindkét Andreas, Alexander Hinneburg**, [ _felfedezése a terület, a témakör koherencia mértékek_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A webes keresés és adatbányászati nyolcadik ACM nemzetközi konferencia eljárást. ACM, 2015.

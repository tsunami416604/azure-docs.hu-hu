---
title: Gyűjtemény Analysis - Azure dokumentum |} Microsoft Docs
description: Hogyan összefoglalója elemzése a dokumentumok, ideértve például kifejezés tanulási modellezési témakör vagy témakör modell elemzés Azure ML munkaterület használatával nagy gyűjteménye.
services: machine-learning
author: kehuan
ms.author: kehuan
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, MicrosoftDocs/mlreview, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 60d65b17d4cbe8a45ff3fb62b06852d7b945e8f1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34832859"
---
# <a name="document-collection-analysis"></a>A dokumentum gyűjtemény elemzés

Ez a forgatókönyv bemutatja, hogyan összegzése és elemezheti a dokumentumok, ideértve például kifejezés tanulási modellezési témakör vagy témakör modell elemzés Azure ML munkaterület használatával nagy gyűjteménye. Az Azure Machine Learning-munkaterület feliratkozott nagyon nagy dokumentumgyűjteményt könnyen méretezési biztosít, és képzése és modellek belül különböző számítási környezeteket, kezdve a helyi számítási az adatok tudományos virtuális gépek Spark-fürt hangolására mechanizmusok szolgál. Könnyű fejlesztés Jupyter notebookok Azure Machine Learning-munkaterületen keresztül valósul meg.

## <a name="link-to-the-gallery-github-repository"></a>A gyűjtemény GitHub-tárházban csatolása

A valós forgatókönyv nyilvános GitHub-tárházban összes anyagok, beleértve a mintakódok, szükséges ehhez a példához tartalmazza:

[https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis](https://github.com/Azure/MachineLearningSamples-DocumentCollectionAnalysis)

## <a name="overview"></a>Áttekintés

A nagy mennyiségű adatot (különösen strukturálatlan szöveg) naponta összegyűjtött jelentős kihívás rendezéséhez, keresése és szövegek hatalmas mennyiségű ismertetése. A dokumentum gyűjtemény elemzés forgatókönyv segít egy hatékony és automatizált végpontok közötti munkafolyamat nagy dokumentumgyűjteményt elemzése és alsóbb rétegbeli NLP feladatok engedélyezése.

A jelen forgatókönyv által szállított fő elemei a következők:

1. Tanulási dokumentumokból mintájára több szavak kifejezés helyett szerepel.

1. A dokumentum gyűjteményben szereplő alapul szolgáló témakörök felderítéséhez.

1. Dokumentumok képviselő helyi elosztásával.

1. Bemutató módszerek rendezéséhez, keresése és összefoglalójához dokumentumok a helyi tartalom alapján.

Az ebben a forgatókönyvben bemutatott eljárások lehetővé teszi számos különféle kritikus ipari terheléshez, például a témakör trendek anomáliadetektálási, a dokumentum gyűjtemény összegzése és a hasonló dokumentum keresési felderítése. A dokumentum elemzés, például a kormányzati jogszabályok, legfrissebb híreket, termékismertetőket, felhasználói visszajelzések és tudományos kutatási cikkek számos különböző típusú alkalmazhatók.

A gépi tanulási technikák/algoritmusok ebben a forgatókönyvben a következők:

1. Szöveg feldolgozása és a tisztítás

1. Kifejezés tanulás

1. A témakör modellezési

1. Corpus összegzése

1. Helyi trendeket és anomáliadetektálás

## <a name="prerequisites"></a>Előfeltételek

Ez a példa futtatásához az Előfeltételek a következők:

* Győződjön meg arról, hogy megfelelően telepítette [Azure Machine Learning-munkaterület](../service/overview-what-is-azure-ml.md) követve a [telepítse, és hozzon létre a gyors üzembe helyezés](../service/quickstart-installation.md).

* Ebben a példában minden számítási környezetben is futhat. Azonban javasoljuk, hogy azt futtatnia a Többmagos virtuális gép legalább 16GB memória, és 5GB szabad lemezterület.

## <a name="create-a-new-workbench-project"></a>Új munkaterület-projekt létrehozása

Hozzon létre egy új projektet, ebben a példában egy sablon használatával:
1.  Nyissa meg az Azure Machine Learning-munkaterület
2.  Az a **projektek** lapján kattintson a **+** aláírásához, és válassza ki **új projekt**
3.  Az a **új projekt létrehozása** ablaktáblán, töltse ki az adatokat az új projekt
4.  Az a **keresési Projektsablonjai** keresési mezőbe, írja be a "Dokumentum gyűjtemény Analysis", és válassza ki a sablont
5.  Kattintson a **Create** (Létrehozás) gombra

## <a name="data-description"></a>Adatok leírása

Az ebben a forgatókönyvben használt adatkészlet szöveg összegzéseket és az Egyesült Államok kongresszusa által végrehajtott minden jogszabályi műveletet társított metaadatot tartalmaz. Az adatgyűjtés [GovTrack.us](https://www.govtrack.us/), amely nyomon követi az Amerikai Egyesült Államokban Kongresszus tevékenységeit, és segít Americans nemzeti jogszabályi folyamataikat részt. A tömeges adatok letölthető keresztül [Ez a hivatkozás](https://www.govtrack.us/data/congress/) manuális parancsfájl, amely nem szerepel ebben a forgatókönyvben használatával. Részletesen ismerteti az adatok letöltése nem található a [GovTrack API-JÁNAK dokumentációja](https://www.govtrack.us/developers/api).

### <a name="data-source"></a>Adatforrás

Ebben a forgatókönyvben a nyers adatokat gyűjtött az Egyesült Államok kongresszusa (javasolt váltók és megoldások) bevezetett 1973 a júniusi 2017 számára jogszabályi műveletsorozat (a 115th kongresszusok 93rd). Az összegyűjtött adatok JSON formátumban van, és a jogszabályi műveletek adatait gazdag készletét tartalmazza. Tekintse meg [a GitHub-hivatkozás](https://github.com/unitedstates/congress/wiki/bills) adatmezők részletes leírását. Ebben a forgatókönyvben belül a bemutató célból adatmezők csak egy részét a JSON-fájlokból könyvtárban találhatók. Egy előre lefordított TSV fájl `CongressionalDataAll_Jun_2017.tsv` jogszabályi műveletet rekordokat tartalmazó valósul meg ebben a forgatókönyvben. A TSV fájl tölthető le automatikusan vagy a notebookok `1_Preprocess_Text.ipynb` a notebook mappában vagy `preprocessText.py` a Python-csomag.

### <a name="data-structure"></a>Adatszerkezet

Az adatfájl kilenc adatmezők szerepelnek. Az adat mezőnevét az neve és leírása az alábbiak szerint.

| Mezőnév | Típus | Leírás | Hiányzó értéket tartalmaz. |
|------------|------|-------------|---------------|
| `ID` | Karakterlánc | A számlázási/megoldás azonosítója. Ez a mező formátuma [bill_type] [szám]-[Kongresszus]. Például "hconres1-93" azt jelenti, hogy a számlázási típusa "hconres" (végre House az egyidejű feloldási, tekintse meg [Ez a dokumentum](https://github.com/unitedstates/congress/wiki/bills#basic-information)), a számlázási értéke "1"és a Kongresszus értéke "93". | Nem |
| `Text` | Karakterlánc | A tartalom a számlázási megoldás. | Nem |
| `Date` | Karakterlánc | A dátum a számlázási/megoldás eredetileg javasolt. "Éééé-hh-nn" formátumban. | Nem |
| `SponsorName` | Karakterlánc | Az elsődleges támogatója, javasolt a számlázási/megoldás neve. | Igen |
| `Type` | Karakterlánc | Az elsődleges cím típusú támogatása, vagy a "rep" (jellemző), vagy a "küldése" (senator). | Igen |
| `State` | Karakterlánc | Az elsődleges megbízó állapotát. | Igen |
| `District` | Egész szám | Az elsődleges támogatója, ha a megbízó neve képviselője körzeti száma. | Igen |
| `Party` | Karakterlánc | Az elsődleges megbízó fél. | Igen |
| `Subjects` | Karakterlánc | A számlázási által a Kongresszus könyvtár összesítve hozzáadott tulajdonos feltételeit. A feltételek vesszővel vannak összefűzendő. Ezeket a feltételeket a HR-részleg a Kongresszus könyvtár a szerint, és még nincsenek általában a számlázási információkat első közzétételekor. Ezt követően bármikor adhatók hozzá. Így egy számlázási élettartama végén, néhány tulajdonos nem lehet többé. | Igen |

## <a name="scenario-structure"></a>A forgatókönyv struktúra

A dokumentum gyűjtemény elemzés példa termékek esetében két típusa van felosztva. Az első típus több iPython jegyzetfüzetek megjelenítése a teljes munkafolyamat részletes leírását. A második típus egy Python-csomag, valamint a kódot néhány olyan, hogy a csomag. A Python-csomag nincs elég általános sok használati esetek kiszolgálására.

Ebben a példában a fájlokat az alábbiak szerint vannak rendszerezve.

| Fájlnév | Típus | Leírás |
|-----------|------|-------------|
| `aml_config` | Mappa | Az Azure Machine Learning-munkaterület konfigurációt tartalmazó mappa, tekintse meg [ebben a dokumentációban](./experimentation-service-configuration-reference.md) részletes kísérlet végrehajtási konfiguráció |
| `Code` | Mappa | A Python-parancsfájlok és a Python-csomag mentéséhez használt kód mappát |
| `Data` | Mappa | A köztes fájlok mentéséhez használt Adatmappa |
| `notebooks` | Mappa | A Jupyter notebookok mappa |
| `Code/documentAnalysis/__init__.py` | Python-fájl | A Python-csomag init fájl |
| `Code/documentAnalysis/configs.py` | Python-fájl | A dokumentum elemzés Python-csomag, beleértve az előre definiált állandók a konfigurációs fájlt használja |
| `Code/documentAnalysis/preprocessText.py` | Python-fájl | A Python fájlt az alárendelt tevékenységek adatok előfeldolgozása |
| `Code/documentAnalysis/phraseLearning.py` | Python-fájl | Ismerje meg, az adatokból kifejezések, és a nyers adatok átalakítására szolgáló Python fájl |
| `Code/documentAnalysis/topicModeling.py` | Python-fájl | A Python-fájl egy rejtett Dirichlet foglalási (LDA) témakör modell betanításához használandó |
| `Code/step1.py` | Python-fájl | A dokumentum gyűjtemény elemzés 1. lépés: szöveg előfeldolgozása |
| `Code/step2.py` | Python-fájl | A dokumentum gyűjtemény elemzés 2. lépés: tanulási kifejezés |
| `Code/step3.py` | Python-fájl | A dokumentum gyűjtemény elemzés 3. lépés: betanítása és kiértékelése LDA témakör modell |
| `Code/runme.py` | Python-fájl | Példa egy fájl összes lépések futtatásával |
| `notebooks/1_Preprocess_Text.ipynb` | iPython Notebook | Szöveg előfeldolgozása és a nyers adatok átalakítása |
| `notebooks/2_Phrase_Learning.ipynb` | iPython Notebook | Ismerje meg a szöveg adatokból kifejezések (után adatátalakítás) |
| `notebooks/3_Topic_Model_Training.ipynb` | iPython Notebook | Tanítási LDA témakör |
| `notebooks/4_Topic_Model_Summarization.ipynb` | iPython Notebook | A dokumentum gyűjtemény LDA témakör modell betanítását alapján tartalmának összefoglalója |
| `notebooks/5_Topic_Model_Analysis.ipynb` | iPython Notebook | A helyi tartalom-gyűjtemény elemeinek szövegfájlok elemzése és egyéb metaadatot, a dokumentum gyűjteményhez társított nyilvánosfelhő adatainak összefüggéseket |
| `notebooks/6_Interactive_Visualization.ipynb` | iPython Notebook | Interaktív képi megjelenítés megismert témakör modell |
| `notebooks/winprocess.py` | Python-fájl | Többprocesszoros feldolgozás notebookok használják a python-parancsfájl |
| `README.md` | Markdown-fájlként | Az információs markdown-fájl |

### <a name="data-ingestion-and-transformation"></a>Adatfeldolgozást és átalakítás

A lefordított dataset `CongressionalDataAll_Jun_2017.tsv` Blobtárolóba menti, és elérhető mind a jegyzetfüzetek és a Python parancsfájlok belül. Az adatfeldolgozást és átalakítására két lépésben történik: a szöveges adatok előfeldolgozása, és tanulás kifejezést.

#### <a name="preprocess-text-data"></a>Szöveges adatok előfeldolgozása

A előfeldolgozási lépés érvényes természetes nyelvű feldolgozási technikák tisztítsa meg és készítse elő a nyers adatokat. E kifejezés tanulási és rejtett témakör modellezési előanyagát funkcionál. A részletes részletes leírása megtalálható a notebook `1_Preprocess_Text.ipynb`. Szerepel továbbá egy Python-parancsfájl `step1.py` felel meg a notebook.

Ebben a lépésben a TSV partíciósadat-fájlnak az Azure Blob Storage-ból letöltött, és egy Pandas DataFrame importálni. Minden egyes a DataFrame sor eleme egy egyetlen javul hosszú szöveges karakterlánc vagy egy "document". Minden egyes dokumentum majd oszlik, amelyek valószínűleg a mondatok, kifejezéseket vagy subphrases adattömbök szöveg. A felosztás célja, hogy a folyamat a kereszt-mondat vagy kereszt-kifejezés word karakterláncok használatát, ha a tanulás kifejezések tanulási kifejezés letiltása.

Nincsenek definiálva a előfeldolgozási lépés, mind a notebook és a Python-csomag több funkció. A legtöbb, a feladat érhető el ezen kódok két sornyi meghívásával.

```python
# Read raw data into a Pandas DataFrame
textDF = getData()

# Write data frame with preprocessed text out to TSV file
cleanedDataFrame = CleanAndSplitText(textDF, saveDF=True)
```

#### <a name="phrase-learning"></a>Kifejezés tanulás

A kifejezés tanulási lépés a legfontosabb kifejezések dokumentumok nagy gyűjteménye között további egy alapvető keretrendszert valósítanak meg. Jogosult a dokumentumban ismertetett "[Multiword kifejezések modellezési kifejezések fa korlátozott továbbfejlesztett témakör modellezési a Conversational beszéd](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf)", amely eredetileg bemutatott a a 2012 IEEE Workshop szóbeli nyelvével Technológia. Kifejezés tanulási lépés részletes végrehajtását látható a Notebook iPython `2_Phrase_Learning.ipynb` és a Python-parancsfájl `step2.py`.

Ebben a lépésben a megtisztított szöveges bemeneti vesz igénybe, és megtanulja a dokumentumok nagy gyűjteménye szerepel a következő kifejezéseket. A kifejezés tanulási, amely három feladatok lehet osztani iteratív folyamat: n-g száma, a súlyozott Pointwise kölcsönös található adatok a bennük foglalt szavak lehetséges kifejezések rangsorolja és újraírási szöveg kifejezést. Ezen három feladatok végrehajtásának egymás után a többszöri mindaddig, amíg az adott kifejezések tudásra lett.

A dokumentum elemzés Python csomagban, a Python osztály `PhraseLearner` definiálva van a `phraseLearning.py` fájlt. Alább a használt kifejezések további kódrészletet.

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
> A kifejezés tanulási lépés többprocesszoros feldolgozás segítségével valósul meg. Azonban további Processzormagok tegye **nem** végrehajtási gyorsabb jelenti. A tesztelés során a nem javul a teljesítmény, legfeljebb nyolc processzormaggal növeli a többprocesszoros feldolgozás miatt. Ismerje meg a virtuális gép nyolc processzormaggal (3,6 GHz-es) 25 000 kifejezések körülbelül két fél órára végrehajtásának.
>

### <a name="topic-modeling"></a>A témakör modellezési

Egy rejtett témakör modell használata tanulási LDA harmadik lépése ebben a forgatókönyvben. A [gensim](https://radimrehurek.com/gensim/) Python-csomag további szükséges ebben a lépésben egy [LDA témakör modell](https://en.wikipedia.org/wiki/Latent_Dirichlet_allocation). Ebben a lépésben a megfelelő jegyzetfüzet `3_Topic_Model_Training.ipynb`. Azt is jelentheti `step3.py` a dokumentum elemzés csomag számára.

Ebben a lépésben egy LDA témakör modell ismerje meg, és a hyper-paraméterek beállításának a fő feladat. Van több paramétert kell hangolni kell amikor az egy LDA modell betanításához, de a legfontosabb paraméter témakörök számát. Túl kevés témakörök nem rendelkezik a dokumentum gyűjteményhez; insight de túl sok jár a "túlzott fürtözését" egy corpus sok alacsony, magas hasonló témakörökre. Ebben a forgatókönyvben célja megjelenítése a témakörök számát hangolását. Az Azure Machine Learning-munkaterület biztosít a szabadsága futtatásra kísérletek eltérő konfigurációjával különböző számítási környezeteket.

A dokumentum elemzés Python csomagban néhány funkciók meghatározott segítségével a felhasználók mérje fel, a témakörök legjobb számát. Az első módszer a témakör modell koherencia kiértékelésével van. Nincsenek a támogatott négy értékelési mátrixok: `u_mass`, `c_v`, `c_uci`, és `c_npmi`. E négy metrika részleteit ismerteti [a dokumentum](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A második megoldás, a birtokolt kibővített corpus a perplexity kiértékeléséhez.

Perplexity értékeléséhez "U" alakzat görbe várhatóan megállapítása a témakörök legjobb számát. És a szögletes pozíciója a legjobb választás. Több alkalommal értékeli a különböző véletlenszám-generálás kezdőértéke, és az átlagos ajánlott. A koherencia értékelje ki kellene lennie a "n" alakul, ami azt jelenti, hogy növelje meg a témakörök a koherencia növekszik, és csökkentse. Egy példa ábrázolása a perplexity és `c_v` koherencia látható módon.

![Perplexity](./media/scenario-document-collection-analysis/Perplexity_Value.png)

![c_v koherencia](./media/scenario-document-collection-analysis/c_v_Coherence.png)

Ebben a forgatókönyvben a perplexity növeli jelentősen után 200 témaköreit, míg után, valamint a 200 témakörök jelentősen csökkenti a koherencia érték. Alapján ezeket a diagramok és az általános témaköröket és az keresztül fürtözött témakörök, válassza a 200 témakörök jó választás lehet.

Egy LDA témakör modell egy kísérletben futtatja, vagy betanítása és kiértékelése különböző témakör számú konfigurációja egy egyetlen kísérlet futtatása több LDA modellek betanítása is. Javasoljuk, hogy több alkalommal futtatása egy konfigurációt, és majd kérje le a átlagos koherencia és/vagy perplexity értékelések. A dokumentum elemzés csomag részleteinek található `step3.py` fájlt. Egy példa kódrészletet a következőképpen történik.

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
> A végrehajtás ideje, egy LDA témakör modell betanításához például a corpus, hyper paraméter konfigurációs, valamint a gépen magok száma több tényezőtől függ. Több CPU-magokat használó betanítja gyorsabb modell. Azonban az azonos hyper paraméterrel több maggal beállítás azt jelenti, hogy kevesebb frissítések betanítás során. Azt javasoljuk, hogy **legalább 100 frissítést egy átszervezett LDA modell betanításához**. A frissítések számát és a hyper-paraméterek közötti kapcsolat ismertet [a feladás egy vagy több](https://groups.google.com/forum/#!topic/gensim/ojySenxQHi4) és [a feladás egy vagy több](http://miningthedetails.com/blog/python/lda/GensimLDA/). A tesztelés során tartott körülbelül 3 órát 200 témakörök konfigurációját használja egy LDA modell betanításához `workers=15`, `passes=10`, `chunksize=1000` a virtuális gép 16 mag (2,0 GHz-es).
>

### <a name="topic-summarization-and-analysis"></a>A témakör összefoglaló és elemzése

A témakör összefoglaló és elemzés áll két notebookok, amíg nem megfelelő funkciók a dokumentum elemzés csomagban vannak.

A `4_Topic_Model_Summarization.ipynb`, azt mutatja be, hogy összesítse a LDA témakör modell betanítását alapján dokumentum tartalmát. Az összegzési a 3. lépésben megjegyzett LDA témakör modellre vonatkozik. Azt illusztrálja, hogyan mérésére a fontos vagy a minőség, a témakör a témakörhöz, és a dokumentum tisztaságát mérték használatával. Ezt a mértéket tisztaságát feltételezi a dokumentumok megjelentek dominálja rejtett témakörök szemantikailag több fontos, mint gyengén rejtett témakörök sok dokumentum elosztva. Ez a dokumentumban bemutatott "[rejtett témakör modellezési a hang Corpus összegzési](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf)."

Notebook `5_Topic_Model_Analysis.ipynb` elemezheti a helyi tartalom-gyűjtemény elemeinek dokumentumok és egyéb metaadatot, a dokumentum gyűjteményhez társított nyilvánosfelhő adatainak összefüggéseket ismerteti. Néhány előkészítésére a biztosíthat a felhasználóknak jobb megértése érdekében a megjegyzett témakör és a dokumentumgyűjtemény jegyzetfüzet be.

Notebook `6_Interactive_Visualization.ipynb` bemutatja, hogyan interaktívan szemléltetheti megismert témakör modell. Ez magában foglalja a négy interaktív képi megjelenítés feladatok.

## <a name="conclusion"></a>Összegzés

A valós forgatókönyvekben emel ki, hogyan a jól ismert szöveg analytics módszereivel (az eset, kifejezést tanulási és LDA témakör modellezési) eredményezett egy robusztus modell, és hogyan Azure Machine Learning munkaterület segítségével nyomon követheti a modell teljesítményét, és zökkenőmentesen nagyobb léptékű tanulókkal. A további részletek:

* Kifejezés tanulási és témakör modellezési használatával feldolgozni a dokumentumok gyűjteménye, és egy robusztus modell létrehozása. Ha a gyűjtemény dokumentumok hatalmas, Azure Machine Learning-munkaterület könnyedén méretezhető azt be, és ki. Emellett a felhasználók rendelkeznek a szabadságot, hogy kísérletek futtatnak különböző számítási környezet egyszerűen az Azure Machine Learning-munkaterületen.

* Az Azure Machine Learning-munkaterület notebookok részletes módon és teljes a kísérlet futtatásához Python-parancsfájl futtatásához mindkét lehetőségeket is kínál.

* Hyper-paraméter hangolása témakörök legjobb számát keresése az Azure Machine Learning-munkaterület használatával további szükséges. Az Azure Machine Learning-munkaterület segítségével nyomon követheti a modell teljesítmény és zökkenőmentesen különböző tanulókkal magasabb méretekben futtatható.

* Az Azure Machine Learning-munkaterület futtatási előzményei és megismert modellek is kezelheti. Ez lehetővé teszi a adatszakértőkön gyorsan azonosíthatja a legjobb teljesítményt modellek, és keresse meg a parancsfájlok és azok létrehozásához használt adatokat.

## <a name="references"></a>Referencia

* **Komócsin J. Hazen, János Richardson**, [ _korlátozott kifejezések fája Multiword kifejezések modellezési továbbfejlesztett témakör modellezési Conversational beszéd_](http://people.csail.mit.edu/hazen/publications/Hazen-SLT-2012.pdf). Nyelvi technológia Workshop (SLT), 2012 IEEE szóbeli. IEEE, 2012.

* **Komócsin J. Hazen**, [ _rejtett témakör modellezési a hang Corpus összegzési_](http://people.csail.mit.edu/hazen/publications/Hazen-Interspeech11.pdf). a nemzetközi beszéd kommunikációs társítás 12. éves konferencia. 2011.

* **Michael Roder, mindkét Andreas, Alexander Hinneburg**, [ _témakör koherencia intézkedések terület felfedezése_](http://svn.aksw.org/papers/2015/WSDM_Topic_Evaluation/public.pdf). A webes keresés és adatbányászat nyolcadik ACM nemzetközi konferencia eljárást. ACM, 2015.

---
title: "Orvosbiológiai entitás felismerés - csapat adatok tudományos folyamat - Azure Machine Learning |} Microsoft Docs"
description: "Egy csoport adatok tudományos folyamat projekt gyors üzembe helyezés orvosbiológiai entitás felismerés Azure Machine Learning munkaterület mély tanulás által."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: bradsev
ms.openlocfilehash: 4e8450cc20718185a3cea02bf8fbb6b97dd91ddb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Team adatok tudományos folyamat (TDSP) sablonnal orvosbiológiai entitás felismerés

Entitás kiolvasásához egy részfeladatnál annak regisztrálása az információk kinyerése (más néven [nevesített-entitás felismerési (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), adattömbösítő entitáshoz, és az entitás azonosítója). A valós forgatókönyv az a célja, hogy jelöljön ki egy bonyolult természetes nyelvű feldolgozása (NLP) feladatot például entitás kibontási strukturálatlan szövegből megoldására Azure Machine Learning-munkaterület használata:

1. Hogyan kell még betanítani Neurális word beágyazásokat egy szöveges corpus készül 18 millió PubMed kivonatok használatával, a modell [Spark Word2Vec megvalósítási](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Hogyan hozhat létre a entitás kinyerés az a GPU-t Azure Data tudományos virtuális gép (GPU DS VM) részletes hosszú rövid távú memória (LSTM) ismétlődő Neurális hálózat modellt az Azure-on.
2. Bemutatják, hogy tartományspecifikus word beágyazásokat modell is outperform általános word beágyazásokat modellek az entitás felismerés feladatban. 
3. Betanítása, és azok Azure Machine Learning munkaterület használatával a részletes tanulási modelljeit szemléltetik.

4. Azt mutatják be az Azure Machine Learning-munkaterületen a következő lehetőségeket:

    * A probléma [Team adatok tudományos folyamat (TDSP) struktúra és sablonok](how-to-use-tdsp-in-azure-ml.md)
    * A projekt függőségek, beleértve a letöltésének és telepítésének automatizált felügyelete
    * A különböző számítási környezeteket Python parancsfájlok végrehajtása
    * Előzményeinek nyomon követését Python-parancsfájl futtatása
    * A távoli Spark feladatok végrehajtásának számítási környezetekben, HDInsight Spark 2.1 fürtök használata
    * Távoli GPU-alapú virtuális gépek Azure-feladatok végrehajtása
    * Egyszerű operationalization mély tanulási modellek webszolgáltatásként Azure tároló szolgáltatás (ACS)

## <a name="use-case-overview"></a>Használja az eset áttekintése
Orvosbiológiai névvel rendelkező entitás egy kritikus fontosságú lépésről összetett orvosbiológiai NLP feladatokhoz, mint: 
* Kibontása a Megjegyzések az elnevezett entitások ilyen betegségek, kábítószerek, chemicals és jelenségek elektronikus orvosi vagy állapotát rögzíti.
* Kábítószerrel felderítése
* Például két gyógyszer interakció kábítószerrel-elleni kapcsolat vagy gén fehérjét kapcsolat típusok különböző entitás közötti interakciókat ismertetése.

A felhasználási forgatókönyve összpontosít hogyan nagy mennyiségű strukturálatlan adatok corpus Medline PubMed kivonatok például elemzése egy word-beágyazás modell betanításához. Majd a kimeneti beágyazásokat számít automatikusan generált funkciók a Neurális entitás kivonatoló képzése érdekében.

Az eredmények megjelenítése, hogy a orvosbiológiai entitás kibontási modell betanítási szolgáltatások beágyazás tartományspecifikus szó outperforms a modell betanítása a szolgáltatás általános típus. A tartományspecifikus modell 7012 entitások megfelelően (9475) kívül észleli az F1-pontszám 0.73 F1-pontszám modell általános 0.61 5274 entitások képest.

Az alábbi ábra az adatok feldolgozása és a modellek betanítása használt architektúráját mutatja be.

![Architektúra](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Adatok leírása

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modell betanítási adatok
Először verzióját letöltöttük a nyers MEDLINE absztrakt adatait [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Az XML-fájlok formájában a nyilvánosan elérhető az adatok a [FTP-kiszolgáló](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Nincsenek 892 XML-fájlok a kiszolgálón, és az XML-fájlok mindegyikén 30 000 cikkek az adatokat. További információt a adatgyűjtési lépés a projekt struktúra szakaszban találhatók. Minden fájl szerepel a mezők 
        
        abstract
        affiliation
        authors
        country 
        delete: boolean if False means paper got updated so you might have two XMLs for the same paper.
        file_name   
        issn_linking    
        journal 
        keywords    
        medline_ta: this is abbreviation of the journal nam 
        mesh_terms: list of MeSH terms  
        nlm_unique_id   
        other_id: Other IDs 
        pmc: Pubmed Central ID  
        pmid: Pubmed ID
        pubdate: Publication date
        title

### <a name="2-lstm-model-training-data"></a>2. LSTM modell betanítási adatok

A Neurális entitás kibontási modell betanítása, és a nyilvánosan elérhető adatkészletek alapján értékeli ki. Ezek az adatkészletek kapcsolatos részletes leírását beszerzéséhez a következő források is hivatkozhat:
 * [Életrajza-entitás felismerés feladata a következő BioNLP/NLPBA 2004.](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR feladat corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - feladat 9.1 kábítószerrel használata)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Az Azure katalógusában GitHub-tárházban csatolása
Az alábbiakban látható a nyilvános GitHub-tárházban, amely tartalmazza a kód valós forgatókönyv hivatkozásra, és részletes leírás: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Előfeltételek 

* Egy Azure [előfizetés](https://azure.microsoft.com/free/)
* Az Azure Machine Learning-munkaterület. Lásd: [a telepítési útmutató](quickstart-installation.md). Az Azure Machine Learning-munkaterület jelenleg csak a következő operációs rendszerek telepíthetők: 
    * Windows 10 vagy Windows Server 2016
    * macOS Sierra (vagy újabb)


### <a name="azure-services"></a>Azure-szolgáltatások
* [HDInsight Spark-fürt](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) verzió Spark 2.1 Linux (HDI 3.6) kibővített számításhoz. Alább MEDLINE kivonatok teljes mennyisége feldolgozni, minimális konfigurációjának szüksége: 
    * Átjárócsomópont: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) mérete
    * Munkavégző csomópontokhoz: legalább 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). A munkahelyi 11 munkavégző csomópontokhoz D12_V2 méretű használtuk.
* [NC6 adatok tudományos virtuális gép (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) méretezett számításhoz.

### <a name="python-packages"></a>Python-csomagokat

A szükséges függőségek a forgatókönyv projekt mappában aml_config/conda_dependencies.yml fájlban vannak definiálva. Az ebben a fájlban definiált függőségek automatikusan létrehozza a kísérletekhez docker, a virtuális gép és a HDI elleni fürt célokat. A Conda környezet fájlformátumot kapcsolatos részletekért tekintse meg a [Itt](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Alapvető utasításokat az Azure Machine Learning (AML) munkaterület
* [Áttekintés](overview-what-is-azure-ml.md)
* [Telepítés](quickstart-installation.md)
* [TDSP használatával](how-to-use-tdsp-in-azure-ml.md)
* [Hogyan fájlok olvasása és írása](how-to-read-write-files.md)
* [Jupyter notebookok használata](how-to-use-jupyter-notebooks.md)
* [GPU használata](how-to-use-gpu.md)

## <a name="scenario-structure"></a>A forgatókönyv struktúra
A forgatókönyvhöz TDSP struktúra és dokumentáció projektsablonjai (1. ábra), amely a következő használjuk a [TDSP életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projekt kapott utasítások alapján jön létre [Itt](./how-to-use-tdsp-in-azure-ml.md).


![Adja meg a projekt adatai](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

A részletes adatelemezési munkafolyamatot a következőképpen történik:

### <a name="1-data-acquisition-and-understanding"></a>1. Adatgyűjtés és adatértelmezés

Lásd: [adatok megszerzését és ismertetése](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

A nyers MEDLINE corpus 27 millió kivonatok összesen rendelkezik, ahol a körülbelül 10 millió cikkek üres absztrakt mezővel rendelkezik. Az Azure HDInsight Spark segítségével dolgozza fel big Data típusú adatok, amelyek nem tölthető be a memóriába, mint egyetlen számítógépen egy [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Először az adatok letölti azokat a Spark-fürt. Az alábbi lépéseket hajtja végre, akkor a [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* elemezni az XML-fájlok használatával Medline XML-elemző
* Az absztrakt szöveg mondat felosztásával, a lexikális elemekké alakításának és a nagybetűk normalizálási előfeldolgozása.
* Zárja ki a cikkeket, ahol absztrakt mező üres, vagy rendelkezik rövid szöveges 
* a word Szójegyzék a képzés kivonatok létrehozása
* a word-beágyazás Neurális modell betanításához. További információkért lásd: [GitHub kód hivatkozás](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) a kezdéshez.


XML-fájlok elemzés után adatok formátuma a következő: 

![Minta](./media/scenario-tdsp-biomedical-recognition/datasample.png)

A Neurális entitás kibontási modell betanítása, és a nyilvánosan elérhető adatkészletek alapján értékeli ki. Ezek az adatkészletek kapcsolatos részletes leírását beszerzéséhez a következő források is hivatkozhat:
 * [Életrajza-entitás felismerés feladata a következő BioNLP/NLPBA 2004.](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [BioCreative V CDR feladat corpus](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - feladat 9.1 kábítószerrel használata)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modellezés

Lásd: [modellezési](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

Modellezési a lépést, ahol megmutatjuk, hogyan használja a saját word beágyazás modell betanítása az előző szakaszban letöltött adatok és a többi alárendelt feladatok használni. Bár a PubMed adatok használjuk, a beágyazásokat létrehozni a feldolgozási folyamat általános, és a más tartományokba word beágyazásokat betanítása használható fel újra. Beágyazásokat kell lennie az adatok pontos ábrázolását fontos, hogy a word2vec be van tanítva, a nagy mennyiségű adat.
A word beágyazásokat készen van, ha azt is betanítása a megjegyzett beágyazásokat inicializálni a beágyazása réteget használó mély Neurális hálózat modell. Azt jelöli, hogy a beágyazási a rétegen nem trainable, de ez nem kötelező. A tanítási modell beágyazás Word felügyeletlen, és így képes kihasználni a címke nélküli szövegek dolgozunk. Azonban az entitásmodell felismerés képzésének felügyelt tanítás során feladat és pontosságát a mennyiségétől és a manuális feliratozva adatok minőségének függ. 


#### <a name="21-feature-generation"></a>2.1. Szolgáltatás létrehozása

Lásd: [generációs funkció](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec felügyeletlen tanulási algoritmus, amely a Neurális hálózat modell egy címke nélküli képzési corpus betanítja beágyazás szó. Képes a folyamatos vektor az összes szó a corpus, amely a szemantikai adatait jelöli. Ezek a modellek egyszerű Neurális hálózatokat és a rejtett rétegben. A word vektorok/beágyazásokat backpropagation és stochastic átmenetes módszeren által meghatározott küszöbértéket. Két különböző word2vec modellek, nevezetesen a Skip Gramot és a folyamatos-tulajdonságcsomag-az-szavak (ellenőrzése [Itt](https://arxiv.org/pdf/1301.3781.pdf) további részletekért). Mivel a word2vec, amely támogatja a Skip-gramot modell, a MLlib végrehajtásának használjuk azt röviden azt itt (címéből veszi a lemezkép [hivatkozás](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Kihagyás Gramot modell](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

A modell hierarchikus Softmax és negatív mintavételi használ a teljesítmény optimalizálása érdekében. Hierarchikus SoftMax (H-SoftMax) bináris fák inspirálta közelítés. H-SoftMax lényegében lecseréli a strukturálatlan SoftMax réteg hierarchikus réteg, amely a szavakat, amikor elhagyja rendelkezik. Ez lehetővé teszi felbontani valószínűségértékének inverzét számítja egy word sorozatát valószínűségi számításoknál használhatja, amely menti, nem kell a költséges normalizálási kiszámításához az összes szó keresztül történő kiszámítása. Mivel egy elosztott terhelésű bináris fa log2 mélységig (|} V |}) (V a szóhasználatának), csak azt kell kiértékelni legfeljebb log2 (|} V |}) Szerezzen be egy szót végső valószínűségét csomópontok. A környezet c megadott word w valószínűségét majd egyszerűen a során a megfelelő valószínűség termék és bal kerül, illetve, hogy a Levélcsomópont vezethet. Azt a Huffman-fa adatkészlet szó a gyakoriság alapján annak érdekében, hogy gyakrabban szó első rövidebb felelősséget hozhat létre. További információkért lásd: [Ez a hivatkozás](http://sebastianruder.com/word-embeddings-softmax/).
A kép forrása [Itt](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Megjelenítés

A word beágyazásokat kell, ha azt szeretné jelenítheti meg őket, és szemantikailag hasonló szavakat közötti kapcsolatot. 

![W2V hasonlóság](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Azt mutatták két különböző módokat a beágyazásokat megjelenítése. Az elsőt a PEM használ a magas dimenzionális vektor a 2-D vektoros projekt. Ennek eredménye jelentős adatvesztéshez és a képi megjelenítés nem legpontosabb. A második pedig a PEM használandó [t nemzeti-Szakértő](https://distill.pub/2016/misread-tsne/). t nemzeti-Szakértő egy lineáris granularitása csökkentési technika, amely jól használható a nagy a többdimenziós adatok beillesztése két vagy három dimenziókat, amelyek megjeleníthetők a szóró rajzot adhatja.  A modellek minden nagy dimenziós objektum egy két vagy three dimensional pont oly módon, hogy az objektumok közeli pontok van modellezve, és eltérő objektumok távoli pontok van modellezve. Úgy működik, két részből áll. Először létrehozza a valószínűségi eloszlás a magasabb dimenzionális területen úgy, hogy hasonló objektumok rendelkeznek az éppen kivételezett nagy valószínűséggel párok keresztül, és eltérő pont első kivételezett alacsony valószínűségét, hogy. Ezután határozza meg a hasonló valószínűségi eloszlás alacsony dimenzionális térképen pontokon keresztül, és minimálisra csökkenti a KL Divergenciát a két terjesztéseket tekintetében helyét a térképen pontok között. A kis dimenzió pontok helyét nyert minimalizálja a KL Divergenciát átmenetes módszeren használatával. De a t-nemzeti Szakértő nem feltétlenül mindig megbízható. Megvalósítás részletei található [Itt](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


A következő ábrán látható, a a t-nemzeti Szakértő képi megjelenítés biztosítja a word vektorok és a potenciális fürtözési kombinációját további elkülönítését. 


* A PEM képi megjelenítés

![PEM](./media/scenario-tdsp-biomedical-recognition/pca.png)

* A t-nemzeti-Szakértő a képi megjelenítés

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* "Kapcsolatos" legközelebbi pontok (azok kapcsolatos összes altípusainak)

![A pontok kapcsolatos legközelebbi](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. A Neurális entitás kivonatoló képzése

Lásd: [betanítása a Neurális entitás kivonatoló](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

Az adatcsatorna-továbbító Neurális hálózat architektúra szenved, hogy azok kezelni az egyes bemeneti és kimeneti, függetlenül a más bemenetekhez és kimenetekhez probléma. Ez az architektúra feladatütemezési-sorozat szalagcímkézési feladatokat, mint a gépi fordítás és entitás kibontási nem modell. Ismétlődő Neurális hálózat modellek kapcsolatos probléma, mivel azok továbbítani az információkat a következő csomópont a most számított. Ez a tulajdonság neve rendelkező memória a hálózaton, mivel korábban számított olvassa el az alábbi ábrán látható módon képes:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Eredeti RNNs ténylegesen érinti a a [átmenetes probléma Távlatpont](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) miatt, amelyek nincsenek ki a korábban látott összes információt. A probléma lesz nyilvánvaló, csak akkor, ha a környezet nagy mennyiségű szükséges előrejelzéshez alakításához. De például LSTM modellek nem érinti a ilyen probléma, valójában a célja, hogy ne feledje a hosszú távú függőségek. Ellentétben, amelyek egyetlen Neurális hálózat RNNs vanília a LSTMs rendelkezik minden cella négy Neurális hálózat közötti kapcsolat. Részletesen ismerteti, hogyan LSTM, tekintse meg a [a feladás egy vagy több](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM Cell](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Most hozzáfoghat saját LSTM-alapú ismétlődő Neurális hálózat próbálja, majd próbálja meg bontsa ki például a kábítószerrel entitástípusok, a mentes és jelenség PubMed adatokból említi. Az első lépés az beszerzése címkézett adatok nagy mennyiségű, és meg kellene rendelkeznie kitalál, mert a rendszer egyszerű! Egészségügyi adatok nagy mennyiségű személy bizalmas információkat tartalmaz, és ezért nincsenek nyilvánosan elérhető. Jelenleg két különböző datasets adatkészletekben nyilvánosan elérhető kombinációja támaszkodnak. Az első adatkészletet Semeval 2013 - feladat 9.1 (kábítószerrel felismerés) származó, a másik BioCreative V CDR feladatból. A Microsoft kombinálásával vannak, és ezek az adatkészletek két címkézését, hogy azt kábítószerek mind orvosi szövegek a betegségek észleli, és értékelje ki a word beágyazásokat automatikus. Megvalósítási leírását, [GitHub kód hivatkozás](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

A modell architektúra, amely azt a kódok és az összehasonlításhoz használja az alábbi táblázat mutatja. Különböző datasets adatkészletekben megváltoztató paraméter a sorozat maximális hossza (Itt 613).

![LSTM modell](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Modell kiértékelése
Lásd: [modell kiértékelése](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

A megosztott feladat a kiértékelési parancsfájlt használjuk [életrajza-entitás felismerés feladata a következő életrajza NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) a pontosság, visszaírási és F1 pontszám modell kiértékelése. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>A-domain és általános word modellek beágyazás

Az alábbiakban egy kétféle szolgáltatás pontosságát összehasonlítása: (1) word beágyazásokat PubMed kivonatok és (2) word beágyazásokat képezni a Google hírek képezni. Jól látható, hogy a tartományban található modell outperforms az általános modell. Ezért ha egy adott szó modell beágyazás helyett egy általános használata sokkal hasznos. 

* #1. feladat: Kábítószerek és betegségek észlelése

![1 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc1.png)

A Microsoft más adatkészletek a hasonló módon végezze el a word beágyazásokat értékelése és az adott tartományban található modell mindig jobb.

* #2. feladat: Fehérjék, cella sor, cella típusa, DNS és RNS észlelése

![2 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* #3. feladat: Chemicals és betegségek észlelése

![3 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* #4. feladat: Kábítószerek észlelése

![4 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* #5. feladat: Gének észlelése

![5 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow és CNTK
A jelentésben szereplő modellek képzett Keras használata TensorFlow néven foghatók össze. CNTK háttérrendszerrel Keras nem támogatja a "visszirányú" végezhető el a munkahelyi időpontjában. Ezért az összehasonlítást, azt a CNTK háttér egyirányú LSTM modell betanítása és azt egy egyirányú LSTM modell TensorFlow háttérrendszerrel képest. A Keras a CNTK 2.0 telepítése [Itt](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![6 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc6.png)

A Microsoft arra a következtetésre jutott, hogy CNTK hajt végre, mint a helyes, mindkét Tensorflow / epoch (60 másodperc CNTK és a Tensorflow 75 másodperc) és a vizsgálati entitások észlelt száma a képzési idő szempontjából. Az egyirányú rétegek értékelésre használjuk.


### <a name="3-deployment"></a>3. Környezet

Lásd: [telepítési](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Helyeztünk üzembe egy webszolgáltatás-bővítmény a fürtön a [Azure tároló szolgáltatás (ACS)](https://azure.microsoft.com/services/container-service/). A operationalization környezet rendelkezések Docker és Kubernetes webszolgáltatás avatkozzon a fürtben. További információ a operationalization folyamatról található [Itt](model-management-service-deploy.md ).


## <a name="conclusion"></a>Összegzés

Hogyan sikerült egy Spark Word2Vec algoritmussal word beágyazási modell betanításához, és ezután használja a kibontott beágyazásokat szolgáltatások egy entitás kiolvasásához mély Neurális hálózat betanítása részleteit keresztül betöltése folyamatban. A orvosbiológiai tartomány rendelkezik alkalmaztunk, a képzési folyamat. A folyamat azonban nem elég általános észlelése egyéni entitástípusok más tartományok alkalmazandó. Csak kevés az adat kell, és könnyen módosíthatja a munkafolyamat itt jelenik meg egy másik tartomány.

## <a name="references"></a>Referencia

* Tomas Mikolov, Kai Chen, Greg Corrado és Jeffrey Dean. 2013a. Word felelősséget a vektoros hatékony becslése. ICLR eljárásban.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado és Jeff Dean. 2013b. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. Az eljárás NIPS lapok: 3111 – 3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen és Sampo Pyysalo. 2016. [Hogyan vonat jó Word beágyazásokat orvosbiológiai NLP](http://aclweb.org/anthology/W/W16/W16-2922.pdf), az eljárás annak a tizenötödik Workshop orvosbiológiai természetes nyelvű feldolgozás, a lapok 166 – 174.
* [Vektoros ábrázolásai szavakat](https://www.tensorflow.org/tutorials/word2vec)
* [Ismétlődő Neurális hálózatokat](https://www.tensorflow.org/tutorials/recurrent)
* [Spark ml Word2Vec észlelt problémákat](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [Spark Word2Vec: szerzett tapasztalatok](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)


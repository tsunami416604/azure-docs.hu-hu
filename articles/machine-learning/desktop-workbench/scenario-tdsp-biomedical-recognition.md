---
title: Orvosbiológiai entitásfelismerés – csoportos adatelemzési folyamat – az Azure Machine Learning |} A Microsoft Docs
description: A csoportos adatelemzési folyamat projekt rövid orvosbiológiai entitásfelismerés, az Azure Machine Learning Workbench a deep learninget használó.
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 0ca8e1081b514d5569c84a6364d55e8f49bee533
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238999"
---
# <a name="biomedical-entity-recognition-using-team-data-science-process-tdsp-template"></a>Orvosbiológiai entitásfelismerés csoportos adatelemzési folyamat (TDSP) sablon használatával

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 



Entitások kinyeréséhez az információk kinyerése alfeladat (más néven [elnevezett entitásfelismerés (NER)](https://en.wikipedia.org/wiki/Named-entity_recognition), darabolás entitás és az entitás azonosítója). A való életből vett forgatókönyv célja, hogy arról, hogy az Azure Machine Learning Workbench használata a természetes nyelvi feldolgozási (NLP) például az entitás kinyerése strukturálatlan szövegből bonyolult feladat megoldásához:

1. Hogyan beágyazásokat modell betanításához egy Neurális szó egy szöveges forrásgyűjteményébe körülbelül 18 millió PubMed kivonatok használatával, a [Spark Word2Vec megvalósítási](https://spark.apache.org/docs/latest/mllib-feature-extraction.html#word2vec).
2. Hogyan hozhat létre egy részletes hosszú rövid távú memória (LSTM) ütemezhetők Neurális hálózat modellt használ az entitások kinyeréséhez egy GPU-kompatibilis Azure Data Science virtuális gépen (GPU DS VM) az Azure-ban.
2. Bemutatják a tartomány-specifikus word beágyazásokat modellek is teljesítményben felülmúlják az általános word beágyazásokat modellek az entitások felismerése feladatban. 
3. Betanítása és üzembe helyezése az Azure Machine Learning Workbench használatával deep learning-modelljeit szemléltetik.

4. Az alábbi képességeket belül az Azure Machine Learning Workbench bemutatása:

    * Példányának [csoportos adatelemzési folyamat (TDSP) szerkezetének és sablonok](how-to-use-tdsp-in-azure-ml.md)
    * A projekt függőségeit, többek között a letöltésének és telepítésének automatikus felügyelete
    * A különböző számítási környezeteket Python-szkriptek végrehajtása
    * Előzményeinek nyomon követését is Python-szkriptek futtatása
    * A távoli Spark-feladatok végrehajtását számítási környezetek, HDInsight Spark 2.1-fürtök használatával
    * Távoli GPU-alapú virtuális gépek Azure-feladatok végrehajtása
    * Az Azure tároló-szolgáltatások (ACS) webszolgáltatásként deep learning-modellek egyszerű operacionalizálás

## <a name="use-case-overview"></a>Használati eset – áttekintés
Orvosbiológiai entitásfelismerés elnevezett el például egy kritikus fontosságú lépésről a összetett orvosbiológiai NLP-feladatokhoz: 
* A nevesített entitásokhoz hírforrásaiból kibontása ilyen betegségek, Drug, chemicals és tünetek elektronikus orvosi és egészségügyi rekordból.
* Az FDA felderítése
* Például két gyógyszer interakciót, az FDA-betegségek kapcsolat és a gene-fehérjék kapcsolat típusai különböző entitások közötti interakciókat ismertetése.

Hogyan nagy mennyiségű strukturálatlan adat forrásgyűjteményébe Medline PubMed kivonatok például egy modell beágyazás szó betanításához elemezhetők a használati eset összpontosít. Ezután a kimeneti beágyazásokat minősül automatikusan létrehozott szolgáltatások egy Neurális entitás információkinyerő betanításához.

Az eredmények megjelenítése, hogy a tartomány-specifikus szó funkciók beágyazása a orvosbiológiai entitás kinyerési modell betanítása outperforms az általános szolgáltatás típusának betanított modell. A tartomány-specifikus modell észlelni tudja 7012 entitások megfelelően (9475) még az általános modell 0.61 F1 – pontszámmal rendelkező 5274 entitások képest 0.73 F1 – pontszámát.

A következő ábrán az adatok feldolgozására, és modelleket taníthat be a használt architektúra látható.

![Architektúra](./media/scenario-tdsp-biomedical-recognition/architecture.png)

## <a name="data-description"></a>Adatok leírása

### <a name="1-word2vec-model-training-data"></a>1. Word2Vec modell betanítási adatok
A nyers MEDLINE absztrakt adatokat az először letöltött [MEDLINE](https://www.nlm.nih.gov/pubs/factsheets/medline.html). Az adatok nyilvánosan elérhető az XML-fájlok formájában azok [FTP-kiszolgáló](https://ftp.ncbi.nlm.nih.gov/pubmed/baseline). Érhetők el 892 XML-fájlok a kiszolgálón, és az XML-fájlok mindegyike 30 000 cikkekben információkat tartalmaz. Az adatgyűjtési lépés további információt a Projektstruktúra szakaszban szerepelnek. Minden egyes fájl szerepel a mezők 
        
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

A Neurális entitás kinyerési modell betanított, és a nyilvánosan elérhető nagy méretű adatkészletek értékeli ki. Ezek az adatkészletek kapcsolatos részletes leírást beszerzéséhez az alábbi források is hivatkozhat:
 * [Bio-entitások felismerése feladata a következő BioNLP/NLPBA 2004.](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [A feladat forrásgyűjteményébe BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - feladat 9.1 (az FDA felismerés)](https://www.cs.york.ac.uk/semeval-2013/task9/)

## <a name="link-to-the-azure-gallery-github-repository"></a>Az Azure-katalógus GitHub-adattárra mutató hivatkozás
Az alábbiakban a a hivatkozásra kattintva a nyilvános GitHub-tárházat a való életből vett forgatókönyv, amely tartalmazza a kódot, és részletes leírása: 

[https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction)


## <a name="prerequisites"></a>Előfeltételek 

* Azure-beli [előfizetés](https://azure.microsoft.com/free/)
* Az Azure Machine Learning Workbench alkalmazásban. Lásd: [telepítési útmutató](quickstart-installation.md). Az Azure Machine Learning Workbench jelenleg csak az alábbi operációs rendszereken telepíthető: 
    * Windows 10 vagy Windows Server 2016
    * macOS Sierra operációs rendszert (vagy újabb)


### <a name="azure-services"></a>Azure-szolgáltatások
* [HDInsight Spark-fürt](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-jupyter-spark-sql) verzió Spark 2.1-es Linux (HDI 3.6) kibővített törölje a számításhoz. A teljes összeget, az alábbiakban tárgyalt MEDLINE kivonatok feldolgozásához, az a minimális konfigurációs van szükség: 
    * Átjárócsomópont: [D13_V2](https://azure.microsoft.com/pricing/details/hdinsight/) mérete
    * Munkavégző csomópontok: legalább 4 [D12_V2](https://azure.microsoft.com/pricing/details/hdinsight/). A munkahelyi 11 munkavégző csomópontok D12_V2 méretű használtuk.
* [NC6 adatelemzési virtuális gép (DSVM)](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-linux-dsvm-intro) vertikális felskálázás törölje a számításhoz.

### <a name="python-packages"></a>Python-csomagok

A szükséges függőségek a aml_config/conda_dependencies.yml fájlt a forgatókönyv projekt mappában vannak definiálva. Ebben a fájlban meghatározott függőségeket automatikusan hozzáférést kapnak a futtatások ellen a docker, a virtuális gép és a HDI-fürt célokat. A Conda-környezet fájlformátumot kapcsolatos részletekért tekintse meg a [Itt](https://conda.io/docs/using/envs.html#create-environment-file-by-hand).

* [TensorFlow](https://www.tensorflow.org/install/)
* [CNTK 2.0](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras)
* [Keras](https://keras.io/#installation)
* NLTK
* Fastparquet

### <a name="basic-instructions-for-azure-machine-learning-aml-workbench"></a>Alapvető utasításokat a Workbench az Azure Machine Learning (AML)
* [Áttekintés](../service/overview-what-is-azure-ml.md)
* [Telepítés](quickstart-installation.md)
* [TDSP használata](how-to-use-tdsp-in-azure-ml.md)
* [Fájlok írása és olvasása](how-to-read-write-files.md)
* [A Jupyter notebookok használata](how-to-use-jupyter-notebooks.md)
* [GPU használata](how-to-use-gpu.md)

## <a name="scenario-structure"></a>A forgatókönyv-struktúra
A forgatókönyvhöz TDSP struktúra és a dokumentáció projektsablonjai (1. ábra), amely a következő használjuk a [TDSP életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md). Projekt létrehozása utasítások alapján [Itt](./how-to-use-tdsp-in-azure-ml.md).


![Projekt információt adhat meg](./media/scenario-tdsp-biomedical-recognition/instantiation-3.png) 

A részletes adatelemzési munkafolyamathoz a következőképpen történik:

### <a name="1-data-acquisition-and-understanding"></a>1. Adatgyűjtés és adatértelmezés

Lásd: [adatok beszerzése és megértése](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md).

A nyers MEDLINE forrásgyűjteményébe 27 millió kivonatok összesen rendelkezik, ahol körülbelül 10 millió cikkek egy üres absztrakt mezőről van. Az Azure HDInsight Spark, amely nem tölthető be a memóriába, mint egyetlen gép big Data típusú adatok feldolgozásához használt egy [Pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html). Először az adatokat, a Spark-fürtön lesz letöltve. A következő lépéseket hajtja végre, akkor a [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html): 
* elemezni az XML-fájlok használatával Medline XML-elemző
* Az absztrakt szövegét, beleértve a mondat felosztása, a jogkivonatok és megkülönbözteti a kis normalizálási előfeldolgozása.
* cikkek, ahol absztrakt mező üres, vagy rendelkezik rövid szöveges kizárása 
* Hozzon létre a word Szójegyzék a betanítási kivonatok
* a szó beágyazás Neurális modell betanításához. További információkért lásd: [GitHub hivatkozás](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/blob/master/code/01_data_acquisition_and_understanding/ReadMe.md) a kezdéshez.


Soubory XML-elemzés, miután adatok formátuma a következő: 

![Adatok minta](./media/scenario-tdsp-biomedical-recognition/datasample.png)

A Neurális entitás kinyerési modell betanított, és a nyilvánosan elérhető nagy méretű adatkészletek értékeli ki. Ezek az adatkészletek kapcsolatos részletes leírást beszerzéséhez az alábbi források is hivatkozhat:
 * [Bio-entitások felismerése feladata a következő BioNLP/NLPBA 2004.](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html)
 * [A feladat forrásgyűjteményébe BioCreative V CDR](http://www.biocreative.org/tasks/biocreative-v/track-3-cdr/)
 * [Semeval 2013 - feladat 9.1 (az FDA felismerés)](https://www.cs.york.ac.uk/semeval-2013/task9/)

### <a name="2-modeling"></a>2. Modellezés

Lásd: [modellezés](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling).

A modellezés éppen a fázis, amelyben bemutatjuk, hogyan használhatja a saját word beágyazás modell betanításához az előző szakaszban letöltött az adatokat, és használhatja más alsóbb rétegbeli tevékenység. Bár a PubMed adatokat használjuk, a folyamat létrehozásához a beágyazásokat általános, és használható fel újra, a más tartományokba szóbeágyazásokból betanításához. Egy pontos adatokat választ, a kívánt beágyazásokat elengedhetetlen, hogy a word2vec be van tanítva, a nagy mennyiségű adat.
Amint rendelkezésünkre áll készen szóbeágyazásokból, hogy a megismert beágyazásokat inicializálása a beágyazása réteget használó Neurális hálózat modell betaníthatja. Azt a beágyazási a rétegen nem trainable megjelölése, de ez nem kötelező. A képzés a modell beágyazás szó felügyeletlen, és ezért vagyunk képes kihasználni a címke nélküli szövegek. Azonban a modell több entitás képzése egy felügyelt tanítás feladatot és annak pontossága attól függ, az összeg és a egy manuális feliratozva adatok minőségét. 


#### <a name="21-feature-generation"></a>2.1. A szolgáltatás létrehozása

Lásd: [generációs funkció](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering).

Word2Vec beágyazás felügyeletlen tanulási algoritmus, amely egy Neurális hálózat modellt egy címke nélküli képzési forrásgyűjteményébe betanítja a szó. A folyamatos vektor az egyes szavak a korpusz, amely a szemantikai információt küld. Ezek a modellek egyszerű Neurális hálózatokat és a egy rejtett rétegben. A vektorok/szóbeágyazásokból visszaterjesztés és sztochasztikus gradiens módszeres küszöbértéket. Word2vec modellek, nevezetesen, a Skip Gram és a folyamatos-tulajdonságcsomag-az-szavakat két típusa van (ellenőrizze [Itt](https://arxiv.org/pdf/1301.3781.pdf) további részletekért). Mivel a word2vec, amelyet a Skip-gram modellt támogat, a MLlib megvalósítását használjuk röviden ismertetünk, itt (származó lemezkép [hivatkozás](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/)): 

![Kihagyás Gram modell](./media/scenario-tdsp-biomedical-recognition/skip-gram.png)

A modell hierarchikus Softmax és negatív mintavételt használ a teljesítmény optimalizálása érdekében. (H-SoftMax) hierarchikus SoftMax egy bináris fák adatközpontoknak előállításához. H-SoftMax lényegében lecseréli az egybesimított SoftMax réteg egy hierarchikus réteget, amely a szavakat, amikor elhagyja rendelkezik. Ez lehetővé teszi számunkra, hogy felbontani sorozatát valószínűségi számításoknál, amely velünk a kapcsolatot a költséges normalizálási kiszámításához keresztül minden szó nem menti azokat egy word valószínűségét kiszámítása. Mivel egy elosztott terhelésű bináris fa egy log2 mélysége (|} V |}) (V a a szöveg Szóhasználati), csak azt kell kiértékelése legfeljebb log2 (|} V |}) Szerezzen be egy szót végső valószínűségét csomópontokat. A környezet c adott word w valószínűségét majd egyszerűen a a valószínűségek megfelelő farmkonfiguráció jobb és bal rendre bekapcsolja a Levélcsomópont okozó körülményekről. Mi a Huffman-fa adatkészlet szó gyakorisága alapján annak érdekében, hogy szavakra gyakoribb rövidebb reprezentációinak hozhat létre. További információkért lásd: [ezt a hivatkozást](http://sebastianruder.com/word-embeddings-softmax/).
Származó lemezkép [Itt](https://ahmedhanibrahim.wordpress.com/2017/04/25/thesis-tutorials-i-understanding-word2vec-for-word-embedding-i/).

##### <a name="visualization"></a>Megjelenítés

Ha már rendelkezünk a szóbeágyazásokból, szeretnénk jelenítheti meg őket, és szemantikailag hasonló szavakat közötti kapcsolatot. 

![W2V hasonlóság](./media/scenario-tdsp-biomedical-recognition/w2v-sim.png)

Azt mutatták két különböző módon a beágyazásokat megjelenítésére. Az elsőt egy PEM használ a magas dimenzionális vektor egy 2 – D vektoros projektre. Ez jelentős adatvesztéshez vezet, és a Vizualizáció nem legpontosabb. A második pedig a PEM-használandó [t nemzeti-Szakértő](https://distill.pub/2016/misread-tsne/). t nemzeti-Szakértő mozgáson alapuló lineáris dimenziói csökkentése, amely kiválóan alkalmas nagy dimenziós adatok beágyazása egy, kettő vagy három dimenziókat, amelyek megjeleníthetők a pontdiagram a terén.  A modellek az egyes magas dimenziós objektumok egy két vagy three dimensional pont oly módon, hogy a hasonló objektumok közeli pontok vannak modellezve, és eltérő objektumok távoli pontok van modellezve. Úgy működik, két részben. Először valószínűségi eloszlását keresztül magasabb dimenzionális rendelkezésre álló helyen úgy, hogy a hasonló objektumokkal rendelkezik felvett, nagy valószínűséggel a párok hoz létre, és eltérő pontok rendelkeznek első kivételezett alacsony valószínűségét. Második alacsony dimenzionális térképen a pontok keresztül hasonló valószínűségi eloszlását meghatározása, és minimálisra csökkenti a KL divergencia helyét a térképen pontok garanciát a két felosztások között. A pontok alacsony dimenzióban lévő helyét nyert minimálisra csökkentik a KL divergencia gradiens módszeres használatával. De a t-nemzeti Szakértő lehetséges, hogy nem mindig megbízható. Megvalósítási részletei találhatók [Itt](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/01_feature_engineering). 


Az alábbi ábrán látható, a t-nemzeti Szakértő vizualizációt még jobban elkülöníti a word vektorok és potenciális fürtözési mintákat biztosít. 


* PEM-Vizualizáció

![PEM](./media/scenario-tdsp-biomedical-recognition/pca.png)

* Vizualizáció-t nemzeti-Szakértő

![t-SNE](./media/scenario-tdsp-biomedical-recognition/tsne.png)

* "Találjanak a rákra" legközelebbi pontok (azok rák összes altípus)

![A rák legközelebbi pontok](./media/scenario-tdsp-biomedical-recognition/nearesttocancer.png)

#### <a name="22-train-the-neural-entity-extractor"></a>2.2. A Neurális entitás információkinyerő betanítása

Lásd: [a Neurális entitás információkinyerő betanításához](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation/ReadMe.md).

A hírcsatorna-továbbító Neurális hálózati architektúra szenved, hogy kezelje az egyes bemeneti és kimeneti, független a többi bemeneteit és kimeneteit egy problémát. Ez az architektúra nem modellje feladatütemezési feladatütemezési címkézési feladatokat, köztük a gépi fordítás és entitások kinyeréséhez. Ismétlődő Neurális hálózat modellek kiküszöbölheti a probléma, azok adhat át adatokat a következő csomópont, most számított. Ez a tulajdonság neve memória kellene a hálózaton, mivel ez a korábban számított adatokat használhatja az alábbi ábrán látható módon:

![RNN](./media/scenario-tdsp-biomedical-recognition/rnn-expanded.png)

Valójában lemeztípusokhoz vanília rnn-EK a [átmenetes probléma Távlatpont](https://en.wikipedia.org/wiki/Vanishing_gradient_problem) miatt, amelyek nem használhatja a korábban látott kapcsolatos összes információt. A probléma válik nyilvánvalóvá, csak akkor, ha a környezet nagy mennyiségű alapuló van szükség. Például LSTM modellek nem érinti a ilyen probléma, valójában tervezték őket, ne felejtse el HOSSZÚTÁVÚ függőségek. Vanília rnn-EK, amelyek egyetlen Neurális hálózat, ellentétben a lstm-EK kell az egyes cellák négy Neurális hálózatok közötti interakciókat. Részletes magyarázatát LSTM működéséről, tekintse meg a [ebben a bejegyzésben](http://colah.github.io/posts/2015-08-Understanding-LSTMs/).

![LSTM cella](./media/scenario-tdsp-biomedical-recognition/lstm-cell.png)

Most próbál meg saját LSTM-alapú ütemezhetők Neurális hálózat felépítését, és próbálkozzon adatokból való kinyeréséhez entitás típusok, pl. Az FDA, betegségek és tünet említései PubMed. Az első lépés az, hogy lekérjük a nagy mennyiségű címkézett adatokkal, és meg kellene rendelkeznie kitalálni, mert ez nem egyszerű! Orvosi adatok nagy mennyiségű információk személy bizalmas adatokat tartalmaz, és ezért nem érhetők el nyilvánosan. A Microsoft két különböző adatkészleteket, amelyek nyilvánosan elérhetők kombinációját támaszkodnak. Az első adatkészletet Semeval 2013 - feladat 9.1 (az FDA felismerés), a másik pedig BioCreative V CDR feladatból. A Microsoft vannak kombinálva, és automatikus címkézés a két adatkészlet, így lehet Drug és orvosi szövegeket a betegségek is észlelni és kiértékelheti a szóbeágyazásokból. A megvalósítás részleteit lásd: [GitHub hivatkozás](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/02_model_creation).

A modell architektúra, hogy minden a kód és az összehasonlításhoz használt alatt jelennek meg. A különböző adatkészletekből megváltoztató paraméter feladatütemezési maximális hossza (Itt 613).

![LSTM-modell](./media/scenario-tdsp-biomedical-recognition/d-a-d-model.png)

#### <a name="23-model-evaluation"></a>2.3. Modell értékelése
Lásd: [minta értékelés](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/02_modeling/03_model_evaluation/ReadMe.md).

A kiértékelés a megosztott feladat szkriptet használjuk [önéletrajz-entitások felismerése feladata a következő önéletrajz NLP/NLPBA 2004](http://www.nactem.ac.uk/tsujii/GENIA/ERtask/report.html) a pontosság, a visszaírási és az F1 pontszám modell kiértékelése. 

#### <a name="in-domain-versus-generic-word-embedding-models"></a>A-domain és a modellek beágyazás általános word

Az alábbiakban a két funkció típus pontosságát összehasonlítását: (1) szóbeágyazásokból PubMed kivonatok és (2) szóbeágyazásokból tanított tanított Google híreket. Jól látható, hogy a tartományban található modell outperforms az általános modell. Ezért kellene egy adott szó modell beágyazása helyett az általános egy következő sokkal nagyobb segítségünkre lesz. 

* #1. feladat: Drug és betegségek észlelése

![1 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc1.png)

A Microsoft egyéb adatkészletekhez a hasonló módon hajthatja végre a szóbeágyazásokból értékelését és az adott tartományban található modell mindig jobb.

* #2. feladat: Fehérjék, cella vonal, típus, DNS és RNS észlelése

![2 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc2.png)

* #3. feladat: Vegyszerek és betegségek észlelése

![3 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc3.png)

* #4. feladat: Drug észlelése

![4 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc4.png)

* #5. feladat: Gének észlelése

![5 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc5.png)

#### <a name="tensorflow-versus-cntk"></a>TensorFlow, CNTK és
A jelentett modellek képzett Keras TensorFlow-háttérrendszer használatával. CNTK-háttérrendszerrel Keras nem támogatja a "visszirányú" ezt a munkát végzett időpontjában. Ezért az összehasonlítást, azt egy egyirányú LSTM modellt CNTK-háttérrendszerrel betanított és képest, a TensorFlow-háttérrendszerrel egyirányú LSTM-modell. A Keras CNTK 2.0 telepítése [Itt](https://docs.microsoft.com/cognitive-toolkit/using-cntk-with-keras). 

![6 modellek összehasonlítása](./media/scenario-tdsp-biomedical-recognition/mc6.png)

A Microsoft arra a következtetésre, hogy működik-e CNTK, jó mindkét Tensorflow, a képzés idő alapidőpont (CNTK és 75 másodperc, a Tensorflow 60 másodperc) és a teszt entitások észlelt számának tekintetében. Az egyirányú rétegek tesztelési használjuk.


### <a name="3-deployment"></a>3. Környezet

Lásd: [üzembe helyezési](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction/tree/master/code/03_deployment).

Az üzembe helyezett webszolgáltatás a fürtön a [Azure Container Service (ACS)](https://azure.microsoft.com/services/container-service/). Az üzembehelyezési környezetet a Docker és a Kubernetes építi ki a fürt kezelése a webszolgáltatás üzembe helyezés. Talál további információkat a operacionalizálás folyamatra vonatkozó [Itt](model-management-service-deploy.md ).


## <a name="conclusion"></a>Összegzés

Mi történt, hogyan, sikerült Word2Vec algoritmus használatával Spark egy word beágyazási modell betanítását, majd a kinyert beágyazásokat lehetőségként entitások kinyeréséhez Neurális hálózat betanítása részleteit keresztül. Mi a betanítási folyamat érvénybe léptette a orvosbiológiai tartományon. A folyamat azonban nem elég általános észleli más tartományok egyéni entitástípusok alkalmazható. Egyszerűen elegendő adat, és egyszerűen módosíthatja az itt bemutatott, egy másik tartomány munkafolyamat.

## <a name="references"></a>Referencia

* Tomas Mikolov, Kai Chen, Greg Corrado és Jeffrey Dean. 2013a. Word semmilyen felelősséget a vektoros hatékony becslésére. ICLR eljárásban.
* Tomas Mikolov, Ilya Sutskever, Kai Chen, Greg S Corrado és Jeff Dean. 2013b. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. NIPS eljárásokban, a lapok 3111 – 3119.
* Billy Chiu, Gamal Crichton, Anna Korhonen és Sampo Pyysalo. 2016. [Hogyan Train jó Szóbeágyazásokból nlp-vel orvosbiológiai](http://aclweb.org/anthology/W/W16/W16-2922.pdf), az eljárás az tizenötödik műhely orvosbiológiai természetes nyelvi feldolgozás, a lapok 166 – 174.
* [Szavak vektor ábrázolása](https://www.tensorflow.org/tutorials/word2vec)
* [Ismétlődő Neurális hálózatokat](https://www.tensorflow.org/tutorials/recurrent)
* [A Spark ml Word2Vec jelentkező problémákat](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/problems-encountered-with-spark-ml-word2vec/)
* [A Spark Word2Vec: tanulságok](https://intothedepthsofdataengineering.wordpress.com/2017/06/26/spark-word2vec-lessons-learned/)


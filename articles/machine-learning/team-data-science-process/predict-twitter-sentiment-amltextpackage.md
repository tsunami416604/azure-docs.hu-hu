---
title: Twitter-különböző vélemények osztályozásával szövegelemzés (AMLPTA) és a csoportos adatelemzési folyamat (TDSP) Azure Machine Learning (AML) csomaggal |} A Microsoft Docs
description: Tdsp-vel (csoportos adatelemzési folyamat) és AMLPTA különböző vélemények osztályozásával használatát ismerteti.
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: 9e5018bc4c7b90897f7f8c91169410284217b172
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45577006"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Twitter-különböző vélemények osztályozásával Azure Machine Learning (AML) csomaggal a szövegelemzés (AMLPTA) és a csoportos adatelemzési folyamat (TDSP)

## <a name="introduction"></a>Bevezetés
Struktúra szabványügyi szervezet és a dokumentáció adatelemzési projektek, azaz egy meghatározott horgonyozva [adattudományi életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), hogy azoknak az hatékony együttműködés megkönnyítése.

Korábban bemutattuk kellett egy [GitHub-tárházat a TDSP projekt felépítése és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Most már lehetővé tettük, példányosítása az Azure Machine Learning-projektek létrehozása [TDSP struktúra és a dokumentáció sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp). A TDSP struktúra és a sablonok használatát az Azure Machine Learning utasításokat [Itt](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

Ebben a példában fogjuk az Azure Machine Learning csomagot a Szövegelemzés és fejleszthet és helyezhet üzembe prediktív modelleket, amelyek különböző vélemények osztályozásával Twitter TDSP használatát mutatja be.


## <a name="use-case"></a>Használati eset
### <a name="twitter-sentiment-polarity-sample"></a>Twitter-vélemények polaritás minta
Ebben a cikkben egy minta megmutatja, hogyan hozható létre, és hajtsa végre a Machine Learning-projektet. A példa a TDSP struktúra és a sablonokat az Azure Machine Learning Workbenchben. A teljes minta megtalálható ez a forgatókönyv. A modellezés feladat tweetek szöveget használatával előrejelzi a vélemények polaritás (pozitív vagy negatív). Ez a cikk ismerteti az útmutatóban ismertetett adatmodellezés feladatok. A forgatókönyv az alábbi feladatokat ismerteti:

1. Az adatok feltárása, a képzés és a egy gépi tanulási modellt az üzembe helyezés, oldja meg a előrejelzési problémát, a használati eset áttekintése itt olvashat. Twitter-vélemények adatok használható ezeket a feladatokat.
2. A TDSP-sablont az Azure Machine Learning használatával a projekthez a projekt végrehajtása. Projekt végrehajtása és a jelentéskészítés a TDSP életciklus szolgál.
3. A megoldás közvetlenül az Azure Container Service-ben az Azure Machine Learning operacionalizálás.

A projekt a Text Analytics csomag az Azure Machine Learning használatát emeli ki.


## <a name="link-to-github-repository"></a>GitHub-adattárra mutató hivatkozás
A GitHub-adattárra mutató hivatkozás [Itt](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Cél
Ez a minta elsődleges célja, hogy bemutatják, hogyan hozható létre, és hajtsa végre a machine learning-projektet az Azure Machine Learning-munkahelyi Bench a csoportos adatelemzési folyamat (TDSP) struktúra és a sablonok használatával. Erre a célra használjuk [Twitter-vélemények adatok](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). A modellezés feladata a vélemény polaritás (pozitív vagy negatív) előrejelzése tweetek szöveget használatával.

### <a name="scope"></a>Hatókör
- Adatfeltárás, a képzés és a egy gépi tanulási modellt, amely a használati eset – áttekintés ismertetett előrejelzési problémájára telepítésének.
- A projekt az Azure Machine Learning az Azure Machine Learning a csoportos adatelemzési folyamat (TDSP) sablon használatával a projekthez tartozó végrehajtását. Projekt végrehajtása és a jelentéskészítési fogjuk használni a TDSP életciklus.
- A megoldás közvetlenül az Azure Container Service szolgáltatásait az Azure Machine Learning operacionalizálás.

A projekt számos funkciót az Azure Machine Learning, az ilyen TDSP struktúra példányosítás és használatát, az Azure Machine Learning munkahelyi Bench kódot és az Azure Container Service a Docker és a Kubernetes használatával egyszerűen operacionalizálás végrehajtási emeli ki.

## <a name="team-data-science-process-tds"></a>Csoportos adatelemzési folyamat (TDS)
A TDSP projekt struktúra és a dokumentáció sablonok hajtsa végre ezt a mintát használjuk. Ebből az következik a [TDSP életciklus](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). A projekt létrejött a biztonsági utasítások alapján [Itt](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Használati eset – áttekintés
A feladat, hogy előre jelezni az egyes twitter sentiment bináris polaritás twitter szöveg kinyert word beágyazásokat szolgáltatások használatával. Részletes leírásáért tekintse meg ezt [tárház](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Adatgyűjtés és adatértelmezés](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Ebben a példában az első lépés, hogy töltse le a sentiment140 adatkészlet és képeznek a tanítási és adatkészletek teszteléséhez. Sentiment140 adatkészlet tartalmazza a tényleges tartalom a tweet (hangulatjelek eltávolítva) együtt a tweet polaritásának (negatív = 0, pozitív = 4), az eltávolított semleges tweeteket. Az eredményül kapott betanítási adatok 1.3 millió sorral rendelkezik és a tesztelés adatok 320 k sorok.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modellezés](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Ez a rész a minta további három alrészének oszlik: 
- **Jellemzőkiemelés** felel meg a funkciók különböző word algoritmus, nevezetesen Word2Vec beágyazás generációja. 
- **Létrehozását modellkezelési** különböző modell a betanítási vonatkozik, például _logisztikai regressziós_ és _átmenetes kiemelési_ előre jelezni a bemeneti szöveg a róluk szóló véleményeket. 
- **Minta értékelés** a betanított modell vonatkozik a tesztelési adatokon.

#### <a name="feature-engineering"></a>Jellemzőkiemelés
Használjuk a <b>Word2Vec</b> szóbeágyazásokból létrehozásához. Először használjuk a Word2Vec algoritmus Skipgram módban a cikkben leírtak szerint [Mikolov, Tomas, és mások. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. A Neurális adatfeldolgozó rendszerek fejlődésére. 2013.](https://arxiv.org/abs/1310.4546) szóbeágyazásokból létrehozásához.

Kihagyás-gram egy sekély Neurális hálózat tart a cél szó egy bemenetként egy gyakran használt adatok vektor kódolása és előrejelzése közeli szavak használatával. Ha V a szószedet mérete, akkor a kimenete rétegen mérete lenne __C * V__ C az a környezet ablak méretének. A skip gramm-alapú architektúra az alábbi ábrán látható.
 
<table class="image" align="center">
<caption align="bottom">Kihagyás-gram modell</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

A részletek a Word2Vec algoritmus és skip-gram modell ezt a mintát hatókörén kívül esik, és az érdekelt olvasónak az igényelt halad át az alábbi hivatkozások további részletekért. A kód 02_A_Word2Vec.py hivatkozott [tensorflow-példák](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Szavak vektor ábrázolása](https://www.tensorflow.org/tutorials/word2vec)
* [Pontosan hogyan működik a word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Megjegyzés a kötegműveletekkel kapcsolatban negatív mintavételi és a zaj Contrastive becslés](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

Után a betanítási folyamat befejeződik, két beágyazási TSV-formátumú fájlok jönnek létre a modellezési szakaszra.

#### <a name="model-training"></a>Modell betanítása
A word vektorok SSWE vagy Word2vec algoritmus használatával jött létre, ha a következő lépés az tényleges vélemények polaritás megjósolni a besorolási modelleket taníthat be. A két típusú funkciók alkalmazunk: Word2Vec és két modellekbe SSWE: logisztikai regressziós és Konvolúciós Neurális hálózat (CNN). 

#### <a name="model-evaluation"></a>Modell értékelése
Kód betölteni és kiértékelheti a tesztelési adathalmazon több betanított modellek biztosítunk.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Üzembe helyezés](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Ez a rész utasításait a mutatók való üzembe helyezése egy előre betanított vélemények előrejelzési modell egy web Service-fürtön az Azure Container Service (AKS) biztosítunk. Az üzembehelyezési környezetet a Docker és a Kubernetes építi ki a fürt kezelése a webszolgáltatás üzembe helyezés. Talál további információkat a operacionalizálás folyamatot [Itt](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Összegzés
Hogy végighaladt-e a részletek hogyan Word2Vec használó word beágyazási modell betanításához, majd a kinyert beágyazásokat lehetőségként előre jelezni a véleménypontszámot Twitter szöveges adatok két különböző modelleket taníthat be. Ezek a modellek egyik helyezünk üzembe az Azure Container Services (AKS). 

## <a name="next-steps"></a>További lépések
A további dokumentáció elolvasása [Azure Machine Learning-csomagot a Text Analytics (AMLPTA)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) és [csoportos adatelemzési folyamat (TDSP)](https://aka.ms/tdsp) a kezdéshez.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Csoportos adatelemzési folyamat (TDSP) használata az Azure Machine Learningben](https://aka.ms/how-to-use-tdsp-in-aml)
* [TDSP projekt sablont az Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML munkahelyi Bench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas, és mások. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. A Neurális adatfeldolgozó rendszerek fejlődésére. 2013.](https://arxiv.org/abs/1310.4546)

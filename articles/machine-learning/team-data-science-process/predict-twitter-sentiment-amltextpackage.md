---
title: Szövegelemzések (AMLPTA) és a Team adatok tudományos folyamat (TDSP) az Azure Machine Learning (AML) csomag véleményeket besorolás Twitter |} Microsoft Docs
description: A céggel kapcsolatos véleményeket besorolás TDSP (csapat az tudományos folyamata) és AMLPTA használatát ismerteti
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
ms.openlocfilehash: 559af47bcf41cd6af59f8ba1b27ff8e64e849925
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36296168"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Twitter véleményeket besorolás Azure Machine Learning (AML) csomag szövegelemzések (AMLPTA) és a Team adatok tudományos folyamat (TDSP)

## <a name="introduction"></a>Bevezetés
Struktúra szabványügyi és dokumentáció található adatok tudományos projektek, azaz egy meghatározott horgonyozva [adatok tudományos életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), kulcsa adatok tudományos csoportban hatékony együttműködés megkönnyítése.

Korábban rendelkezett megjelent egy [GitHub-tárházban TDSP szerkezetének és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Most már engedélyezte a azt létrehozása az Azure Machine Learning-projektek, amely a létrehozásának [TDSP struktúra és dokumentáció található sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp). Az Azure Machine Learning TDSP struktúra és a sablonok használatával utasításai [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

Ez a példa fogjuk az Azure Machine Learning csomag Szövegelemzések és fejlesztésére és üzembe prediktív modelleket, a Twitter véleményeket besorolás TDSP használati bemutatása.


## <a name="use-case"></a>Használati eset
### <a name="twitter-sentiment-polarity-sample"></a>Twitter véleményeket polaritás minta
Ez a cikk egy minta megmutatja, hogyan hozható létre, és hajtsa végre a Machine Learning projekt használja. A minta Azure Machine Learning munkaterület TDSP struktúra és sablonok használja. A teljes minta a forgatókönyv valósul meg. A modellezési feladat előrejelzi a céggel kapcsolatos véleményeket polaritás (pozitív vagy negatív) Twitter-üzeneteket szöveget használatával. Ez a cikk ismerteti az adatok-modellezési feladatokat, a bemutató ismerteti. A forgatókönyv leírja a következő feladatokat:

1. Az adatok feltárása, a képzési és a központi telepítés tartalmazó gépi tanulási modell, amely oldja meg a előrejelzés problémát, a nagybetűk használata áttekintése itt olvashat. Twitter véleményeket adatok a feladatokhoz használt.
2. Ebben a projektben a TDSP sablon az Azure Machine Learning segítségével a projekt végrehajtásának. Projekt végrehajtási és jelentésére a TDSP életciklus szolgál.
3. A megoldás közvetlenül az Azure Machine Learning az Azure Tárolószolgáltatásban operationalization.

A projekt szöveg Analytics csomag az Azure Machine Learning-használatát mutatja be.


## <a name="link-to-github-repository"></a>GitHub-tárházban csatolása
A GitHub-tárházban az mutató hivatkozás [Itt](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Cél
Ez a minta elsődleges célja a bemutatják, hogyan hozható létre, és hajtsa végre a machine learning-projektet a csapat adatok tudományos folyamat (TDSP) struktúra és sablonok használata az Azure Machine Learning munkahelyi többszemélyes. Erre a célra használjuk [véleményeket Twitter data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). A modellezési feladata a céggel kapcsolatos véleményeket polaritás (pozitív vagy negatív) előrejelzése Twitter-üzeneteket szöveget használ.

### <a name="scope"></a>Hatókör
- Az adatok feltárása, képzés és a gépi tanulási modell, amely ismerteti a használható Case áttekintése előrejelzés problémájára központi telepítését.
- Az Azure Machine Learning-sablonnal a csapat adatok tudományos folyamat (TDSP) az Azure Machine Learning ebben a projektben a projekt végrehajtásának. A projekt végrehajtása és a reporting fogjuk használni a TDSP életciklus.
- A megoldás közvetlenül az Azure Machine Learning Azure tároló szolgáltatásokban operationalization.

A projekt több funkciót az Azure Machine Learning, ilyen TDSP struktúra példányának létrehozásánál és használatra, az Azure Machine Learning munkahelyi többszemélyes kódot és az Azure-tároló szolgáltatások Docker és Kubernetes segítségével könnyen operationalization végrehajtását mutatja be.

## <a name="team-data-science-process-tds"></a>Team adatok tudományos folyamat (TDS)
A TDSP struktúra és dokumentáció projektsablonjai végrehajtani ezt a mintát használjuk. Ez azt jelenti a [TDSP életciklus](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/lifecycle). A projekt kapott utasítások alapján jön létre [Itt](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Használja az eset áttekintése
Előre jelezni az egyes twitter véleményeket bináris polaritás twitter szöveg kinyert word beágyazásokat funkciókat használ, akkor a feladatot. A részletes leírásáért tekintse meg a [tárház](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Adatok megszerzését és ismertetése](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Ez a példa első lépése, hogy töltse le a sentiment140 dataset és képeznek a tanítási és adatkészletek tesztelése. Sentiment140 dataset tartalmazza a tweetet tényleges tartalmat (az eltávolított hangulatjelek) és az egyes a tweetet polaritás (negatív = 0, pozitív = 4), valamint az eltávolított semleges Twitter-üzeneteket. Az eredményül kapott betanítási adatok 1.3 millió sort foglalnak és 320 KB-os sorok adatok tesztelés rendelkezik.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modellezési](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Ez a minta része további három alrészének osztható: 
- **Jellemzőkiemelés** megfelel-e funkciók algoritmus, nevezetesen Word2Vec beágyazás különböző word generációját. 
- **A modell létrehozása** különböző modell a tanítási vonatkozik, például _logisztikai regresszió_ és _átmenetes kiemelése_ előre jelezni a bemeneti szöveg céggel kapcsolatos véleményeket. 
- **A modell kiértékelése** alkalmazza a betanított modell vizsgálati adatok felett.

#### <a name="feature-engineering"></a>Jellemzőkiemelés
Használjuk <b>Word2Vec</b> word beágyazásokat létrehozásához. Először használjuk a Word2Vec algoritmus Skipgram módban a dokumentumban leírtak szerint [Mikolov, Tomas, és mások. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. Neurális adatfeldolgozó rendszerek fejlesztéseivel. 2013.](https://arxiv.org/abs/1310.4546) word beágyazásokat létrehozásához.

Kihagyás-gramot véve a cél word bemenetként egy gyakran használt adatok vektoros kódolva, és előre jelezni közelben szavak használatával sekély Neurális hálózat. Ha V a szóhasználatának méretét, akkor a kimenet réteg méretének __C * V__ C az a környezet ablak mérete. A skip gramot alapú architektúra az alábbi ábrán látható.
 
<table class="image" align="center">
<caption align="bottom">Kihagyás-gramot modell</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

A részletek a Word2Vec algoritmus és a skip-gramot modell Ez a minta terjed, és az érdekelt olvasók igényelt halad át az alábbi hivatkozások további részletekért. A hivatkozott kód 02_A_Word2Vec.py [tensorflow példák](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Szavak vektoros ábrázolása](https://www.tensorflow.org/tutorials/word2vec)
* [Pontosan hogyan működik a word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
* [Tudnivalók a zaj Contrastive becslés és negatív mintavétel](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)

A képzési folyamat után TSV formátumban két beágyazási fájlok jönnek létre a modellezési. szakaszra vonatkozóan.

#### <a name="model-training"></a>modell betanítási
A word vektorok SSWE vagy Word2vec algoritmus használatával hozták létre, ha a következő lépés a tényleges véleményeket polaritás előre jelezni a besorolási modell betanításához. A két típusú szolgáltatások érvénybe lépni: Word2Vec és a két modell SSWE: logisztikai regresszió és Convolutional Neurális hálózatokat (CNN). 

#### <a name="model-evaluation"></a>Modell kiértékelése
Kód nyújtunk betölteni, és több betanított modellek számára az adatok TesztKészlet kiértékeléséhez.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Üzembe helyezés](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Ez a kijelző azok egy előre képzett véleményeket előrejelzési modell webszolgáltatásba az Azure tároló szolgáltatás (AKS) fürtön való nyújtunk utasításokat mutató hivatkozások. A operationalization környezet rendelkezések Docker és Kubernetes webszolgáltatás avatkozzon a fürtben. Tekinthet meg további információkat a operationalization folyamathoz [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Összegzés
Betöltése folyamatban van a részletek keresztül történő használatával Word2Vec word beágyazási modell betanításához majd használják, a kibontott beágyazásokat szolgáltatások előre jelezni a céggel kapcsolatos véleményeket pontszám Twitter szöveg adatok két különböző modell betanításához. Ezek a modellek közül a rendszer Azure tároló szolgáltatás (AKS). 

## <a name="next-steps"></a>További lépések
Olvassa el a további dokumentációt a [Azure Machine Learning csomag a szöveg Analytics (AMLPTA)](https://docs.microsoft.com/en-us/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) és [Team adatok tudományos folyamat (TDSP)](https://aka.ms/tdsp) a kezdéshez.

## <a name="references"></a>Referencia
* [Csoportos adatelemzési folyamat](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Az Azure Machine Learning Team adatok tudományos folyamat (TDSP) használata](https://aka.ms/how-to-use-tdsp-in-aml)
* [Az Azure Machine Learning TDSP projektsablon](https://aka.ms/tdspamlgithubrepo)
* [Az Azure ML munkahelyi többszemélyes](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [Mikolov, Tomas, és mások. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. Neurális adatfeldolgozó rendszerek fejlesztéseivel. 2013.](https://arxiv.org/abs/1310.4546)

---
title: A word beágyazásokat Twitter sentiment előrejelzése az Azure-ban az Team tudományos folyamat használatával |} Microsoft Docs
description: A adattudomány projektek végrehajtásához szükséges lépéseket.
services: machine-learning
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev
ms.openlocfilehash: 47c8b9c9d364a7959066132a873b93c2bb001e26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>A word beágyazásokat Twitter sentiment előrejelzése az Team tudományos folyamat használatával

Ez a cikk bemutatja, hogyan használatával hatékonyan dolgozhatnak a _Word2Vec_ algoritmus beágyazás word és a _véleményeket-specifikus Word beágyazás (SSWE)_ becsülhető, a Twitter sentiment algoritmus [Azure Machine Learning](../service/index.yml). Twitter véleményeket polaritás előrejelzésére további információkért tekintse meg a [MachineLearningSamples-TwitterSentimentPrediction tárház](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) a Githubon. A kulcsot, adattudomány projektek hatékony együttműködést elősegítő normalizálnia felépítését és egy meghatározott adattudomány életciklusával projektek dokumentációját. A [Team adatok tudományos folyamat (TDSP)](overview.md) biztosít az ilyen típusú strukturált [életciklus](lifecycle.md). 

Adattudomány projektek létrehozása a _TDSP sablon_ a szabványos keretet biztosít az Azure Machine Learning projektek. Korábban, a TDSP csoport, amely egy [GitHub-tárházban TDSP szerkezetének és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Most példánya létre, a Machine Learning projektek [TDSP sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp) engedélyezve vannak. Útmutatásért lásd: használata [TDSP struktúra projektek TDSP sablonnal](../desktop-workbench/how-to-use-tdsp-in-azure-ml.md) az Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Twitter véleményeket polaritás minta

Ez a cikk egy minta megmutatja, hogyan hozható létre, és hajtsa végre a Machine Learning projekt használja. A minta Azure Machine Learning munkaterület TDSP struktúra és sablonok használja. A teljes minta foglalt [Ez a forgatókönyv](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). A modellezési feladat előrejelzi a céggel kapcsolatos véleményeket polaritás (pozitív vagy negatív) Twitter-üzeneteket szöveget használatával. Ez a cikk ismerteti az adatok-modellezési feladatokat, a bemutató ismerteti. A forgatókönyv leírja a következő feladatokat:

- Az adatok feltárása, a képzési és a központi telepítés tartalmazó gépi tanulási modell, amely oldja meg a előrejelzés problémát, a nagybetűk használata áttekintése itt olvashat. [Twitter véleményeket adatok](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) a feladatokhoz használt.
- Ebben a projektben a TDSP sablon az Azure Machine Learning segítségével a projekt végrehajtásának. Projekt végrehajtási és jelentésére a TDSP életciklus szolgál.
- A megoldás közvetlenül az Azure Machine Learning az Azure Tárolószolgáltatásban operationalization.

A projekt emel ki, az alábbi szolgáltatások az Azure Machine Learning:

- Példánylétrehozás és a TDSP structure használatát.
- Az Azure Machine Learning-munkaterület kód végrehajtása.
- Könnyen operationalization az a Tárolószolgáltatás a Docker és Kubernetes használatával.

## <a name="team-data-science-process"></a>Csoportos adatelemzési folyamat
Ez a minta hajtható végre, az Azure Machine Learning munkaterület TDSP projekt struktúra és dokumentáció sablonokat kell használni. A minta valósítja meg a [TDSP életciklus](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), az alábbi ábrán látható módon:

![TDSP életciklusa](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

A TDSP projekt létrehozása az Azure Machine Learning munkaterület alapján [ezeket az utasításokat](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), az alábbi ábrán látható módon:

![TDSP Azure Machine Learning-munkaterület létrehozása](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Adatok megszerzését és előkészítése](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Ez a példa első lépése, hogy töltse le a sentiment140 dataset és az adatok felosztani a modell betanítására és tesztelésére adatkészletek. A sentiment140 az adatkészletben a tweetet tényleges tartalmat (az eltávolított hangulatjelek). A dataset is tartalmaz minden tweetet polaritásának (negatív = 0, pozitív = 4) a semleges Twitter-üzeneteket az eltávolítása. Az adatok felosztásának, miután a betanítási adatok 1.3 millió sort foglalnak pedig a tesztelési adatokat 320,000 sorokat.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Modell fejlesztési](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

A minta a következő lépése, hogy az adatok a modellezése. A modellezési feladat három részre oszlik:

- Jellemzőkiemelés: funkciók a modell létrehozása algoritmusok beágyazás különböző word használatával. 
- A modell létrehozása: a bemeneti szöveg véleményeket becsülhető különböző modell betanításához. Ezek a modellek például _logisztikai regresszió_ és _átmenetes kiemelése_.
- A modell kiértékelése: a vizsgálati adatok a betanított modell kiértékelése.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[A szolgáltatás műszaki osztály](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word beágyazásokat létrehozásához a Word2Vec és SSWE algoritmust használja. 


#### <a name="word2vec-algorithm"></a>Word2Vec algoritmus

A Skip-Gramot modellben a Word2Vec algoritmust használnak. Ez a modell esetén, tekintse meg a dokumentum által Tomas Mikolov, és mások. "[Elosztott szavak és kifejezések és azok Compositionality ábrázolásai. Neurális adatfeldolgozó rendszerek fejlesztéseivel. ](https://arxiv.org/abs/1310.4546)" 2013.

A Skip-Gramot modell egy sekély Neurális hálózat. A bemeneti érték a cél szó, amely egy egy közbeni vektor, előre jelezni közelben szavak használt kódolása. Ha **V** van a szóhasználatának méretét, majd a kimeneti réteg mérete __C * V__ C az a környezet ablak mérete. Az alábbi ábrán láthatók az architektúra, amely a Skip-Gramot modellen alapul:

![Kihagyás-Gramot modell](./media/predict-twitter-sentiment/skip-gram-model.PNG)

A részletes beállítás esetén a Word2Vec algoritmus és a Skip-Gramot modell nem térnek a minta tulajdonságairól. További információkért tekintse meg a következő hivatkozásokat:

- [A hivatkozott TensorFlow példák 02_A_Word2Vec.PY kódot](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Vektoros ábrázolásai szavakat](https://www.tensorflow.org/tutorials/word2vec)
- [Pontosan hogyan működik a word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Tudnivalók a zaj Contrastive becslés és negatív mintavétel](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmus véleményeket-specifikus Word-beágyazás
A SSWE algoritmus megpróbálja kapcsolatos egyik támadható gyenge pontja, amely hasonló környezetekkel, illetve az ellentétes polaritás hasonló word vektorok is rendelkezhet Word2Vec algoritmus. A közös vonásai okozhat a Word2Vec algoritmus nem felel meg pontosan a végrehajtásához feladatokat, mint a céggel kapcsolatos véleményeket elemzés. A SSWE algoritmus megpróbálja kezelni a gyenge pontot által a mondatok polaritás, mind a word környezetben beépítése az adatvesztés funkciókat.

A minta egy variant SSWE algoritmus a következőképpen használja:

- Az eredeti _ngram_ és a sérült _ngram_ bemeneteként szolgálnak.
- A szemantikai elvesztését és szintaktikai elvesztésével stílus ajtópántjának adatvesztés függvény prioritás szolgál.
- A végső adatvesztés függvény szemantikai elvesztését és szintaktikai elvesztésével súlyozott kombinációja.
- Az egyszerűség kedvéért csak a szemantikai közötti entrópia lesz a veszteség függvény.

A minta mutatja, hogy még funkcióval egyszerűbb adatvesztés, a teljesítményét a SSWE beágyazás jobb, mint a Word2Vec beágyazás. Az alábbi ábrán láthatók a céggel kapcsolatos véleményeket-specifikus word beágyazás létrehozásához használt convolutional modell:

![Neurális hálózat modell SSWE inspirálta](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Miután a képzési folyamat befejeződik, két beágyazási lapon elválasztott értékeket (TSV) formátumú fájlok jönnek létre a modellezési. szakaszra vonatkozóan.

A SSWE algoritmusok kapcsolatos további információkért lásd: a dokumentum által Duyu Tang és mások. "[Véleményeket-specifikus tanulási Word-beágyazás Twitter véleményeket besorolási](http://www.aclweb.org/anthology/P14-1146)." Számítási Linguistics (1) tartozó társítását. 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Modell létrehozása](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Miután a word vektorok a SSWE vagy Word2Vec algoritmus használatával jönnek létre, a besorolási modell képzett előre jelezni a tényleges véleményeket polaritás. Kétféle típusú szolgáltatások: Word2Vec és SSWE, két modell is vonatkozik: az átmenet kiemelése modellhez és a logisztikai regresszió modell. Ennek eredményeképpen négy különböző modell képzett.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Modell kiértékelése](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
A modellek képzett, miután a modellek segítségével Twitter szöveges adatok tesztelheti, kiértékelheti az egyes modellekre teljesítmény és. A minta a következő négy modellek értékelődik ki:

- Átmenet Boosting SSWE beágyazás keresztül.
- Logisztikai regresszió SSWE beágyazás keresztül.
- Átmenet Boosting Word2Vec beágyazás keresztül.
- Logisztikai regresszió Word2Vec beágyazás keresztül.

A négy modell teljesítményétől összehasonlítása az alábbi ábrán látható:

![A fogadó működő jellemző (ROC) modellek összehasonlítása](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

A színátmenetes kiemelése modell SSWE szolgáltatással rendelkező nyújtja a legjobb teljesítményt, a modellek összehasonlítása a görbe (AUC) metrika terület használatával.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Üzembe helyezés](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Az utolsó lépés a képzett véleményeket előrejelzési modell webszolgáltatásba az Azure Tárolószolgáltatásban fürt központi telepítése. A minta SSWE beágyazási algoritmus átmenetes kiemelése modellt használ a betanított modell. A operationalization környezet rendelkezései Docker és Kubernetes a fürt avatkozzon webszolgáltatás, az alábbi ábrán látható módon: 

![A Kubernetes-irányítópult](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

A operationalization folyamatáról további információk: [üzembe helyezése az Azure Machine Learning modell webszolgáltatásként](../desktop-workbench/model-management-service-deploy.md).

## <a name="conclusion"></a>Összegzés

Ebben a cikkben megismerte a Word2Vec és a céggel kapcsolatos véleményeket-specifikus Word beágyazás algoritmusokkal egy word-beágyazás modell betanításához. A kibontott beágyazásokat előre jelezni a céggel kapcsolatos véleményeket pontszámok Twitter szöveg adatok több modellek betanítása használták, a szolgáltatások. A használt színátmenet kiemelése modell SSWE szolgáltatás megadott, a lehető legjobb teljesítményt. A modell Azure Machine Learning-munkaterület használatával majd telepített Tárolószolgáltatás a valós idejű webszolgáltatásként.


## <a name="references"></a>Referencia

* [Csoportos adatelemzési folyamat](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Az Azure Machine Learning Team adatok tudományos folyamat (TDSP) használata](https://aka.ms/how-to-use-tdsp-in-aml)
* [Az Azure Machine Learninghez TDSP projektsablonjai](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../service/index.yml)
* [USA bevétel adatkészlet UCI ML tárházból](https://archive.ics.uci.edu/ml/datasets/adult)
* [Orvosbiológiai entitás felismerés TDSP sablonok segítségével](../desktop-workbench/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomas, és mások. "Szavak és kifejezések és azok Compositionality ábrázolásai elosztott. Kiadásokban a Neurális adatfeldolgozó rendszerek." 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, és mások. "A tanulási véleményeket-specifikus Word beágyazás Twitter véleményeket besorolási." HOZZÁFÉRÉS-VEZÉRLÉSI LISTA (1). 2014.](http://www.aclweb.org/anthology/P14-1146)

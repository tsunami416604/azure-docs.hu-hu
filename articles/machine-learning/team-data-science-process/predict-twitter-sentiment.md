---
title: "A word beágyazásokat a csapat adatok tudományos eljárással - Azure Twitter sentiment előrejelzése |} Microsoft Docs"
description: "A adattudomány projektek végrehajtásához szükséges lépéseket"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: fe1c87df40102a62e1e0c8873b25fa3df7d743bc
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/16/2017
---
# <a name="predict-twitter-sentiment-with-word-embeddings-using-the-team-data-science-process"></a>A word beágyazásokat az Team tudományos folyamat használatával Twitter sentiment előrejelzése

Ez a cikk bemutatja, hogyan használatakor hatékonyan dolgozhatnak a **Word2Vec** algoritmus beágyazás word és a **véleményeket adott Word beágyazás (SSWE) algoritmus** becsülhető, a Twitter sentiment a [Azure Machine Learning](../preview/index.yml). További részletek a előrejelzésére feladatát véleményeket polaritás twitter, a következő témakörben: [tárház](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction). Az adatok tudományos projektek hatékony együttműködést elősegítő kulcsa struktúra és a projektek egy meghatározott adatok tudományos életciklusával dokumentáció szabványosítása. A [Team adatok tudományos folyamat (TDSP)](overview.md) biztosít csak ilyen strukturált [életciklus](lifecycle.md). 

Adatok tudományos projektek létrehozása a **TDSP sablon** a szabványos keretet biztosít az Azure Machine Learning projektek. Korábban, a TDSP team kiadott kellett egy [GitHub-tárházban TDSP szerkezetének és sablonok](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Most, hogy a rendszer létre Azure Machine Learning projektek létrehozásának [TDSP struktúra és dokumentáció található sablonok az Azure Machine Learning](https://github.com/amlsamples/tdsp) engedélyezve van. Az Azure Machine Learning TDSP struktúra és a sablonok használatával, lásd: [-projektek a csapat adatok tudományos folyamatsablont szerkezeti](../preview/how-to-use-tdsp-in-azure-ml.md). 


## <a name="the-sentiment-polarity-sample"></a>A céggel kapcsolatos véleményeket polaritás minta

Bemutatja, hogyan hozható létre, és hajtsa végre a machine learning-projekt csapata adatok tudományos folyamat szerkezetének és sablonok az Azure Machine Learning munkahelyi többszemélyes adtak meg ez a minta [forgatókönyv](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). A modellezési feladata a céggel kapcsolatos véleményeket polaritás (pozitív vagy negatív) előrejelzése Twitter-üzeneteket szöveget használ. Ez a cikk a forgatókönyv tartozó feladatok modellezési adatok tagolása. A forgatókönyv leírja a következő feladatokat:

- Az adatok feltárása, képzés és a gépi tanulási modell, amely ismerteti a használható Case áttekintése előrejelzés problémájára központi telepítését. Erre a célra [véleményeket Twitter data](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) szolgál.
- Az Azure Machine Learning-sablonnal a csapat adatok tudományos folyamat (TDSP) az Azure Machine Learning ebben a projektben a projekt végrehajtásának. Projekt végrehajtási és jelentésére a TDSP életciklus szolgál.
- A megoldás közvetlenül az Azure Machine Learning Azure tároló szolgáltatásokban operationalization.

A projekt több funkciót az Azure Machine Learning, ilyen TDSP struktúra példányának létrehozásánál és használatra, az Azure Machine Learning munkahelyi többszemélyes kódot és az Azure-tároló szolgáltatások Docker és Kubernetes segítségével könnyen operationalization végrehajtását mutatja be.

## <a name="team-data-science-process"></a>Csoportos adatelemzési folyamat
A TDSP projekt struktúra és a dokumentáció a sablonok segítségével hajtható végre ez a minta. Ez azt jelenti a [TDSP életciklus]((https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md)). A projekt kapott utasítások alapján jön létre [Itt](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).

![TDSP életciklusa](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

![TDSP példányosítható](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Adatok megszerzését és ismertetése](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
Ez a példa első lépése, hogy töltse le a sentiment140 dataset és képeznek a modell betanítására és tesztelésére adatkészletek. A sentiment140 az adatkészletben a tweetet tényleges tartalmat (az eltávolított hangulatjelek) együtt a tweetet polaritásának (negatív = 0, pozitív = 4), az eltávolított semleges Twitter-üzeneteket. Osztva, az eredményül kapott betanítási adatok rendelkezik 1.3 millió sort foglalnak, illetve tesztelési adatokhoz 320 KB-os sorokat.


## <a name="modelinghttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Modellezési](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

Ez a minta része további három részre oszlik:
 
- **Jellemzőkiemelés** felel meg a különböző algoritmusok beágyazás word funkciók generációja. 
- **A modell létrehozása** különböző modell a tanítási vonatkozik, például _logisztikai regresszió_ és _átmenetes kiemelése_ előre jelezni a bemeneti szöveg céggel kapcsolatos véleményeket. 
- **A modell kiértékelése** alkalmazza a betanított modell vizsgálati adatok felett.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[A szolgáltatás műszaki osztály](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Word2Vec és SSWE az itt word beágyazásokat létrehozásához használt algoritmusok beágyazás szó. 


#### <a name="word2vec"></a>Word2Vec

A Word2Vec algoritmust használnak a Skipgram módban. Ezzel a módszerrel a word beágyazásokat létrehozása esetén, tekintse meg a dokumentum által Tomas Mikolov et al.: [elosztott felelősséget a szavak és kifejezések és azok Compositionality. Neurális adatfeldolgozó rendszerek fejlesztéseivel. 2013.](https://arxiv.org/abs/1310.4546).

Kihagyás-gramot sekély Neurális hálózat. A bemeneti érték a cél word egy gyakran használt adatok vektor, amely közelben szavak becsülhető használ kódolva. Ha **V** van a szóhasználatának méretét, akkor a kimeneti réteg méretének __C * V__ C az a környezet ablak mérete. A skip gramot alapú architektúra az alábbi ábrán látható:

![Kihagyás-gramot modell](./media/predict-twitter-sentiment/skip-gram-model.PNG)

***Kihagyás-gramot modell***

A részletes beállítás esetén a word2vec algoritmus és a skip-gramot modell nem térnek a minta tulajdonságairól. Olvasók is több annak működésével kapcsolatos adatokat is tekintse meg a következő hivatkozásokat:

- [A kód 02_A_Word2Vec.py hivatkozott TensorFlow példák](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)
- [Szavak vektoros ábrázolása](https://www.tensorflow.org/tutorials/word2vec)
- [Pontosan hogyan működik a word2vec?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf)
- [Tudnivalók a zaj Contrastive becslés és negatív mintavétel](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf)


#### <a name="sswe"></a>SSWE
A **véleményeket adott Word beágyazás (SSWE) algoritmus** megpróbálja kapcsolatos egyik támadható gyenge pontja, hogy a szavakat hasonló környezetekkel és ellentétes polaritás lehet hasonló word vektorok Word2vec algoritmus. A hasonló alkalmazásesemények azt jelenti, hogy Word2vec nem hajthatja végre pontosan a feladatok, például a céggel kapcsolatos véleményeket analysis. A SSWE algoritmus megpróbálja kezelni a gyenge pontot által a mondatok polaritás, mind a word környezetben beépítése az adatvesztés funkciókat.

Ez a minta egy SSWE változatát használja. A SSWE is az eredeti ngram és sérült ngram bemeneti, és azt használja a rangsorolási stílus ajtópántjának adatvesztés függvény szintaktikai elvesztését, mind a szemantikai adatvesztés. Ultimate adatvesztés függvény szintaktikai adatvesztés mind az szemantikai adatvesztés súlyozott kombinációja. Egyszerűség csak a szemantikai közötti entrópia lesz a veszteség függvény. Később ismertető, még a egyszerűbb adatvesztés funkcióval teljesítményét a SSWE beágyazás jobb, mint a Word2Vec beágyazás.

A SSWE inspirálta Neurális hálózat a mintában használt mintáját a következő ábrán látható:

![ROC-modellek összehasonlítása](./media/predict-twitter-sentiment/embedding-model-2.PNG)

***Convolutional modell véleményeket-specifikus word beágyazás létrehozásához.***


Miután a képzési folyamat befejeződik, két beágyazási TSV formátumú fájlok jönnek létre a modellezési. szakaszra vonatkozóan.

A SSWE algoritmusok kapcsolatos további információkért lásd: a dokumentum által Duyu Tang et al.: [tanulási véleményeket-specifikus Word beágyazása a céggel kapcsolatos véleményeket besorolás Twitter. HOZZÁFÉRÉS-VEZÉRLÉSI LISTA (1). 2014.](http://www.aclweb.org/anthology/P14-1146) 


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Modell létrehozása](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
A word vektorok SSWE vagy Word2vec algoritmusok használatával hozták létre, ha a következő lépés a tényleges véleményeket polaritás előre jelezni a besorolási modell betanításához. Kétféle típusú szolgáltatások Word2Vec és SSWE, két modell, a GBM modell és logisztikai regresszió modellt alkalmaznak. Ezért négy különböző modell betanítása közben vannak.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Modell kiértékelése](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
A négy modell betanítása adatok ellenőrzése az előző lépésben most a modell teljesítmény értékeléséhez használja. 

1. Átmenet Boosting keresztül SSWE beágyazás
2. Logisztikai regresszió keresztül SSWE beágyazás
3. Átmenet Boosting keresztül Word2Vec beágyazás
4. Logisztikai regresszió keresztül Word2Vec beágyazás

![ROC-modellek összehasonlítása](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

A GBM modell SSWE szolgáltatásokkal, a legjobb egy AUC metrika használatával.


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Üzembe helyezés](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

Ez a rész a betanított véleményeket előrejelzési modell (SSWE beágyazás + GBM modell) egy webszolgáltatás-bővítmény, az Azure tároló szolgáltatás (ACS) fürt központilag telepíti. A operationalization környezet rendelkezések Docker és Kubernetes webszolgáltatás avatkozzon a fürtben. Tekinthet meg további információkat a operationalization folyamathoz [Itt](https://docs.microsoft.com/en-us/azure/machine-learning/preview/model-management-service-deploy).

![kubenetes_dashboard](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)


## <a name="conclusion"></a>Összegzés

A részletes információt a Word2Vec és SSWE algoritmusokkal word-beágyazás modell betanításához volt ismertetése, és előre jelezni a céggel kapcsolatos véleményeket pontszámok Twitter szöveg adatok több modellek betanítása a kibontott beágyazásokat használták, a szolgáltatások. A céggel kapcsolatos véleményeket adott kifejezések használata Embeddings(SSWE) szolgáltatás átmenetes súlyozott fa modell megadott, a lehető legjobb teljesítményt. Ez a modell majd telepítették az Azure-tároló szolgáltatásokat Azure Machine Learning munkahelyi többszemélyes segítségével valós idejű webszolgáltatásként.


## <a name="references"></a>Referencia

* [Team tudományos folyamata](https://docs.microsoft.com/en-us/azure/machine-learning/team-data-science-process/overview) 
* [Az Azure Machine Learning Team adatok tudományos folyamat (TDSP) használata](https://aka.ms/how-to-use-tdsp-in-aml)
* [Az Azure Machine Learning TDSP projektsablon](https://aka.ms/tdspamlgithubrepo)
* [Az Azure ML munkahelyi többszemélyes](https://docs.microsoft.com/en-us/azure/machine-learning/preview/)
* [USA bevétel UCI ML tárházból adatkészlet](https://archive.ics.uci.edu/ml/datasets/adult)
* [Orvosbiológiai entitás felismerés TDSP sablonnal](https://docs.microsoft.com/en-us/azure/machine-learning/preview/scenario-tdsp-biomedical-recognition)
* [Mikolov, Tomas, és mások. Szavak és kifejezések és azok compositionality elosztott ábrázolásai. Neurális adatfeldolgozó rendszerek fejlesztéseivel. 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, és mások. "A tanulási véleményeket-specifikus Word beágyazás Twitter véleményeket besorolási." HOZZÁFÉRÉS-VEZÉRLÉSI LISTA (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
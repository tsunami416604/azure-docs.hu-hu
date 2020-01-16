---
title: A modell értelmezése Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy a modell miért teszi a jóslatokat az Azure Machine Learning SDK használatával. A képzés során felhasználható, hogy megtudja, hogyan teszi lehetővé a modell előrejelzéseit.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 339ab811969a3de6ce87d529e1bf77f325be4071
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968485"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>A modell értelmezése Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>A modell értelmezésének áttekintése

A tolmácsolás kritikus fontosságú az adatszakértők és az üzleti döntéshozók számára, így biztosítva a vállalati szabályzatoknak, az iparági szabványoknak és a kormányzati szabályozásoknak való megfelelést:
+ Az adatszakértőknek meg kell adniuk modelljeiket a vezetők és az érdekelt felek számára, hogy megértsék az eredmények értékét és pontosságát 
+ Az üzleti döntéshozóknak meg kell bízniuk az átláthatóság biztosításában a végfelhasználók számára a megbízhatóságuk megszerzéséhez és fenntartásához

A modell fejlesztésének két fő fázisában fontos a gépi tanulási modell elmagyarázása.
+ A gépi tanulási modell fejlesztési ciklusának betanítási fázisában. A modell-tervezők és-értékelők a modell értelmező kimenetét használhatják a hipotézisek ellenőrzéséhez és az érdekelt felekkel való bizalom kiépítéséhez. Emellett a modellen alapuló elemzéseket is használják a hibakereséshez, a modell viselkedésének érvényesítéséhez, valamint a torzítás vagy a jelentéktelen funkciók ellenőrzéséhez.
+ A következtetési fázisban az üzembe helyezett modellek átláthatósága lehetővé teszi a vezetők számára, hogy megértsék a modell működésének módját, valamint azt, hogy a rendszer hogyan kezeli és befolyásolja a valós életben lévő döntéseket. 

## <a name="interpretability-with-azure-machine-learning"></a>Értelmezés Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan valósítja meg a modell-értelmező fogalmakat az SDK-ban.

Az SDK osztályok és metódusok használatával a következőket érheti el:
+ A szolgáltatás fontossági értékei mind a nyers, mind a mérnöki funkciók esetében
+ Valós idejű adatkészletek értelmezése nagy léptékben, a képzés és a következtetések során.
+ Interaktív vizualizációk, amelyek segítséget nyújtanak az adatmintázatok felderítésében, valamint a betanítás időpontjában.


A gépi tanulásban a **funkciók** a célként megadott adatpontok előrejelzésére szolgáló adatmezők. Például a hitelkockázat előrejelzéséhez az életkor, a fiók mérete és a fiók kora adatmezőket lehet használni. Ebben az esetben a kor, a fiók mérete és a fiók kora **funkciók**. A szolgáltatás fontossága azt mutatja be, hogy az egyes adatmezők hogyan érintik a modell előrejelzéseit. Előfordulhat például, hogy az életkor nagy mértékben használatban van az előrejelzésben, míg a fiók mérete és kora nem befolyásolja az előrejelzés pontosságát. Ez a folyamat lehetővé teszi, hogy az adatszakértők elmagyarázzák az eredményül kapott előrejelzéseket, így az érintettek a modellben a legfontosabb adatpontok betekintést nyerhetnek.

Ezeknek az eszközöknek a használatával a gépi tanulási modelleket **globálisan az összes adattal**megmagyarázhatja, vagy **egy adott adatponton helyileg** , a legkorszerűbb technológiák használatával könnyen használható és méretezhető módon.

Az értelmező osztályok több SDK-csomagon keresztül érhetők el. Ismerje meg, hogyan [TELEPÍTHET SDK-csomagokat Azure Machine Learninghoz](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, a fő csomag, amely a Microsoft által támogatott funkciókat tartalmazza.

* `azureml.contrib.interpret`, előzetes verzió és kísérleti funkciók, amelyeket kipróbálhat.

* `azureml.train.automl.automlexplainer` csomag az automatizált gépi tanulási modellek értelmezéséhez.

> [!IMPORTANT]
> A `contrib` névtérben lévő tartalom nem teljes mértékben támogatott. Mivel a kísérleti funkciók megérettek, a rendszer fokozatosan a fő névtérbe helyezi át őket.

## <a name="how-to-interpret-your-model"></a>A modell értelmezése

Az értelmező osztályok és módszerek alkalmazásával megismerheti a modell globális viselkedését vagy az adott előrejelzéseket. Az előző neve globális magyarázat, és az utóbbi neve helyi magyarázat.

A metódusok kategorizálva is megadhatók aszerint, hogy a módszer a modell agnosztikus vagy modell-specifikus. Egyes metódusok bizonyos típusú modelleket céloznak meg. Például a SHAP 's Tree magyarázóer csak a faalapú modellekre vonatkozik. Egyes módszerek fekete dobozként kezelik a modellt, például a magyarázatot vagy a SHAP kernel-magyarázatát. Az `interpret`-csomag az adatkészletek, a modellek típusai és a használati esetek alapján használja ki ezeket a különböző módszereket.

A kimenet azon információk halmaza, amelyekkel az adott modell előrejelzését végezheti el, például:
* Globális/helyi relatív funkció fontossága
* Globális/helyi szolgáltatás és előrejelzési kapcsolat

### <a name="explainers"></a>Magyarázatok

Ez a csomag a értelmezzen [-Community](https://github.com/interpretml/interpret-community/), egy nyílt forráskódú Python-csomag, a értelmezhető modellek betanítása és a tábla AI-rendszerek ismertetése terén fejlesztett értelmező technikákat használja. A [tolmácsolás – a Közösség](https://github.com/interpretml/interpret-community/) az SDK által támogatott magyarázatokat üzemeltető gazdagépként működik, és jelenleg a következő értelmező módszereket támogatja:

* **Shap Tree**deformáló: [Shap](https://github.com/slundberg/shap)'s Tree deformálója, amely a fák és a fák különböző részeire jellemző, a többhelyes idő gyors alakításának értékelésére koncentrál.
* A **Shaper részletes ismertetése: a**The [Shaper](https://github.com/slundberg/shap)magyarázata alapján a Deep deformáló "egy nagy sebességű közelítési algoritmus az értékek alakításához a Deep learning-modellekben, amely egy, az DeepLIFT-beli [Shap-dokumentumban](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)leírt módon létesített kapcsolatban. A TensorFlow modellek és kerasz modellek támogatottak a TensorFlow-háttér használatával (a PyTorch előzetes támogatása is elérhető).
* **Shap lineáris elmagyarázó**: a [Shap](https://github.com/slundberg/shap)lineáris magyarázata a lineáris modell értékeit határozza meg, opcionálisan könyveli a szolgáltatások közötti összefüggéseket.

* **Shap kernel-elmagyarázó**: a [Shap](https://github.com/slundberg/shap)kernel-magyarázata egy speciális súlyozású helyi lineáris regressziót használ az egyes modellekhez tartozó SHAP-értékek becsléséhez.
* A **magyarázó elmagyarázó**: az utánozó elmagyarázása azon alapul, hogy a [globális helyettesítő modellek](https://christophm.github.io/interpretable-ml-book/global.html) betanítása a tábla modelljeinek utánzására szolgál. A globális helyettesítő modell egy belsőleg értelmezhető modell, amely úgy van kiképezve, hogy a lehető legpontosabban közelítse meg a fekete doboz modelljeinek előrejelzéseit. Az adattudós értelmezheti a helyettesítő modellt a fekete Box-modellel kapcsolatos következtetések összeállításához. A következő értelmezhető modellek egyikét használhatja a helyettesítő modellként: LightGBM (LGBMExplainableModel), lineáris regresszió (LinearExplainableModel), sztochasztikus gradiens deillatú elmagyarázható modell (SGDExplainableModel) és döntési fa ( DecisionTreeExplainableModel).


* A **permutáció funkciójának fontossági magyarázata**: a permutáció funkció fontossága a [Breiman véletlenszerű erdei papírján](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (lásd a 10. szakaszt) használt besorolási és regressziós modelleket ismerteti. Magas szinten a működésének módja az, hogy a teljes adatkészlet esetében véletlenszerűen végzi el az adatok egy funkciójának a kiszámítását, és kiszámítja, hogy a teljesítmény mérőszáma milyen mértékben változik. Minél nagyobb a változás, annál fontosabb a funkció.

* **Lime-elmagyarázó** (`contrib`): [a lime-alapú, lime](https://github.com/marcotcr/lime)-elmagyarázó a helyi helyettesítő modellek létrehozásához használja a korszerű, értelmezhető modell-AGNOSZTIKUS magyarázatot (lime) használó algoritmust. A globális helyettesítő modellektől eltérően a LIME a helyi helyettesítő modellek betanítására összpontosít az egyes előrejelzések elmagyarázása érdekében.
* **Han Text elmagyarázó** (`contrib`): a Han Text magyarázóer egy hierarchikus hanghálózatot használ a modell magyarázatának beszerzéséhez egy adott fekete Box Text-modell szöveges adatainak használatával. Egy adott fekete Box-modell előre jelzett kimenetei között betanítja a HAN helyettesítő modellt. Miután globálisan betanítást végez a Text corpusban, egy adott dokumentumhoz tartozó finomhangolási lépést ad hozzá a magyarázatok pontosságának javításához. A HAN kétirányú RNN használ két figyelmet a mondatok és a szó figyelemmel. Miután a DNN betanítják a fekete dobozos modellre, és egy adott dokumentumra finomítják, a felhasználó kinyerheti a szó fontosságát a figyelmet tartalmazó rétegekből. A HAN pontosabban jelenik meg, mint a mész vagy a formázás a szöveges adatmennyiséghez, de költségesebb a betanítási idő szempontjából is. Javítottuk a felhasználót, hogy a betanítási idő csökkentése érdekében a felhasználó a kesztyű szó beágyazásával inicializálja a hálózatot. A betanítási idő jelentősen növelhető a HAN távoli Azure GPU virtuális gépen való futtatásával. A HAN megvalósítását ["hierarchikus figyelmet igénylő hálózatok a dokumentumok besorolásához (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)című cikk írja le.


* **Táblázatos magyarázat**: `TabularExplainer` a következő logikát alkalmazza a Direct [SHAP](https://github.com/slundberg/shap) -magyarázatok meghívásához:

    1. Ha faalapú modell, alkalmazza a SHAP `TreeExplainer`, máskülönben
    2. Ha ez egy DNN modell, alkalmazza a SHAP `DeepExplainer`, különben
    3. Ha lineáris modell, alkalmazza a SHAP `LinearExplainer`, különben
    3. Kezelés fekete dobozos modellként és a SHAP `KernelExplainer` alkalmazása


a `TabularExplainer` a Direct SHAP-magyarázatokkal jelentős funkciókat és teljesítménybeli fejlesztéseket is tett:

* **Az inicializálási adatkészlet összefoglalása**. Azokban az esetekben, amikor a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis reprezentatív mintát, amely felgyorsítja a globális és a helyi magyarázatot is.
* **A kiértékelési adatkészlet mintavételezése**. Ha a felhasználó a kiértékelési minták nagy készletét adja vissza, de valójában nem szükséges mindegyiket kiértékelni, a mintavételi paraméter értéke TRUE (igaz) lehet, hogy felgyorsítsa a globális magyarázatot.

A következő ábra a közvetlen és a meta-magyarázatok aktuális szerkezetét mutatja be.

[![Machine Learning értelmező architektúrája](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Támogatott modellek

A Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`vagy `scipy.sparse.csr_matrix` formátumú adatkészleteken betanított modelleket az SDK értelmező `explain` csomagja támogatja.

A magyarázó függvények bemenetként is elfogadják a modelleket és a folyamatokat. Ha meg van adni egy modellt, a modellnek meg kell valósítania az előrejelzési függvényt `predict` vagy `predict_proba`, amely megfelel a Scikit konvenciónak. Ha a folyamat (a folyamat parancsfájljának neve) van megadva, a magyarázat függvény azt feltételezi, hogy a futó folyamat parancsfájlja egy előrejelzést ad vissza. Támogatjuk a PyTorch, a TensorFlow és a kerasz Deep learning-keretrendszereken keresztül betanított modelleket.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `explain` csomag helyi és távoli számítási célokkal való együttműködésre lett tervezve. Ha helyileg fut, az SDK-függvények nem fognak kapcsolatba lépni az Azure-szolgáltatásokkal. A magyarázatot távolról is futtathatja Azure Machine Learning számítási feladatait, és naplózhatja a magyarázat adatait Azure Machine Learning futtatási előzmények szolgáltatásba. Az információk naplózása után a magyarázatokból származó jelentések és vizualizációk azonnal elérhetők Azure Machine Learning munkaterületen a felhasználók elemzéséhez.


## <a name="next-steps"></a>Következő lépések

Tekintse meg az [útmutató](how-to-machine-learning-interpretability-aml.md) a modellek helyi és Azure Machine learning távoli számítási erőforrásokon való értelmezésének engedélyezéséhez című témakört. További forgatókönyvek: [minta notebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .

---
title: Modell értelmezhetősége az Azure Machine Learningben
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan magyarázhatja el, hogy a modell miért készít előrejelzéseket az Azure Machine Learning SDK használatával. A betanítás és a következtetés során használható, hogy megértse, hogyan készít a modell előrejelzéseket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063990"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modell értelmezhetősége az Azure Machine Learningben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>A modell értelmezhetőségének áttekintése

Az értelmezhetőség kritikus fontosságú az adatszakértők és az üzleti döntéshozók számára egyaránt a vállalati irányelveknek, iparági szabványoknak és kormányzati előírásoknak való megfelelés biztosítása érdekében:
+ Az adatszakértőknek szükségük van arra, hogy elmagyarázzák modelljeiket a vezetőknek és az érdekelt eknek, hogy megértsék megállapításaik értékét és pontosságát. 
+ Az üzleti döntéshozóknak meg kell békében tartaniuk azt a képességet, hogy átláthatóságot biztosítsanak a végfelhasználók számára, hogy elnyerjék és fenntartsák bizalmukat

A gépi tanulási modell ismertetésének engedélyezése fontos a modellfejlesztés két fő fázisában:
+ A gépi tanulási modell fejlesztési ciklusának betanítási fázisában. A modelltervezők és -értékelők a modellek értelmezhetőségi kimenetével ellenőrizhetik a hipotéziseket, és bizalmat építhetnek az érdekelt felekkel. A modell betekintési adatait is használják a hibakereséshez, a modell viselkedésének a céljaiknak megfelelő érvényesítéséhez, valamint az elfogultság vagy a jelentéktelen funkciók ellenőrzéséhez.
+ A következtetési fázisban, mivel az átláthatóság körül telepített modellek lehetővé teszi a vezetők számára, hogy megértsék ", ha telepített", hogyan működik a modell, és hogyan döntéseket kezelik, és hatással van az emberek a valós életben. 

## <a name="interpretability-with-azure-machine-learning"></a>Értelmezhetőség az Azure Machine Learning segítségével

Ebben a cikkben megtudhatja, hogyan modell értelmezhetőségi fogalmak az SDK-ban implementált.

Az SDK-ban lévő osztályok és módszerek használatával a következőket kaphatja meg:
+ Fontossági értékek mind a nyers, mind a mesterséges funkciókban
+ Értelmezhetőség valós adatkészleteken nagy méretekben, betanítás és következtetés során.
+ Interaktív vizualizációk, amelyek segítik Önt az adatok mintáinak és magyarázatainak felfedezésében a képzési időben


A gépi tanulásban a **funkciók** a céladatpont előrejelzésére használt adatmezők. A hitelkockázat előrejelzéséhez például az életkor, a fiókméret és a fiókéletkor adatmezői használhatók. Ebben az esetben az életkor, a fiók mérete és a fiók **életkora funkciók.** A szolgáltatásfontosság megmutatja, hogy az egyes adatmezők hogyan befolyásolták a modell előrejelzéseit. Például az életkor erősen használható az előrejelzésben, míg a fiók mérete és kora nem befolyásolja jelentősen az előrejelzés pontosságát. Ez a folyamat lehetővé teszi az adatszakértők számára, hogy elmagyarázzák az eredményül kapott előrejelzéseket, így az érdekelt felek láthatják, hogy milyen adatpontok a legfontosabbak a modellben.

Ezekkel az eszközökkel elmagyarázhatja a gépi tanulási **modelleket globálisan az összes adaton,** vagy **helyileg egy adott adatponton** a legkorszerűbb technológiák használatával, könnyen használható és méretezhető módon.

Az értelmezhetőségi osztályok több SDK-csomagon keresztül érhetők el. Ismerje meg, hogyan [telepítheti az SDK-csomagokat az Azure Machine Learninghez.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* `azureml.interpret`, a fő csomag, amely a Microsoft által támogatott funkciókat tartalmazza.

* `azureml.contrib.interpret`, megtekintheti az előzetes verziót és a kipróbálható kísérleti funkciókat.

* `azureml.train.automl.automlexplainer`automatikus gépi tanulási modellek értelmezéséhez.

> [!IMPORTANT]
> A `contrib` névtér tartalma nem teljes mértékben támogatott. Ahogy a kísérleti funkciók kiforrnak, fokozatosan átkerülnek a fő névtérbe.

## <a name="how-to-interpret-your-model"></a>Hogyan kell értelmezni a modell

Az értelmezhetőségi osztályok és módszerek segítségével megismerheti a modell globális viselkedését vagy adott előrejelzéseket. Az előbbit globális magyarázatnak, az utóbbit pedig helyi magyarázatnak nevezik.

A módszerek is kategorizálható alapján, hogy a módszer modell agnosztikus vagy modell specifikus. Egyes módszerek bizonyos típusú modelleket céloznak meg. A SHAP famagyarázója például csak faalapú modellekre vonatkozik. Egyes módszerek a modellt fekete dobozként kezelik, mint például a mimikai magyarázó vagy a SHAP kernelmagyarázója. A `interpret` csomag használja ezeket a különböző megközelítéseket adatkészletek, modelltípusok és használati esetek alapján.

A kimenet egy olyan információkészlet, amely arról szól, hogy egy adott modell hogyan készíti az előrejelzést, például:
* Globális/helyi relatív funkciófontosság
* Globális/helyi funkció- és előrejelzési kapcsolat

### <a name="explainers"></a>Magyarázók

Ez a csomag az [Interpret-Community](https://github.com/interpretml/interpret-community/)- egy nyílt forráskódú python csomagban kifejlesztett értelmezhetőségi technikákat használja az értelmezhető modellek képzésére és a blackbox AI rendszerek magyarázatára. [Az Interpret-Community](https://github.com/interpretml/interpret-community/) az SDK által támogatott magyarázók állomása, és jelenleg a következő értelmezhetőségi technikákat támogatja:

* **SHAP Famagyarázó:** [SHAP](https://github.com/slundberg/shap)'s fa magyarázó, amelynek középpontjában a polinom idő gyors SHAP értékbecslésalgoritmus jellemző a fák és együttesek a fák.
* **SHAP Deep Explainer**: A [SHAP](https://github.com/slundberg/shap)magyarázata alapján a Deep Explainer "egy nagy sebességű közelítési algoritmus a SHAP értékekhez a deep learning modellekben , amely a DEEPLIFT-tel való kapcsolatra épül a [SHAP NIPS papírban](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)leírtak szerint . TensorFlow modellek és Keras modellek a TensorFlow háttér-támogatott (van is előzetes támogatást PyTorch)".
* **SHAP Lineáris magyarázó:** [A SHAP](https://github.com/slundberg/shap)'s Linear explainer kiszámítja a SHAP értékeket egy lineáris modellhez, opcionálisan figyelembe véve a funkciók közötti korrelációkat.

* **SHAP Kernel Explainer**: [A SHAP](https://github.com/slundberg/shap)'s Kernel magyarázója egy speciálisan súlyozott helyi lineáris regressziót használ bármely modell SHAP értékeinek becsléséhez.
* **Utánozza Explainer:** Utánozza explainer alapul az ötlet a képzés [globális helyettesítő modellek](https://christophm.github.io/interpretable-ml-book/global.html) utánozni blackbox modellek. A globális helyettesítő modell egy gyújtószikramentesen értelmezhető modell, amely be van tanítva, hogy a lehető legpontosabban közelítse meg a fekete doboz modell előrejelzéseit. Az adattudós értelmezheti a helyettesítő modellt, hogy következtetéseket vonjon le a fekete doboz modellről. A helyettesítő modellként a következő értelmezhető modellek egyikét használhatja: LightGBM (LGBMExplainableModel), Lineáris regresszió (LinearExplainableModel), Sztochastikus gradiens esésmagyarázható modell (SGDExplainableModel) és Decision Tree ( DecisionTreeExplainableModel).


* **Permutációs funkció fontossági magyarázó:** Permutációs funkció fontossági egy olyan technika, amelyet a [Breiman's Random Forests papír](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) által inspirált osztályozási és regressziós modellek magyarázatára használnak (lásd 10. szakasz). Magas szinten, a hogyan működik, véletlenszerűen csoszogó adatok egy funkció egy időben a teljes adatkészletet, és kiszámítja, hogy mennyi a teljesítmény mutató az érdeklődés i. Minél nagyobb a változás, annál fontosabb ez a funkció.

* **LIME Explainer** (`contrib`): Lime [alapján](https://github.com/marcotcr/lime)a LIME explainer a legkorszerűbb helyi értelmezhető modell-agnosztikus magyarázatok (LIME) algoritmust használja a helyi helyettesítő modellek létrehozásához. Ellentétben a globális helyettesítő modellek, LIME összpontosít képzés helyi helyettesítő modellek megmagyarázni az egyéni előrejelzések.
* **HAN szövegmagyarázó** (`contrib`): Han szövegmagyarázó használ hierarchikus figyelem hálózat beszerzése modell magyarázatok szöveges adatok egy adott fekete doboz szöveg modell. A HAN helyettesítő modell egy adott fekete doboz modell előre jelzett kimenetek bevonatok. A szövegkorpusz globális betanítása után egy adott dokumentumfinomítási lépést ad hozzá a magyarázatok pontosságának javítása érdekében. Han kétirányú RNN-t használ két figyelemréteggel, mondat- és szófigyeléshez. Miután a DNN be van tanítva a fekete doboz modellre, és egy adott dokumentumra finomhangolva van, a felhasználó kinyerheti a fontos betűs szavakat a figyelemrétegekből. Han kimutatták, hogy pontosabb, mint a LIME vagy shap a szöveges adatok, de drágább a képzési idő szempontjából is. Fejlesztések történtek annak érdekében, hogy a felhasználó a glove szóbeágyazásával inicializálhatja a hálózatot a betanítási idő csökkentése érdekében. A betanítási idő jelentősen javítható a HAN távoli Azure-GPU virtuális gépen való futtatásával. A HAN megvalósítását a ["Hierarchikus figyelemhálózatok a dokumentumok osztályozásához (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification)című dokumentumismertetiban ismertetik.


* **Táblázatos**magyarázó: `TabularExplainer` a következő logikát alkalmazza a Közvetlen [SHAP](https://github.com/slundberg/shap) magyarázók meghívására:

    1. Ha faalapú modellről van szó, alkalmazza a SHAP `TreeExplainer`,
    2. Ha DNN modellről van `DeepExplainer`szó, alkalmazza a SHAP ,
    3. Ha lineáris modellről van `LinearExplainer`szó, alkalmazza a SHAP ,
    3. Kezelje feketedobozos modellként, és alkalmazza a SHAP-t`KernelExplainer`


`TabularExplainer`jelentős funkció- és teljesítménynövelő fejlesztéseket hajtott végre a közvetlen SHAP Explainers felett:

* **Az inicializálási adatkészlet összegzése**. Azokban az esetekben, ahol a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis reprezentatív mintákat, amelyek felgyorsítják mind a globális, mind a helyi magyarázatot.
* **Mintavétel ez a kiértékelési adatkészlet**. Ha a felhasználó sikeres egy nagy kiértékelési minták, de valójában nem kell az összes kiértékelése, a mintavételi paraméter beállítható igaz, hogy gyorsítsa fel a globális magyarázatot.

Az alábbi ábra a közvetlen és metamagyarázók aktuális szerkezetét mutatja be.

[![Gépi tanulási értelmezhetőségi architektúra](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Támogatott modellek

A `numpy.array`Python , , `pandas.DataFrame` `iml.datatypes.DenseData`vagy `scipy.sparse.csr_matrix` formátumban adatkészletekre betanított modelleket `explain` az SDK értelmezhetőségi csomagja támogatja.

A magyarázat függvények a modelleket és a folyamatokat is bemenetként fogadják el. Ha egy modell van megadva, a `predict` `predict_proba` modellnek végre kell hajtania az előrejelzési funkciót, vagy amely megfelel a Scikit-egyezménynek. Ha egy folyamat (a folyamat parancsfájl neve) van megadva, a magyarázat függvény feltételezi, hogy a futó folyamat parancsfájl egy előrejelzést ad vissza. Támogatjuk a PyTorch, TensorFlow és Keras deep learning keretrendszereken keresztül képzett modelleket.

### <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `explain` csomag helyi és távoli számítási célokkal egyaránt működik. Ha helyileg fut, az SDK-függvények nem lépnek kapcsolatba egyetlen Azure-szolgáltatással sem. A magyarázatot távolról futtathatja az Azure Machine Learning Compute szolgáltatásban, és naplózhatja a magyarázatadatait az Azure Machine Learning run history services szolgáltatásába. Miután ezeket az információkat naplózták, a magyarázatból származó jelentések és vizualizációk könnyen elérhetők az Azure Machine Learning-munkaterületen felhasználói elemzéshez.


## <a name="next-steps"></a>További lépések

Tekintse meg a [helyi](how-to-machine-learning-interpretability-aml.md) és az Azure Machine Learning távszámítási erőforrásokkal kapcsolatos modellek értelmezhetőségét. Tekintse meg a [mintanotebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) további forgatókönyveket.

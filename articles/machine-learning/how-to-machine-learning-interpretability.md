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
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631414"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modell értelmezhetősége az Azure Machine Learningben
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>A modell értelmezhetőségének áttekintése

Az adatszakértők, könyvvizsgálók és üzleti döntéshozók számára egyaránt kritikus fontosságú az adatszakértők, könyvvizsgálók és üzleti döntéshozók számára a vállalati irányelveknek, iparági szabványoknak és kormányzati előírásoknak való megfelelés biztosítása érdekében:

+ Az adatszakértőknek képesnek kell lenniük arra, hogy elmagyarázzák modelljeiket a vezetőknek és az érdekelt eknek, hogy megértsék megállapításaik értékét és pontosságát. Azt is megkövetelik, hogy értelmezhetőlegyen a modellek hibakeresése, és megalapozott döntéseket hozzanak arról, hogyan lehetne javítani őket. 

+ A jogi ellenőröknek olyan eszközökre van szükségük, amelyek a szabályozási megfeleléssel kapcsolatos modellek et validálják, és nyomon követik, hogy a modellek döntései hogyan befolyásolják az embereket. 

+ Az üzleti döntéshozóknak lelki békére van szükségük azáltal, hogy képesek biztosítani az átláthatóságot a végfelhasználók számára. Ez lehetővé teszi számukra, hogy keresni és fenntartani a bizalmat.


A gépi tanulási modell ismertetésének engedélyezése fontos a modellfejlesztés két fő fázisában:
+ A képzési szakaszban a modelltervezők és -értékelők a modell értelmezhetőségi kimenetét használhatják a hipotézisek ellenőrzésére és az érdekelt felekkel való bizalom kiépítésére. A modell betekintési adatait is használják a hibakereséshez, a modell viselkedésének a céljaiknak megfelelő érvényesítéséhez, valamint a modell tisztességtelenvagy jelentéktelen funkciók ellenőrzéséhez.

+ A következtetési fázisban, mivel az átláthatóság körül telepített modellek lehetővé teszi a vezetők számára, hogy megértsék ", ha telepített", hogyan működik a modell, és hogyan döntéseket kezelik, és hatással van az emberek a valós életben. 

## <a name="interpretability-with-azure-machine-learning"></a>Értelmezhetőség az Azure Machine Learning segítségével

Az értelmezhetőségi osztályok több SDK-csomagon keresztül érhetők el: (További információ az [SDK-csomagok Azure Machine Learninghez történő telepítéséről)](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* `azureml.interpret`, a fő csomag, amely a Microsoft által támogatott funkciókat tartalmazza.

* `azureml.contrib.interpret`, megtekintheti az előzetes verziót és a kipróbálható kísérleti funkciókat.

* `azureml.train.automl.automlexplainer`automatikus gépi tanulási modellek értelmezéséhez.

Általános `pip install azureml-interpret` `pip install azureml-interpret-contrib` használatra, valamint `pip install azureml-interpret-contrib` az értelmezhetőségi csomagok leéséhez használható AutoML-használathoz.


> [!IMPORTANT]
> A `contrib` névtér tartalma nem teljes mértékben támogatott. Ahogy a kísérleti funkciók kiforrnak, fokozatosan átkerülnek a fő névtérbe.
.



## <a name="how-to-interpret-your-model"></a>Hogyan kell értelmezni a modell

Az SDK-ban lévő osztályok és módszerek használatával a következőket teheti:
+ Magyarázza el a modell előrejelzését a teljes modell és/vagy az egyes adatpontok jellemzőfontossági értékeinek létrehozásával. 
+ A modell értelmezhetőségét valós adatkészleteken, nagy méretekben, betanítás és következtetés során érheti el.
+ Interaktív vizualizációs irányítópult használata az adatok mintáinak és magyarázatainak felderítéséhez a betanítási időben


A gépi tanulásban a **funkciók** a céladatpont előrejelzésére használt adatmezők. A hitelkockázat előrejelzéséhez például az életkor, a fiókméret és a fiókéletkor adatmezői használhatók. Ebben az esetben az életkor, a fiók mérete és a fiók **életkora funkciók.** A szolgáltatásfontosság megmutatja, hogy az egyes adatmezők hogyan befolyásolták a modell előrejelzéseit. Például az életkor erősen használható az előrejelzésben, míg a fiók mérete és kora nem befolyásolja jelentősen az előrejelzési értékeket. Ez a folyamat lehetővé teszi az adatszakértők számára, hogy elmagyarázzák az eredményül kapott előrejelzéseket, így az érdekelt felek láthatják, hogy milyen funkciók a legfontosabbak a modellben.

Itt megismerheti a támogatott értelmezhetőségi technikákat, a támogatott gépi tanulási modelleket és a támogatott futtatási környezeteket.


## <a name="supported-interpretability-techniques"></a>Támogatott értelmezhetőségi technikák

 `azureml-interpret`az [Interpret-Community](https://github.com/interpretml/interpret-community/)-ben kifejlesztett értelmezhetőségi technikákat használja, egy nyílt forráskódú python csomagot az értelmezhető modellek képzésére és a blackbox AI rendszerek magyarázatára. [Az Interpret-Community](https://github.com/interpretml/interpret-community/) az SDK által támogatott magyarázók állomása, és jelenleg a következő értelmezhetőségi technikákat támogatja:

|Értelmezhetőségi technika|Leírás|Típus|
|--|--|--------------------|
|1. SHAP fa magyarázó| [SHAP](https://github.com/slundberg/shap)'s fa magyarázó, amelynek középpontjában a polinom idő gyors SHAP érték becslési algoritmus kifejezett **fák és együttesek a fák**.|Modellspecifikus|
|2. SHAP mélymagyarázó| A [SHAP](https://github.com/slundberg/shap)magyarázata alapján a Deep Explainer "egy nagy sebességű közelítési algoritmus a SHAP értékekhez a deep learning modellekben , amely a [SHAP NIPS papírban](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions)leírt DeepLIFT-el való kapcsolatra épül . **TensorFlow** modellek és **Keras** modellek a TensorFlow háttér-támogatott (van is előzetes támogatást PyTorch)".|Modellspecifikus|
|3. SHAP lineáris magyarázó| [A SHAP](https://github.com/slundberg/shap)'s Linear explainer kiszámítja a SHAP értékeket egy **lineáris modellhez,** opcionálisan figyelembe véve a funkciók közötti korrelációkat.|Modellspecifikus|
|4. SHAP kernel magyarázó| [A SHAP](https://github.com/slundberg/shap)'s Kernel magyarázó egy speciálisan súlyozott helyi lineáris regressziót használ **bármely modell**SHAP értékeinek becsléséhez.|Modell-agnosztikus|
|5. Utánozza Explainer (Global Helyettes)| Utánozza explainer alapul az ötlet a képzés [globális helyettesítő modellek](https://christophm.github.io/interpretable-ml-book/global.html) utánozni blackbox modellek. A globális helyettesítő modell egy gyújtószikramentesen értelmezhető modell, amely be van tanítva, hogy a lehető legpontosabban közelítse **meg bármely fekete dobozmodell** előrejelzéseit. Az adatszakértők értelmezhetik a helyettesítő modellt, hogy következtetéseket vonjanak le a fekete doboz modellről. A helyettesítő modellként a következő értelmezhető modellek egyikét használhatja: LightGBM (LGBMExplainableModel), Lineáris regresszió (LinearExplainableModel), Sztochastikus gradiens esésmagyarázható modell (SGDExplainableModel) és Decision Tree (DecisionTreeExplainableModel).|Modell-agnosztikus|
|6. Permutációs funkció fontossági magyarázó (PFI)| A permutációs funkciófontosság egy olyan módszer, amely a [Breiman Random Forests című tanulmánya](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) által ihletett osztályozási és regressziós modellek magyarázatára szolgál (lásd 10. szakasz). Magas szinten, a hogyan működik, véletlenszerűen csoszogó adatok egy funkció egy időben a teljes adatkészletet, és kiszámítja, hogy mennyi a teljesítmény mutató az érdeklődés i. Minél nagyobb a változás, annál fontosabb ez a funkció. A PFI meg tudja magyarázni **az alapul szolgáló modellek** általános viselkedését, de nem magyarázza meg az egyes előrejelzéseket. |Modell-agnosztikus|




A fent leírt értelmezhetőségi technikák mellett támogatunk `TabularExplainer`egy másik [SHAP-alapú magyarázót](https://github.com/slundberg/shap)is, amelyet úgy hívunk. A modelltől függően a támogatott SHAP magyarázók egyikét `TabularExplainer` használja:

* TreeExplainer az összes fa alapú modellhez
* DeepExplainer DNN modellekhez
* LinearExplainer lineáris modellekhez
* KernelExplainer az összes többi modellhez

`TabularExplainer`jelentős funkció- és teljesítménynövelő fejlesztéseket hajtott végre a közvetlen SHAP Explainers felett:

* **Az inicializálási adatkészlet összegzése**. Azokban az esetekben, ahol a magyarázat sebessége a legfontosabb, összefoglaljuk az inicializálási adatkészletet, és létrehozunk egy kis reprezentatív mintákat, amelyek felgyorsítják az általános és az egyéni jellemzőfontossági értékek létrehozását.
* **Mintavétel ez a kiértékelési adatkészlet**. Ha a felhasználó nagy kiértékelési mintákat ad át, de valójában nem kell mindegyiket kiértékelni, a mintavételi paraméter igaz értékre állítható, hogy felgyorsítsa az általános modell magyarázatainak kiszámítását.

Az alábbi ábra a támogatott magyarázók aktuális szerkezetét mutatja be.

[![Gépi tanulási értelmezhetőségi architektúra](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Támogatott gépi tanulási modellek

Az `azureml.interpret` SDK csomagja a következő adatkészlet-formátumokkal betanított modelleket támogatja:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

A magyarázat függvények a modelleket és a folyamatokat is bemenetként fogadják el. Ha egy modell van megadva, a `predict` `predict_proba` modellnek végre kell hajtania az előrejelzési funkciót, vagy amely megfelel a Scikit-egyezménynek. Ha a modell nem támogatja ezt, akkor csomagolja a modell egy `predict` `predict_proba` függvényt, amely ugyanazt az eredményt, mint vagy a Scikit, és használja, hogy a burkoló függvény a kiválasztott magyarázó. Ha egy folyamat meg van adva, a magyarázat függvény feltételezi, hogy a futó folyamat parancsfájl egy előrejelzést ad vissza. Ezzel a csomagolási technikával támogathatja a `azureml.interpret` PyTorch, a TensorFlow és a Keras deep learning keretrendszereken keresztül betanított modelleket, valamint a klasszikus gépi tanulási modelleket.

## <a name="local-and-remote-compute-target"></a>Helyi és távoli számítási cél

A `azureml.interpret` csomag helyi és távoli számítási célokkal egyaránt működik. Ha helyileg fut, az SDK-függvények nem lépnek kapcsolatba egyetlen Azure-szolgáltatással sem. 

A magyarázat ot távolról futtathatja az Azure Machine Learning Compute szolgáltatásban, és naplózhatja a magyarázatadatait az Azure Machine Learning run history szolgáltatásába. Miután ezeket az információkat naplózták, a magyarázatból származó jelentések és vizualizációk könnyen elérhetők az Azure Machine Learning stúdióban felhasználói elemzésre.


## <a name="next-steps"></a>További lépések

Tekintse meg a [helyi](how-to-machine-learning-interpretability-aml.md) és az Azure Machine Learning távszámítási erőforrásokkal kapcsolatos modellek értelmezhetőségét. Tekintse meg a [mintanotebookok](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) további forgatókönyveket.

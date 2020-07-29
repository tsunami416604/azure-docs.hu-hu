---
title: Featurization a AutoML-kísérletekben
titleSuffix: Azure Machine Learning
description: Megtudhatja, milyen featurization-beállításokat Azure Machine Learning az ajánlatokat, és hogyan támogatja az automatikus ML-kísérletek a szolgáltatások fejlesztését.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: 950f258e7380d7fbd25e1a5fe2dd4673ba122c52
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321584"
---
# <a name="featurization-in-automated-machine-learning"></a>Featurization az automatizált gépi tanulásban

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebben az útmutatóban a következőket fogja elsajátítani:

- Milyen featurization-beállítások Azure Machine Learning ajánlatokat.
- A funkciók testre szabása [automatizált gépi tanulási kísérletekhez](concept-automated-ml.md).

A *szolgáltatás-mérnöki* folyamat az adat tartományon alapuló ismeretét használja olyan funkciók létrehozásához, amelyek segítenek a Machine learning (ml) algoritmusok jobb megismerésében. Azure Machine Learning az adatméretezési és-normalizálás technikákat a funkciók mérnöki működésének megkönnyítésére alkalmazza a rendszer. Ezeket a technikákat és a szolgáltatás-fejlesztéseket együttesen *featurization* nevezzük az automatizált gépi tanulásban, vagy *AutoML*, kísérletekben.

Ez a cikk azt feltételezi, hogy már tudja, hogyan konfigurálhat egy AutoML-kísérletet. A konfigurálással kapcsolatos további információkért tekintse meg a következő cikkeket:

- Első [lépésként: az automatikus ml-kísérletek konfigurálása a Pythonhoz készült Azure Machine learning SDK használatával](how-to-configure-auto-train.md).
- Alacsony kódú vagy nem kódolású felhasználói élmény: [automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése a Azure Machine learning Studio használatával](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Featurization konfigurálása

Az automatikus [skálázási és normalizáló technikákat](#featurization) minden automatizált gépi tanulási kísérlet során alapértelmezés szerint alkalmazza a rendszer az adataira. Ezek a technikák olyan featurization-típusok, amelyek *bizonyos* , különböző léptékű funkciókra érzékeny algoritmusokat nyújtanak. Engedélyezheti azonban a további featurization, például a *hiányzó értékek imputálási*, *kódolását*és *átalakítását*is.

> [!NOTE]
> Az automatizált gépi tanulási featurization (például a funkciók normalizálása, a hiányzó adatokat kezelő vagy a numerikus szöveg konvertálása) lépései az alapul szolgáló modell részévé válnak. Ha az előrejelzési modellt használja, a betanítás során alkalmazott featurization-lépéseket a rendszer automatikusan alkalmazza a bemeneti adatokra.

A Python SDK-val konfigurált kísérletek esetében engedélyezheti vagy letilthatja a featurization-beállítást, és további megadhatja a kísérlethez használni kívánt featurization-lépéseket. Ha a Azure Machine Learning Studiot használja, tekintse [meg a featurization engedélyezésének lépéseit](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A következő táblázat a `featurization` [AutoMLConfig osztály](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)elfogadott beállításait mutatja be:

|Featurization-konfiguráció | Leírás|
------------- | ------------- |
|`"featurization": 'auto'`| Azt határozza meg, hogy az előfeldolgozás részeként a rendszer automatikusan végrehajtja az [guardrails és a featurization lépéseket](#featurization) . Ez az alapértelmezett beállítás.|
|`"featurization": 'off'`| Meghatározza, hogy a featurization lépések ne legyenek automatikusan elvégezve.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Megadja, hogy a rendszer testreszabott featurization-lépéseket használ. [Megtudhatja, hogyan szabhatja testre a featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatikus featurization

Az alábbi táblázat összefoglalja az adataira automatikusan alkalmazott technikákat. Ezeket a technikákat az SDK-val vagy a Studióval konfigurált kísérletekre alkalmazza a rendszer. Ha le szeretné tiltani ezt a viselkedést, állítsa be `"featurization": 'off'` az `AutoMLConfig` objektumban.

> [!NOTE]
> Ha úgy tervezi, hogy a AutoML által létrehozott modelleket egy [ONNX-modellbe](concept-onnx.md)exportálja, csak a csillaggal ("*") jelölt featurization-beállítások támogatottak a ONNX formátumban. További információ a [modellek ONNX való átalakításáról](concept-automated-ml.md#use-with-onnx).

|Featurization &nbsp; lépések| Leírás |
| ------------- | ------------- |
|**A nagyfokú és a variancia nélküli funkciók eldobása*** |Ezeket a funkciókat a betanítási és az ellenőrzési készletekből dobja el. Az összes hiányzó értékkel rendelkező szolgáltatásokra vonatkozik, amelyek az összes sorban azonos értékkel rendelkeznek, vagy magas fokú (például kivonatok, azonosítók vagy GUID azonosítók).|
|**Hiányzó értékek imputált értéke*** |Numerikus funkciók esetében az érték az oszlopban szereplő értékek átlagát tartalmazza.<br/><br/>A kategorikus funkciók esetében a leggyakoribb értékkel kell eltulajdonítani a bevonást.|
|**További funkciók előállítása*** |A DateTime funkciók esetében: év, hónap, nap, hét napja, év napja, negyedév, év hete, óra, perc, másodperc.<br/><br/>A szöveges funkciókhoz: unigrams, bigrams és Trigrams alapuló kifejezés gyakorisága. További információ arról, [hogy ez hogyan történik a bertban.](#bert-integration)|
|**Átalakítás és kódolás***|A több egyedi értékkel rendelkező numerikus funkciók átalakítása kategorikus funkciókba.<br/><br/>A kis-és nagymértékű kategorikus funkciók esetében egy gyors kódolást használunk. A rendszer egy-egy gyors kivonatoló kódolást használ a magas fokú, kategorikus funkciókhoz.|
|**Word-beágyazások**|A szöveges Képtulajdonság egy előképzésen alapuló modell használatával alakítja át a szöveges tokenek vektorait a mondatokra. Az egyes Word-dokumentumok beágyazási vektora a többivel együtt a dokumentum-szolgáltatás vektorának előállítására szolgál.|
|**Cél kódolások**|A kategorikus funkciók esetében ez a lépés leképezi az egyes kategóriákat a regressziós problémák átlagos céljával, valamint az osztályok valószínűségét az egyes osztályok számára a besorolási problémák esetében. A rendszer a gyakoriságon alapuló súlyozást és a k-fold kereszt-ellenőrzést alkalmazza, hogy csökkentse a ritka adatkategóriák által okozott leképezés és zaj túlillesztését.|
|**Szöveges cél kódolása**|Szöveges bevitel esetén a rendszer egy halmozott lineáris modellt használ, amelyben a rendszer az egyes osztályok valószínűségét állítja elő.|
|**Bizonyítékok súlyozása (jaj)**|Kiszámítja a jaj-et a kategorikus oszlopok korrelációja alapján a cél oszlophoz. A jaj kiszámításának eredménye az osztályban és az osztályon kívüli valószínűségek arányának naplója. Ez a lépés egységenként egy numerikus funkció oszlopot hoz létre, és eltávolítja a hiányzó értékek és a kiugró adatok explicit módon történő bevonásának szükségességét.|
|**Fürt távolsága**|A k – a csoportosítási modellt jelenti az összes numerikus oszlopban. *K* új szolgáltatásokat (egy új numerikus funkciót) hoz létre, amely az egyes minták távolságát tartalmazza az egyes fürtök középpontját.|

## <a name="data-guardrails"></a>Az adatguardrails

Az *adatok guardrails* segítségével azonosíthatja az adataival kapcsolatos lehetséges problémákat (például a hiányzó értékeket vagy az [osztály egyensúlyhiányát](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)). Emellett segítenek a jobb eredmények javítása érdekében.

A rendszer alkalmazza az guardrails:

- **SDK-kísérletek esetén**: Ha a paraméterek `"featurization": 'auto'` vagy az `validation=auto` `AutoMLConfig` objektumban vannak megadva.
- **Studio-kísérletek**esetén: Ha engedélyezve van az automatikus featurization.

A kísérlethez tartozó guardrails áttekintéséhez tekintse át a következőt:

- Azáltal, hogy `show_output=True` az SDK használatával elküld egy kísérletet.

- A Studióban, az automatikus ML-Futtatás **guardrails** lapján.

### <a name="data-guardrail-states"></a>Adatguardrail állapotok

Az adatguardrails három állapot egyikét jeleníti meg:

|Állam| Leírás |
|----|---- |
|**Telt**| A rendszer nem észlelt adatproblémákat, és nincs szükség beavatkozásra. |
|**Kész**| A módosítások érvénybe lépnek az adatain. Javasoljuk, hogy tekintse át a AutoML által végzett javítási műveleteket, hogy a módosítások megfeleljenek a várt eredményeknek. |
|**Riasztást kap**| A rendszer adatproblémát észlelt, de nem tudta kijavítani. Javasoljuk, hogy módosítsa és javítsa ki a problémát.|

### <a name="supported-data-guardrails"></a>Támogatott adatguardrails

A következő táblázat ismerteti a jelenleg támogatott guardrails, valamint a kísérlet elküldésekor esetlegesen megjelenő társított állapotokat:

Guardrail|status|Trigger feltétele &nbsp; &nbsp;
---|---|---
**Hiányzó szolgáltatási értékek imputálási** |Telt <br><br><br> Kész| A betanítási adatok nem észleltek hiányzó szolgáltatási értékeket. További információ a [hiányzó értékű imputálási.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> A rendszer hiányzó szolgáltatási értékeket észlelt a betanítási adatokban, és imputáltak voltak.
**Magas fokú szolgáltatások kezelését** |Telt <br><br><br> Kész| A rendszer elemezte a bemeneteket, és nem észlelt magas szintű funkciókat. <br><br> A rendszer a Kiemelt funkciókat észlelte a bemenetekben, és kezelte azokat.
**Ellenőrzés felosztásának ellenőrzése** |Kész| Az érvényesítési konfiguráció értékre lett állítva `'auto'` , a betanítási adatként pedig *kevesebb, mint 20 000 sor*szerepel. <br> A betanított modell minden egyes iterációját kereszt-ellenőrzéssel érvényesíti a program. További információ az [érvényesítési](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data)információkról. <br><br> Az érvényesítési konfiguráció értékre lett állítva `'auto'` , a betanítási adatként pedig *több mint 20 000 sor*szerepel. <br> A bemeneti adatok egy betanítási adatkészletbe és egy ellenőrzési adatkészletbe vannak osztva a modell érvényesítéséhez.
**Osztály kiegyensúlyozásának észlelése** |Telt <br><br><br><br><br> Riasztást kap | A rendszer elemezte a bemeneteket, és az összes osztály kiegyensúlyozottan szerepel a betanítási adatokban. Az adatkészletek akkor tekinthetők kiegyensúlyozottnak, ha az egyes osztályok jó ábrázolással rendelkeznek az adatkészletben, a minták számával és arányával mérve. <br><br><br> A rendszer kiegyensúlyozatlan osztályokat észlelt a bemenetekben. A modell torzításának javításához javítsa ki a kiegyenlítési problémát. További információ a [kiegyensúlyozatlan adatvédelemről](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
**Memória-problémák észlelése** |Telt <br><br><br><br> Kész |<br> A kiválasztott értékeket (horizont, lag, gördülő ablak) elemezték, és a rendszer nem észlelt memóriabeli problémákat. További információ az idősorozat- [előrejelzési konfigurációkról](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment). <br><br><br>A kiválasztott értékeket (horizont, lag, gördülő ablak) elemezte a rendszer, és a kísérlet miatt kifogyhat a memória. A késés vagy a működés közbeni ablak konfigurációk ki lettek kapcsolva.
**Gyakoriság észlelése** |Telt <br><br><br><br> Kész |<br> Az idősorozat elemzése megtörtént, és az összes adatpont az észlelt gyakorisággal van igazítva. <br> <br> Az idősorozat elemzése megtörtént, és az észlelt gyakorisággal nem igazított adatpontok észlelhetők. Ezek az adatpontok el lettek távolítva az adatkészletből. További információ az [idősorozat-előrejelzés adatainak előkészítéséről](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Featurization testreszabása

Testreszabhatja a featurization beállításait, így biztosíthatja, hogy a ML-modellek betanításához használt adatokat és szolgáltatásokat a megfelelő előrejelzések képezzék.

A featurizations testreszabásához adja meg  `"featurization": FeaturizationConfig` az `AutoMLConfig` objektumot az objektumban. Ha a kísérlethez a Azure Machine Learning Studiot használja, tekintse meg a [útmutató cikket](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

A támogatott testreszabások a következők:

|Testreszabás|Definíció|
|--|--|
|**Oszlop céljának frissítése**|A megadott oszlop szolgáltatás típusának felülbírálása.|
|**A transzformátor paraméterének frissítése** |Frissítse a megadott átalakító paramétereit. Jelenleg támogatja az *imputált* (mean, leggyakoribb és közepes) és a *HashOneHotEncoder*.|
|**Oszlopok eldobása** |Meghatározza az eldobni kívánt oszlopokat a featurized.|
|**Transzformátorok letiltása**| Megadja a featurization-folyamathoz használandó blokk-átalakítókat.|

Hozza létre az `FeaturizationConfig` OBJEKTUMOT API-hívások használatával:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="bert-integration"></a>BERT-integráció 
A [Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) az automatikus ml featurization rétegében van használatban. Ebben a rétegben bemutatjuk, hogy egy oszlop tartalmaz-e szabad szöveget vagy más típusú adattípusokat, például időbélyegeket vagy egyszerű számokat, és ennek megfelelően szabadkézi. A BERTban a felhasználó által megadott címkéket használva finomítjuk/betanítjuk a modellt, majd kimeneti dokumentum-beágyazásokat végezünk (a BERT esetében ezek a speciális [CLS] jogkivonathoz tartozó végleges rejtett állapotok), más funkciókkal, például az időbélyeg-alapú szolgáltatásokkal (például a hét napjaival), illetve számos jellemző adatkészlettel rendelkező számokkal. 

A BERT engedélyezéséhez GPU-számítást kell használni a betanításhoz. Ha CPU-számítást használ, akkor a BERT helyett a AutoML engedélyezi a BiLSTM DNN Képtulajdonság. A BERT meghívásához be kell állítania a "enable_dnn: true" értéket a automl_settingsban, és GPU-számítást kell használnia (például vm_size = "STANDARD_NC6" vagy magasabb GPU). Tekintse [meg ezt a jegyzetfüzetet példaként](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

A AutoML a következő lépéseket hajtja végre a BERT esetében (kérjük, vegye figyelembe, hogy a "enable_dnn: true" értéket kell beállítania a automl_settingsban, hogy ezek történnek):

1. Az előfeldolgozás, beleértve az összes szöveges oszlop jogkivonatok létrehozása ("StringCast" átalakítót fog látni a végső modell featurization összegzésében. Látogasson el [erre a jegyzetfüzetbe](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) , és tekintse meg, hogyan hozhatja létre a modell featurization összegzését a `get_featurization_summary()` metódus használatával.

```python
text_transformations_used = []
for column_group in fitted_model.named_steps['datatransformer'].get_featurization_summary():
    text_transformations_used.extend(column_group['Transformations'])
text_transformations_used
```

2. Az összes szöveges oszlopot egyetlen szöveges oszlopba fűzi, ezért az utolsó modellben a "StringConcatTransformer" jelenik meg. 

> [!NOTE]
> A BERT implementációja a betanítási minta teljes szöveges hosszát 128 tokenre korlátozza. Ez azt jelenti, hogy az összes szöveges oszlop összefűzése esetén ideális esetben legfeljebb 128 tokennek kell lennie. Ideális esetben, ha több oszlop van jelen, az egyes oszlopokat fel kell metszeni úgy, hogy ez a feltétel teljesül. Ha például két szöveges oszlop szerepel az adattárban, mindkét szöveges oszlopot 64-tokenre kell metszeni (feltéve, hogy mindkét oszlopot egyenlően kell megjeleníteni a végső összefűzött szöveg oszlopban), mielőtt AutoML az információkat. A >128 tokent tartalmazó összefűzött oszlopok esetén a BERT 's tokenizer Layer a 128-tokenekre fogja lerövidíteni ezt a bemenetet.

3. A szolgáltatás-elsöprő lépésekben a AutoML összehasonlítja a BERTt az adatok egy mintáján (a szavak és a megjelenő Word-beágyazások), és meghatározza, hogy a BERT pontos előrelépést eredményezne-e. Ha azt állapítja meg, hogy a BERT jobb, mint az alapterv, a AutoML ezt követően a BERTt használja a Text featurization, amely az optimális featurization stratégia, és a teljes adatok featurizing folytatja. Ebben az esetben a "PretrainedTextDNNTransformer" a végső modellben jelenik meg.

A AutoML jelenleg 100 nyelvet támogat, és az adatkészlet nyelvétől függően a AutoML kiválasztja a megfelelő BERT modellt. Német nyelven a német BERT modellt használjuk. Angol nyelven az angol BERT modellt használjuk. Minden más nyelven a többnyelvű BERT modellt használjuk.

A következő kódban a német BERT modellt indítja el a rendszer, mivel az adatkészlet nyelve "DEU", az [ISO besorolása](https://iso639-3.sil.org/code/hbs)szerint a német nyelvhez tartozó 3 betűs nyelvkód:

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan állíthatja be az automatikus ML-kísérleteket:

    * Első [lépésként: az automatikus ml-kísérletek konfigurálása a Azure Machine learning SDK használatával](how-to-configure-auto-train.md).
    * Alacsony kódú vagy kód nélküli felhasználói felület esetén: [hozzon létre AUTOMATIZÁLT ml-kísérleteket a Azure Machine learning Studióban](how-to-use-automated-ml-for-ml-models.md).

* További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

* További információ a [regressziós modellek automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő betanításról](how-to-auto-train-remote.md).

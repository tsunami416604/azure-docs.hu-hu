---
title: Featurization a AutoML-kísérletekben
titleSuffix: Azure Machine Learning
description: Megtudhatja, milyen featurization-beállításokat Azure Machine Learning ajánlatokat, és hogyan támogatja a szolgáltatások fejlesztését az automatikus ml-kísérleteknél.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 93e18a95e30c21a44f9ca7df92925323930a9ce8
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122429"
---
# <a name="featurization-with-automated-machine-learning"></a>Featurization automatizált gépi tanulással

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből az útmutatóból megtudhatja, hogy milyen featurization-beállításokat kínál, és hogyan szabhatja testre őket az [automatizált gépi tanulási kísérletekhez](concept-automated-ml.md).

A szolgáltatás-mérnöki folyamat az adat tartományon alapuló ismeretét használja olyan funkciók létrehozásához, amelyek segítenek a ML-algoritmusok jobb megismerésében. Azure Machine Learning az adatméretezési és a normalizálás technikákat a funkciók mérnöki működésének megkönnyítése érdekében alkalmazza a rendszer. Ezeket a technikákat és a szolgáltatások fejlesztését együttesen a gépi tanulás, a AutoML és a kísérletek featurization is nevezzük.

Ez a cikk azt feltételezi, hogy már ismeri a AutoML-kísérlet konfigurálásának módját. További részletekért tekintse meg a következő cikkeket:

* A Code első felhasználói felülete: [AUTOMATIZÁLT ml-kísérletek konfigurálása a PYTHON SDK-val](how-to-configure-auto-train.md).
* Alacsony/nem kódokkal kapcsolatos felhasználói élmény: [automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése a Azure Machine learning Studióval](how-to-use-automated-ml-for-ml-models.md)

## <a name="configure-featurization"></a>Featurization konfigurálása

Az automatikus [skálázási és normalizáló technikákat](#featurization) minden automatizált gépi tanulási kísérlet során alapértelmezés szerint alkalmazza a rendszer az adataira. Ezek a skálázási és normalizáló technikák olyan featurization, amelyek *bizonyos* , különböző léptékű funkciókra érzékeny algoritmusokat nyújtanak. Ugyanakkor további featurization is engedélyezhet, például **hiányzó értékeket imputálási, kódolást** és **átalakításokat**.

> [!NOTE]
> Az automatizált gépi tanulás featurization lépései (a funkciók normalizálása, a hiányzó adatkezelés, a szöveg konvertálása a numerikus formátumba stb.) az alapul szolgáló modell részévé válnak. A modell előrejelzésekhez való használatakor a betanítás során alkalmazott azonos featurization-lépéseket automatikusan alkalmazza a rendszer a bemeneti adatokra.

Az SDK-val konfigurált kísérletek esetében engedélyezheti vagy letilthatja a beállítást, `featurization` és további módon megadhatja a kísérlethez használni kívánt featurization-lépéseket. Ha a Azure Machine Learning Studiot használja, tekintse meg a featurization engedélyezése [a következő lépésekkel](how-to-use-automated-ml-for-ml-models.md#customize-featurization)című témakört.

A következő táblázat a `featurization` [AutoMLConfig osztály](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)elfogadott beállításait mutatja be. 

|Featurization-konfiguráció | Leírás|
------------- | ------------- |
|**`"featurization": 'auto'`**| Azt jelzi, hogy az előfeldolgozás részeként a rendszer automatikusan végrehajtja az [guardrails és a featurization lépéseket](#featurization) . **Alapértelmezett beállítás**.|
|**`"featurization": 'off'`**| Azt jelzi, hogy a featurization lépéseket nem kell automatikusan elvégezni.|
|**`"featurization":`&nbsp;`'FeaturizationConfig'`**| Azt jelzi, hogy a testreszabott featurization lépést kell használni. [Megtudhatja, hogyan szabhatja testre a featurization](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatikus featurization

Az alábbi táblázat összefoglalja az adataira automatikusan alkalmazott technikákat. Ez az SDK-n vagy a studión keresztül konfigurált kísérletek esetében fordul elő. Ha le szeretné tiltani ezt a viselkedést, állítsa be `"featurization": 'off'` az `AutoMLConfig` objektumban.

> [!NOTE]
> Ha úgy tervezi, hogy az automatikus ML által létrehozott modelleket egy [ONNX-modellbe](concept-onnx.md)exportálja, a ONNX formátuma csak a *-val jelzett featurization-beállításokat támogatja. További információ a [modellek ONNX való átalakításáról](concept-automated-ml.md#use-with-onnx). 

|Featurization &nbsp; lépések| Leírás |
| ------------- | ------------- |
|**A nagyfokú és a variancia nélküli funkciók eldobása*** |Ezeket a betanítási és érvényesítési készletekből elhúzhatja, beleértve az összes hiányzó értékkel rendelkező szolgáltatásokat, az összes sorban vagy a magas fokú (például kivonatok, azonosítók vagy GUID azonosítók) értéket.|
|**Hiányzó értékek imputált értéke*** |Numerikus funkciók esetében az oszlopban szereplő értékek átlaga.<br/><br/>A kategorikus funkciók esetében a leggyakoribb értékkel kell beszámítani az imputált funkciókat.|
|**További funkciók előállítása*** |A DateTime funkciók esetében: év, hónap, nap, hét napja, év napja, negyedév, év hete, óra, perc, másodperc.<br/><br/>A szöveges funkciókhoz: unigrams, bi-gramm és Tri-Character-gramm alapján történő kifejezés gyakorisága.|
|**Átalakítás és kódolás***|A kevés egyedi értékkel rendelkező numerikus funkciók a kategorikus funkciókba lesznek átalakítva.<br/><br/>A rendszer egy gyors kódolást végez az alacsony szintű a magas fokú, egy gyors kivonatolású kódolás.|
|**Word-beágyazások**|Szöveges Képtulajdonság, amely egy előre betanított modell használatával átalakítja a szöveges tokenek vektorait a mondatokra. A dokumentumokban lévő összes Word beágyazási vektora össze van összesítve egy dokumentum-szolgáltatás vektorának létrehozásához.|
|**Cél kódolások**|A kategorikus funkciók esetében az egyes kategóriákat a regressziós problémák átlagos céljának megfelelően leképezi, valamint az osztály valószínűségét az egyes osztályok számára a besorolási problémák esetén. A rendszer a gyakoriságon alapuló súlyozást és a k-fold kereszt-ellenőrzést alkalmazza a leképezések és a ritka adatkategóriák által okozott zaj csökkentése érdekében.|
|**Szöveges cél kódolása**|Szöveges bevitel esetén a rendszer egy halmozott lineáris modellt használ, amelyben a rendszer az egyes osztályok valószínűségét állítja elő.|
|**Bizonyítékok súlyozása (jaj)**|Kiszámítja a jaj-et a kategorikus oszlopok korrelációja alapján a cél oszlophoz. A számítás az osztályban és az osztályon kívüli valószínűségek arányának naplója. Ez a lépés egy numerikus funkció oszlopát jeleníti meg, és eltávolítja a hiányzó értékek és a kiugró eljárás explicit módon történő bevonásának szükségességét.|
|**Fürt távolsága**|A k – a csoportosítási modellt jelenti az összes numerikus oszlopon.  Az új funkciók megjelenítése, egy új numerikus szolgáltatás, amely az egyes minták távolságát az egyes fürtök középpontját tartalmazza.|

## <a name="data-guardrails"></a>Az adatguardrails

Az adatok guardrails segítségével azonosíthatja az adataival kapcsolatos lehetséges problémákat (például a hiányzó értékeket, az [osztály egyensúlyhiányát](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data)), és segítheti a javító műveleteket a jobb eredmények érdekében. 

Az adatguardrails alkalmazva 

* **SDK-kísérletek**esetén, ha a paraméterek `"featurization": 'auto'` vagy az `validation=auto` `AutoMLConfig` objektumban vannak megadva.
* **Studio-kísérletek**esetén, ha engedélyezve van az *automatikus featurization* .  

Áttekintheti a kísérlethez kapcsolódó guardrails

* `show_output=True`Egy kísérletnek a PYTHON SDK-val való elküldéséhez.

* A Studióban, az automatikus ML **guardrails** lapján.

### <a name="data-guardrail-states"></a>Adatguardrail állapotok

Az adatguardrails három állapot egyikét jeleníti meg: **átadott**, **elkészült**vagy **riasztásos**.

|Állam| Leírás |
|----|---- |
|**Telt**| Nem észlelhető adatprobléma, és nincs szükség felhasználói beavatkozásra. |
|**Kész**| A módosítások érvénybe lépnek az adatain. Javasoljuk, hogy a felhasználók ellenőrizzék az automatikus ML-es javítási műveleteket, hogy a módosítások megfeleljenek a várt eredményeknek. |
|**Riasztást kap**| A rendszer olyan adatproblémát észlelt, amely nem orvosolható. Javasoljuk, hogy a felhasználók módosítsák és javítsák a problémát.| 

A következő táblázat ismerteti a jelenleg támogatott guardrails, valamint azokat a társított állapotokat, amelyeket a felhasználók a kísérlet elküldésekor megkaphatnak.

Guardrail|Állapot|Trigger feltétele &nbsp; &nbsp;
---|---|---
**Hiányzó szolgáltatási értékek imputálási** |*Telt* <br><br><br> *Kész*| A betanítási adatok nem észleltek hiányzó szolgáltatási értékeket. További információ a [hiányzó értékek imputálási.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> A rendszer hiányzó szolgáltatási értékeket észlelt a betanítási adatokban, és imputált.
**Magas fokú szolgáltatások kezelését** |*Telt* <br><br><br> *Kész*| A rendszer elemezte a bemeneteket, és nem észlelt magas fokú sarkalatos funkciót. További információ a [magas fokú kardinális funkciók észleléséről.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> A rendszer a feladatokhoz tartozó magas minőségi funkciókat észlelte, és kezelte azokat.
**Ellenőrzés felosztásának ellenőrzése** |*Kész*| Az érvényesítési konfiguráció az "Auto" értékre van állítva, a betanítási adatként pedig **kevesebb mint 20 000 sor**szerepel. <br> A betanított modell minden egyes iterációját kereszt-ellenőrzés útján érvényesítjük. További információ az [érvényesítési](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data) információkról. <br><br> Az érvényesítési konfiguráció az "Auto" értékre van állítva, a betanítási adatként pedig **több mint 20 000 sor**szerepel. <br> A bemeneti adatok egy betanítási adatkészletbe és egy ellenőrzési adatkészletbe vannak osztva a modell érvényesítéséhez.
**Osztály kiegyensúlyozásának észlelése** |*Telt* <br><br><br><br><br> *Riasztást kap* | A rendszer elemezte a bemeneteket, és az összes osztály kiegyensúlyozottan szerepel a betanítási adatokban. Az adatkészletek akkor tekinthetők kiegyensúlyozottnak, ha az egyes osztályok jó ábrázolással rendelkeznek az adatkészletben, a minták számával és arányával mérve. <br><br><br> A rendszer kiegyensúlyozatlan osztályokat észlelt a bemenetekben. A modell torzításának javításához javítsa ki a kiegyenlítési problémát. További információ a [kiegyensúlyozatlan adatvédelemről.](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data)
**Memória-problémák észlelése** |*Telt* <br><br><br><br> *Kész* |<br> A kiválasztott {Horizon, lag, Rolling Window} érték elemzése megtörtént, és a rendszer nem észlelt memóriabeli problémákat. További információ az idősorozat- [előrejelzési konfigurációkról.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment) <br><br><br>A kiválasztott {Horizon, lag, Rolling Window} értékek elemzése megtörtént, és a kísérlet valószínűleg kifogy a memóriából. A késés vagy a működés közbeni ablak konfigurációja ki van kapcsolva.
**Gyakoriság észlelése** |*Telt* <br><br><br><br> *Kész* |<br> Az idősorozat elemzése megtörtént, és az összes adatpont az észlelt gyakorisággal van igazítva. <br> <br> Az idősorozat elemzése megtörtént, és az észlelt gyakorisággal nem igazított adatpontok észlelhetők. Ezek az adatpontok el lettek távolítva az adatkészletből. További információ az [idősorozat-előrejelzés adatainak előkészítéséről.](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data)

## <a name="customize-featurization"></a>Featurization testreszabása

Testreszabhatja a featurization beállításait, így biztosíthatja, hogy a ML-modellek betanításához használt adatokat és szolgáltatásokat a vonatkozó előrejelzések is kiszolgálják. 

A featurizations testreszabásához adja meg  `"featurization": FeaturizationConfig` az `AutoMLConfig` objektumot az objektumban. Ha a kísérlethez a Azure Machine Learning Studiot használja, tekintse meg a [útmutató](how-to-use-automated-ml-for-ml-models.md#customize-featurization)című témakört.

A támogatott Testreszabás az alábbiakat tartalmazza:

|Testreszabás|Definíció|
|--|--|
|**Oszlop céljának frissítése**|Felülbírálja a szolgáltatás típusát a megadott oszlophoz.|
|**A transzformátor paraméterének frissítése** |A megadott átalakító paramétereinek frissítése. Jelenleg támogatja az imputált (mean, leggyakoribb & medián) és a HashOneHotEncoder.|
|**Oszlopok eldobása** |Az eldobni kívánt oszlopok featurized.|
|**Transzformátorok letiltása**| A featurization-folyamathoz használandó transzformátorok letiltása.|

Hozza létre a FeaturizationConfig objektumot az API-hívások használatával:
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
## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan állíthatja be az automatizált ML-kísérleteket,

    * A Code Experience ügyfelei számára: [automatikus ml-kísérletek konfigurálása a Azure Machine learning SDK-val](how-to-configure-auto-train.md).
    * Az alacsony/nem kódokkal rendelkező ügyfelek számára: [hozza létre az automatizált gépi tanulási kísérleteket Azure Machine learning Studióban](how-to-use-automated-ml-for-ml-models.md).

* További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).

* További információ a [regressziós modell automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.

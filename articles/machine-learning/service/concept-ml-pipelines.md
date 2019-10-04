---
title: Mi az a ML-folyamat?
titleSuffix: Azure Machine Learning
description: Ebben a cikkben megismerkedhet a machine learning-folyamatokat hozhat létre Azure Machine Learning-SDK-val a Python és a folyamatok használatának előnye. Machine learning (gépi tanulás) folyamatok segítségével az adatszakértők hozhat létre, optimalizálhat és kezelhet a machine learning-munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 08/08/2019
ms.custom: seodec18
ms.openlocfilehash: 07efde7c3664ba1866e59f23c31b9c385ed9c366
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71035489"
---
# <a name="what-are-ml-pipelines-in-azure-machine-learning"></a>Mik a Azure Machine Learning ML-folyamatok?

Ismerje meg, hogyan hozhat létre és kezelhet Azure Machine Learningekkel rendelkező gépi tanulási folyamatokat. 

Machine learning (gépi tanulás) folyamatok, az adatszakértők, adatmérnökök és informatikai szakemberek számára használatával közösen dolgozhatnak a lépéseit:
+ Adat-előkészítés, például normalizations és átalakítások
+ Modell betanítása
+ Modell értékelése
+ Környezet

Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md).

![Gépi tanulási folyamatok Azure Machine Learning](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-adatcsatorna-technológiát használok?

Az Azure-felhő számos más folyamatot is biztosít, amelyek mindegyike más célra szolgál. A következő táblázat felsorolja a különböző folyamatokat, és azt, hogy mire szolgálnak:

| Folyamat | Művelet | Kanonikus cső |
| ---- | ---- | ---- |
| Azure Machine Learning folyamatok | Az újrafelhasználható gépi tanulási munkafolyamatokat definiálja, amelyek sablonként használhatók a gépi tanulási forgatókönyvekhez. | Adat> modell |
| [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | A feladatok végrehajtásához szükséges adatáthelyezési,-átalakítási és-ellenőrzési tevékenységek csoportjai.  | Adat>i adatszolgáltatások |
| [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Az alkalmazás folyamatos integrációja és továbbítása bármely platformra/felhőbe  | Code-> alkalmazás/szolgáltatás |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Miért érdemes létrehozni az Azure Machine Learning folyamatok?

A gépi tanulási folyamatok gyorsabbá, hordozhatóságot és újrahasznosítást tesznek jobbá a munkafolyamatban, így az infrastruktúra és az automatizálás helyett a szaktudásra, a gépi tanulásra koncentrálhat.

A folyamatok több **lépésből**állnak, amelyek a folyamat különböző számítási egységei. Az egyes lépések egymástól függetlenül futtathatók, és elkülönített számítási erőforrásokat is használhatnak.
A független lépések lehetővé teszik, hogy egyszerre több adatszakértő is működjön ugyanazon a folyamaton, és ne legyenek túlterhelt számítási erőforrások, és az egyes lépésekhez egyszerűen különböző számítási típusokat/méreteket használhat.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újrafuttatni kívánt különböző lépésekre ugrik, például egy frissített tanítási parancsfájlt, és kihagyja a nem módosult lépéseket. Az azonos paradigmát változatlan parancsfájlok a lépés végrehajtásához szükséges vonatkozik. Ez a funkció segít elkerülni a költséges és időigényes lépéseket, például az adatfeldolgozást és-átalakítást, ha a mögöttes adat nem módosult.

A Azure Machine Learning számos eszközkészletet és keretrendszert használhat, például PyTorch vagy TensorFlow a folyamat egyes lépéseihez. Az Azure koordinátákat a különböző [számítási célok](concept-azure-machine-learning-architecture.md) között, így a köztes adatok könnyedén megoszthatók az alsóbb rétegbeli számítási célok használatával.

A [folyamat-kísérletek metrikáit](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) közvetlenül Azure Portal vagy a munkaterület kezdőlapján [(előzetes verzió)](https://ml.azure.com)követheti nyomon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat újrafuttatását bármely platformról vagy veremből.

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Legfontosabb előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;fut.**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. A rendelkezésre álló számítási erőforrások hatékony felhasználása a különböző számítási célokon, például a HDInsight, a GPU-adatelemzési virtuális gépeken és a Databricks futó egyes folyamatok lépéseinek futtatásával.|
|**Újrahasznosíthatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követést és verziókezelés**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="the-python-sdk-for-pipelines"></a>A Python SDK folyamatok

[A PYTHON SDK használatával](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) hozza létre a ml-folyamatokat a kívánt integrált fejlesztői környezetbe (ide) vagy Jupyter-jegyzetfüzetbe. Az Azure Machine Learning SDK alkalmazás-előkészítés és-folyamatok lépéseit párhuzamosan futtatni, amikor nincs függőség nem található imperatív szerkezeteket kínál. 

Deklaratív adatok-függőségek használatával, optimalizálhatja a feladatokat. Az SDK a gyakori feladatokhoz, például az adatátvitelhez és a modell-közzétételhez készült előre összeállított modulok keretrendszerét tartalmazza. A keretrendszert kiterjesztheti úgy, hogy saját konvenciókat modellez, és a folyamatokon belül egyéni lépéseket is felhasználhat. A számítási célokat és a tárolási erőforrásokat közvetlenül az SDK-ból is kezelheti.

Mentse a folyamatokat sablonként, és telepítse őket egy REST-végpontra a Batch-pontozási vagy átképzési feladatokhoz.

Két Python-csomag található a Azure Machine Learning: [azureml-pipeline-Core](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a azureml-pipeline- [lépéseket](https://docs.microsoft.com/python/api/azureml-pipeline-steps/?view=azure-ml-py)tartalmazó folyamatokhoz. A gyors kezdéshez használja az előre elkészített modulok egyikét, például:

* Python-szkript futtatása egy lépésben a [PythonScriptStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)
* A tárolási lehetőségek közötti adatátvitel a [DataTransferStep](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep)
* AutoML-folyamat lépésének létrehozása a [AutoMLStep](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlstep)

## <a name="next-steps"></a>További lépések

+ Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md).

+ Megtudhatja, hogyan [futtathat batch-előrejelzéseket nagy mennyiségű adattal](tutorial-pipeline-batch-scoring-classification.md).

+ Tekintse [meg a folyamatok SDK-dokumentációját](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py).

+ Próbálja ki például a [Azure Machine learning-folyamatokat](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines)bemutató Jupyter notebookokat. Útmutató [jegyzetfüzetek futtatásához a szolgáltatás megismeréséhez](samples-notebooks.md).

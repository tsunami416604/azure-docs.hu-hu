---
title: 'Folyamatok: gépi tanulási munkafolyamatok optimalizálása'
titleSuffix: Azure Machine Learning service
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
ms.openlocfilehash: a9965dbbca939f566048312af921061a188ee50d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884244"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Újrafelhasználható ML-folyamatok létrehozása Azure Machine Learning szolgáltatásban

Ebben a cikkben megismerheti a Pythonhoz készült Azure Machine Learning SDK-val felépíthető gépi tanulási folyamatokat, valamint a folyamatok használatának előnyeit.

## <a name="what-are-machine-learning-pipelines"></a>Mik azok a machine learning-folyamatokat?

Machine learning (gépi tanulás) folyamatok, az adatszakértők, adatmérnökök és informatikai szakemberek számára használatával közösen dolgozhatnak a lépéseit:
+ Adat-előkészítés, például normalizations és átalakítások
+ Modell betanítása
+ Modell értékelése
+ Környezet

Az alábbi ábrán egy példa folyamat látható:

![Gépi tanulási folyamatok Azure Machine Learning szolgáltatásban](./media/concept-ml-pipelines/pipeline-flow.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Milyen Azure-adatcsatorna-technológiát használok?

Az Azure-felhő számos más folyamatot is biztosít, amelyek mindegyike más célra szolgál. A következő táblázat felsorolja a különböző folyamatokat, és azt, hogy mire szolgálnak:

| Folyamat | Művelet | Kanonikus cső |
| ---- | ---- | ---- |
| Azure Machine Learning folyamatok | Az újrafelhasználható gépi tanulási munkafolyamatokat definiálja, amelyek sablonként használhatók a gépi tanulási forgatókönyvekhez. | Adat> modell |
| [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | A feladatok végrehajtásához szükséges adatáthelyezési,-átalakítási és-ellenőrzési tevékenységek csoportjai.  | Adat>i adatszolgáltatások |
| [Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | Az alkalmazás folyamatos integrációja és továbbítása bármely platformra/felhőbe  | Code-> alkalmazás/szolgáltatás |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Miért érdemes létrehozni az Azure Machine Learning folyamatok?

A Pythonhoz készült [Azure Machine learning SDK](#the-python-sdk-for-pipelines) -val ml-folyamatokat hozhat létre, valamint elküldheti és nyomon követheti az egyes folyamatok futtatásait.

A folyamatok optimalizálhatja a munkafolyamatot az egyszerűség, gyorsabb, hordozhatósága és újból. Azure Machine Learningekkel rendelkező folyamatok létrehozásakor az infrastruktúra és az automatizálás helyett a szakértelemre, a gépi tanulásra koncentrálhat.

A folyamatok több lépésből állnak, amelyek a folyamat különböző számítási egységei. Az egyes lépések egymástól függetlenül futtathatók, és elkülönített számítási erőforrásokat is használhatnak. Ez lehetővé teszi, hogy egyszerre több adatszakértő is működjön ugyanazon a folyamaton, és ne legyenek túlterhelt számítási erőforrások, és az egyes lépésekhez egyszerűen különböző számítási típusokat/méreteket kell használni.

A folyamat megtervezése után a folyamat betanítási ciklusa gyakran nagyobb finomhangolást mutat. Amikor újrafuttat egy folyamatot, a Futtatás az újrafuttatni kívánt különböző lépésekre ugrik, például egy frissített tanítási parancsfájlt, és kihagyja a nem módosult lépéseket. Az azonos paradigmát változatlan parancsfájlok a lépés végrehajtásához szükséges vonatkozik. Ez a funkció segít elkerülni a költséges és időigényes lépéseket, például az adatfeldolgozást és-átalakítást, ha a mögöttes adat nem módosult.

A Azure Machine Learning számos eszközkészletet és keretrendszert használhat, például PyTorch vagy TensorFlow a folyamat egyes lépéseihez. Az Azure koordinátákat a különböző [számítási célok](concept-azure-machine-learning-architecture.md) között, így a köztes adatok könnyedén megoszthatók az alsóbb rétegbeli számítási célok használatával.

Is [követheti a metrikákat, a folyamat kísérletek](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) közvetlenül az Azure Portalon. A folyamat közzététele után beállíthat egy REST-végpontot, amely lehetővé teszi a folyamat bármely platformról vagy veremből való újrafuttatását.

## <a name="key-advantages"></a>Főbb előnyök

A gépi tanulási munkafolyamatok folyamatainak használatának fő előnyei a következők:

|Legfontosabb előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;fut.**|A lépéseket párhuzamosan vagy egymás után, megbízható és felügyelet nélküli módon történő futtatásra ütemezze. Az adatok előkészítése és modellezése az elmúlt napokban vagy hetekben is elvégezhető, és a folyamatok lehetővé teszik, hogy a folyamat futása közben más feladatokra összpontosítson. |
|**Különböző számítás**|Több folyamat is használható, amelyek megbízhatóan vannak összehangolva a heterogén és skálázható számítási erőforrások és a tárolási hely között. Külön folyamat lépéseinek futtatása különböző számítási célokon, például HDInsight, GPU-adatelemzési virtuális gépeken és Databricks. Ez hatékonyan kihasználja a rendelkezésre álló számítási lehetőségeket.|
|**Újrahasznosíthatóság**|Hozzon létre folyamat-sablonokat bizonyos forgatókönyvekhez, például az újraképzéshez és a Batch-pontozáshoz. A külső rendszerekből származó közzétett folyamatokat egyszerű REST-hívásokkal aktiválhatja.|
|**Nyomon követést és verziókezelés**|Az adatok és az eredmények elérési útjának manuális nyomon követése helyett használja a folyamatok SDK-t az adatforrások, bemenetek és kimenetek explicit elnevezéséhez és verziójának megkereséséhez. A szkriptek és az információk külön is kezelhetők a hatékonyság növelése érdekében.|
|**Együttműködés**|A folyamatok lehetővé teszik az adatszakértők számára, hogy működjenek együtt a gépi tanulási tervezési folyamat minden területén, miközben egyidejűleg dolgozhatnak a folyamat lépésein.|

## <a name="the-python-sdk-for-pipelines"></a>A Python SDK folyamatok

A Python használatával a gépi Tanulási folyamatokat hozhat létre. Az Azure Machine Learning SDK alkalmazás-előkészítés és-folyamatok lépéseit párhuzamosan futtatni, amikor nincs függőség nem található imperatív szerkezeteket kínál. Használhatja a Jupyter-jegyzetfüzetekben vagy más előnyben részesített IDE-ben.

Deklaratív adatok-függőségek használatával, optimalizálhatja a feladatokat. Az SDK a gyakori feladatokhoz, például az adatátvitelhez és a modell-közzétételhez készült előre összeállított modulok keretrendszerét tartalmazza. Kiterjesztheti a keretrendszert a saját konvenciók modellezésére úgy, hogy olyan egyéni lépéseket valósít meg, amelyek felhasználhatók a folyamatok között. A számítási célokat és a tárolási erőforrásokat közvetlenül az SDK-ból is kezelheti.

A folyamatokat sablonként mentheti, és egy REST-végpontra telepítheti, így kötegelt pontozási vagy átképzési feladatokat ütemezhet.

Ha szeretné megtudni, hogyan hozhat létre sajátt, tekintse meg a folyamatok és a jegyzetfüzetek [PYTHON SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) -dokumentációját a következő szakaszban.

## <a name="example-notebooks"></a>Példa notebookok

A következő notebookok folyamatokat az Azure Machine Learning bemutatása: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md).

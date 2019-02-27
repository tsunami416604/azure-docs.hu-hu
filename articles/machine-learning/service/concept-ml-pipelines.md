---
title: 'A folyamatok: machine learning-munkafolyamatok optimalizálása'
titleSuffix: Azure Machine Learning service
description: Ebben a cikkben megismerkedhet a machine learning-folyamatokat hozhat létre Azure Machine Learning-SDK-val a Python és a folyamatok használatának előnye. A gépi tanulási (ML-) folyamatokat az adatelemzők gépi tanulási munkafolyamatok létrehozásához, optimalizálásához és felügyeletéhez használják.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 12/4/2018
ms.custom: seodec18
ms.openlocfilehash: d4bef53a21e6ab7b55c16e27083b818929fbd47c
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879254"
---
# <a name="build-machine-learning-pipelines-with-the-azure-machine-learning-service"></a>Hozhat létre a machine learning-folyamatokat az Azure Machine Learning szolgáltatással

Ebben a cikkben megismerkedhet a machine learning-folyamatokat hozhat létre az Azure Machine Learning-SDK Pythonhoz készült, és a folyamatok használatának előnye.

## <a name="what-are-machine-learning-pipelines"></a>Mik azok a machine learning-folyamatokat?

Machine learning (gépi tanulás) folyamatok, az adatszakértők, adatmérnökök és informatikai szakemberek számára használatával közösen dolgozhatnak a lépéseit:
+ Adat-előkészítés, például normalizations és átalakítások
+ Modell betanítása
+ Modell értékelése
+ Környezet 

Az alábbi ábrán látható egy példa folyamatot:

![Machine learning-folyamatokat az Azure Machine Learning szolgáltatásban.](./media/concept-ml-pipelines/pipelines.png)

### <a name="which-azure-pipeline-technology-should-i-use"></a>Melyik Azure folyamat technológia használjam?

Az Azure-felhő nyújt számos más folyamatokban, minden más céllal. Az alábbi táblázat a különböző folyamatok, és hogy mire szolgálnak a:

| Folyamat | Művelet | Canonical cső |
| ---- | ---- | ---- |
| Az Azure Machine Learning-folyamatok | Meghatározza az újrafelhasználható machine learning-munkafolyamatok, amelyek a teljes gépi tanulási forgatókönyvek sablonként használható. | Modell-adatok > |
| [Azure Data Factory-folyamatok](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Csoportokkal kapcsolatos adatmozgatás átalakítása és vezérlési tevékenységek a feladat végrehajtásához szükséges.  | Adat-adatok > |
| [Az Azure-folyamatok](https://azure.microsoft.com/services/devops/pipelines/) | A folyamatos integrációt és teljesítést az alkalmazás bármely platform bármely felhőbeli  | Kód -> alkalmazás/szolgáltatás |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Miért érdemes létrehozni az Azure Machine Learning folyamatok?

Használhatja a [Azure Machine Learning SDK Pythonhoz készült](#the-python-sdk-for-pipelines) gépi Tanulási folyamatok is nyújt, és nyomon követése az egyes folyamatok létrehozásához.

A folyamatok optimalizálhatja a munkafolyamatot az egyszerűség, gyorsabb, hordozhatósága és újból. Az Azure Machine Learning folyamatok készítésekor összpontosíthat szaktudását, a machine learning, az infrastruktúra helyett.

Eltérő lépésekkel lehetővé teszi csak azokat a lépéseket kell, a munkafolyamat tesztelése és finomhangolása futtassa újra. Egy lépésre egy számítási egységben, a folyamat. A fenti ábrán látható, a feladat az adatok előkészítéséről sok lépésből magába foglaló. Ezeket a lépéseket tartalmazza, de nem kizárólagosan normalizálási, átalakítási, ellenőrzés és featurization. Adatforrások és a köztes adatok felhasználják a folyamat, mely menti számítási időt és erőforrásokat. 

Miután a folyamat arra tervezték, nincs gyakran több finomhangoló körül a betanítási ciklus a folyamat. Ha újra futtathatja egy folyamatot, a futtatási ugrik a lépéseket, amelyek újra kell futtatnia, például egy frissített tanítási szkriptet, és kihagyja a mi nem változott. Az azonos paradigmát változatlan parancsfájlok a lépés végrehajtásához szükséges vonatkozik. 

Az Azure Machine Learning használható különböző eszközökre és keretrendszereket, mint a Microsoft Cognitive Toolkit vagy TensorFlow, a folyamat minden lépése. Az Azure koordináták között a különböző [számítási céljainak](concept-azure-machine-learning-architecture.md) használhatja, hogy a köztes adatokat az alsóbb rétegbeli számítási példányokkal rendelkező könnyen megoszthatók. 

Is [követheti a metrikákat, a folyamat kísérletek](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) közvetlenül az Azure Portalon. 

## <a name="key-advantages"></a>Főbb előnyök

Fő előnye az, hogy a teljes gépi tanulási munkafolyamatok folyamatok létrehozásához a következők:

|Legfontosabb előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;fut.**|Ütemezzen néhány lépést, amelyek párhuzamosan vagy egymás után futnak megbízható módon, felügyelet nélkül. Mivel adatelőkészítéshez és modellezés is utolsó napok vagy hetek, most összpontosíthat más feladatok a folyamat futása közben. |
|**Vegyes és sokféle számítási**|Több folyamatot, amely megbízhatóan koordinálja a heterogén és méretezhető számítási erőforrások és a tárolók használata. A folyamat egyes lépéseit a különböző számítási célokhoz, például a HDInsight, a GPU adatelemzési virtuális gépek és a Databricks futtathatja. Ez lehetővé teszi a rendelkezésre álló számítási lehetőségek hatékony kihasználását.|
|**Újrahasznosíthatóság**|Bizonyos forgatókönyvek esetén, például az átképezési és kötegelt pontozási folyamatokat is sablonok kialakítása. Egyszerű REST-hívásokon keresztül a külső rendszerekből elindíthatja őket.|
|**Nyomon követést és verziókezelés**|Helyett manuálisan követési adatok és az eredmény elérési iterálni, használatával az egyes folyamatok SDK explicit módon adja és verzió az adatok adatforrásokat, bemenetként, és adja vissza. Parancsfájlok és megnövelt hatékonyság adatok külön-külön is kezelheti.|

## <a name="the-python-sdk-for-pipelines"></a>A Python SDK folyamatok

A Python használatával a gépi Tanulási folyamatokat hozhat létre. Az Azure Machine Learning SDK alkalmazás-előkészítés és-folyamatok lépéseit párhuzamosan futtatni, amikor nincs függőség nem található imperatív szerkezeteket kínál. Használhatja azt a Jupyter notebooks, vagy egy másik által választott integrált fejlesztői környezetben. 

Deklaratív adatok-függőségek használatával, optimalizálhatja a feladatokat. Az SDK olyan keretrendszer, előre elkészített méretére vonatkozó gyakori feladatokat, például az adatforgalom és a modell közzétételi tartalmazza. A keretrendszer egyéni lépéseket, amelyek újrafelhasználható folyamatok közötti implementálásával saját konvenciók modellezésére bővítheti. Közvetlenül az SDK-t a számítási célokhoz és a tárolási erőforrások is kezelheti.

A folyamatok Mentés sablonként, és telepítse őket a REST-végponton, így a kötegelt pontozás vagy megőrzési feladatokat ütemezhet.

Megtudhatja, hogyan hozhat létre a saját, tekintse meg a [folyamatok Python SDK referenciadokumentumai](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) és a következő szakaszban a notebookot.

## <a name="example-notebooks"></a>Példa notebookok
 
A következő notebookok folyamatokat az Azure Machine Learning bemutatása: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [az első folyamat létrehozása](how-to-create-your-first-pipeline.md).

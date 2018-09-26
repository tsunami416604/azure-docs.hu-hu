---
title: Machine learning-folyamatokat – az Azure Machine Learning-szolgáltatás készítése
description: Ebben a cikkben megismerkedhet a machine learning-folyamatokat hozhat létre Azure Machine Learning-SDK-val a Python és a folyamatok használatának előnye. Machine learning (gépi tanulás) folyamatok segítségével az adatszakértők hozhat létre, optimalizálhat és kezelhet a machine learning-munkafolyamatokat.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 09/24/2018
ms.openlocfilehash: e1181d07ed51b1edf5da68d759d871703bd1540f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47162780"
---
# <a name="pipelines-and-azure-machine-learning"></a>Folyamatok és az Azure Machine Learning

Ebben a cikkben megismerkedhet a machine learning-folyamatokat hozhat létre Azure Machine Learning-SDK-val a Python és a folyamatok használatának előnye.

## <a name="what-are-machine-learning-pipelines"></a>Mik azok a machine learning-folyamatokat?

Machine learning (gépi tanulás) folyamatok segítségével az adatszakértők hozhat létre, optimalizálhat és kezelhet a machine learning-munkafolyamatokat. Egy tipikus folyamat magában foglalja a lépéseit, amelyek a következő területeken:

+ Adat-előkészítés, például normalizations és átalakítások
+ A modellek betanítása, például a finomhangolással hyper és érvényesítés
+ Modell telepítése és kiértékelése  

Az alábbi ábrán látható egy példa folyamatot:

[ ![Machine learning-folyamatokat az Azure Machine Learning szolgáltatásban](./media/concept-ml-pipelines/pipelines.png) ] (. / media/concept-ml-pipelines/machine-learning-pipelines-big.png#lightbox)

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Miért érdemes létrehozni az Azure Machine Learning folyamatok?

A [Azure Machine Learning SDK Pythonhoz készült](#the-python-sdk-for-pipelines) nyújt, és nyomon követése az egyes folyamatok, valamint a gépi Tanulási folyamatok létrehozására használható.

A folyamatok optimalizálhatja a munkafolyamatot az egyszerűség, gyorsabb, hordozhatósága és újból. Az Azure Machine Learning folyamatok készítésekor összpontosíthat tapasztalatai legjobb &mdash; gépi tanulás &mdash; infrastruktúra helyett.

Eltérő lépésekkel lehetővé teszi csak azokat a lépéseket, a munkafolyamat tesztelése és finomhangolása kell futtatnia. Egy lépésre egy számítási egységben, a folyamat. A fenti ábrán látható, a feladat az adatok előkészítésének többek között számos lépést is igénybe vehet, de nem kizárólagosan a, normalizálási, átalakítási, ellenőrzés és featurization.

Miután a folyamat arra tervezték, nincs gyakran több finomhangoló a folyamat a betanítási hurok körül. Ha újra futtathatja egy folyamatot, a futtatási ugrik a lépéseket, amelyek újra kell futtatnia, például egy frissített tanítási szkriptet, és kihagyja a mi nem változott. Az azonos paradigmát változatlan parancsfájlok a lépés végrehajtásához szükséges vonatkozik. 

Az Azure Machine Learning használható különböző eszközökre és keretrendszereket, mint a Microsoft Cognitive Toolkit, tensorflow-hoz vagy az egyes lépések a folyamatban. Az Azure koordináták között a különböző [számítási céljainak](concept-azure-machine-learning-architecture.md) használhatja, hogy a köztes adatokat az alsóbb rétegbeli számítási példányokkal rendelkező könnyen megoszthatók. 

## <a name="key-advantages"></a>Főbb előnyök

A következő fő előnye az, hogy a teljes gépi tanulási munkafolyamatok folyamatok létrehozásához:

|Legfontosabb előnye|Leírás|
|:-------:|-----------|
|**Felügyelet nélküli&nbsp;fut.**|Néhány lépéssel párhuzamosan vagy a feladatütemezés futtatása egy megbízható és a felügyelet nélküli módon ütemezhet. Adatelőkészítés és modellezés óta is utolsó napokat vagy heteket, most összpontosíthat más feladatok a folyamat futása közben. |
|**Vegyes és sokféle számítási**|Több folyamatot, amely megbízhatóan koordinálja a heterogén és méretezhető számítási erőforrások és a tárolók használata. A folyamat egyes lépéseit futtatni a különböző számítási célhelyei, például a HDInsight, GPU adatelemzési virtuális gépek és a Databricks, hatékonnyá használja az elérhető számítási lehetőségek közül.|
|**Újrahasznosíthatóság**|A folyamatok bizonyos forgatókönyvek esetén például átképezési és a kötegelt pontozás templatized is lehet.  Egyszerű REST-hívásokon keresztül a külső rendszerekből aktiválható.|
|**Nyomon követést és verziókezelés**|Helyett manuálisan követési adatok és az eredmény elérési iterálni, használatával az egyes folyamatok SDK explicit módon nevezze el és verzió az adatok adatforrásokat, bemenetként, és kiírja, valamint szkripteket és adatokat külön-külön az fokozott termelékenység kezelésére.|

## <a name="the-python-sdk-for-pipelines"></a>A Python SDK folyamatok

A Python használatával a gépi Tanulási folyamatokat hozhat létre. Az Azure Machine Learning SDK alkalmazás-előkészítés és-folyamatok lépéseit párhuzamosan futtatni, amikor nincs függőség nem található imperatív szerkezeteket kínál. Használhatja azt a Jupyter notebookok vagy egy másik elsődleges IDE-ben. 

Deklaratív adatok-függőségek használatával, optimalizálhatja a feladatokat. Az SDK egy keretrendszer, előre elkészített méretére vonatkozó gyakori feladatokat tartalmaz, például a data transfer, számítási tároló létrehozása és közzététel modell. A keretrendszer egyéni lépéseket, amelyek újrafelhasználható folyamatok közötti implementálásával saját konvenciók modellek is kiterjeszthető.

Folyamatok menthető, sablonok, és a egy REST-végponthoz is telepíthető, így a kötegelt pontozás vagy megőrzési feladatokat ütemezhet.

Tekintse meg a [folyamatok Python SDK referenciadokumentumai](http://aka.ms/aml-sdk) és a következő szakaszban megtudhatja, hogyan hozhat létre saját a notebookot.

## <a name="example-notebooks"></a>Példa notebookok
 
A következő notebook az Azure Machine Learning folyamatok mutatja be: `pipeline/pipeline-batch-scoring.ipynb`.
 
Ezeket a notebookokat lekérése:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

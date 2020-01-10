---
title: Mi az a munkaterület?
titleSuffix: Azure Machine Learning
description: A munkaterület a Azure Machine Learning legfelső szintű erőforrása. Megőrzi az összes képzési folyamat előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezekkel az információkkal meghatározhatja, hogy melyik betanítási Futtatás a legjobb modellt állítja elő
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: 3d8a1b965724c0fcb9f07b382ed446d41fe23dac
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772567"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Mi az Azure Machine Learning munkaterület?

A munkaterület a Azure Machine Learning legfelső szintű erőforrása, amely központosított helyet biztosít a Azure Machine Learning használatakor létrehozott összes összetevővel való együttműködéshez.  A munkaterület megőrzi az összes betanítási Futtatás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezekkel az információkkal meghatározhatja, hogy melyik betanítási Futtatás a legjobb modellt állítja elő.  

Ha egy modellt szeretne, regisztrálja azt a munkaterületen. Ezután a regisztrált modell-és pontozási szkriptekkel üzembe helyezheti az Azure Container Instances, az Azure Kubernetes Service-t vagy egy mezőre programozható Gate array (FPGA)-t REST-alapú HTTP-végpontként. A modellt egy Azure IoT Edge eszközre is üzembe helyezheti modulként.

Az elérhető díjszabás és szolgáltatások attól függnek, hogy az [alapszintű vagy a Enterprise kiadás](overview-what-is-azure-ml.md#sku) ki van-e választva a munkaterületen. A kiadást a [munkaterület létrehozásakor](#create-workspace)válassza ki.  Az alapszintű Enterprise kiadásra is [frissítheti](#upgrade) .

## <a name="taxonomy"></a>Elnevezési rendszert 

A munkaterület taxonómiaét az alábbi ábra szemlélteti:

[![-munkaterület besorolása](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

A diagram a munkaterület következő összetevőit jeleníti meg:

+ A munkaterületek tartalmazhatnak [Azure Machine learning számítási példányokat](concept-compute-instance.md), a Azure Machine learning futtatásához szükséges Python-környezettel konfigurált Felhőbeli erőforrásokat.

+ A [felhasználói szerepkörök](how-to-assign-roles.md) lehetővé teszik a munkaterület más felhasználókkal, csapatokkal vagy projektekkel való megosztását.
+ A kísérletek futtatásához [számítási célokat](concept-azure-machine-learning-architecture.md#compute-targets) kell használni.
+ A munkaterület létrehozásakor a [társított erőforrások](#resources) is létrejönnek Önnek.
+ A [kísérletek](concept-azure-machine-learning-architecture.md#experiments) olyan képzések, amelyeket a modellek létrehozásához használhat.  
+ A [folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) újrafelhasználható munkafolyamatok a modell betanításához és átképzéséhez.
+ Az [adatkészletek](concept-azure-machine-learning-architecture.md#datasets-and-datastores) segítséget nyújtanak a modell betanításához és a folyamat létrehozásához használt adat kezeléséhez.
+ Ha már rendelkezik egy olyan modellel, amelyet telepíteni szeretne, hozzon létre egy regisztrált modellt.
+ Hozzon létre egy [telepítési végpontot](concept-azure-machine-learning-architecture.md#endpoints)a regisztrált modell és egy pontozási parancsfájl használatával.

## <a name="tools-for-workspace-interaction"></a>A munkaterület-interakció eszközei

A munkaterületet a következő módokon használhatja:

+ A weben:
    + [Azure Machine Learning Studio](https://ml.azure.com) 
    + [Azure Machine learning Designer (előzetes verzió)](concept-designer.md) – csak [Enterprise Edition](overview-what-is-azure-ml.md#sku) -munkaterületeken érhető el.
+ Bármely Python-környezetben a [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)-val.
+ Bármilyen R-környezetben az [r-hez készült Azure Machine learning SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html)-val.
+ A parancssorban a Azure Machine Learning [CLI bővítmény](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) használatával

## <a name="machine-learning-with-a-workspace"></a>Gépi tanulás munkaterülettel

A gépi tanulási feladatok beolvassák és/vagy leírják a munkaterületet.

+ Futtasson egy kísérletet a modell betanításához – a kísérlet futtatása az eredményeket a munkaterületre.
+ Az automatizált ML használatával betaníthatja a modelleket – a betanítási eredményeket a munkaterületre írja.
+ Modell regisztrálása a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modell használatával hozza létre a központi telepítést.
+ Újrafelhasználható munkafolyamatok létrehozása és futtatása.
+ Megtekintheti a gépi tanulási összetevőket, például a kísérleteket, a folyamatokat, a modelleket, a központi telepítéseket.
+ Modellek nyomon követése és figyelése.

## <a name="workspace-management"></a>Munkaterület-kezelés

A következő munkaterület-felügyeleti feladatokat is végrehajthatja:

| Munkaterület-felügyeleti feladat   | Portál              | Studio | Python SDK/R SDK       | CLI        |
|---------------------------|---------|---------|------------|------------|
| Munkaterületek létrehozása        | **&check;**     | | **&check;** | **&check;** |
| Munkaterület-hozzáférés kezelése    | **&check;**   || |  **&check;**    |
| Frissítés a Enterprise Edition verzióra    | **&check;** | **&check;**  | |     |
| Számítási erőforrások létrehozása és kezelése    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Jegyzetfüzet-alapú virtuális gép létrehozása |   | **&check;** | |     |

> [!NOTE]
> A számítási példányok csak az **USA északi középső** régióját vagy **Egyesült Királyság déli régiójat**tartalmazó munkaterületekhez érhetők el.
>Ha a munkaterület bármely más régióban található, akkor továbbra is létrehozhat és használhat [notebookos virtuális gépet](concept-compute-instance.md#notebookvm) .

## <a name='create-workspace'></a>Munkaterület létrehozása

Munkaterületek létrehozásakor eldöntheti, hogy [alapszintű vagy Enterprise kiadással](overview-what-is-azure-ml.md#sku)hozza-e létre. A kiadás meghatározza a munkaterületen elérhető szolgáltatásokat. A nagyvállalati kiadás többek között a [Azure Machine learning Designerhez](concept-designer.md) és az [automatizált gépi tanulási kísérletek](tutorial-first-experiment-automated-ml.md)létrehozásához szükséges Studio-verzióhoz biztosít hozzáférést.  További részletek és díjszabási információk: [Azure Machine learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning/).

Több módon is létrehozhat egy munkaterületet:  

* A pont-és kattintási felület [Azure Portal](how-to-manage-workspace.md) az egyes lépések lépésein végezhető el.
* A [Pythonhoz készült Azure Machine learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) használatával létrehozhat egy munkaterületet a Python-parancsfájlok vagy a Jupiter-jegyzetfüzetek segítségével
* Ha automatizálni vagy testre szeretné szabni a vállalati biztonsági szabványokkal való létrehozást, használjon [Azure Resource Manager sablont](how-to-create-workspace-template.md) vagy a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md) -t.
* Ha a Visual Studio Code-ban dolgozik, használja a [vs Code bővítményt](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="upgrade"></a>Frissítés a Enterprise Edition verzióra

[A munkaterületet az alapszintű és a nagyvállalati verzióról is frissítheti](how-to-manage-workspace.md#upgrade) Azure Portal használatával. Az Enterprise Edition-munkaterületeket nem lehet alapszintű kiadási munkaterületre visszaminősíteni. 

## <a name="resources"></a>Társított erőforrások

Amikor új munkaterületet hoz létre, az automatikusan létrehoz több Azure-erőforrást, amelyet a munkaterület használ:

+ [Azure Container Registry](https://azure.microsoft.com/services/container-registry/): regisztrálja a betanítás során használt Docker-tárolókat, és amikor üzembe helyezi a modellt. A költségek csökkentése érdekében az ACR a telepítési lemezképek létrehozása előtt **betöltődik** .
+ [Azure Storage-fiók](https://azure.microsoft.com/services/storage/): a munkaterület alapértelmezett adattára.  A Azure Machine Learning számítási példányokhoz használt Jupyter-jegyzetfüzeteket is itt tárolja.
+ [Azure Application Insights](https://azure.microsoft.com/services/application-insights/): a modellekkel kapcsolatos figyelési információkat tárolja.
+ [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): a számítási célok és a munkaterület által igényelt egyéb bizalmas információk által használt titkokat tárolja.

> [!NOTE]
> Az új verziók létrehozása mellett használhatja a meglévő Azure-szolgáltatásokat is.

## <a name="next-steps"></a>Következő lépések

A Azure Machine Learning megkezdéséhez tekintse meg a következőt:

+ [Azure Machine Learning áttekintése](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása](how-to-manage-workspace.md)
+ [Munkaterületek kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: Ismerkedés az első ML-kísérlettel a Python SDK-val](tutorial-1st-experiment-sdk-setup.md)
+ [Oktatóanyag: az Azure Machine Learning első lépései az R SDK-val](tutorial-1st-r-experiment.md)
+ [Oktatóanyag: az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md) (csak [Enterprise Edition](overview-what-is-azure-ml.md#sku) -munkaterületeken érhető el)
+ [Oktatóanyag: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md) (csak [Enterprise Edition](overview-what-is-azure-ml.md#sku) -munkaterületeken érhető el)

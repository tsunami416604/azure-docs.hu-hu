---
title: Mi az a munkaterület?
titleSuffix: Azure Machine Learning
description: A munkaterület az Azure Machine Learning legfelső szintű erőforrása. Megőrzi az összes betanítási futtatások előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezen információk segítségével határozza meg, hogy melyik betanítási futtatás állítja elő a legjobb modellt
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 12/27/2019
ms.openlocfilehash: 03bc49c24e3c2d32e97f3e5e03bd39da63428a6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505578"
---
# <a name="what-is-an-azure-machine-learning-workspace"></a>Mi az Azure Machine Learning-munkaterület?

A munkaterület az Azure Machine Learning legfelső szintű erőforrása, amely központosított helyet biztosít az Azure Machine Learning használatasorán létrehozott összes összetevővel való munkához.  A munkaterület az összes betanítási futtatás előzményeit, beleértve a naplókat, a metrikákat, a kimenetet és a parancsfájlok pillanatképét. Ezt az információt használja annak meghatározására, hogy melyik betanítási futtatás állítja elő a legjobb modellt.  

Ha van egy önnek tetsző modell, regisztrálja azt a munkaterületen. Ezután a regisztrált modell és a pontozási parancsfájlok használatával üzembe helyezheti az Azure Container Instances, az Azure Kubernetes szolgáltatás, vagy egy mező-programozható kaputömb (FPGA) a REST-alapú HTTP-végpont. A modellt egy Azure IoT Edge-eszközre is telepítheti modulként.

A díjszabás és a rendelkezésre álló funkciók attól függnek, hogy az [Alapszintű vagy a Nagyvállalati kiadás](overview-what-is-azure-ml.md#sku) ki van-e jelölve a munkaterülethez. A munkaterület létrehozásakor válassza ki [a kiadást.](#create-workspace)  Az Alapszintű verzióról az Enterprise kiadásra is [frissíthet.](#upgrade)

## <a name="taxonomy"></a>Taxonómia 

A munkaterület taxonómiát az alábbi ábra szemlélteti:

[![Munkaterületi taxonómia](./media/concept-workspace/azure-machine-learning-taxonomy.png)](./media/concept-workspace/azure-machine-learning-taxonomy.png#lightbox)

Az ábrán a munkaterület következő összetevői láthatók:

+ A munkaterület [azure Machine Learning számítási példányokat](concept-compute-instance.md)tartalmazhat, az Azure Machine Learning futtatásához szükséges Python-környezettel konfigurált felhőerőforrásokat.

+ [A felhasználói szerepkörök](how-to-assign-roles.md) lehetővé teszik a munkaterület megosztását más felhasználókkal, csoportokkal vagy projektekkel.
+ [A számítási célok](concept-azure-machine-learning-architecture.md#compute-targets) a kísérletek futtatásához használatosak.
+ A munkaterület létrehozásakor a [kapcsolódó erőforrások](#resources) is létrejönnek.
+ [A kísérletek](concept-azure-machine-learning-architecture.md#experiments) olyan képzési futtatások, amelyeket a modellek létrehozásához használ.  
+ [A folyamatok](concept-azure-machine-learning-architecture.md#ml-pipelines) újrafelhasználható munkafolyamatok a modell betanításához és átképzéséhez.
+ [Adatkészletek](concept-azure-machine-learning-architecture.md#datasets-and-datastores) támogatás a modellbetanításhoz és a folyamat létrehozásához használt adatok kezeléséhez.
+ Miután egy modellt szeretne telepíteni, hozzon létre egy regisztrált modellt.
+ A regisztrált modell és a pontozási parancsfájl segítségével hozzon létre egy [központi telepítési végpontot.](concept-azure-machine-learning-architecture.md#endpoints)

## <a name="tools-for-workspace-interaction"></a>Eszközök a munkaterülettel való interakcióhoz

A munkaterületet az alábbi módokon használhatja:

+ Az interneten:
    + [Azure Machine Learning stúdió](https://ml.azure.com) 
    + [Azure Machine Learning designer (előzetes verzió)](concept-designer.md) – Csak [az Enterprise edition munkaterületeken](overview-what-is-azure-ml.md#sku) érhető el.
+ Bármely Python-környezetben az [Azure Machine Learning SDK pythonhoz.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
+ Bármely R-környezetben az [Azure Machine Learning SDK r.](https://azure.github.io/azureml-sdk-for-r/reference/index.html)
+ A parancssorban az Azure Machine Learning [CLI bővítmény](https://docs.microsoft.com/azure/machine-learning/reference-azure-machine-learning-cli) használatával

## <a name="machine-learning-with-a-workspace"></a>Gépi tanulás munkaterülettel

A gépi tanulási feladatok összetevőkolvasása és/vagy írása a munkaterületre.

+ Kísérletezzen egy modell betanításához – a kísérlet futtatási eredményeit írja a munkaterületre.
+ Automatikus ML használatával betanítja a modellt – beírja a betanítási eredményeket a munkaterületre.
+ Modell regisztrálása a munkaterületen.
+ Modell üzembe helyezése – a regisztrált modell segítségével hozzon létre egy központi telepítést.
+ Újrafelhasználható munkafolyamatok létrehozása és futtatása.
+ Megtekintheti a gépi tanulási összetevőket, például a kísérleteket, a folyamatokat, a modelleket, a központi telepítéseket.
+ Kövesse nyomon és figyelje a modelleket.

## <a name="workspace-management"></a>Munkaterület-kezelés

A munkaterület-felügyeleti feladatokat a következő tevékenységekkel is elvégezheti:

| Munkaterület-kezelési feladat   | Portál              | Studio | Python SDK / R SDK       | parancssori felület        |
|---------------------------|---------|---------|------------|------------|
| Munkaterület létrehozása        | **&check;**     | | **&check;** | **&check;** |
| Munkaterületi hozzáférés kezelése    | **&check;**   || |  **&check;**    |
| Frissítés az Enterprise edition verzióra    | **&check;** | **&check;**  | |     |
| Számítási erőforrások létrehozása és kezelése    | **&check;**   | **&check;** | **&check;** |  **&check;**   |
| Jegyzetfüzet virtuális gép létrehozása |   | **&check;** | |     |

> [!WARNING]
> Az Azure Machine Learning-munkaterület áthelyezése egy másik előfizetésre, vagy a tulajdonában lévő előfizetés áthelyezése egy új bérlőre, nem támogatott. Ez hibákat okozhat.

## <a name="create-a-workspace"></a><a name='create-workspace'></a>Munkaterület létrehozása

Amikor létrehoz egy munkaterületet, eldöntheti, hogy [az Alapszintű vagy a Nagyvállalati kiadással](overview-what-is-azure-ml.md#sku)hozza-e létre. A kiadás határozza meg a munkaterületen elérhető szolgáltatásokat. Az Enterprise edition többek között hozzáférést biztosít az [Azure Machine Learning tervezőjéhez](concept-designer.md) és az automatizált gépi tanulási kísérletek készítésének stúdióverziójához. [automated machine learning experiments](tutorial-first-experiment-automated-ml.md)  További részletekért és díjszabási információkért lásd: [Azure Machine Learning díjszabása.](https://azure.microsoft.com/pricing/details/machine-learning/)

A munkaterület létrehozásának többféle módja van:  

* Használja az [Azure Portalegy](how-to-manage-workspace.md) point-and-click felület végigvezeti az egyes lépéseket.
* Az [Azure Machine Learning SDK python-hoz](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py#workspace) segítségével hozzon létre egy munkaterületet menet közben Python-parancsfájlokból vagy Jupiter-jegyzetfüzetekből
* Használjon [Egy Azure Resource Manager sablont](how-to-create-workspace-template.md) vagy az Azure Machine Learning [CLI-t,](reference-azure-machine-learning-cli.md) ha a létrehozást vállalati biztonsági szabványokkal kell automatizálnia vagy testre kell szabnia.
* Ha a Visual Studio Kód alkalmazásában dolgozik, használja a [VS Code bővítményt.](tutorial-setup-vscode-extension.md)

> [!NOTE]
> A munkaterület neve nem i.

## <a name="upgrade-to-enterprise-edition"></a><a name="upgrade"></a>Frissítés az Enterprise edition verzióra

Az Azure Portal használatával [frissítheti munkaterületét Basic-ről Enterprise Edition-re.](how-to-manage-workspace.md#upgrade) Az Enterprise edition munkaterület nem minősíthető alapszintű kiadási munkaterületre. 

## <a name="associated-resources"></a><a name="resources"></a>Kapcsolódó erőforrások

Amikor új munkaterületet hoz létre, az automatikusan több Azure-erőforrást hoz létre, amelyeket a munkaterület használ:

+ [Azure Container Registry:](https://azure.microsoft.com/services/container-registry/)Regisztrálja a docker-tárolók, amelyek a betanítás során, és amikor egy modell üzembe helyezésekor. A költségek minimalizálása érdekében az ACR **lusta, amíg** üzembe helyezési lemezképek jönnek létre.
+ [Azure Storage-fiók:](https://azure.microsoft.com/services/storage/)A munkaterület alapértelmezett adattáraként használatos.  Az Azure Machine Learning számítási példányaihoz használt Jupyter-jegyzetfüzetek is itt tárolódnak.
+ [Azure Application Insights:](https://azure.microsoft.com/services/application-insights/)A modellek figyelési adatait tárolja.
+ [Azure Key Vault:](https://azure.microsoft.com/services/key-vault/)Tárolja a számítási célok és más bizalmas információk által használt titkos kulcsokat, amelyeket a munkaterület nek szüksége van.

> [!NOTE]
> Az új verziók létrehozása mellett a meglévő Azure-szolgáltatásokat is használhatja.

## <a name="next-steps"></a>További lépések

Az Azure Machine Learning első lépései:

+ [Az Azure Machine Learning áttekintése](overview-what-is-azure-ml.md)
+ [Munkaterület létrehozása](how-to-manage-workspace.md)
+ [Munkaterületek kezelése](how-to-manage-workspace.md)
+ [Oktatóanyag: Első lépések a Python SDK-val végzett első ML-kísérlet létrehozásához](tutorial-1st-experiment-sdk-setup.md)
+ [Oktatóanyag: Az Azure Machine Learning és az R SDK első lépései](tutorial-1st-r-experiment.md)
+ [Oktatóanyag: Az első besorolási modell létrehozása automatizált gépi tanulással](tutorial-first-experiment-automated-ml.md) (csak [az Enterprise edition](overview-what-is-azure-ml.md#sku) munkaterületeken érhető el)
+ [Oktatóanyag: Az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md) (csak [az Enterprise edition](overview-what-is-azure-ml.md#sku) munkaterületeken érhető el)

---
title: A példában Jupyter notebookok
titleSuffix: Azure Machine Learning service
description: Keresse meg és Fedezze fel az Azure Machine Learning szolgáltatás Python SDK példában Jupyter notebookok használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 05/29/2019
ms.custom: seodec18
ms.openlocfilehash: ea4d5a807c25ea0406b49dac8a83ef1a34e0e8b3
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391782"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>A Jupyter notebookok használata az Azure Machine Learning szolgáltatás megismeréséhez

A [Azure Machine Learning notebookok tárház](https://github.com/azure/machinelearningnotebooks) tartalmazza a legújabb Azure Machine Learning Python SDK-minták. Ezeket a notebookokat Juypter úgy tervezték, Fedezze fel az SDK-t, és saját machine learning-projektek modelleket szolgál.

Ez a cikk bemutatja, hogyan érheti el a tárházat a következő környezetekben a:

- [Virtuális gép az Azure Machine Learning-jegyzetfüzet](#azure-machine-learning-notebook-vm)
- [A saját notebook server használata](#bring-your-own-jupyter-notebook-server)
- [Adatelemző virtuális gép](#data-science-virtual-machine)
- [Azure Notebooks](#azure-notebooks)

> [!NOTE]
> Miután már klónozta a tárházat, az oktatóanyag notebookok találja a **oktatóanyagok** mappát, és a funkcióspecifikus notebookok a **útmutatóval-to-használat – azureml** mappa.

## <a name="azure-machine-learning-notebook-vm"></a>Virtuális gép az Azure Machine Learning-jegyzetfüzet

A minták használatának legegyszerűbb módja, hogy végezze el a [felhőalapú notebook rövid](quickstart-run-cloud-notebook.md). Miután végeztünk ezzel, akkor egy dedikált notebook server előre betöltött az SDK-t és a minta tárház. Nincs letöltések vagy telepítése szükséges.

## <a name="bring-your-own-jupyter-notebook-server"></a>A saját Jupyter Notebook server használata

Ha szeretné saját notebook server használata a helyi fejlesztési, kövesse az alábbi lépéseket:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Ezeket az utasításokat a rövid útmutató és oktatóanyag notebookokhoz szükséges alapszintű SDK-csomagok telepítéséhez. Más mintafüzetek megkövetelheti további összetevők telepítéséhez. További információkért lásd: [telepítse az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="data-science-virtual-machine"></a>Adatelemzési virtuális gép

Az adatelemzési virtuális gép (DSVM) egy személyre szabott Virtuálisgép-rendszerképet, amelyet kifejezetten adatelemzésre. Ha Ön [hozzon létre egy DSVM](how-to-configure-environment.md#dsvm), az SDK-t és a notebook server telepítése és konfigurálása történik meg. Azonban továbbra is kell hozzon létre egy munkaterületet, és klónozza a mintaadattárat.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="azure-notebooks"></a>Azure Notebooks

A [Azure notebookok](https://notebooks.azure.com/), az SDK-t és a notebook server telepítése és konfigurálása történik meg. Az Azure-jegyzetfüzetek segítségével megismerheti a teljes körűen felügyelt, kis igényű notebook környezetet biztosít.

A minta tárház Azure notebookokban eléréséhez lépjen a keresztül az Azure Machine Learning-munkaterületet a [az Azure portal](https://portal.azure.com). Az a **áttekintése** szakaszban jelölje be **első lépései az Azure-jegyzetfüzetekben**.

## <a name="next-steps"></a>További lépések

Fedezze fel a [notebookok minta](https://aka.ms/aml-notebooks) felderítheti, mely az Azure Machine Learning szolgáltatás vagy végezheti el, próbálja meg ezekben az oktatóanyagokban:

- [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

- [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-data-prep.md)
---
title: A példában Jupyter notebookok
titleSuffix: Azure Machine Learning service
description: Megkeresheti és használhatja például a Jupyter notebookokat a Azure Machine Learning Service Python SDK megismeréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 07/31/2019
ms.custom: seodec18
ms.openlocfilehash: 14962b936d1c09a6c50daa7bec460ce11dbefe5d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860392"
---
# <a name="explore-azure-machine-learning-service-with-jupyter-notebooks"></a>Azure Machine Learning szolgáltatás megismerése Jupyter notebookokkal

A [Azure Machine learning jegyzetfüzetek tárháza](https://github.com/azure/machinelearningnotebooks) tartalmazza a legújabb Azure Machine learning Python SDK-mintákat. Ezek a Juypter-jegyzetfüzetek úgy vannak kialakítva, hogy segítsenek az SDK megismerésében, és modellként szolgálni a saját gépi tanulási projektekhez.

Ez a cikk bemutatja, hogyan érheti el a tárházat az alábbi környezetekben:

- [Azure Machine Learning notebook VM](#notebookvm)
- [Saját notebook-kiszolgáló használata](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Miután klónozotta a tárházat, az **oktatóanyagok** mappában és a szolgáltatás-specifikus jegyzetfüzetekben találja a **használati útmutatóban** szereplő jegyzetfüzeteket a azureml mappában.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-notebook-vm"></a>Minták beolvasása Azure Machine Learning notebook virtuális gépen

A minták megkezdésének legegyszerűbb módja az [oktatóanyag befejezése: Környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)beállítása. Ha elkészült, az SDK-val és a minta adattárral előre be kell töltenie egy dedikált jegyzetfüzet-kiszolgálót. Nincs szükség letöltésre vagy telepítésre.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Minták beolvasása a notebook-kiszolgálón

Ha saját notebook-kiszolgálót szeretne a helyi fejlesztéshez, kövesse az alábbi lépéseket:

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Ezek az utasítások a gyors üzembe helyezési és oktatóanyag-jegyzetfüzetekhez szükséges alap SDK-csomagokat telepítik. Más minta-jegyzetfüzetek esetében előfordulhat, hogy további összetevőket kell telepítenie. További információ: [install the Azure Machine learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Minták beolvasása a DSVM

A Data Science Virtual Machine (DSVM) egy testreszabott virtuálisgép-rendszerkép, amely kifejezetten az adatelemzéshez készült. Ha [létrehoz egy DSVM](how-to-configure-environment.md#dsvm), az SDK és a notebook-kiszolgáló telepítve van és konfigurálva van. Azonban továbbra is létre kell hoznia egy munkaterületet, és el kell végeznie a minta tárház klónozását.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a [minta jegyzetfüzetekkel](https://aka.ms/aml-notebooks) , hogy felderítse, mi Azure Machine learning szolgáltatás, vagy próbálja ki az alábbi oktatóanyagokat:

- [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

- [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-auto-train-models.md)

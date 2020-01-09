---
title: Példa Jupyter-jegyzetfüzetekre
titleSuffix: Azure Machine Learning
description: Megkeresheti és használhatja például a Jupyter jegyzetfüzeteket a Azure Machine Learning Python for SDK megismeréséhez.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: 1f155d153ef7af8a83fb86a37cd1b9fa826ad2ff
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75533432"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>A Azure Machine Learning megismerése Jupyter notebookokkal

A [Azure Machine learning jegyzetfüzetek tárháza](https://github.com/azure/machinelearningnotebooks) tartalmazza a legújabb Azure Machine learning Python SDK-mintákat. Ezek a Juypter-jegyzetfüzetek úgy vannak kialakítva, hogy segítsenek az SDK megismerésében, és modellként szolgálni a saját gépi tanulási projektekhez.

Ez a cikk bemutatja, hogyan érheti el a tárházat az alábbi környezetekben:

- [Azure Machine Learning számítási példány](#notebookvm)
- [Saját notebook-kiszolgáló használata](#byo)
- [Data Science Virtual Machine](#dsvm)

> [!NOTE]
> Miután klónozotta a tárházat, az **oktatóanyagok** mappában és a szolgáltatás-specifikus jegyzetfüzetekben találja a **használati útmutatóban** szereplő jegyzetfüzeteket a azureml mappában.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Minták beolvasása Azure Machine Learning számítási példányon

A minták megkezdésének legegyszerűbb módja az [oktatóanyag: telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)beszerzése. Ha elkészült, az SDK-val és a minta adattárral előre be kell töltenie egy dedikált jegyzetfüzet-kiszolgálót. Nincs szükség letöltésre vagy telepítésre.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Minták beolvasása a notebook-kiszolgálón

Ha saját notebook-kiszolgálót szeretne a helyi fejlesztéshez, kövesse az alábbi lépéseket:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Ezek az utasítások a gyors üzembe helyezési és oktatóanyag-jegyzetfüzetekhez szükséges alap SDK-csomagokat telepítik. Más minta-jegyzetfüzetek esetében előfordulhat, hogy további összetevőket kell telepítenie. További információ: [install the Azure Machine learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/install).

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Minták beolvasása a DSVM

A Data Science Virtual Machine (DSVM) egy testreszabott virtuálisgép-rendszerkép, amely kifejezetten az adatelemzéshez készült. Ha [létrehoz egy DSVM](how-to-configure-environment.md#dsvm), az SDK és a notebook-kiszolgáló telepítve van és konfigurálva van. Azonban továbbra is létre kell hoznia egy munkaterületet, és el kell végeznie a minta tárház klónozását.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>Következő lépések

Ismerkedjen meg a [minta jegyzetfüzetekkel](https://aka.ms/aml-notebooks) , hogy megismerje, milyen Azure Machine learning teheti, vagy próbálja ki a következő oktatóanyagokat:

- [Rendszerkép-besorolási modell betanítása és üzembe helyezése a MNIST](tutorial-train-models-with-aml.md)

- [Készítse elő az adatfeldolgozást, és használja az automatizált gépi tanulást a regressziós modell betanításához a New York-i taxiban](tutorial-auto-train-models.md)

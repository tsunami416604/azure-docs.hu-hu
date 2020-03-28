---
title: Példa Jupyter notebookokra
titleSuffix: Azure Machine Learning
description: Keresse meg és használja a példa Jupyter notebookok az Azure Machine Learning Python SDK-hoz.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 7242b82ee5c43878a33731bd1f02b685020f22b0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78673625"
---
# <a name="explore-azure-machine-learning-with-jupyter-notebooks"></a>Fedezze fel az Azure Machine Learninget jupyter-jegyzetfüzetekkel

A [példa Az Azure Machine Learning Notebooks tárház](https://github.com/azure/machinelearningnotebooks) tartalmazza a legújabb Azure Machine Learning Python SDK-mintákat. Ezeket a Juypter-noteszgépeket úgy tervezték, hogy segítsenek az SDK felfedezésében, és modellként szolgáljanak saját gépi tanulási projektjeihez.

Ez a cikk bemutatja, hogyan érheti el a tárházat a következő környezetekben:

- [Azure Machine Learning számítási példány](#notebookvm)
- [Saját notebook-kiszolgáló elhozása](#byo)
- [Adatelemzési virtuális gép](#dsvm)

> [!NOTE]
> Miután klónozta a tárházat, az **oktatóanyagok** mappájában oktatóanyagok mappájában és szolgáltatásspecifikus jegyzetfüzeteket talál a **hogyan használható azureml** mappában.

<a name="notebookvm"></a>
## <a name="get-samples-on-azure-machine-learning-compute-instance"></a>Minták beszereznie az Azure Machine Learning számítási példányát

A minták kezdeti lépései legegyszerűbben az [Oktatóanyag: Telepítési környezet és munkaterület](tutorial-1st-experiment-sdk-setup.md)befejezése. Miután elkészült, lesz egy dedikált notebook-kiszolgáló előre betöltött az SDK és a minta tárház. Nincs szükség letöltésre vagy telepítésre.

<a name="byo"></a>

## <a name="get-samples-on-your-notebook-server"></a>Minták beszereznie a jegyzetfüzet-kiszolgálón

Ha saját jegyzetfüzet-kiszolgálót szeretne hozni helyi fejlesztésre, kövesse az alábbi lépéseket:

[!INCLUDE [aml-your-server](../../includes/aml-your-server.md)]

Ezek az utasítások telepítik a rövid útmutatóhoz és az oktató jegyzetfüzetekhez szükséges alap SDK-csomagokat. Más mintajegyzetfüzetek további összetevők telepítését is szükségessé tehetik. További információ: [Az Azure Machine Learning SDK telepítése pythonhoz](https://docs.microsoft.com/python/api/overview/azure/ml/install)című témakörben talál.

<a name="dsvm"></a>
## <a name="get-samples-on-dsvm"></a>Minták beszereznie a DSVM-en

Az adatelemzési virtuális gép (DSVM) egy testreszabott virtuálisgép-rendszer, amely et kifejezetten adatelemzési célokra készült. Ha [DSVM-et hoz létre,](how-to-configure-environment.md#dsvm)az SDK és a notebook-kiszolgáló telepítve és konfigurálva van. Azonban továbbra is létre kell hoznia egy munkaterületet, és klónoznia kell a mintatárházat.

[!INCLUDE [aml-dsvm-server](../../includes/aml-dsvm-server.md)]

## <a name="next-steps"></a>További lépések

Fedezze fel a [mintajegyzetfüzeteket,](https://aka.ms/aml-notebooks) és fedezze fel, mire képes az Azure Machine Learning, vagy próbálja ki az alábbi oktatóanyagokat:

- [Lemezkép-besorolási modell betanítása és üzembe helyezése az MNIST segítségével](tutorial-train-models-with-aml.md)

- [Adatok előkészítése és automatikus gépi tanulás használata regressziós modell betanításához a NYC taxi adatkészlettel](tutorial-auto-train-models.md)

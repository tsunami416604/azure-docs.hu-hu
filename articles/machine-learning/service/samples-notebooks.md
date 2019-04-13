---
title: A példában Jupyter notebookok
titleSuffix: Azure Machine Learning service
description: Keresse meg és Fedezze fel az Azure Machine Learning szolgáltatás a Pythonban példában Jupyter notebookok használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: b597d6efa87aa2811ce42f3315698acfa17426b2
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548588"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>A Jupyter notebookok használata az Azure Machine Learning szolgáltatás megismeréséhez

Az Ön kényelme érdekében kialakította Jupyter Python notebookok segítségével ismerje meg az Azure Machine Learning szolgáltatás egy sorozatát. 

Ismerje meg, hogyan lehet a szolgáltatás az a dokumentáció ezen a helyen, majd ezeket a notebookokat segítségével testre szabhatja őket az adott helyzethez. 

Az alábbi elérési utak egyik használhatja ezeket a notebookokat minta egy notebook server futtatásához.  Ha a kiszolgáló már fut, keresse meg az oktatóanyag-notebookjait **oktatóanyagok** mappát, vagy böngészhet a különböző szolgáltatásai **útmutatóval-to-használat – azureml** mappát.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Próbálja ki az Azure notebookok: Ingyenes Jupyter notebookok a felhőben

Nem kell mást Azure notebookok használatának megkezdéséhez. A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) már telepítve és konfigurálva van az Ön számára [Azure notebookok](https://notebooks.azure.com/). A telepítés és a jövőbeli frissítések automatikusan felügyelt Azure-szolgáltatások használatával.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>A Data Science virtuális gép (DSVM) használata

A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) és notebook server már telepítve és konfigurálva, a dsvm-hez. 

Miután [hozzon létre egy DSVM](how-to-configure-environment.md#dsvm), ezeket a lépéseket használhatja a dsvm-hez a jegyzetfüzet futtatásához.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>A saját Jupyter notebook server használata

Ezek a lépések használatával hozzon létre egy helyi Jupyter Notebook kiszolgálót a számítógépen.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

A rövid útmutató a rövid útmutató és oktatóanyag jegyzetfüzetek futtatásához szükséges csomagokat telepíti.  Más mintafüzetek további összetevők lehet szükség.  Ezek az összetevők kapcsolatos további információkért lásd: [telepítse az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="next-steps"></a>További lépések

Fedezze fel a [GitHub-jegyzetfüzetek tárházat az Azure Machine Learning szolgáltatás](https://aka.ms/aml-notebooks)

Próbálja meg ezekben az oktatóanyagokban:
+ [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

+ [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-data-prep.md)

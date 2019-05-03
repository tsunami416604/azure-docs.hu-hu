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
ms.openlocfilehash: cd88fd85ce6d18287c700a54e42b6237a42ea5c9
ms.sourcegitcommit: eea74d11a6d6ea6d187e90e368e70e46b76cd2aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2019
ms.locfileid: "65035377"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>A Jupyter notebookok használata az Azure Machine Learning szolgáltatás megismeréséhez

Az Ön kényelme érdekében kialakította Jupyter Python notebookok segítségével ismerje meg az Azure Machine Learning szolgáltatás egy sorozatát. 

Ismerje meg, hogyan lehet a szolgáltatás az a dokumentáció ezen a helyen, majd ezeket a notebookokat segítségével testre szabhatja őket az adott helyzethez. 

Az alábbi elérési utak egyik használhatja ezeket a notebookokat minta egy notebook server futtatásához.  Ha a kiszolgáló már fut, keresse meg az oktatóanyag-notebookjait **oktatóanyagok** mappát, vagy böngészhet a különböző szolgáltatásai **útmutatóval-to-használat – azureml** mappát.

## <a name="a-managed-cloud-notebook-server"></a>Egy felügyelt felhőalapú notebook server

Is könnyen a saját felhőalapú notebook server használatának első lépései. A minta-jegyzetfüzet és a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) már telepített és konfigurált Önnek a felhőalapú erőforrás létrehozása után.  

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

* A minták a notebook weblapon érhetők el.

## <a name="a-data-science-virtual-machine-dsvm"></a>A Data Science virtuális gép (DSVM)

A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) és notebook server már telepítve és konfigurálva, a dsvm-hez. 

Miután [hozzon létre egy DSVM](how-to-configure-environment.md#dsvm), ezeket a lépéseket használhatja a dsvm-hez a jegyzetfüzet futtatásához.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]

## <a name="your-own-jupyter-notebook-server"></a>A saját Jupyter Notebook server

Ezek a lépések használatával hozzon létre egy helyi Jupyter Notebook kiszolgálót a számítógépen.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

A telepítési utasításokat a rövid útmutató és oktatóanyag jegyzetfüzetek futtatásához szükséges csomagokat telepíti.  Más mintafüzetek további összetevők lehet szükség.  Ezek az összetevők kapcsolatos további információkért lásd: [telepítse az Azure Machine Learning SDK Pythonhoz készült](https://docs.microsoft.com/python/api/overview/azure/ml/install).

## <a name="azure-notebooks"></a>Azure Notebooks

A minta-jegyzetfüzetek és a [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) már telepítve és konfigurálva, [Azure notebookok](https://notebooks.azure.com/). A telepítés és a jövőbeli frissítések automatikusan felügyelt Azure-szolgáltatások használatával.

Használja a [az Azure portal](https://portal.azure.com) Azure notebookok használatának megkezdéséhez.  Nyissa meg a munkaterületet és a **áttekintése** szakaszban jelölje be **első lépései az Azure-jegyzetfüzetekben**.

## <a name="next-steps"></a>További lépések

+ Ismerkedés az Azure Machine Learning szolgáltatás a GitHub-adattárban mintafüzetek: https://aka.ms/aml-notebooks

Próbálja meg ezekben az oktatóanyagokban:
+ [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

+ [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-data-prep.md)
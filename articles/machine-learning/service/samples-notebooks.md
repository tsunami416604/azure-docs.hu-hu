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
ms.openlocfilehash: 12da1b20c5e4e6299445b8ec8ec90eeec6711e2c
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805518"
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

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Automatizált machine learning-telepítő 

_Csak a notebookok a alkalmazni ezeket a lépéseket a **how-to-use-azureml/automated-machine-learning** mappát._

A fenti lehetőségek bármelyikét használhatja, amíg is a környezet telepítése és az alábbi utasítások egyszerre hozzon létre egy munkaterületet. 

1. Telepítés [Mini-conda](https://conda.io/miniconda.html). Válassza ki a 3.7-es verzióját vagy újabb verziója. Kövesse az utasításokat követve telepítse. 
   >[!NOTE]
   >Egy meglévő conda is használhat, például hosszú 4.4.10 verzió vagy újabb. Használat `conda -V` verzió megjelenítése. A paranccsal frissítheti a conda-verzió: `conda update conda`. Hiba esetén nem kell kifejezetten a mini-conda telepítéséhez.

1. Töltse le a mintafüzetek a [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) zip és csomagolja ki a tartalmát egy helyi könyvtárba. Az alkalmazások automatikus machine learning-jegyzetfüzetek szerepelnek a `how-to-use-azureml/automated-machine-learning` mappát.

1. Állítsa be egy új Conda-környezetet. 
   1. Nyisson meg egy Conda-parancssort a helyi gépen.
   
   1. Keresse meg a fájlokat, amelybe kibontotta a helyi gépen.
   
   1. Nyissa meg a **automatikus gépi tanuláson** mappát.
   
   1. Hajtsa végre `automl_setup.cmd` a Windows, a conda-parancssorban vagy a `.sh` fájlt az operációs rendszer. Végrehajtása körülbelül 10 percet is igénybe vehet.

      A beállítási szkriptet:
      + Létrehoz egy új conda-környezetet
      + Telepíti a szükséges csomagokat
      + Konfigurálja a widget
      + Elindul a jupyter notebook
      
   >[!NOTE]
   > A parancsfájlnak a conda-környezet nevét nem kötelező paraméterként. Az alapértelmezett conda-környezet neve `azure_automl`. A pontos parancsát az operációs rendszertől függ. Ez akkor hasznos, ha egy új környezet létrehozása vagy egy új verziójára való frissítését. Például az "automl_setup.cmd azure_automl_sandbox" segítségével hozzon létre egy evironment neve azure_automl_sandbox. 
      
1. A szkript befejezése után látni fogja a Jupyter notebook kezdőlapja a böngészőben.

1. Keresse meg az elérési utat, ahová mentette a notebookok. 

1. Nyissa meg az automatikus gépi tanuláson mappát, majd nyissa meg a **configuration.ipynb** notebookot. 

1. A cellák hajtsa végre a jegyzetfüzetet Machine Learning Services erőforrás-szolgáltató regisztrálása és a egy munkaterület létrehozása.

Most már készen áll megnyitását és futtatását a notebookok menti a helyi gépen.


## <a name="next-steps"></a>További lépések

Fedezze fel a [GitHub-jegyzetfüzetek tárházat az Azure Machine Learning szolgáltatás](https://aka.ms/aml-notebooks)

Próbálja meg ezekben az oktatóanyagokban:
+ [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

+ [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-data-prep.md)

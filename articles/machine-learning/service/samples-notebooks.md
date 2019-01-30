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
ms.openlocfilehash: 096b72e8abf43d803a5e6c4aa016f904e795366c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250943"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>A Jupyter notebookok használata az Azure Machine Learning szolgáltatás megismeréséhez


Az Ön kényelme érdekében kialakította Jupyter Python notebookok segítségével ismerje meg az Azure Machine Learning szolgáltatás egy sorozatát. 

Ismerje meg, hogyan lehet a szolgáltatás az a dokumentáció ezen a helyen, majd ezeket a notebookokat segítségével testre szabhatja őket az adott helyzethez. 

## <a name="prerequisite"></a>Előfeltétel

Végezze el a [Azure Machine Learning Python rövid](quickstart-get-started.md) hozzon létre egy munkaterületet, majd indítsa el az Azure-jegyzetfüzeteket.

## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Próbálja ki az Azure notebookok: Ingyenes Jupyter notebookok a felhőben

Nem kell mást Azure notebookok használatának megkezdéséhez. A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) már telepítve és konfigurálva van az Ön számára [Azure notebookok](https://notebooks.azure.com/). A telepítés és a jövőbeli frissítések automatikusan felügyelt Azure-szolgáltatások használatával.
  
+ Futtatásához a **oktatóanyag notebookok alapvető**:
  1. Lépjen a [Azure notebookok](https://notebooks.azure.com/).
    
  1. Keresse meg a **oktatóanyagok** mappájában a **bevezetés** az előfeltételként szükséges rövid útmutató során létrehozott könyvtár.
    
  1. Nyissa meg a notebook szeretné futtatni.
    
+ Futtatásához **más notebookok**:

  1. [Importálja a mintafüzetek](https://aka.ms/aml-clone-azure-notebooks) be Azure-jegyzetfüzeteket.

  1. Munkaterület konfigurációs fájl felvétele a könyvtárba, az alábbi módszerek egyikével:
     + Másolás a **config.json** fájlt a **bevezetés** kódtár az új klónozott könyvtár.

     + Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
    
  1. Nyissa meg a notebook szeretné futtatni.     


## <a name="use-a-data-science-virtual-machine-dsvm"></a>A Data Science virtuális gép (DSVM) használata

A [Azure Machine Learning SDK Pythonhoz készült](https://aka.ms/aml-sdk) és notebook server már telepítve és konfigurálva, a dsvm-hez. Kövesse az alábbi lépéseket a jegyzetfüzetek futtatása.

1. [Hozzon létre egy DSVM](how-to-configure-environment.md#dsvm).

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

1. Munkaterület konfigurációs fájl felvétele a könyvtárba, az alábbi módszerek egyikével:
    * Másolás a **aml_config\config.json** a klónozott könyvtárba az előfeltételként szükséges rövid útmutató segítségével létrehozott fájlt.

    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

## <a name="use-your-own-jupyter-notebook-server"></a>A saját Jupyter notebook server használata

Ezek a lépések használatával hozzon létre egy helyi Jupyter Notebook kiszolgálót a számítógépen.

1. Győződjön meg arról, sikeresen befejezte az előfeltételként szükséges gyorsútmutatót, melyen telepítette az Azure Machine Learning SDK-k.

1. Klónozza [a GitHub-adattárat](https://aka.ms/aml-notebooks).

1. Munkaterület konfigurációs fájl felvétele a könyvtárba, az alábbi módszerek egyikével:
    * Másolás a **aml_config\config.json** a klónozott könyvtárba az előfeltételként szükséges rövid útmutató segítségével létrehozott fájlt.
    
    * Hozzon létre egy új munkaterületet, a kód használatával a [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Indítsa el a notebook-kiszolgálót a klónozott könyvtárból.

1. Nyissa meg a notebook tartalmazó mappa.

1. Nyissa meg a notebookot.

<a name="auto"></a>

## <a name="automated-ml-setup"></a>Automatizált ML-telepítő 

**Csak a notebookok a alkalmazni ezeket a lépéseket a `automated-machine-learning` mappát.**

A fenti lehetőségek bármelyikét használhatja, amíg is a környezet telepítése és az alábbi utasítások egyszerre hozzon létre egy munkaterületet. 

1. Telepítés [Mini-conda](https://conda.io/miniconda.html). Válassza ki a 3.7-es verzióját vagy újabb verziója. Kövesse az utasításokat követve telepítse. 
   >[!NOTE]
   >Egy meglévő conda is használhat, például hosszú 4.4.10 verzió vagy újabb. Használat `conda -V` verzió megjelenítése. A paranccsal frissítheti a conda-verzió: `conda update conda`. Hiba esetén nem kell kifejezetten a mini-conda telepítéséhez.

1. Töltse le a mintafüzetek a [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) zip és csomagolja ki a tartalmát egy helyi könyvtárba. Az alkalmazások automatikus machine learning-jegyzetfüzetek szerepelnek a `how-to-use-azureml/automated-machine-learning` mappát.

1. Állítsa be egy új Conda-környezetet. 
   1. Nyisson meg egy Conda-parancssort a helyi gépen.
   
   1. Keresse meg a fájlokat, amelybe kibontotta a helyi gépen.
   
   1. Nyissa meg a `automated-machine-learning` mappát.
   
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

1. Nyissa meg az automatikus gépi tanuláson mappát, majd nyissa meg a `configuration.ipynb` notebookot. 

1. A cellák hajtsa végre a jegyzetfüzetet Machine Learning Services erőforrás-szolgáltató regisztrálása és a egy munkaterület létrehozása.

Most már készen áll megnyitását és futtatását a notebookok menti a helyi gépen.


## <a name="next-steps"></a>További lépések

Fedezze fel a [GitHub-jegyzetfüzetek tárházat az Azure Machine Learning szolgáltatás](https://aka.ms/aml-notebooks)

Próbálja meg ezekben az oktatóanyagokban:
+ [Betanítása és a egy kép osztályozási modell a MNIST üzembe helyezése](tutorial-train-models-with-aml.md)

+ [Adatok előkészítése, és automatizált gépi tanulás a NYC taxi adatkészlet regressziós modell betanítása](tutorial-data-prep.md)

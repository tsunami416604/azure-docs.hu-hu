---
title: Python
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan alakíthatja át az adatátalakítást a Python használatával Azure Machine Learning Designerben.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.custom: designer
ms.openlocfilehash: e27844f9f534ea4db1aba53c12fb3947e7269846
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644471"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Python-kód végrehajtása a Azure Machine Learning Designerben

Ebből a cikkből megtudhatja, hogyan használhatja a [Python parancsfájl](algorithm-module-reference/execute-python-script.md) -modult az egyéni logika Azure Machine learning Designerhez való hozzáadásához. A következő útmutatóban a Panda Library használatával egyszerű funkció-mérnöki műveleteket hajthat végre.

A beépített Kódszerkesztő használatával gyorsan hozzáadhat egyszerű Python-logikát. Ha összetettebb programkódot szeretne hozzáadni, vagy további Python-kódtárakat tölt fel, használja a zip-fájl metódusát.

Az alapértelmezett végrehajtási környezet a Python anacondas eloszlását használja. Az előre telepített csomagok teljes listájáért tekintse meg a [Python-szkriptek moduljának végrehajtása](algorithm-module-reference/execute-python-script.md) című lapot.

![Python bemeneti térképének végrehajtása](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>A Designerben írt python végrehajtása

### <a name="add-the-execute-python-script-module"></a>A Python-parancsfájl végrehajtása modul hozzáadása

1. Keresse meg a **Python-szkript végrehajtása** modult a tervező palettán. A **Python nyelve** szakaszban található.

1. Húzza a modult a folyamat vászonra.

### <a name="connect-input-datasets"></a>Bemeneti adatkészletek csatlakoztatása

Ez a cikk a minta adatkészletet, az **Automobile Price-adatokat (RAW)** használja. 

1. Húzza az adatkészletet a folyamat vászonra.

1. Az adatkészlet kimeneti portjának csatlakoztatása a **Python parancsfájl** -végrehajtó moduljának bal felső bemeneti portjához. A tervező elérhetővé teszi a bemenetet paraméterként a belépési pont parancsfájljában.
    
    A megfelelő bemeneti port a tömörített Python-kódtárak számára van fenntartva.

    ![Adatkészletek csatlakoztatása](media/how-to-designer-python/connect-dataset.png)
        

1. Jegyezze fel, hogy melyik bemeneti portot használja. A tervező a bal oldali bemeneti portot a változóhoz `dataset1` és a középső bemeneti porthoz rendeli `dataset2` . 

A bemeneti modulok nem kötelezőek, mivel közvetlenül a **Python-szkript végrehajtása** modulban létrehozhat vagy importálhat adatokat.

### <a name="write-your-python-code"></a>Python-kód írása

A Designer egy kezdeti belépési pont parancsfájlt biztosít a saját Python-kódjának szerkesztéséhez és megadásához. 

Ebben a példában a pandák használatával két oszlopot egyesítünk a személygépkocsi-adatkészletben, az **Ár** -és a **lóerőben**, egy új oszlop létrehozásához ( **dollár/lóerő**). Ez az oszlop azt jelzi, hogy mennyit fizet minden lóerőért, ami hasznos funkció lehet annak eldöntéséhez, hogy az autó jó üzlet-e a pénzért. 

1. Válassza a **Python-szkript végrehajtása** modult.

1. A vászon jobb oldalán megjelenő ablaktáblán válassza a **Python-szkript** szövegmezőt.

1. Másolja és illessze be a következő kódot a szövegmezőbe.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    A folyamatnak az alábbi képet kell kinéznie:
    
    ![Python-folyamat végrehajtása](media/how-to-designer-python/execute-python-pipeline.png)

    A belépési pont parancsfájljának tartalmaznia kell a függvényt `azureml_main` . A **Python parancsfájl** -modul végrehajtása két bemeneti portra van leképezve.

    A visszatérési értéknek pandák Dataframe kell lennie. Modul kimenetként legfeljebb két dataframes adhat vissza.
    
1. A folyamat elküldése.

Most már rendelkezik egy adatkészlettel, amely a **dollár/HP**új funkcióval rendelkezik, ami hasznos lehet az autó ajánlójának betanításához. Ez egy példa a szolgáltatások kinyerésére és a dimenzióját csökkentésére. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [importálhatja a saját adatait](how-to-designer-import-data.md) Azure Machine learning Designerben.
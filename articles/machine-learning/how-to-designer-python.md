---
title: Python
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Pythont az Azure Machine Learning tervezőjében az adatok átalakításához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.date: 02/28/2020
ms.openlocfilehash: a2bd9845cd29c7d139e2042f39b4697847639207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455791"
---
# <a name="execute-python-code-in-azure-machine-learning-designer"></a>Python-kód végrehajtása az Azure Machine Learning tervezőjében

Ebben a cikkben megtudhatja, hogyan használhatja a [Python-parancsfájl végrehajtása](algorithm-module-reference/execute-python-script.md) modul egyéni logika hozzáadása az Azure Machine Learning designer. A következő útmutató, használja a Pandas könyvtár nem egyszerű funkció mérnöki.

A beépített kódszerkesztővel gyorsan hozzáadhat egyszerű Python-logikát. Ha összetettebb kódot szeretne hozzáadni, vagy további Python-tárakat szeretne feltölteni, használja a zip-fájl módszert.

Az alapértelmezett végrehajtási környezet a Python Anacondas-disztribúcióját használja. Az előre telepített csomagok teljes listáját a [Python Script modul végrehajtásának referencialapján találja.](algorithm-module-reference/execute-python-script.md)

![Python-bemeneti térkép végrehajtása](media/how-to-designer-python/execute-python-map.png)

## <a name="execute-python-written-in-the-designer"></a>Python végrehajtása a tervezőben

### <a name="add-the-execute-python-script-module"></a>A Python-parancsfájl végrehajtása modul hozzáadása

1. Keresse meg a **Python-parancsfájl végrehajtása** modula a tervezőpaletta. Megtalálható a Python **nyelv** szakaszban.

1. Húzza a modult a csővezeték vászonra.

### <a name="connect-input-datasets"></a>Bemeneti adatkészletek csatlakoztatása

Ez a cikk a minta adatkészletet, **az Automobile price data (Raw)** modellt használja. 

1. Húzza az adatkészletet a folyamatvászonra.

1. Csatlakoztassa az adatkészlet kimeneti portját a **Python-parancsfájl végrehajtása** modul bal felső bemeneti portjához. A tervező a bemeneti bemenetet paraméterként teszi elérhetővé a belépési pont parancsfájljának.
    
    A megfelelő bemeneti port a tömörített python-könyvtárak számára van fenntartva.

    ![Adatkészletek összekapcsolása](media/how-to-designer-python/connect-dataset.png)
        

1. Jegyezze fel, hogy melyik bemeneti portot használja. A tervező hozzárendeli a bal `dataset1` oldali bemeneti `dataset2`portot a változóhoz, a középső bemeneti portot pedig a hoz. 

A bemeneti modulok megadása nem kötelező, mivel közvetlenül a **Python-parancsfájl végrehajtása** modulban hozhat létre vagy importálhat adatokat.

### <a name="write-your-python-code"></a>A Python-kód írása

A tervező egy kezdeti belépési pont parancsfájlt biztosít a saját Python-kód szerkesztéséhez és megadásához. 

Ebben a példában a Pandák segítségével kombinálhatja az autó adatkészletében található két oszlopot, az **Ár** és **a Lóerő**, hogy új oszlopot hozzon létre, **dolláronként lóerőnként**. Ez az oszlop azt mutatja, hogy mennyit fizet az egyes lóerő, ami hasznos funkció annak eldöntésére, ha egy autó egy jó üzlet a pénzt. 

1. Válassza ki a **Python-parancsfájl végrehajtása** modult.

1. A vászon jobb oldalán megjelenő ablaktáblán jelölje ki a **Python-parancsfájl** szövegmezőjét.

1. Másolja a következő kódot a szövegmezőbe.

    ```python
    import pandas as pd
    
    def azureml_main(dataframe1 = None, dataframe2 = None):
        dataframe1['Dollar/HP'] = dataframe1.price / dataframe1.horsepower
        return dataframe1
    ```
    A folyamatnak a következő képen kell kinéznie:
    
    ![Python-folyamat végrehajtása](media/how-to-designer-python/execute-python-pipeline.png)

    A belépési pont parancsfájlnak tartalmaznia kell a függvényt. `azureml_main` Két függvényparaméter van, amelyek leképezik a **Python-parancsfájl végrehajtása** modul két bemeneti portját.

    A visszatérési értéknek Pandas dataframe-nek kell lennie. Modulkimenetként legfeljebb két adatkeretet adhat vissza.
    
1. Küldje el a folyamatot.

Most már rendelkezik egy adatkészletaz új funkció **dollár / HP**, ami hasznos lehet a képzés egy autó ajánló. Ez egy példa a funkciók kinyerésére és a méretcsökkentésre. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [importálhatja saját adatait az](how-to-designer-import-data.md) Azure Machine Learning tervezőjében.
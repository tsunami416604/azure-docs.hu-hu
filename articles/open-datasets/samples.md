---
title: Példa Jupyter-jegyzetfüzetekre a NOAA-adat használatával
titleSuffix: Azure Open Datasets
description: Használjon például Jupyter jegyzetfüzeteket az Azure Open-adatkészletekhez, hogy megtudja, hogyan tölthetők be a megnyitott adatkészletek, és hogyan használhatók a bemutatóhoz. A technikák a Spark és a pandák használatát is feldolgozzák az adatfeldolgozáshoz.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: c24363caac1db8dd8ce21b690ef989b2beb97f2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88506056"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Példa a Jupyter-jegyzetfüzetek megmutatják, hogyan bővítheti az adatokat a megnyitott adatkészletekkel 
Az Azure Open-adatkészletek számára készült Jupyter-jegyzetfüzetek bemutatják, hogyan tölthetők be a megnyitott adatkészletek, és hogyan használhatók a bemutató adatai gazdagítása érdekében. A technikák a Apache Spark és a pandák használatát is feldolgozzák az adatfeldolgozáshoz.

>[!IMPORTANT]
>Ha nem Spark-környezetben dolgozik, a nyitott adatkészletek lehetővé teszik, hogy egyszerre csak egy hónapos adatokat töltsön le, hogy bizonyos osztályok ne MemoryError el a nagyméretű adatkészletekkel.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>A NOAA integrált Surface Database-(ISD-) adatkészletek betöltése 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Az időjárási adatmennyiség egy legutóbbi hónapjának betöltése egy Panda-dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Ismerje meg, hogyan tölthetők be a régi időjárási adatai a kedvenc Panda-dataframe. |
|[Töltsön be egy legutóbbi havi időjárási adatmennyiséget egy Spark-dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Ismerje meg, hogyan tölthetők be a régi időjárási adatai a kedvenc Spark-dataframe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Bemutatóhoz való csatlakozás a NOAA ISD-adatszolgáltatásokkal 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Bemutatóhoz való csatlakozás időjárási adatbevitelsel – pandák](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Az érzékelő helyeinek egy 1 hónapos bemutató adatkészletét kell összekapcsolni egy Panda dataframe időjárási beolvasásával.  |
|[Bemutatóhoz való csatlakozás időjárási adatbevitelsel – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Összekapcsolhatja az érzékelő helyeinek bemutató-adatkészletét egy Spark-dataframe időjárási beolvasásával. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Csatlakozzon a New York-i taxi-adatszolgáltatáshoz a NOAA ISD- 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Az időjárási adatvesztéssel dúsított taxi Trip-adatpanda](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Töltse be a New York-i zöld taxi adatait (több mint 1 hónap), és gazdagítsa az időjárási adatokkal egy Panda dataframe. Ez a példa felülbírálja a metódust `get_pandas_limit` , és kiegyensúlyozza az adatterhelési teljesítményt az adat mennyiségével.|
|[Az időjárási adatbevitelsel dúsított taxi Trip-adatcsatorna – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | A Spark dataframe-ben betöltheti a New York-i zöld taxi-adatgyűjtést  |

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: regressziós modellezés automatizált gépi tanulással és nyitott adatkészlettel](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK a nyílt adatkészletekhez](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure Open Datasets-katalógus](https://azure.microsoft.com/services/open-datasets/catalog/)
* [Azure Machine Learning adatkészlet létrehozása a megnyitott adatkészletből](how-to-create-azure-machine-learning-dataset-from-open-dataset.md)
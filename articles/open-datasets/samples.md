---
title: A példában Jupyter notebookok használata a NOAA adatkészlet megnyitása
titleSuffix: Azure Open Datasets
description: A példában Jupyter-notebookok Azure nyissa meg a adatkészletek használatával megtudhatja, hogyan tölthet be nyissa meg az adatkészletek, és a használatukat bemutató adatokat feldúsítani. Ennek technikái a következők a Spark és a Pandas adatok feldolgozásához.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 05/02/2019
ms.openlocfilehash: b62a2690e5879e45a14d0b06a38e8c5171dda14e
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442333"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>A példában Jupyter notebookok bemutatják, hogyan nyitott adatkészletekkel adatokat feldúsítani 
A példában Jupyter notebookok Azure nyissa meg a adatkészletekhez bemutatják, hogyan nyissa meg az adatkészletek betöltése, és a használatukat bemutató adatokat feldúsítani. Ennek technikái a következők az Apache Spark és a Pandas adatok feldolgozásához.

>[!IMPORTANT]
>A Spark környezetben működik, nyissa meg az adatkészletek lehetővé teszi nagy adatkészletekkel MemoryError elkerülése érdekében csak egy hónapnyi adat bizonyos osztályokat egyszerre letöltése.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>A NOAA integrált Surface adatbázis (ISD) adatok betöltése 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Egy újabb hónap, az időjárási adatok betöltése a Pandas dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Ismerje meg, hogyan tölthet be a kedvenc Pandas dataframe korábbi időjárási adatokat. |
|[Egy újabb hónap, az időjárási adatok betöltése a Spark dataframe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Ismerje meg, hogyan tölthet be a kedvenc Spark dataframe korábbi időjárási adatokat.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Csatlakozzon a NOAA ISD bemutató adatait 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Az időjárási adatok - Pandas bemutató adatait](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Csatlakozzon az érzékelő helyek az időjárás olvasmányok 1 hónapos bemutató adatkészlet a Pandas dataframe.  |
|[Csatlakozás az időjárási adatok – bemutató adatok Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Az érzékelő helyek az időjárás olvasmányok bemutató adatkészlet csatlakozzon a Spark dataframe. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>A NOAA ISD adatokkal NYC i taxik adatait 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Időjárási adatok - Pandas renderelésre taxi útadatok](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Betölteni NYC zöld i taxik adatait (több mint 1 hónap), és bővítheti, időjárási adatok Pandas dataframe használata. Ez a példa felülírja a metódus `get_pandas_limit` , és elosztja az adatok betöltési teljesítmény az adatok mennyisége.|
|[Időjárási adatok – Spark renderelésre taxi útadatok](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | NYC zöld taxi-adatok betöltése, és azt időjárási adatok, a Spark dataframe való bővítését.  |

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Automatizált gépi tanulással és a egy megnyitott adatkészlet modellezési regresszió](tutorial-opendatasets-automl.md)
* [Python SDK nyílt adatkészletek esetében](https://aka.ms/open-datasets-api)
* [Az Azure Open adatkészletek katalógus](https://azure.microsoft.com/services/open-datasets/catalog/)

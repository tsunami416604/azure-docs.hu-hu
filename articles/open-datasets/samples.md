---
title: Példa Jupyter notebookok noaa adatokat használó
titleSuffix: Azure Open Datasets
description: Használja a Jupyter-jegyzetfüzetek példát az Azure Open Datasets-hez, hogy megtudja, hogyan tölthet be nyitott adatkészleteket, és hogyan gazdagíthatja a bemutató adatokat. A technikák közé tartozik a Spark és a Pandák használata az adatok feldolgozásához.
ms.service: open-datasets
ms.topic: sample
author: cjgronlund
ms.author: cgronlun
ms.date: 11/04/2019
ms.openlocfilehash: 8b96a35db91a282be1fb5e4c6143e6bd0a0203f2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73606143"
---
# <a name="example-jupyter-notebooks-show-how-to-enrich-data-with-open-datasets"></a>Példa Jupyter-jegyzetfüzetekre, amelyek bemutatják, hogyan gazdagíthatja az adatokat a nyílt adatkészletekkel 
A példa Jupyter notebookok az Azure Open datasets bemutatja, hogyan töltheti be a nyitott adatkészletek és használja őket a bemutató adatok bővítésére. A technikák közé tartozik az Apache Spark és a Pandák használata az adatok feldolgozásához.

>[!IMPORTANT]
>Nem Spark-környezetben végzett munka esetén az Open Datasets lehetővé teszi, hogy bizonyos osztályok on-val egyszerre csak egy hónapnyi adatot töltsön le, hogy elkerülje a nagy adatkészletekkel rendelkező MemoryError-t.

## <a name="load-noaa-integrated-surface-database-isd-data"></a>NOAA integrált felületadatbázis (ISD) adatainak betöltése 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Töltsön be egy legutóbbi hónap időjárási adatait egy Pandas adatkeretbe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/02-weather-to-pandas-dataframe.ipynb) | Ismerje meg, hogyan töltheti be a korábbi időjárási adatokat kedvenc Pandas adatkeretébe. |
|[Időjárási adatok egy legutóbbi hónapjának betöltése egy Spark-adatkeretbe](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-access/01-weather-to-spark-dataframe.ipynb) | Ismerje meg, hogyan töltheti be a korábbi időjárási adatokat a kedvenc Spark-adatkeretbe.  |

## <a name="join-demo-data-with-noaa-isd-data"></a>Bemutatóadatok összeillesztése NOAA ISD-adatokkal 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[Csatlakozzon demo adatok időjárási adatok - Panda](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/02-weather-join-in-pandas.ipynb) | Csatlakozzon az érzékelők helyeinek 1 hónapos bemutató adatkészletéhez a Pandas adatkeretben mért időjárási adatokkal.  |
|[Demo adatok összeillesztése időjárási adatokkal – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/01-weather-join-in-spark.ipynb) | Csatlakozzon az érzékelő helyek bemutató adatkészletéhez a Spark-adatkeretben leolvasott időjárási adatokkal. |

## <a name="join-nyc-taxi-data-with-noaa-isd-data"></a>Csatlakozz on NYC taxi adatok NOAA ISD adatok 
|Jegyzetfüzet        | Leírás                                    |
|----------------|------------------------------------------------|
|[A taxiút adatai dúsított időjárási adatok - Pandák](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/04-nyc-taxi-join-weather-in-pandas.ipynb) | Töltse nyc zöld taxi adatok (több mint 1 hónap), és gazdagítsa azt időjárási adatok at A Pandas dataframe. Ez a példa `get_pandas_limit` felülbírálja a metódust, és kiegyenlíti az adatterhelési teljesítményt az adatok mennyiségével.|
|[Időjárás-adatokkal gazdagított taxiút-adatok – Spark](https://github.com/Azure/OpenDatasetsNotebooks/blob/master/tutorials/data-join/03-nyc-taxi-join-weather-in-spark.ipynb) | Töltse be a NYC zöld taxi adatait, és gazdagítsa az időjárási adatokkal a Spark adatkeretben.  |

## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Regressziós modellezés automatizált gépi tanulással és nyílt adatkészlettel](/azure/machine-learning/service/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Python SDK nyílt adatkészletekhez](/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py)
* [Azure Open Datasets-katalógus](https://azure.microsoft.com/services/open-datasets/catalog/)

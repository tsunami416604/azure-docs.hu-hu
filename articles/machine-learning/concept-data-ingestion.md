---
title: Adatfeldolgozási beállítások
titleSuffix: Azure Machine Learning
description: Ismerkedjen meg a gépi tanulási modellek betanítására szolgáló adatfeldolgozási lehetőségekkel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.openlocfilehash: 3370fc72b45720e2aae5731623843a1bc498911c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652073"
---
# <a name="data-ingestion-in-azure-machine-learning"></a>Adatfeldolgozás a Azure Machine Learningban

Ebből a cikkből megtudhatja, milyen előnyökkel és hátrányokkal jár együtt a Azure Machine Learning a következő adatfeldolgozási lehetőségek közül. 

1. [Azure Data Factory](#use-azure-data-factory) folyamatok
2. [Python SDK Azure Machine Learning](#use-the-python-sdk)

Az adatfeldolgozás az a folyamat, amelyben a strukturálatlan adatok kinyerése egy vagy több forrásból történik, majd a gépi tanulási modellek előkészítése. Az is időigényes, különösen ha kézzel történik, és ha nagy mennyiségű adattal rendelkezik több forrásból. A tevékenység automatizálása felszabadítja az erőforrásokat, és gondoskodik arról, hogy a modellek a legfrissebb és a vonatkozó adatait használják.

Javasoljuk, hogy először a Azure Data Factory (ADF) használatával értékelje ki, mivel kifejezetten az adatok kinyerésére, betöltésére és átalakítására készült. Ha az ADF használatával nem tudja kielégíteni a követelményeit, használhatja a Python SDK-t egyéni kódú megoldás fejlesztéséhez, illetve az ADF és a Python SDK együttes használatával, hogy az igényeinek megfelelő összesített adatfeldolgozási munkafolyamatot hozzon létre.

## <a name="use-azure-data-factory"></a>Az Azure Data Factory használata

A [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) natív támogatást nyújt az adatfeldolgozási folyamatokhoz használt adatforrás-figyeléshez és-eseményindítóhoz.  

Az alábbi táblázat összefoglalja az adatfeldolgozási munkafolyamatok Azure Data Factory használatának előnyeit és hátrányait.

|Szakemberek számára|Hátrányok
---|---
Kifejezetten az adatok kinyerésére, betöltésére és átalakítására készült.|A jelenleg korlátozott számú Azure Data Factory folyamattal kapcsolatos feladatot biztosít 
Lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatáthelyezés és az átalakítások méretezése érdekében.|Költséges a létrehozás és a karbantartás. További információért tekintse meg Azure Data Factory [díjszabási lapját](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .
Különböző Azure-eszközökkel integrált, például [Azure Databricks](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook) és [Azure functions](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity) | A parancsfájlok nem futnak natív módon, hanem külön számítási feladatokra támaszkodnak a parancsfájlok futtatásához. 
Natív módon támogatja az adatforráshoz indított adatfeldolgozást| 
Az adatelőkészítési és-modell-betanítási folyamatok elkülönül.|
Beágyazott adatbányászati képesség Azure Data Factory adatfolyamok|
Alacsony kódú [felhasználói felületet](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) biztosít a nem parancsfájl-kezelési módszerekhez |

Ezek a lépések és a következő ábra a Azure Data Factory adatfeldolgozási munkafolyamatát szemlélteti.

1. Az adatok lekérése a forrásaiból
1. Alakítsa át és mentse az adatokat egy kimeneti blob-tárolóba, amely adattárolóként szolgál Azure Machine Learning
1. Az előkészített adattárolással a Azure Data Factory folyamat meghívja a képzési Machine Learning folyamatot, amely a modell betanításához felkészített adatfeldolgozást fogadja.


    ![ADF-adatfeldolgozás](media/concept-data-ingestion/data-ingest-option-one.svg)

## <a name="use-the-python-sdk"></a>A Python SDK használata 

A [PYTHON SDK](https://docs.microsoft.com/python/api/overview/azureml-sdk/?view=azure-ml-py)-val beépítheti az adatfeldolgozási feladatokat egy [Azure Machine learning folyamat](how-to-create-your-first-pipeline.md) lépésbe.

Az alábbi táblázat összefoglalja az SDK-t és az adatfeldolgozási feladatokhoz szükséges ML folyamatokat.

Szakemberek számára| Hátrányok
---|---
Saját Python-parancsfájlok konfigurálása | A nem támogatja natív módon az adatforrás módosításának aktiválását. Logic app-vagy Azure-függvények megvalósításához szükséges
Az adatelőkészítés az összes modell betanításának részeként|Fejlesztési ismereteket igényel az adatfeldolgozási parancsfájlok létrehozásához
Támogatja az adatelőkészítési parancsfájlokat különböző számítási céloknál, beleértve a [Azure Machine learning számítást](concept-compute-target.md#azure-machine-learning-compute-managed) is |Nem biztosít felhasználói felületet a betöltési mechanizmus létrehozásához.

A következő ábrán a Azure Machine Learning folyamat két lépésből áll: az adatfeldolgozás és a modell betanítása. Az adatfeldolgozási lépés magában foglalja a Python-kódtárak és a Python SDK használatával elvégezhető feladatokat, például az adatok helyi/webes forrásokból való kinyerését, valamint az alapvető adatátalakításokat, például a hiányzó imputálási. A betanítási lépés ezután az előkészített adatokat bemenetként használja a betanítási szkriptnek a gépi tanulási modell betanításához. 

![Azure-folyamat + SDK-adatfeldolgozás](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan automatizálhatja és kezelheti az adatfeldolgozási folyamatokat az Azure- [folyamatokkal](how-to-cicd-data-ingestion.md).
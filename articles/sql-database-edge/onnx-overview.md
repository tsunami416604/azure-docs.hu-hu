---
title: Machine learning és AI with ONNX in Azure SQL Database Edge – előzetes verzió | Microsoft Docs
description: Az Azure SQL Database Edge előzetes verziójában a Machine learning az Open neurális hálózati Exchange (ONNX) formátumú modelleket támogatja. A ONNX egy nyílt formátum, amellyel különböző gépi tanulási keretrendszerek és eszközök közötti adatcsere-modelleket lehet használni.
keywords: az SQL Database Edge üzembe helyezése
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: ronychatterjee
ms.author: achatter
ms.reviewer: davidph
ms.date: 11/04/2019
ms.openlocfilehash: 4771db21a0ea5e841dbe12c8ce915b3833a8a30d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692327"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Gépi tanulás és AI a ONNX SQL Database Edge előzetes verzióban

Az Azure SQL Database Edge előzetes verziójában a Machine learning az [Open neurális hálózati Exchange (ONNX)](https://onnx.ai/) formátumú modelleket támogatja. A ONNX egy nyílt formátum, amellyel különböző [gépi tanulási keretrendszerek és eszközök](https://onnx.ai/supported-tools)közötti adatcsere-modelleket lehet használni.

## <a name="supported-tool-kits"></a>Támogatott eszközkészletek

A ONNXMLTools lehetővé teszi, hogy különböző gépi tanulási eszközkészletből származó modelleket alakítsanak át egy ONNX-modellbe. A numerikus adattípusok és az egyoszlopos bemenetek esetében jelenleg a következő eszközkészletek támogatottak:

* [scikit-learn](https://github.com/onnx/sklearn-onnx)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Kerasz](https://github.com/onnx/keras-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)
* [Spark ML (kísérleti)](https://github.com/onnx/onnxmltools/tree/master/onnxmltools/convert/sparkml)
* [LightGBM](https://github.com/onnx/onnxmltools)
* [libsvm](https://github.com/onnx/onnxmltools)
* [XGBoost](https://github.com/onnx/onnxmltools)

## <a name="get-onnx-models"></a>ONNX-modellek beolvasása

Több módon is beszerezheti a modelleket a ONNX formátumban:

- [ONNX Model Zoo](https://github.com/onnx/models): több előre betanított ONNX-modellt tartalmaz különböző típusú feladatokhoz. A Windows Machine Learning által támogatott verziókat is letöltheti és használhatja.

- [Natív exportálás gépi tanulási képzési keretrendszerekből](https://onnx.ai/supported-tools): számos oktatási keretrendszer támogatja a natív exportálási funkciókat a ONNX, ami lehetővé teszi a betanított modell mentését a ONNX formátum egy adott verziójára. Például: láncolás, Caffee2 és PyTorch. Emellett az olyan szolgáltatások, mint a [Azure Machine learning Service](https://azure.microsoft.com/services/machine-learning-service/) és az [Azure Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) , natív ONNX-exportálást is biztosítanak.

  - A ONNX-modellek felhőbe Custom Vision használatával történő betanításával és exportálásával kapcsolatban lásd az [oktatóanyag: ONNX-modell használata Custom Vision és a Windows ml (előzetes verzió)](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/custom-vision-onnx-windows-ml)című témakört.

- [Meglévő modellek konvertálása a WinMLTools használatával](https://docs.microsoft.com/windows/ai/windows-ml/convert-model-winmltools): Ez a Python-csomag lehetővé teszi, hogy a modellek több képzési keretrendszer-FORMÁTUMból ONNX-re legyenek konvertálva. Megadhatja, hogy a ONNX melyik verzióját szeretné átalakítani a modellre, attól függően, hogy az alkalmazás mely verzióira vonatkozik. Ha nem ismeri a Pythont, használhatja a [Windows Machine learning UI-alapú irányítópultot](https://github.com/Microsoft/Windows-Machine-Learning/tree/master/Tools/WinMLDashboard) a modellek átalakításához.

> [!IMPORTANT]
> Az Azure SQL Database Edge nem támogatja az összes ONNX-verziót. Jelenleg csak a numerikus adattípusok előre történő előrejelzése támogatott a ONNX modell használatával.

Ha már rendelkezik ONNX-modellel, Azure SQL Database Edge-ben telepítheti a modellt. Ezután [natív pontozást alkalmazhat a prediktív T-SQL függvénnyel](/sql/advanced-analytics/sql-native-scoring/).

## <a name="limitations"></a>Korlátozások

Ez a támogatás jelenleg **numerikus adattípusú**modellekre korlátozódik:

- [int és bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [valós és lebegőpontos](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
A más numerikus típusok a CAST és [a convert paranccsal](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)alakíthatók át támogatott típusokra.

A modell bemeneteit strukturálni kell, hogy a modell minden bemenete egy tábla egyetlen oszlopának felel meg. Ha például pandák dataframe használ a modell betanításához, akkor minden bemenetnek külön oszlopnak kell lennie a modellhez.

## <a name="next-steps"></a>További lépések

- [SQL Database Edge üzembe helyezése Azure Portal](deploy-portal.md)
- [ONNX-modell üzembe helyezése Azure SQL Database Edge előzetes verziójában](deploy-onnx.md)

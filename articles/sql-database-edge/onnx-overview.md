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
ms.date: 11/07/2019
ms.openlocfilehash: 976c849f9cb48e1c197f70d10e911216a6a7425c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822851"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Gépi tanulás és AI a ONNX SQL Database Edge előzetes verzióban

Az Azure SQL Database Edge előzetes verziójában a Machine learning az [Open neurális hálózati Exchange (ONNX)](https://onnx.ai/) formátumú modelleket támogatja. A ONNX egy nyílt formátum, amellyel különböző [gépi tanulási keretrendszerek és eszközök](https://onnx.ai/supported-tools)közötti adatcsere-modelleket lehet használni.

## <a name="overview"></a>Áttekintés

Ha Azure SQL Database Edge-ben szeretné kikövetkeztetni a gépi tanulási modelleket, először be kell szereznie egy modellt. Ez lehet egy előre betanított modell vagy egy egyéni modell, amely az Ön által választott keretrendszerrel lett kiképezve. Azure SQL Database Edge támogatja a ONNX formátumot, és a modellt erre a formátumra kell konvertálnia. A modell pontossága nem befolyásolható, és ha már rendelkezik a ONNX-modellel, a modellt üzembe helyezheti Azure SQL Database Edge-ben, és használhatja [a natív pontozást a prediktív T-SQL függvénnyel](/sql/advanced-analytics/sql-native-scoring/).

## <a name="get-onnx-models"></a>ONNX-modellek beolvasása

Több módon is beszerezheti a modelleket a ONNX formátumban:

- [ONNX Model Zoo](https://github.com/onnx/models): számos előre betanított ONNX-modellt tartalmaz különböző típusú feladatokhoz, amelyek letölthetők és használatra készek.

- [Natív Exportálás az ml betanítási keretrendszerekből](https://onnx.ai/supported-tools): számos oktatási keretrendszer támogatja a natív exportálási funkciókat a ONNX, ami lehetővé teszi a betanított modell mentését a ONNX formátum egy adott verziójára, beleértve a [PyTorch](https://pytorch.org/docs/stable/onnx.html), a láncot és a Caffe2. Emellett a modell olyan szolgáltatásokat is kínál, mint például az [automatikus Machine learning funkció a Azure Machine learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) és az [Azure Custom Vision Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) ONNX-exportálást biztosít.

- [Meglévő modellek konvertálása](https://github.com/onnx/tutorials#converting-to-onnx-format): a natív exportálást nem támogató keretrendszerek esetében léteznek önálló csomagok, amelyek a modelleket ONNX formátumra alakítják át. Példák és oktatóanyagok: [Konvertálás ONNX formátumra](https://github.com/onnx/tutorials#converting-to-onnx-format). 

### <a name="supported-frameworks"></a>Támogatott keretrendszerek

A ONNX-átalakítók lehetővé teszik különböző gépi tanulási keretrendszerekből betanított modellek átalakítását a ONNX formátumba. A népszerű átalakítók a következők: 

* [PyTorch](http://pytorch.org/docs/master/onnx.html)
* [Tensorflow](https://github.com/onnx/tensorflow-onnx)
* [Kerasz](https://github.com/onnx/keras-onnx)
* [Scikit-learn](https://github.com/onnx/sklearn-onnx)
* [CoreML](https://github.com/onnx/onnxmltools)

A támogatott keretrendszerek teljes listájáért lásd: [Konvertálás ONNX formátumra](https://github.com/onnx/tutorials#converting-to-onnx-format).

## <a name="limitations"></a>Korlátozások

A Azure SQL Database Edge jelenleg nem támogatja az összes ONNX modellt. A támogatás a **numerikus adattípusú**modellekre korlátozódik:

- [int és bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql5)
- [valós és lebegőpontos](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
A más numerikus típusok a [Cast és a convert](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)paranccsal alakíthatók át támogatott típusokra.

A modell bemeneteit strukturálni kell, hogy a modell minden bemenete egy tábla egyetlen oszlopának felel meg. Ha például pandák dataframe használ a modell betanításához, akkor minden bemenetnek külön oszlopnak kell lennie a modellhez.

## <a name="next-steps"></a>További lépések

- [SQL Database Edge üzembe helyezése Azure Portal](deploy-portal.md)
- [ONNX-modell üzembe helyezése Azure SQL Database Edge előzetes verziójában](deploy-onnx.md)

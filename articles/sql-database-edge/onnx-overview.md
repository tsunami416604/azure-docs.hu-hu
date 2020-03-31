---
title: Gépi tanulás és AI onnx-szal az Azure SQL Database Edge előzetes verzióban | Microsoft dokumentumok
description: Az Azure SQL Database Edge Preview gépi tanulása támogatja az Open Neural Network Exchange (ONNX) formátumú modelleket. Az ONNX egy nyílt formátum, amelynek segítségével modelleket válthat a különböző gépi tanulási keretrendszerek és eszközök között.
keywords: sql adatbázis peremének telepítése
services: sql-database-edge
ms.service: sql-database-edge
ms.subservice: machine-learning
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.date: 03/26/2020
ms.openlocfilehash: 7813a08b6b18e517b81e8c4bfac660d198eba7f7
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366270"
---
# <a name="machine-learning-and-ai-with-onnx-in-sql-database-edge-preview"></a>Gépi tanulás és AI ONNX-szel az SQL Database Edge előzetes verzióban

Az Azure SQL Database Edge Preview gépi tanulása támogatja az [Open Neural Network Exchange (ONNX)](https://onnx.ai/) formátumú modelleket. Az ONNX egy nyílt formátum, amelynek segítségével modelleket válthat unk fel különböző [gépi tanulási keretrendszerek és eszközök](https://onnx.ai/supported-tools)között.

## <a name="overview"></a>Áttekintés

Ha az Azure SQL Database Edge gépi tanulási modelljeit kikövetkezteti, először be kell szereznie egy modellt. Ez lehet egy előre betanított modell vagy egy egyéni modell betanított a választott keretrendszer. Az Azure SQL Database Edge támogatja az ONNX formátumot, és a modellt erre a formátumra kell konvertálnia. Nincs hatással a modell pontosságára, és ha már rendelkezik az ONNX modell, telepítheti a modellt az Azure SQL Database Edge és [a natív pontozás a PREDICT T-SQL függvény.](/sql/advanced-analytics/sql-native-scoring/)

## <a name="get-onnx-models"></a>ONNX modellek beolvasása

A modell ONNX formátumú beszerzéséhez:

- **Modellépítési szolgáltatások:** Szolgáltatások, például az [Azure Machine Learning automatizált machine learning szolgáltatás](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) és az Azure Custom Vision [Service](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) támogatása közvetlenül exportálja a betanított modellt az ONNX formátumban.

- [**Meglévő modellek konvertálása és/vagy exportálása**](https://github.com/onnx/tutorials#converting-to-onnx-format): Számos képzési keretrendszer (pl. [PyTorch](https://pytorch.org/docs/stable/onnx.html), Chainer és Caffe2) támogatja a natív exportálási funkciókat az ONNX-re, amely lehetővé teszi a betanított modell mentését az ONNX formátum egy adott verziójára. A natív exportálást nem támogató keretrendszerek esetében vannak önálló ONNX Converter telepítőcsomagok, amelyek lehetővé teszik a különböző gépi tanulási keretrendszerekből betanított modellek ETNX formátumra történő konvertálását.

     **Támogatott keretrendszerek**
   * [PyTorch](http://pytorch.org/docs/master/onnx.html)
   * [Tensorflow](https://github.com/onnx/tensorflow-onnx)
   * [Keras](https://github.com/onnx/keras-onnx)
   * [Scikit-learn](https://github.com/onnx/sklearn-onnx)
   * [CoreML](https://github.com/onnx/onnxmltools)
    
    A támogatott keretrendszerek és példák teljes listáját az [OnNX formátumra konvertálás című témakörben talál.](https://github.com/onnx/tutorials#converting-to-onnx-format)

## <a name="limitations"></a>Korlátozások

Jelenleg nem minden ONNX-modellt támogat az Azure SQL Database Edge. A támogatás **numerikus adattípusokkal**rendelkező modellekre korlátozódik:

- [int és bigint](https://docs.microsoft.com/sql/t-sql/data-types/int-bigint-smallint-and-tinyint-transact-sql)
- [valós és úszó](https://docs.microsoft.com/sql/t-sql/data-types/float-and-real-transact-sql).
  
Más numerikus típusok a CAST [és](https://docs.microsoft.com/sql/t-sql/functions/cast-and-convert-transact-sql)a CONVERT segítségével támogatott típusokká konvertálhatók.

A modell bemeneteit úgy kell felépíteni, hogy a modell minden egyes bemenete egy táblázat egyetlen oszlopának feleljen meg. Ha például egy panda dataframe-ot használ egy modell betanításához, akkor minden bemenetnek külön oszlopnak kell lennie a modellhez.

## <a name="next-steps"></a>További lépések

- [SQL Database Edge telepítése az Azure Portalon keresztül](deploy-portal.md)
- [ONNX-modell üzembe helyezése az Azure SQL Database Edge előzetes verzióján](deploy-onnx.md)

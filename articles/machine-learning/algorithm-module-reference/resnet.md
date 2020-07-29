---
title: ResNet
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre képbesorolási modellt a ResNet algoritmus használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450730"
---
# <a name="resnet"></a>ResNet

Ez a cikk azt ismerteti, hogyan használható a **ResNet** modul a Azure Machine learning Designerben (előzetes verzió) a rendszerkép-besorolási modell létrehozásához a ResNet algoritmus használatával.  

Ez a besorolási algoritmus egy felügyelt tanulási módszer, amelyhez címkézett adatkészlet szükséges. A címkével ellátott Képkönyvtár beszerzésével kapcsolatos további információkért tekintse meg a [Konvertálás Képkönyvtár](convert-to-image-directory.md) -modulba című témakört. A modellt úgy is betaníthatja, hogy a modell és a címkével ellátott képkönyvtárat bemenetként adja meg a [Pytorch-modell betanításához](train-pytorch-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére a [score képmodell](score-image-model.md)használatával.

### <a name="more-about-resnet"></a>További információ a ResNet

A ResNet kapcsolatos további részletekért tekintse meg [ezt a papírt](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) .

## <a name="how-to-configure-resnet"></a>A ResNet konfigurálása

1.  Adja hozzá a **ResNet** modult a folyamathoz a tervezőben.  

2.  A **modell neve**mezőben adja meg egy adott ResNet-struktúra nevét, és válasszon a támogatott ResNet közül: "resnet18", "resnet34", "resnet50", "resnet101", "resnet152", "resnet152", "resnext50 \_ 32x4d", "resnext101 \_ 32x8d", "wide_resnet50 \_ 2", "wide_resnet101 \_ 2".

3.  Az előre **betanított**beállításnál válassza ki, hogy a ImageNet-on előzetesen képzett modellt kíván-e használni. Ha be van jelölve, a modell finomhangolása a kiválasztott előre betanított modell alapján végezhető el. Ha nincs kiválasztva, a kiképzést elvégezheti a semmiből.

4.  A **DenseNet** modul, a képzés és az érvényesítés képkészlet-modul kimenetének csatlakoztatása a [Pytorch-modellhez](train-pytorch-model.md). 

5. A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A folyamat futásának befejezése után a modell a pontozáshoz való használatához a [Pytorch modellt](train-pytorch-model.md) a [képmodellhez](score-image-model.md)kell kapcsolni az új bemeneti példák értékének előrejelzéséhez.

## <a name="technical-notes"></a>Technikai megjegyzések  

###  <a name="module-parameters"></a>Modul paramétereinek  

| Name       | Tartomány | Típus    | Alapértelmezett           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Modell neve | Bármelyik   | Mód    | resnext101 \_ 32x8d | Egy bizonyos ResNet-struktúra neve       |
| Imagenet | Bármelyik   | Logikai | True (Igaz)              | Azt határozza meg, hogy az ImageNet-on előre betanított modellt használ-e |
|            |       |         |                   |                                          |

###  <a name="output"></a>Kimenet  

| Name            | Típus                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nem betanított modell | UntrainedModelDirectory | Egy képzetlen ResNet-modell, amely csatlakoztatható a Pytorch-modellhez. |

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
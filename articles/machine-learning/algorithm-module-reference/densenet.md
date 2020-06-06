---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre képbesorolási modellt a densenet algoritmus használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: db0e2f90ee45d4e1c0173cbc037084793a66e149
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450646"
---
# <a name="densenet"></a>DenseNet

Ez a cikk azt ismerteti, hogyan használható a **DenseNet** modul a Azure Machine learning Designerben (előzetes verzió) a rendszerkép-besorolási modell létrehozásához a DenseNet algoritmus használatával.  

Ez a besorolási algoritmus egy felügyelt tanulási módszer, amelyhez címkézett adatkészlet szükséges. A címkével ellátott Képkönyvtár beszerzésével kapcsolatos további útmutatásért tekintse meg az [átalakítás rendszerkép-](convert-to-image-directory.md) modulba című témakört. A modellt úgy is betaníthatja, hogy a modellt és a címkével ellátott képkönyvtárat bemenetként adja meg a [Pytorch-modell betanításához](train-pytorch-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére a [score képmodell](score-image-model.md)használatával.

### <a name="more-about-densenet"></a>További információ a DenseNet

További részletekért tekintse meg a következő témakört: [sűrűn összekapcsolt, összekapcsolt hálózati hálózatok](https://arxiv.org/abs/1608.06993) .

## <a name="how-to-configure-densenet"></a>A DenseNet konfigurálása

1.  Adja hozzá a **DenseNet** modult a folyamathoz a tervezőben.  

2.  A **modell neve**mezőben adja meg egy adott densenet-struktúra nevét, és válasszon a támogatott densenet közül: "densenet121", "densenet161", "densenet169", "densenet201".

3.  Az előre **betanított**beállításnál válassza ki, hogy a ImageNet-on előzetesen képzett modellt kíván-e használni. Ha be van jelölve, a modell finomhangolása a kiválasztott előre betanított modell alapján végezhető el. Ha nincs kiválasztva, a kiképzést elvégezheti a semmiből.

4.  A **memória hatékonysága**beállításnál megadhatja, hogy az ellenőrzőpontot használja-e, ami sokkal nagyobb memória-hatékony, de lassabb. További információ: https://arxiv.org/pdf/1707.06990.pdf.

5.  A **DenseNet** modul, a képzés és az érvényesítés képkészlet-modul kimenetének csatlakoztatása a [Pytorch-modellhez](train-pytorch-model.md). 

6. A folyamat elküldése.


## <a name="results"></a>Results (Eredmények)

A folyamat futásának befejezése után a modell a pontozáshoz való használatához a [Pytorch modellt](train-pytorch-model.md) a [képmodellhez](score-image-model.md)kell kapcsolni az új bemeneti példák értékének előrejelzéséhez.

## <a name="technical-notes"></a>Technikai megjegyzések  

###  <a name="module-parameters"></a>Modul paramétereinek  

| Name             | Tartomány | Típus    | Alapértelmezett     | Description                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Modell neve       | Bármelyik   | Mód    | densenet201 | Egy bizonyos densenet-struktúra neve     |
| Imagenet       | Bármelyik   | Logikai | True (Igaz)        | Azt határozza meg, hogy az ImageNet-on előre betanított modellt használ-e |
| Hatékony memória | Bármelyik   | Logikai | False (Hamis)       | Az ellenőrzőpontok használata, amely sokkal nagyobb memória-hatékonyságot, de lassabb |

###  <a name="output"></a>Kimenet  

| Name            | Típus                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nem betanított modell | UntrainedModelDirectory | Egy képzetlen densenet-modell, amely csatlakoztatható a Pytorch-modellhez. |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

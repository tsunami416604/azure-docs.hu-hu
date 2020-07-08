---
title: Képkönyvtár felosztása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan készíthet előrejelzéseket egy betanított képmodell használatával a Azure Machine Learning pontszám rendszerkép-modell moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 9673b3260425fd9244c635beaf77d367a14cac54
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84810193"
---
# <a name="split-image-directory"></a>Képkönyvtár felosztása

Ez a témakör azt ismerteti, hogyan használható a lemezkép-könyvtár felosztása a Azure Machine Learning Designerben (előzetes verzió).

Ez a modul különösen akkor hasznos, ha a képadatokat betanítási és tesztelési készletekbe kell elkülöníteni. 

## <a name="how-to-configure-split-image-directory"></a>A felosztott rendszerkép könyvtárának konfigurálása

1. Adja hozzá a **felosztott rendszerkép könyvtára** modult a folyamathoz. Ez a modul a "Computer Vision/képadatok átalakítása" kategóriában található.

2. Kapcsolja össze azt a modult, amelynek kimenete a rendszerkép könyvtára.

3. Az **első kimenetben található rendszerképek bemeneti hányada** , amely a bal oldali felosztásban lévő adatok százalékos arányát adja meg, alapértelmezés szerint 0,9.


## <a name="technical-notes"></a>Technikai megjegyzések

### <a name="expected-inputs"></a>Várt bemenetek

| Name                  | Típus           | Description              |
| --------------------- | -------------- | ------------------------ |
| Bemeneti rendszerkép könyvtára | ImageDirectory | Felosztható Képkönyvtár |

### <a name="module-parameters"></a>Modul paramétereinek

| Name                                   | Típus  | Tartomány | Választható | Description                            | Alapértelmezett |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Az első kimenetben található rendszerképek töredéke | Float | 0-1   | Kötelező | Az első kimenetben található rendszerképek töredéke | 0.9     |

### <a name="outputs"></a>Kimenetek

| Name                    | Típus           | Description                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Kimeneti rendszerkép directory1 | ImageDirectory | A kijelölt lemezképeket tartalmazó könyvtár |
| Kimeneti rendszerkép directory2 | ImageDirectory | Az összes többi rendszerképet tartalmazó könyvtár |

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 


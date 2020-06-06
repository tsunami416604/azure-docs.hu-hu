---
title: Konvertálás rendszerkép-könyvtárba
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adatkészletet a Képkönyvtár formátumára a rendszerkép konvertálása a képkönyvtárba modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450632"
---
# <a name="convert-to-image-directory"></a>Konvertálás rendszerkép-könyvtárba

Ez a cikk bemutatja, hogyan alakítható át a képadatkészlet a kép könyvtára adattípusra a képfájl konvertálása a rendszerkép-címtárba, amely szabványosított adatformátumot tartalmaz a képekhez kapcsolódó feladatokban, például a Azure Machine Learning Designerben (előzetes verzió) képbesorolásban.

## <a name="how-to-use-convert-to-image-directory"></a>Az átalakítás Rendszerképbeli könyvtárba való használata  

1.  A kísérlethez adja hozzá a **Convert to rendszerkép Directory** -modult. Ezt a modult a "Computer Vision/képadatok átalakítása" kategóriában találja a modul listában. 

2.  Rendszerkép-adatkészlet csatlakoztatása bemenetként. Ellenőrizze, hogy van-e rendszerkép a bemeneti adatkészletben.
    A következő adatkészlet-formátumok támogatottak:

    - Tömörített fájl a következő kiterjesztésekben: ". zip", ". tar", ". gz", ". bz2".
    - 1 tömörített fájlt tartalmazó mappa a fenti érvényes kiterjesztésekben. 
    - Képeket tartalmazó mappa.

    > [!NOTE]
    > A rendszerkép kategóriája rögzíthető a modul kimenetében, ha a képkészletet torchvision ImageFolder formátumban rendezik, további információért tekintse meg a [torchvision-adatkészleteket](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) . Ellenkező esetben csak a lemezképek lesznek mentve.

3.  A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A Képkönyvtár-modulba **való átalakítás** kimenete Képkönyvtár formátumú, és csatlakoztatható más rendszerképhez kapcsolódó modulokhoz, amelyekben a bemeneti port formátuma is a rendszerkép könyvtára.

## <a name="technical-notes"></a>Technikai megjegyzések 

###  <a name="expected-inputs"></a>Várt bemenetek  

| Name          | Típus                  | Description   |
| ------------- | --------------------- | ------------- |
| Bemeneti adatkészlet | AnyDirectory, ZipFile | Bemeneti adatkészlet |

###  <a name="output"></a>Kimenet  

| Name                   | Típus           | Description            |
| ---------------------- | -------------- | ---------------------- |
| Kimeneti rendszerkép könyvtára | ImageDirectory | Kimeneti rendszerkép könyvtára |

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

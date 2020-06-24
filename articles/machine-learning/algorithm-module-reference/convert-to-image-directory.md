---
title: Átalakítás képkönyvtárrá
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adatkészletet a Képkönyvtár formátumára a rendszerkép konvertálása a képkönyvtárba modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 41724753df0d529e4c44344e8e975e68ee5eafd6
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84904592"
---
# <a name="convert-to-image-directory"></a>Átalakítás képkönyvtárrá

Ez a cikk bemutatja, hogyan alakítható át a képadatkészlet a kép könyvtára adattípusra a képfájl konvertálása a rendszerkép-címtárba, amely szabványosított adatformátumot tartalmaz a képekhez kapcsolódó feladatokban, például a Azure Machine Learning Designerben (előzetes verzió) képbesorolásban.

## <a name="how-to-use-convert-to-image-directory"></a>Az átalakítás Rendszerképbeli könyvtárba való használata  

1.  A kísérlethez adja hozzá a **Convert to rendszerkép Directory** -modult. Ezt a modult a "Computer Vision/képadatok átalakítása" kategóriában találja a modul listában. 

2.  Rendszerkép-adatkészlet csatlakoztatása bemenetként. Ellenőrizze, hogy van-e rendszerkép a bemeneti adatkészletben.
    A következő adatkészlet-formátumok támogatottak:

    - Tömörített fájl a következő kiterjesztésekben: ". zip", ". tar", ". gz", ". bz2".
    - Képeket tartalmazó mappa. **Javasoljuk, hogy először tömörítse a mappát, majd használja a tömörített fájlt adatkészletként**.

    > [!NOTE]
    > Ha a képkészletet felügyelt tanulásban használja, a címkét kötelező megadni.
    > Képbesorolási feladat esetén a címke a modul kimenetében a "Category" képként hozható létre, ha a képkészletet torchvision ImageFolder formátumban rendezi. Ellenkező esetben a rendszer csak a képeket címke nélkül menti. Íme egy példa arra, hogyan rendezheti a képkészletet a címke lekéréséhez, a képkategória nevet adja meg az almappa neveként. További információért tekintse meg a [torchvision adatkészleteket](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

3.  A folyamat elküldése.

## <a name="results"></a>Results (Eredmények)

A Képkönyvtár-modulba **való átalakítás** kimenete Képkönyvtár formátumú, és csatlakoztatható más rendszerképhez kapcsolódó modulokhoz, amelyekben a bemeneti port formátuma is a rendszerkép könyvtára.

## <a name="technical-notes"></a>Technikai megjegyzések 

###  <a name="expected-inputs"></a>Várt bemenetek  

| Name          | Típus                  | Leírás   |
| ------------- | --------------------- | ------------- |
| Bemeneti adatkészlet | AnyDirectory, ZipFile | Bemeneti adatkészlet |

###  <a name="output"></a>Kimenet  

| Name                   | Típus           | Leírás            |
| ---------------------- | -------------- | ---------------------- |
| Kimeneti rendszerkép könyvtára | ImageDirectory | Kimeneti rendszerkép könyvtára |

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

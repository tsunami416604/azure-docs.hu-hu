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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450112"
---
# <a name="convert-to-image-directory"></a>Átalakítás képkönyvtárrá

Ez a cikk bemutatja, hogyan alakítható át a képadatkészlet a Képkönyvtár típusú adattípusra a képfájl konvertálása a rendszerkép-címtárba, amely szabványosított adatformátumot tartalmaz a rendszerképekhez kapcsolódó feladatokban, például a képbesorolásban Azure Machine Learning Designerben.

## <a name="how-to-use-convert-to-image-directory"></a>Az átalakítás Rendszerképbeli könyvtárba való használata  

1.  Adja hozzá az **átalakítás a rendszerképhez könyvtárba** modult a vászonra. Ezt a modult a "Computer Vision/képadatok átalakítása" kategóriában találja a modul listában. 

2.  A **Képkönyvtári modulba való konvertálás** bemenetének fájl-adatkészletnek kell lennie. [Regisztrálja a rendszerkép-adatkészletet](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) , és kapcsolja össze a modul bemeneti portjával. Ellenőrizze, hogy van-e rendszerkép a bemeneti adatkészletben. A Designer jelenleg nem támogatja a képadatkészlet megjelenítését.
 
    A következő adatkészlet-formátumok támogatottak:

    - Tömörített fájl a következő kiterjesztésekben: ". zip", ". tar", ". gz", ". bz2".
    - Képeket tartalmazó mappa. **Javasoljuk, hogy először tömörítse a mappát, majd használja a tömörített fájlt adatkészletként**.

    > [!WARNING]
    > Az **adatimportálási** modul **nem** használható a képadatkészlet importálására, mert az **adatimportálási** modul kimeneti típusa DataFrame könyvtár, amely csak a fájl elérési útja karakterláncot tartalmazza.
    

    > [!NOTE]
    > - Ha a képkészletet felügyelt tanulásban használja, meg kell adnia a betanítási adatkészlet címkéjét.
    > - Képbesorolási feladat esetén a címke a modul kimenetében a "Category" képként hozható létre, ha a képkészletet torchvision ImageFolder formátumban rendezi. Ellenkező esetben a rendszer csak a képeket címke nélkül menti. Az alábbi példa bemutatja, hogyan rendezheti a képkészletet a címke beolvasásához, ha a képkategóriát almappa neveként használja. 
    > - Az egyes kategóriák mappájában nem kell azonos számú lemezképet feltöltenie.
    > - A következő kiterjesztésű (kisbetűs) képek támogatottak: ". jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". WebP". Egy mappában több típusú képet is használhat.    
    > - További információért tekintse meg a [torchvision adatkészleteket](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Ha a képkészletet használja a pontozáshoz, a modul bemeneti fájljának adatkészlete nem minősített képeket tartalmazhat.
    
3.  A folyamat elküldése. Ezt a modult a GPU vagy a CPU is futtathatja.

## <a name="results"></a>Results (Eredmények)

A Képkönyvtár-modulba **való átalakítás** kimenete Képkönyvtár formátumú, és csatlakoztatható más rendszerképhez kapcsolódó modulokhoz, amelyekben a bemeneti port formátuma is a rendszerkép könyvtára.

## <a name="technical-notes"></a>Technikai megjegyzések 

###  <a name="expected-inputs"></a>Várt bemenetek  

| Név          | Típus                  | Leírás   |
| ------------- | --------------------- | ------------- |
| Bemeneti adatkészlet | AnyDirectory, ZipFile | Bemeneti adatkészlet |

###  <a name="output"></a>Kimenet  

| Név                   | Típus           | Leírás            |
| ---------------------- | -------------- | ---------------------- |
| Kimeneti rendszerkép könyvtára | ImageDirectory | Kimeneti rendszerkép könyvtára |

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

---
title: 'Konvertálás CSV-re: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan alakíthatja át az adatkészletet a Azure Machine Learning CSV-formátumba úgy, hogy az adathalmazokat egy, az R-vagy Python-parancsfájl-modulok használatával letöltött, exportált vagy megosztható CSV formátumúra alakítsa át
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 0e92201552b4d448a619a801d2ee64032c8bbefe
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314436"
---
# <a name="convert-to-csv-module"></a>Konvertálás CSV-modulba

Ez a cikk a Azure Machine Learning Designer egyik modulját ismerteti.

Ezzel a modullal olyan CSV-formátumba alakíthatja át az adatkészletet, amely letölthető, exportálható vagy megosztható R-vagy Python-parancsfájl-modulok használatával.

### <a name="more-about-the-csv-format"></a>További információ a CSV-formátumról 

A CSV-formátum, amely a "vesszővel tagolt értékek", számos külső gépi tanulási eszköz által használt fájlformátum. A CSV gyakori formátum, ha olyan nyílt forráskódú nyelvekkel dolgozik, mint például az R vagy a Python.

Még ha Azure Machine Learning is dolgozik a legtöbb munkában, időnként előfordulhat, hogy az adatkészlet CSV-re való konvertálása külső eszközökön is hasznos lehet. Példa:

+ Töltse le a CSV-fájlt, és nyissa meg az Excelben, vagy importálja egy kapcsolódó adatbázisba.  
+ Mentse a CSV-fájlt a Felhőbeli tárhelyre, és kapcsolódjon hozzá Power BI a vizualizációk létrehozásához.  
+ Az R és a Python alkalmazásban az adatfeldolgozáshoz használja a CSV formátumot. 

Az adatkészlet CSV-re konvertálásakor a rendszer az Azure ML-munkaterületen menti a CSV-fájlt. Az Azure Storage segédprogram használatával közvetlenül is megnyithatja és használhatja a fájlt. A CSV-t a tervezőben is elérheti, ha kiválasztja a **Konvertálás CSV** -re modult, majd a jobb oldali panel **outputs (kimenetek** ) lapján a hisztogram ikont választja a kimenet megtekintéséhez. A CSV-fájlt letöltheti az eredmények mappájából egy helyi könyvtárba.  

## <a name="how-to-configure-convert-to-csv"></a>A CSV-re történő konvertálás konfigurálása


1.  Adja hozzá a konvertálás CSV-modult a folyamathoz. Ez a modul a Designer **Adatátalakítási** csoportjában található. 

2. Csatlakoztatása bármely modulhoz, amely egy adatkészletet eredményez.   
  
3.  A folyamat futtatása.

### <a name="results"></a>Eredmények
  

A jobb oldali panelen válassza a **kimenetek** fület a CSV-re való **konvertáláshoz**, és válassza ki az egyik ikont a **portok kimenete**alatt.  

+ **Adatkészlet regisztrálása**: válassza ki az ikont, és mentse a CSV-fájlt az Azure ml-munkaterületre külön adatkészletként. Az adatkészletet modulként is megtalálhatja a modul fájában a **saját adatkészletek** szakaszban.

 + **Kimenet megtekintése**: válassza ki a szem ikont, és kövesse az utasításokat a **Results_dataset** mappa tallózásához, majd töltse le az adat. csv fájlt.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
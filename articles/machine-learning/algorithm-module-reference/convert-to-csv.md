---
title: 'Konvertálás CSV-vé: modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Convert to CSV modult az Azure Machine Learningben egy adatkészlet et egy CSV-formátumba, amely letölthető, exportálható vagy megosztható R vagy Python parancsfájlmodulokkal.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: fc2a043e8f1565cf5fe45ba0b072ad015076635e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477680"
---
# <a name="convert-to-csv-module"></a>Konvertálás CSV-modullá

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal konvertálhat egy adatkészletet egy CSV-formátumba, amely letölthető, exportálható vagy megosztható Az R vagy Python parancsfájlmodulokkal.

### <a name="more-about-the-csv-format"></a>További információk a CSV formátumról 

A CSV formátum, amely a "vesszővel tagolt értékeket" jelenti, egy olyan fájlformátum, amelyet számos külső gépi tanulási eszköz használ. A CSV egy gyakori csereformátum, ha nyílt forráskódú nyelvekkel dolgozik, például R vagy Python nyelven.

Még akkor is, ha a legtöbb munkát az Azure Machine Learning, előfordulhat, hogy hasznos lehet átalakítani az adatkészletet CSV-re, hogy külső eszközökben használható. Példa:

+ Töltse le a CSV-fájlt, és nyissa meg az Excel programmal, vagy importálja relációs adatbázisba.  
+ Mentse a CSV-fájlt a felhőalapú tárhelyre, és csatlakozzon hozzá a Power BI-ból, és hozzon létre vizualizációkat.  
+ A CSV formátum segítségével előkészítheti az adatokat az R és python használatra. 

Amikor egy adatkészletet CSV-vé konvertál, a csv az Azure ML-munkaterületre kerül. Az Azure storage-segédprogram segítségével közvetlenül megnyithatja és használhatja a fájlt. A tervezőben lévő CSV-t úgy is elérheti, hogy kiválasztja a **Konvertálás CSV-vé** modult, majd a jobb oldali panel **kimenetek** lapján a hisztogram ikont választja a kimenet megtekintéséhez. A CSV-t letöltheti az Eredmények mappából egy helyi könyvtárba.  

## <a name="how-to-configure-convert-to-csv"></a>A Konvertálás CSV-vé való konvertálás konfigurálása


1.  Adja hozzá a Konvertálás CSV-vé modult a folyamathoz. Ez a modul a tervező **Adattranszformáció** csoportjában található. 

2. Csatlakoztassa bármely olyan modulhoz, amely adatkészletet ad ki.   
  
3.  Küldje el a folyamatot.

### <a name="results"></a>Results (Eredmények)
  

Válassza a **Kimenetek** lapot a **Konvertálás CSV-vé**panel jobb oldali paneljén, majd a **Port kimenetek**alatt jelölje ki az ikonok egyikét.  

+ **Adatkészlet regisztrálása:** Válassza ki az ikont, és mentse vissza a CSV-fájlt az Azure ML-munkaterületre külön adatkészletként. Az adatkészletet modulként megtalálhatja a modulfában a **Saját adatkészletek** szakaszban.

 + **Kimenet megtekintése**: Válassza ki a szem ikont, és kövesse az utasításokat a **Results_dataset** mappában való böngészéshez, és töltse le a data.csv fájlt.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
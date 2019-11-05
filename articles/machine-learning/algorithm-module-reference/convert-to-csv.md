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
ms.openlocfilehash: 999f78ab08e1a2c9dd6b28d853e49fbb559fab83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493851"
---
# <a name="convert-to-csv-module"></a>Konvertálás CSV-modulba

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal olyan CSV-formátumba alakíthatja át az adatkészletet, amely letölthető, exportálható vagy megosztható R-vagy Python-parancsfájl-modulok használatával.

### <a name="more-about-the-csv-format"></a>További információ a CSV-formátumról 

A CSV-formátum, amely a "vesszővel tagolt értékek", számos külső gépi tanulási eszköz által használt fájlformátum. A CSV gyakori formátum, ha olyan nyílt forráskódú nyelvekkel dolgozik, mint például az R vagy a Python.

Még ha Azure Machine Learning is dolgozik a legtöbb munkában, időnként előfordulhat, hogy az adatkészlet CSV-re való konvertálása külső eszközökön is hasznos lehet. Példa:

+ Töltse le a CSV-fájlt, és nyissa meg az Excelben, vagy importálja egy kapcsolódó adatbázisba.  
+ Mentse a CSV-fájlt a Felhőbeli tárhelyre, és kapcsolódjon hozzá Power BI a vizualizációk létrehozásához.  
+ Az R és a Python alkalmazásban az adatfeldolgozáshoz használja a CSV formátumot. Kattintson a jobb gombbal a modul kimenetére, hogy létrehozza az adatok közvetlenül a Pythonból vagy egy Jupyter-jegyzetfüzetből való eléréséhez szükséges kódot. 

Ha az adatkészletet CSV-fájllá alakítja át, a fájl az Azure ML-munkaterületen lesz mentve. Az Azure Storage segédprogrammal közvetlenül is megnyithatja és használhatja a fájlt, vagy kattintson a jobb gombbal a modul kimenetére, majd töltse le a CSV-fájlt a számítógépére, vagy használja az R vagy Python kódban.  

## <a name="how-to-configure-convert-to-csv"></a>A CSV-re történő konvertálás konfigurálása


1.  Adja hozzá a [Konvertálás CSV](./convert-to-csv.md) -modult a folyamathoz. Ez a modul a tervező **adatformátum-konverziók** csoportjában található. 

2. Csatlakoztatása bármely modulhoz, amely egy adatkészletet eredményez.   
  
3.  A folyamat futtatása.

### <a name="results"></a>Results (Eredmények)
  

Kattintson duplán a CSV-re történő [Konvertálás](./convert-to-csv.md)kimenetére, és válasszon egyet ezek közül.  

 + **Eredmény-adatkészlet – > Letöltés**: azonnal megnyithatja az adat másolatát CSV formátumban, amelyet a helyi mappába menthet. Ha nem ad meg mappát, a rendszer az alapértelmezett fájlnevet alkalmazza, és a CSV-fájlt a helyi **letöltések** könyvtárba menti.


 + **Eredmény adatkészlet – > mentés adatkészletként**: a CSV-fájlt visszamenti az Azure ml-munkaterületre külön adatkészletként.

 + **Adatelérési kód generálása**: az Azure ml két kódrészletet hoz létre az adateléréshez a Python vagy az R használatával. Az adatelemek eléréséhez másolja a kódrészletet az alkalmazásba. (*Az adatelérési kód előállítása hamarosan elérhető lesz.* )

## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
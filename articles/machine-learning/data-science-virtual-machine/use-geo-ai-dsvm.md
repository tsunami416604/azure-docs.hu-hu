---
title: A Geo AI használata
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan használhatja a Geo AI Data Science virtuális gép adatok elemzésére és modellek térinformatikai adatok alapján történő létrehozásához.
keywords: deep learning, AI, adatelemzési eszközök, adattudományi virtuális gép, térinformatikai elemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278416"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A Geo mesterséges intelligencia adattudományi virtuális gép használata

A Geo AI Data Science virtuális gép segítségével adatokat lehívhatja elemzésre, adathuzavonát hajthat végre, és modelleket hozhat létre a térinformatikai adatokat használó AI-alkalmazásokhoz. Miután kiépítette a Geo AI Data Science virtuális gépét, és az ArcGIS-fiókján keresztül bejelentkezett az ArcGIS Pro-ba, online megkezdheti az ArcGIS asztali számítógéppel és az ArcGIs-ekkel való interakciót. Az ArcGIS-t python-felületekről és egy R nyelvi hídból is elérheti, amely előre konfigurálva van a Geo-Data Science virtuális gépen. Gazdag AI-alkalmazások létrehozásához kombinálja a Geo-Data Science virtuális gépet a gépi tanulási és mélytanulási keretrendszerekkel és más, rajta elérhető adatelemzési szoftverekkel.  


## <a name="configuration-details"></a>A konfiguráció részletei

A Python-függvénytár, az [Arcgis-szel](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm)való kapcsolódásra használt ArcGIS a data science virtuális gép globális ```c:\anaconda```gyökérkondakörnyezetében van telepítve, amely a alkalmazásban található.

- Ha a Python t futtatja egy ```activate``` parancssorból, futtassa a conda root Python környezetben való aktiváláshoz.
- Ha IDE vagy Jupyter notebookot használ, kiválaszthatja a környezetet vagy a kernelt, hogy megbizonyosodjon arról, hogy a megfelelő conda környezetben van.

Az ArcGIS-hez vezető R-híd [egy arcgisbinding](https://github.com/R-ArcGIS/r-bridge) nevű R-könyvtárként van telepítve a ```C:\Program Files\Microsoft\ML Server\R_SERVER```microsoft machine learning server fő önálló példányában, amely a helyen található. A Visual Studio, az RStudio és a Jupyter már előre konfigurálva van az R-környezet használatára, és hozzáférhet nek az ```arcgisbinding``` R-könyvtárhoz. 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI Data Science virtuálisgép-minták

Az alapadat-elemzési virtuális gép gépi tanulási és deep-learning keretalapú mintaaain kívül térinformatikai minták egy készlete is rendelkezésre áll a Geo AI Data Science virtuális gép részeként. Ezek a minták segítségével a térinformatikai adatok és az ArcGIS szoftver segítségével elindíthatja a a ai-alkalmazások fejlesztését:


1. [A térinformatikai elemzés megkezdése a Pythonnal:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Az arcpy könyvtár biztosítja a térinformatikai adatok nak az ArcGIS-hez való keresztüli, térinformatikai adatokkal való használatát bemutató bevezető [minta.](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulást a térinformatikai adatokkal, majd az eredményt az ArcGIS térképén jelenítheti meg.

2. [A térinformatikai elemzés első lépései az R:](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)Bevezető minta, amely bemutatja, hogyan dolgozhat térinformatikai adatokkal az ArcGIS R felületén keresztül, amelyet az [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) könyvtár biztosít. 

3. [Képpontszintű földhasználati besorolás:](https://github.com/Azure/pixel_level_land_classification)Olyan oktatóanyag, amely bemutatja, hogyan hozhat létre mély neurális hálózati modellt, amely bemenetként elfogad egy légi felvételt, és egy telekborító-címkét ad vissza. A talajtakaró címkéi például *erdősek* és *a víz*. A modell a kép minden képpontja esetében ilyen címkét ad vissza. 


## <a name="next-steps"></a>További lépések

Az adatelemzési virtuális gép használatával további minták itt érhetők el:

* [Minták](dsvm-samples-and-walkthroughs.md)
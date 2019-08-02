---
title: Az a földrajzi mesterséges intelligenciát használó adatelemző virtuális gép – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a földrajzi mesterséges Intelligencia adatelemző virtuális gép-adatok elemzésére és a földrajzi adatok alapján modelleket.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: vijetaj
ms.openlocfilehash: 9bca7089e6137b3780e3d22f50887e880be29d8e
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565070"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A földrajzi mesterséges intelligenciát használó adatelemző virtuális gép használata

A földrajzi mesterséges Intelligencia adatelemző virtuális gép használatával adatlehívást elemzéshez, hajtsa végre az adatok konvertálását és modelleket az AI-alkalmazások, amelyek a földrajzi információk felhasználása. Miután a földrajzi mesterséges Intelligencia adatelemző virtuális gép kiépítése és ArcGIS Pro bejelentkezett az ArcGIS-fiókjával, megkezdése ArcGIS asztali és az ArcGis online implementálására. Az ArcGIS a Python-felületek és a egy R nyelv híd előre konfigurálva a Geo-adatelemzési virtuális gép keresztül is elérhető. Gazdag AI-alkalmazások létrehozásához, kombinálva, a gép tanulási és deep learning-keretrendszerek és egyéb adatok tudományos szoftverek az adatelemző virtuális gép elérhető.  


## <a name="configuration-details"></a>Konfiguráció részletei

A Python-kódtár [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), amely szolgál ArcGIS-felület telepítve van a globális legfelső szintű conda környezetében az adatelemző virtuális gép, amely a következő címen található ```c:\anaconda```. 

- Ha a Python egy parancssorban futtatja, futtassa ```activate``` conda legfelső szintű Python környezetbe történő aktiválásához. 
- Ha egy IDE- vagy Jupyter notebookot használja, a környezet vagy a kernel ellenőrizze, hogy a megfelelő conda-környezetben is kiválaszthatja. 

Az R-híd ArcGIS telepítve van, az R-tár nevű [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) a fő Microsoft R server különálló példány található a ```C:\Program Files\Microsoft\ML Server\R_SERVER```. A Visual Studio, az RStudio és Jupyter már előre konfigurálva a R-környezetben, és hozzáférést kap a ```arcgisbinding``` R könyvtár. 


## <a name="geo-ai-data-science-vm-samples"></a>A földrajzi mesterséges Intelligencia adatelemző virtuális gép minták

A gépi Tanulási és deep learning-keretrendszer-alapú-mintákat az az alapvető adatelemző virtuális gép mellett a földrajzi mintákat is tartalmaz a földrajzi mesterséges Intelligencia adatelemző virtuális gép részeként. Ezek a minták segítségével gyorsan elindíthatja a térinformatikai adatok és az ArcGIS-szoftverek használatával AI-alkalmazások fejlesztését. 


1. [A térinformatikai elemzések és a Python használatával való Ismerkedés](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Bevezető minta, amely bemutatja, hogyan dolgozhat térinformatikai adataival a Python felületen keresztül a [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -könyvtár által biztosított ArcGIS. Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulás a térinformatikai adatok és ArcGIS egy térképen az eredmény megjelenítése. 

2. A [térinformatikai elemzések az R-vel való](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)beszerzése: Bevezető minta, amely bemutatja, hogyan dolgozhat a térinformatikai adataival az R felületen a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -könyvtár által biztosított ArcGIS. 

3. [Képpont szintű földhasználat besorolása](https://github.com/Azure/pixel_level_land_classification): Egy oktatóanyag, amely bemutatja, hogyan hozhat létre egy olyan mély neurális hálózati modellt, amely egy adott antenna-rendszerképet fogad el bemenetként, és visszaadja a földre kiterjedő címkét. Példák a föld-cover címkék "erdős" vagy "víz." A modell minden képpont ilyen egy címkét a rendszerkép adja vissza. A modell a Microsoft nyílt forráskódú használatával lett összeállítva [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) deep learning keretrendszer. 


## <a name="next-steps"></a>További lépések

Itt érhetők el, amelyek használják az adatelemző virtuális gép további példákat:

* [Példák](dsvm-samples-and-walkthroughs.md)


---
title: Az a földrajzi mesterséges intelligenciát használó adatelemző virtuális gép – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a földrajzi mesterséges Intelligencia adatelemző virtuális gép-adatok elemzésére és a földrajzi adatok alapján modelleket.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: 872bfdfdef28dbb88861b723bd3186faea461cad
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410006"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A földrajzi mesterséges intelligenciát használó adatelemző virtuális gép használata

A földrajzi mesterséges Intelligencia adatelemző virtuális gép használatával adatlehívást elemzéshez, hajtsa végre az adatok konvertálását és modelleket az AI-alkalmazások, amelyek a földrajzi információk felhasználása. Miután a földrajzi mesterséges Intelligencia adatelemző virtuális gép kiépítése és ArcGIS Pro bejelentkezett az ArcGIS-fiókjával, megkezdése ArcGIS asztali és az ArcGis online implementálására. Az ArcGIS a Python-felületek és a egy R nyelv híd előre konfigurálva a Geo-adatelemzési virtuális gép keresztül is elérhető. Gazdag AI-alkalmazások létrehozásához, kombinálva, a gép tanulási és deep learning-keretrendszerek és egyéb adatok tudományos szoftverek az adatelemző virtuális gép elérhető.  


## <a name="configuration-details"></a>Konfiguráció részletei

A Python-kódtár [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), amely szolgál ArcGIS-felület telepítve van a globális legfelső szintű conda környezetében az adatelemző virtuális gép, amely a következő címen található ```c:\anaconda```. 

- Ha a Python egy parancssorban futtatja, futtassa ```activate``` conda legfelső szintű Python környezetbe történő aktiválásához. 
- Ha egy IDE- vagy Jupyter notebookot használja, a környezet vagy a kernel ellenőrizze, hogy a megfelelő conda-környezetben is kiválaszthatja. 

Az R-híd ArcGIS telepítve van, az R-tár nevű [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) a fő Microsoft R server különálló példány található a ```C:\Program Files\Microsoft\ML Server\R_SERVER```. A Visual Studio, az RStudio és Jupyter már előre konfigurálva a R-környezetben, és hozzáférést kap a ```arcgisbinding``` R könyvtár. 


## <a name="geo-ai-data-science-vm-samples"></a>A földrajzi mesterséges Intelligencia adatelemző virtuális gép minták

A gépi Tanulási és deep learning-keretrendszer-alapú-mintákat az az alapvető adatelemző virtuális gép mellett a földrajzi mintákat is tartalmaz a földrajzi mesterséges Intelligencia adatelemző virtuális gép részeként. Ezek a minták segítségével gyorsan elindíthatja a térinformatikai adatok és az ArcGIS-szoftverek használatával AI-alkalmazások fejlesztését. 


1. [Első Python-térinformatikai elemzés paraméterkészlettel lesz kiadva](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): A Python felületén által biztosított ArcGIS térinformatikai adatok használatát bemutató egy bevezető minta a [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) könyvtár. Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulás a térinformatikai adatok és ArcGIS egy térképen az eredmény megjelenítése. 

2. [Első térinformatikai elemzés az r nyelv paraméterkészlettel lesz kiadva](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Egy bevezető minta bemutatja, hogyan dolgozhat a térinformatikai adatok által biztosított ArcGIS az R-felület használatával a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) könyvtár. 

3. [Képpontos szintű föld besorolás használata](https://github.com/Azure/pixel_level_land_classification): Ez az oktatóanyag bemutatja, hogyan hozhat létre egy olyan Neurális hálózat-modell, légi felvételeken fogad bemenetként, és adja vissza a föld-cover címke. Példák a föld-cover címkék "erdős" vagy "víz." A modell minden képpont ilyen egy címkét a rendszerkép adja vissza. A modell a Microsoft nyílt forráskódú használatával lett összeállítva [Cognitive Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) deep learning keretrendszer. 


## <a name="next-steps"></a>További lépések

Itt érhetők el, amelyek használják az adatelemző virtuális gép további példákat:

* [Példák](dsvm-samples-and-walkthroughs.md)


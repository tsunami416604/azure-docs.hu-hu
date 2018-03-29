---
title: A földrajzi mesterséges Eszközintelligencia adatok tudományos virtuálisgép - Azure használatával |} Microsoft Docs
description: Hogyan használható egy földrajzi AI virtuális gépet az Azure-on.
keywords: a mélyhivatkozással tanulási, AI adatok tudományos eszközök, az adatok tudományos virtuális gépet, a földrajzi elemzés
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: gokuma
ms.openlocfilehash: f7ee109c26f8b2f6107dfcb4853d5517384aef76
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A földrajzi mesterséges Eszközintelligencia adatok tudományos virtuális gépek használata

A földrajzi AI adatok tudományos VM segítségével adatlehívás elemzés, wrangling végezhet, és állítsa be modellek AI használó alkalmazások programozása földrajzi információk. Miután a földrajzi AI adatok tudományos VM kiosztása és ArcGIS Pro be van jelentkezve a ArcGIS fiókjához, elindíthatja a ArcGIS asztali és ArcGis online implementálására. A Python felületek és az R nyelv híd előre konfigurálva van a virtuális Gépre földrajzi-adatok tudományos ArcGIS is elérhető. Gazdag AI alkalmazásokat hozhatnak létre, összevonásához a gép tanulási és mély tanulási keretrendszerek és egyéb adatok tudományos szoftverek érhető el az adatok tudományos virtuális gépen.  


## <a name="configuration-details"></a>Konfiguráció részletei

Python kódtár [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), amely szolgál ArcGIS illesztőfelület telepítve van a globális legfelső szintű conda környezetben a következő címen található adatok tudományos virtuális gép ```c:\anaconda```. 

- Ha a Python egy parancssorban futtatja, futtassa ```activate``` conda legfelső szintű Python környezetbe aktiválásához. 
- Ha egy IDE- vagy Jupyter notebook használ, választhatja a környezet vagy a kernel, hogy biztosan a megfelelő conda környezetben. 

Az R-hídon ArcGIS van telepítve egy R könyvtár nevű [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) a fő Microsoft R server önálló példányát helyen lévő ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio Rstudióból és Jupyter már előre konfigurált, az R-környezet használata, és hozzáférhetnek a ```arcgisbinding``` R könyvtár. 


## <a name="geo-ai-data-science-vm-samples"></a>Földrajzi AI adatok tudományos VM minták

A gépi tanulás és minták-keretrendszeren alapuló adatok tudományos alap virtuális tanulási mély kívül a földrajzi minták a földrajzi AI adatok tudományos VM részeként is megadja. Ezek a minták segítséget a földrajzi adatok és a ArcGIS szoftver AI alkalmazások fejlesztésének jump-start. 


1. [Az a földrajzi analytics Python első közölt](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): egy bevezető minta a Python felületén által biztosított ArcGIS földrajzi adatok használata a [arcpy](http://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) könyvtárban. Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulási földrajzi adatokkal, és jelenítheti meg az eredményt a ArcGIS a térképen. 

2. [Az r földrajzi analytics első közölt](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): egy bevezető minta azt mutatja be a R felületén által biztosított ArcGIS földrajzi adatok a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) könyvtárban. 

3. [Képpont szintű föld használja besorolás](https://github.com/Azure/pixel_level_land_classification): egy oktatóanyag, amely bemutatja, mély Neurális hálózat modell, amely fogad el bemenetként légifelvételes kép, és visszaadja a föld-borítóján címkék létrehozása. Föld-borítóján címkék példák "erdős" vagy "vízjel." A modell felirataként, minden képpont a kép adja vissza. A modell-t a Microsoft nyílt forráskódú [kognitív Toolkit (CNTK)](https://www.microsoft.com/en-us/cognitive-toolkit/) mély oktatási keretrendszer. A példa is bemutatja, hogyan a képzési horizontális [Azure Batch AI](https://docs.microsoft.com/azure/batch-ai/) vagy a modell előrejelzéseket ArcGIS Pro szoftver használatára. 


## <a name="next-steps"></a>További lépések

Az adatok tudományos VM használó további minták itt érhetők el:

* [Minták](dsvm-samples-and-walkthroughs.md)


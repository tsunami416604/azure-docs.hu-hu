---
title: A Geo AI használata
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan elemezheti a Geo AI Data Science Virtual Machine az adatelemzéshez és a modellek térinformatikai információ alapján történő létrehozásához.
keywords: Deep learning, AI, adatelemzési eszközök, adatelemzési virtuális gép, térinformatikai elemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "70278416"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A Geo mesterséges intelligencia Data Science Virtual Machine használata

A Geo AI Data Science VM segítségével adatokat olvashat be az elemzéshez, adatokat huzavona el, és modelleket építhet ki a térinformatikai adatokat használó AI-alkalmazásokhoz. Miután kiépítte a Geo AI-Data Science VM, és bejelentkezett a ArcGIS Pro-ba a ArcGIS-fiókkal, elindíthatja a ArcGIS Desktop és a ArcGIs online interakcióját. A ArcGIS a Python-felületekről és egy, a Geo-Data Science VM előre konfigurált R nyelvi hídra is elérheti. Gazdag AI-alkalmazások létrehozásához egyesítse a Geo-Data Science VMt a gépi tanulással és a mélyreható tanulási keretrendszerekkel és a rajta elérhető egyéb adatelemzési szoftverekkel.  


## <a name="configuration-details"></a>Konfiguráció részletei

A ArcGIS-vel való kapcsolathoz használt Python-könyvtár, a [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), amely a (z) ```c:\anaconda```rendszerhez tartozó Data Science VM globális legfelső szintű Conda-környezetében található.

- Ha a Pythont a parancssorból futtatja, akkor ```activate``` a Conda root Python-környezetbe való aktiváláshoz futtassa a parancsot.
- IDE-vagy Jupyter-jegyzetfüzet használata esetén kiválaszthatja a környezetet vagy a rendszermagot, és meggyőződhet arról, hogy a megfelelő Conda-környezetben van-e.

A ArcGIS R-híd a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) nevű r-könyvtárként van telepítve a fő Microsoft Machine learning Server önálló példányában, amely a ```C:\Program Files\Microsoft\ML Server\R_SERVER```következő helyen található:. A Visual Studio, a RStudio és a Jupyter már előre konfigurálva van az R-környezet használatához, és hozzáfér az ```arcgisbinding``` r-könyvtárhoz. 


## <a name="geo-ai-data-science-vm-samples"></a>Geo AI Data Science VM minták

Az alapszintű Data Science VM gépi tanulási és mélyreható mintákon alapuló mintáján kívül térinformatikai mintákat is biztosítunk a Geo AI-Data Science VM részeként. Ezek a minták segítséget nyújtanak a mesterséges intelligenciát használó alkalmazások fejlesztésének megkezdéséhez térinformatikai és ArcGIS szoftver használatával:


1. [Ismerkedés a térinformatikai elemzéssel a Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)használatával: egy bevezető minta, amely bemutatja, hogyan dolgozhat térinformatikai információkkal a Python felületen keresztül, hogy a ArcGIS a [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -könyvtár biztosít. Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulást a térinformatikai adatkezeléssel, majd megjelenítheti az eredményeket a ArcGIS egy térképén.

2. [Első lépések a térinformatikai elemzések és az r](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb)használatával: bevezető minta, amely bemutatja, hogyan dolgozhat térinformatikai adataival az r felülettel a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -könyvtár által biztosított ArcGIS. 

3. [Képpont szintű földhasználat besorolása](https://github.com/Azure/pixel_level_land_classification): egy oktatóanyag, amely bemutatja, hogyan hozható létre egy olyan mély neurális hálózati modell, amely egy adott antenna-rendszerképet fogad el bemenetként, és visszaadja a földre kiterjedő címkét. Ilyenek például az *erdő* és a *víz*. A modell egy ilyen címkét ad vissza a képen található összes képponthoz. 


## <a name="next-steps"></a>További lépések

A Data Science VMt használó további minták itt érhetők el:

* [Példák](dsvm-samples-and-walkthroughs.md)
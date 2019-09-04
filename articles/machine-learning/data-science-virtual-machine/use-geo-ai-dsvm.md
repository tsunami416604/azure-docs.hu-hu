---
title: A Geo AI használata
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg, hogyan használhatja a földrajzi mesterséges Intelligencia adatelemző virtuális gép-adatok elemzésére és a földrajzi adatok alapján modelleket.
keywords: deep learning, AI, beépített adatelemzési eszközzel, az adatelemzési virtuális gépet, a térinformatikai elemzés
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278416"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>A földrajzi mesterséges intelligenciát használó adatelemző virtuális gép használata

A földrajzi mesterséges Intelligencia adatelemző virtuális gép használatával adatlehívást elemzéshez, hajtsa végre az adatok konvertálását és modelleket az AI-alkalmazások, amelyek a földrajzi információk felhasználása. Miután kiépítte a Geo AI-Data Science VM, és bejelentkezett a ArcGIS Pro-ba a ArcGIS-fiókkal, elindíthatja a ArcGIS Desktop és a ArcGIs online interakcióját. A ArcGIS a Python-felületekről és egy, a Geo-Data Science VM előre konfigurált R nyelvi hídra is elérheti. Gazdag AI-alkalmazások létrehozásához egyesítse a Geo-Data Science VMt a gépi tanulással és a mélyreható tanulási keretrendszerekkel és a rajta elérhető egyéb adatelemzési szoftverekkel.  


## <a name="configuration-details"></a>Konfiguráció részletei

A ArcGIS-vel való kapcsolathoz használt Python-könyvtár, a [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), amely a (z) ```c:\anaconda```rendszerhez tartozó Data Science VM globális legfelső szintű Conda-környezetében található.

- Ha a Pythont a parancssorból futtatja, akkor ```activate``` a Conda root Python-környezetbe való aktiváláshoz futtassa a parancsot.
- IDE-vagy Jupyter-jegyzetfüzet használata esetén kiválaszthatja a környezetet vagy a rendszermagot, és meggyőződhet arról, hogy a megfelelő Conda-környezetben van-e.

A ArcGIS R-híd a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) nevű r-könyvtárként van telepítve a fő Microsoft Machine learning Server önálló példányában, amely a ```C:\Program Files\Microsoft\ML Server\R_SERVER```következő helyen található:. A Visual Studio, a RStudio és a Jupyter már előre konfigurálva van az r-környezet használatához, és hozzáfér az ```arcgisbinding``` r-könyvtárhoz. 


## <a name="geo-ai-data-science-vm-samples"></a>A földrajzi mesterséges Intelligencia adatelemző virtuális gép minták

Az alapszintű Data Science VM gépi tanulási és mélyreható mintákon alapuló mintáján kívül térinformatikai mintákat is biztosítunk a Geo AI-Data Science VM részeként. Ezek a minták segítséget nyújtanak a mesterséges intelligenciát használó alkalmazások fejlesztésének megkezdéséhez térinformatikai és ArcGIS szoftver használatával:


1. Bevezetés [a térinformatikai elemzés használatába a Python használatával](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Bevezető minta, amely bemutatja, hogyan használhatók térinformatikai információk a Python felületen keresztül a ArcGIS-re a [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) -könyvtár által. Azt is bemutatja, hogyan kombinálhatja a hagyományos gépi tanulást a térinformatikai adatkezeléssel, majd megjelenítheti az eredményeket a ArcGIS egy térképén.

2. [Ismerkedés a térinformatikai elemzéssel az R használatával](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Bevezető minta, amely bemutatja, hogyan dolgozhat a térinformatikai adataival az R felülettel a [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) -könyvtár által biztosított ArcGIS. 

3. [Képpont szintű földhasználat besorolása](https://github.com/Azure/pixel_level_land_classification): Egy oktatóanyag, amely bemutatja, hogyan hozhat létre egy olyan mély neurális hálózati modellt, amely egy adott antenna-rendszerképet fogad el bemenetként, és visszaadja a földre kiterjedő címkét. Ilyenek például az *erdő* és a *víz*. A modell minden képpont ilyen egy címkét a rendszerkép adja vissza. 


## <a name="next-steps"></a>További lépések

Itt érhetők el, amelyek használják az adatelemző virtuális gép további példákat:

* [Példák](dsvm-samples-and-walkthroughs.md)
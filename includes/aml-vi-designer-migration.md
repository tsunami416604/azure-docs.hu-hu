---
title: fájl belefoglalása
description: fájl belefoglalása
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541802"
---
1. Jelentkezzen be az [Azure Machine Learning stúdióba.](https://ml.azure.com)

1. Frissítse munkaterületét Enterprise edition kiadásra.

    A frissítés után az összes vizuális felületi kísérlet a tervezőben folyamatpiszkozattá alakul át.
    
    > [!NOTE]
    > Nem kell frissítenie az Enterprise kiadásra a vizuális felületi webszolgáltatások valós idejű végpontokká alakításához.
    
1. A folyamatpiszkozatok listájának megtekintéséhez nyissa meg a munkaterület tervezői szakaszát. 
    
    A konvertált webszolgáltatások a **Végpontok** > **valós idejű végpontjaira**való navigálással találhatók meg.

1. Válasszon ki egy folyamatpiszkozatot a megnyitásához.

    Ha hiba történt az átalakítási folyamat során, hibaüzenet jelenik meg a probléma megoldásához szükséges utasításokkal. 

### <a name="known-issues"></a>Ismert problémák

 Az alábbiakban felsoroljuk azokat az ismert áttelepítési problémákat, amelyeket manuálisan kell kezelni:

- **Adatok importálása** vagy **adatmodulok exportálása**
        
    Ha a kísérletben **van egy Adatok importálása** vagy **Adatok exportálása** modulja, az adatforrást frissítenie kell az adattárak használatához. Az adattár létrehozásáról az [Adatok elérése az Azure storage-szolgáltatásokban](../articles/machine-learning/how-to-access-data.md)című témakörben olvashat. A felhőbeli tárfiók adatai az **Adatok importálása** vagy az **Adatok exportálása** modul megjegyzéseiben vannak hozzáadva az Ön kényelme érdekében. 
      
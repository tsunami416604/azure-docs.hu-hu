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
ms.openlocfilehash: 08833f869a7838fe893b8e941072078f89033c2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517851"
---
1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Frissítse munkaterületét az Enterprise Edition verzióra.

    A frissítés után az összes Visual Interface-kísérlet át lesz konvertálva a tervezőbe.
    
    > [!NOTE]
    > Nem kell frissítenie a vállalati kiadásra a Visual Interface webszolgáltatások valós idejű végpontokra való átalakításához.
    
1. A folyamat Piszkozatok listájának megtekintéséhez nyissa meg a munkaterület tervező szakaszát. 
    
    A konvertált webszolgáltatások megtalálhatók a **végpontok** > **valós idejű végpontok**használatával.

1. Válassza ki a folyamat piszkozatát a megnyitásához.

    Ha hiba történt az átalakítási folyamat során, hibaüzenet jelenik meg, amely a probléma megoldására vonatkozó utasításokat tartalmazza. 

### <a name="known-issues"></a>Ismert problémák

 Az alábbi ismert áttelepítési problémák kézzel kell foglalkozni:

- **Adatimportálás** vagy adatmodulok **exportálása**
        
    Ha **adatimportálási** vagy-adatmodult használ a kísérletben, frissítenie kell az adatforrást az **adattárolók** használatára. Az adattár létrehozásával kapcsolatos információkért lásd: az [Azure Storage Services-beli adatelérés](../articles/machine-learning/service/how-to-access-data.md). A Felhőbeli Storage-fiókadatok az adatok **importálása** vagy az **adatok exportálása** modulhoz fűzött megjegyzésekben lettek hozzáadva. 
      
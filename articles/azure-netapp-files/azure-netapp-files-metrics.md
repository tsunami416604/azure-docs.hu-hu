---
title: A Azure NetApp Files metrikái | Microsoft Docs
description: A Azure NetApp Files metrikáinak leírása.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: b-juche
ms.openlocfilehash: 9a56fb27fdf9e196291942041d68b249d7f16648
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839233"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

Azure NetApp Files metrikákat biztosít a lefoglalt tárolóban, a tényleges tárterület-használatot, a kötet átviteli sebességét, a IOPS és a késést. A metrikák elemzésével jobban megismerheti a NetApp-fiókok használati mintáját és a kötet teljesítményét.  

## <a name="capacity_pools"></a>A kapacitási készletek használati metrikái

<!-- 
- *Volume pool allocated size*  
    This is the size (GiB) of the provisioned capacity pool.  
--> 
- *Lefoglalt kötet-készlet*  
    Ez az adott kapacitási készletben lévő mennyiségi kvóta (GiB) teljes mennyisége (azaz a kötetek kiosztott méretei összesen). A kötet létrehozása során kiválasztott méret.  
- *Kötet készletének teljes logikai mérete*  
    A kapacitási készlet kötetei között használt logikai terület összesen (GiB).  
<!-- 
- *Volume pool total snapshot size*  
    This is the total of incremental logical space used by the snapshots.  
-->

## <a name="volumes"></a>A kötetek használati metrikái

<!-- 
- *Volume allocated size*   
    This is the volume size (quota) provisioned in GiB.  
--> 
- *Kötet logikai mérete*   
    Egy köteten (GiB) használt teljes logikai terület. Ez a méret magában foglalja az aktív fájlrendszerek és Pillanatképek által használt logikai teret.  
- *Kötet pillanatképének mérete*   
    Ez egy köteten található Pillanatképek által használt növekményes logikai terület.  

## <a name="next-steps"></a>További lépések

* [A Azure NetApp Files tárolási hierarchiájának megismerése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)

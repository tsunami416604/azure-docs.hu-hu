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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 7cf382f511d2ba8452d77bf207f36b749cb31e94
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848795"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

Azure NetApp Files metrikákat biztosít a lefoglalt tárolóban, a tényleges tárterület-használatot, a kötet átviteli sebességét, a IOPS és a késést. A metrikák elemzésével jobban megismerheti a NetApp-fiókok használati mintáját és a kötet teljesítményét.  

## <a name="capacity_pools"></a>A kapacitási készletek használati metrikái

<!-- 
- *Volume pool allocated size*  
    The size (GiB) of the provisioned capacity pool  
--> 
- *Lefoglalt kötet-készlet*  
    Az adott kapacitási készletben lévő mennyiségi kvóta (GiB) összege (azaz a kötetek kiosztott méretei összesen)  
    A kötet létrehozása során kiválasztott méret.  
- *Kötet készletének teljes logikai mérete*  
    A kapacitási készlet kötetei között használt logikai terület teljes mérete (GiB)  
<!-- 
- *Volume pool total snapshot size*  
    The total of incremental logical space used by the snapshots  
-->

## <a name="volumes"></a>A kötetek használati metrikái

<!-- 
- *Volume allocated size*   
    The volume size (quota) provisioned in GiB  
--> 
- *Kötet logikai mérete*   
    A kötetben használt teljes logikai terület (GiB)  
    Ez a méret magában foglalja az aktív fájlrendszerek és Pillanatképek által használt logikai teret.  
- *Kötet pillanatképének mérete*   
   A pillanatképek által a köteten használt növekményes logikai terület  

## <a name="performance-metrics-for-volumes"></a>A kötetek teljesítmény-mérőszámai

- *AverageReadLatency*   
    A kötet olvasási idejének átlagos ideje ezredmásodpercben
- *AverageWriteLatency*   
    A kötet írási idejének átlagos ideje ezredmásodpercben
- *ReadIops*   
    A kötetre irányuló olvasási műveletek száma másodpercenként
- *WriteIops*   
    A kötetbe való írások másodpercenkénti száma

## <a name="next-steps"></a>További lépések

* [A Azure NetApp Files tárolási hierarchiájának megismerése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)

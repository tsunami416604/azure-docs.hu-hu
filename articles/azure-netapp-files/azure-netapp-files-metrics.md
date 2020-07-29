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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460432"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

A Azure NetApp Files a lefoglalt tároló, a tényleges tárterület-használat, a mennyiségi IOPS és a késés mérőszámait biztosítja. A metrikák elemzésével jobban megismerheti a NetApp-fiókok használati mintáját és a kötet teljesítményét.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>A kapacitási készletek használati metrikái

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *A kötet méretéhez lefoglalt készlet*  
    Az adott kapacitási készletben lévő mennyiségi kvóta (GiB) teljes mennyisége (azaz a kötetek kiosztott méretei összesen).  
    Ez a méret a kötetek létrehozásakor kiválasztott méret.  
- *Készlet felhasznált mérete*  
    A kapacitási készlet kötetei között felhasznált logikai terület összesen (GiB).  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>A kötetek használati metrikái

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Kötet felhasznált mérete*   
    A kötetben használt teljes logikai terület (GiB).  
    Ez a méret magában foglalja az aktív fájlrendszerek és Pillanatképek által használt logikai teret.  
- *Kötet pillanatképének mérete*   
   A pillanatképek által használt növekményes logikai terület egy köteten.  

## <a name="performance-metrics-for-volumes"></a>A kötetek teljesítmény-mérőszámai

- *AverageReadLatency*   
    A kötet olvasási idejének átlagos ideje ezredmásodpercben.
- *AverageWriteLatency*   
    A köteten történő írások átlagos ideje ezredmásodpercben.
- *ReadIops*   
    A kötetre irányuló olvasási műveletek száma másodpercenként.
- *WriteIops*   
    A kötetre írások száma másodpercenként.

## <a name="next-steps"></a>További lépések

* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)

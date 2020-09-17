---
title: A Azure NetApp Files metrikái | Microsoft Docs
description: A Azure NetApp Files a lefoglalt tároló, a tényleges tárterület-használat, a mennyiségi IOPS és a késés mérőszámait biztosítja. Használja ezeket a mérőszámokat a használat és a teljesítmény megismeréséhez.
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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707781"
---
# <a name="metrics-for-azure-netapp-files"></a>Az Azure NetApp Files metrikái

A Azure NetApp Files a lefoglalt tároló, a tényleges tárterület-használat, a mennyiségi IOPS és a késés mérőszámait biztosítja. A metrikák elemzésével jobban megismerheti a NetApp-fiókok használati mintáját és a kötet teljesítményét.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>A kapacitási készletek használati metrikái

- *Készlet lefoglalt mérete*   
    A készlet kiépített mérete.

- *A kötet méretéhez lefoglalt készlet*  
    Az adott kapacitási készletben lévő mennyiségi kvóta (GiB) teljes mennyisége (azaz a kötetek kiosztott méretei összesen).  
    Ez a méret a kötetek létrehozásakor kiválasztott méret.  

- *Készlet felhasznált mérete*  
    A kapacitási készlet kötetei között felhasznált logikai terület összesen (GiB).  

- *A készlet összes pillanatképének mérete*    
    A készletben lévő összes kötet pillanatkép-méretének összege.

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

- *Olvasási késleltetés átlagos késése*   
    A kötet olvasási idejének átlagos ideje ezredmásodpercben.
- *Írási késleltetés átlagos késése*   
    A köteten történő írások átlagos ideje ezredmásodpercben.
- *IOPS olvasása*   
    A kötetre irányuló olvasási műveletek száma másodpercenként.
- *IOPS írása*   
    A kötetre írások száma másodpercenként.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Kötet replikációs metrikái

- *A kötet replikálási állapota Kifogástalan*   
    A replikációs kapcsolat feltétele. 

- *A kötet-replikálás átadása*    
    Azt jelzi, hogy a kötet replikálásának állapota "átvitel". 
 
- *Kötet replikációs késési ideje*   
    Az az időtartam másodpercben, ameddig a tükrözött adatmennyiség a forrás mögött marad. 

- *Kötet replikálásának utolsó átvitelének időtartama*   
    Az utolsó átvitel befejezéséhez szükséges idő másodpercben. 

- *Kötet replikálásának utolsó átvitelének mérete*    
    Az utolsó átvitel részeként átvitt bájtok teljes száma. 

- *Kötet replikálásának folyamata*    
    Az aktuális adatátviteli művelethez továbbított adatok teljes mennyisége. 

- *Kötet replikálásának teljes átvitele*   
    A kapcsolathoz továbbított összesített bájtok száma. 

## <a name="next-steps"></a>További lépések

* [Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes.md)

---
title: Metrikák, a Azure NetApp-fájlok |} A Microsoft Docs
description: Metrikák Azure NetApp fájlokat ismerteti.
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
ms.topic: concepts
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 866aa808f4706fa3bce72495dc56f438d567ecb2
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430793"
---
# <a name="metrics-for-azure-netapp-files"></a>Metrikák, a Azure NetApp-fájlok

NetApp Azure Files mérőszámait lefoglalt tároló, tényleges tárterület-használat, kötet átviteli sebesség, IOPS és a késés. Ezek a metrikák elemzésével szerezhet a NetApp fiókok használati mintáját és mennyiségi teljesítményét jobb megértése.  

## <a name="capacity_pools"></a>Kapacitás készletek használati metrikái

- *Kötet lefoglalt készlet mérete*  
    Ez az a kiosztott kapacitást készlet mérete (GiB).  
- *Lefoglalt kötet készlet használt*  
    Ez az a teljes kötet kvóta (GB) a megadott kapacitás készletben (azt jelenti, a teljes kiosztott méretek a kötet a kapacitás a készletben található). Ez a kötet létrehozása során kiválasztott méretét.  
- *Kötet készlet teljes logikai mérete*  
    Ez az a teljes kapacitás készletben található kötetek között használt logikai terület (GB).  
- *Kötet készlet teljes pillanatkép mérete*  
    Ez az az összes növekményes, a pillanatképek által használt logikai területet.  

## <a name="volumes"></a>Kötetek használati metrikái

- *Kötet lefoglalt méret*   
    Ez az a kötet méretétől (kvóta) GiB-ban.  
- *A kötet logikai mérete*   
    Ez az a teljes logikai terület egy köteten (GB) használt. Ez a méret aktív fájlrendszerek és pillanatképek által használt logikai terület tartalmazza.  
- *Kötet-pillanatképek mérete*   
    Ez az a növekményes, a pillanatképek a kötet által használt logikai területet.  

## <a name="next-steps"></a>További lépések

* [Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
* [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
* [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)

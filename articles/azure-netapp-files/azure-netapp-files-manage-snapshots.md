---
title: Pillanatképek kezelése Azure NetApp fájlokkal |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy igény szerinti pillanatképének létrehozása egy kötet vagy a visszaállítás pillanatképből egy új kötetre Azure NetApp fájlok használatával.
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
ms.topic: how-to-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 48cb88b9815ba723d93c18caf63f33b50eea850c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009195"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure Files-NetApp használatával
Az Azure Files-NetApp használatával hozzon létre egy igény szerinti pillanatképet egy kötet vagy egy pillanatképet egy új kötetre visszaállítása.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Egy igény szerinti pillanatkép egy kötet létrehozása
A pillanatképek csak igény szerint hozhat létre.  Pillanatkép-szabályzatok jelenleg nem támogatottak.  
1.  A kötet kezelése panelen kattintson az **pillanatképek**, majd kattintson a **+ Hozzáadás pillanatkép** kötet egy igény szerinti pillanatképének létrehozása.

2.  Új pillanatkép ablakában adja meg a létrehozandó új pillanatkép nevét.   

3. Kattintson az **OK** gombra. 


## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása egy új kötetre
Visszaállíthatja a pillanatkép jelenleg csak az új kötetet. 
1. Nyissa meg a **pillanatképek kezelése** panelen a kötet panelről pillanatkép listájának megjelenítéséhez. 
2. Válassza ki a pillanatkép visszaállítása.  
3. Kattintson a jobb gombbal a pillanatkép neve, és válassza ki **új kötetre visszaállítása** a menüpont az.  

    ![Új kötetre pillanatkép visszaállítása](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Az új ablakában adja meg az új kötet adatait:  
    * **név**   
        Adja meg a kötet, amelyeknek a neve.  
        
        A név egy erőforráscsoporton belül egyedinek kell lennie. Legalább 3 karakter hosszúságúnak kell lennie.  Ez lehet bármely alfanumerikus karaktereket használjon.

    * **Fájl elérési útja**     
        Adja meg a fájl elérési útját az exportálási útvonal, az új kötet létrehozásához használt. Az exportálási útvonal segítségével csatlakoztatása és elérni a kötetet.   
        
        A csatlakoztatási célt az a végpont NFS szolgáltatás IP-cím. Automatikusan hozza létre.   
        
        A fájl elérési útja neve betűket, számokat és kötőjeleket tartalmazhat ("-") csak. 16 – 40 karakter hosszúságúnak kell lennie. 

    * **Kvóta**  
        Adja meg a kötet számára lefoglalt logikaitár.  

        A **rendelkezésre álló kvótát** mező a nem használt terület mennyiségét mutatja a választott kapacitás a készlet, amelyet használhat fel az új kötet létrehozása. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.

    *   **Virtuális hálózat**  
        Adja meg az Azure virtuális hálózat (Vnet), amely alapján szeretne elérni a kötetet. 
        
        A megadott virtuális hálózathoz rendelkeznie kell konfigurált Azure NetApp fájlok. Az Azure Files-NetApp szolgáltatás csak a kötet megegyező helyen lévő virtuális hálózaton elérhetők.  

    ![Új kötetre visszaállítása](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
    
5. Kattintson az **OK** gombra.   
    Az új kötetet, amelyhez a pillanatkép visszaállítása a kötetek panel jelenik meg.


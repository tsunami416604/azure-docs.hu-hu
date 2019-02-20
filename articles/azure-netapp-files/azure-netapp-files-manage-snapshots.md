---
title: Pillanatképek kezelése Azure NetApp fájlokkal |} A Microsoft Docs
description: Ismerteti, hogyan lehet, hogy pillanatképeket hoz létre egy kötet vagy a visszaállítás pillanatképből egy új kötetre Azure NetApp fájlokkal.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 3c69cb076b3b23cd5149e05f1b6ee9ae1ba170a6
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430199"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure Files-NetApp használatával

Az Azure Files-NetApp használatával hozzon létre egy igény szerinti pillanatképet egy kötet vagy egy pillanatképet egy új kötetre visszaállítása.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Egy igény szerinti pillanatkép egy kötet létrehozása

A pillanatképek csak igény szerint hozhat létre. Pillanatkép-szabályzatok jelenleg nem támogatottak.

1.  A kötet panelen kattintson az **pillanatképek**.

    ![Keresse meg a pillanatképek](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kattintson a **+ Hozzáadás pillanatkép** kötet egy igény szerinti pillanatképének létrehozása.

    ![Pillanatkép hozzáadása](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Új pillanatkép ablakában adja meg a létrehozandó új pillanatkép nevét.   

    ![Új pillanatkép](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kattintson az **OK** gombra. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása egy új kötetre

Visszaállíthatja a pillanatkép jelenleg csak az új kötetet. 
1. Nyissa meg a **pillanatképek kezelése** panelen a kötet panelről pillanatkép listájának megjelenítéséhez. 
2. Válassza ki a pillanatkép visszaállítása.  
3. Kattintson a jobb gombbal a pillanatkép neve, és válassza ki **új kötetre visszaállítása** a menüpont az.  

    ![Új kötetre pillanatkép visszaállítása](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Az új ablakában adja meg az új kötet adatait:  
    * **Név**   
        Adja meg a létrehozni kívánt kötet nevét.  
        
        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúságúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Fájl elérési útja**     
        Adja meg az új kötet exportálási útvonalának létrehozásához használni kívánt fájlelérési utat. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.   
        
        A csatlakoztatási cél az NFS-szolgáltatás IP-címének végpontja. Ennek létrehozása automatikusan történik.   
        
        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.

    *   **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  
        A megadott virtuális hálózathoz rendelkeznie kell egy alhálózatot az Azure Files-NetApp delegált. Az Azure Files-NetApp is elérheti, csak az azonos virtuális hálózaton vagy a virtuális hálózaton lévő virtuális hálózatok közötti társviszony-létesítésen keresztül a kötet és ugyanabban a régióban. A kötet a helyszíni hálózatból Express Route keresztül érheti el. 

    * **Alhálózat**  
        Adja meg az alhálózatot, amelyet a kötet használni kívánt.  
        Az alhálózatot, adja meg az Azure NetApp Files szolgáltatásba kell delegálható. Létrehozhat egy új alhálózatot kiválasztásával **új létrehozása** alhálózat mező alatti.  
<!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
-->

5. Kattintson az **OK** gombra.   
    Az új kötetet, amelyhez a pillanatkép visszaállítása a kötetek panel jelenik meg.

## <a name="next-steps"></a>További lépések

[Megismerheti a NetApp Azure-fájlok hierarchiája](azure-netapp-files-understand-storage-hierarchy.md)
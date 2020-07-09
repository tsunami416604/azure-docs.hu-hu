---
title: Pillanatképek kezelése Azure NetApp Files használatával | Microsoft Docs
description: Ismerteti, hogyan lehet pillanatképeket létrehozni egy kötethez, vagy egy pillanatképből egy új kötetre visszaállítani a Azure NetApp Files használatával.
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
ms.topic: how-to
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: ed13c61646bd2a6672b613964507d291a69a6821
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483601"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure NetApp Filesszal

A Azure NetApp Files használatával manuálisan hozhat létre egy kötetre vonatkozó igény szerinti pillanatképet, vagy visszaállíthat egy pillanatképből egy új kötetre. A Azure NetApp Files szolgáltatás nem hoz létre automatikusan kötet-pillanatképeket.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Igény szerinti pillanatkép létrehozása kötethez

Pillanatképeket csak igény szerint hozhat létre. A pillanatkép-szabályzatok jelenleg nem támogatottak.

1.  A kötet panelen kattintson a **Pillanatképek**elemre.

    ![Pillanatképek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kattintson a **+ Pillanatkép hozzáadása** lehetőségre egy kötethez igény szerinti pillanatkép létrehozásához.

    ![Pillanatkép hozzáadása](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Az új pillanatkép ablakban adja meg a létrehozandó új pillanatkép nevét.   

    ![Új pillanatkép](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kattintson az **OK** gombra. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása új kötetre

Jelenleg csak egy új kötetre állíthatja vissza a pillanatképet. 
1. A pillanatképek listájának megjelenítéséhez nyissa meg a kötetek panelen a **Pillanatképek kezelése** panelt. 
2. Válassza ki a visszaállítani kívánt pillanatképet.  
3. Kattintson a jobb gombbal a pillanatkép nevére, és válassza a **Visszaállítás az új kötetre** lehetőséget a menüpontban.  

    ![Pillanatkép visszaállítása új kötetre](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Az új kötet ablakban adja meg az új kötet adatait:  
    * **név**   
        Adja meg a létrehozni kívánt kötet nevét.  
        
        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Fájl elérési útja**     
        Adja meg az új kötet exportálási útvonalának létrehozásához használni kívánt fájlelérési utat. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.   
        
        A csatlakoztatási cél az NFS-szolgáltatás IP-címének végpontja. Ennek létrehozása automatikusan történik.   
        
        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.

    *   **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  
        A megadott vnet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. Azure NetApp Files csak ugyanarról a vnet vagy egy olyan vnet férhet hozzá, amely ugyanabban a régióban található, mint a vnet-társításon keresztül. A kötetet a helyszíni hálózatról Express Route használatával érheti el. 

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell a Azure NetApp Files szolgáltatásnak. Létrehozhat egy új alhálózatot az alhálózat **létrehozása** lehetőség kiválasztásával.  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Kattintson az **OK** gombra.   
    Az új kötet, amelybe a pillanatkép vissza lett állítva, megjelenik a kötetek panelen.

## <a name="next-steps"></a>További lépések

[Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)

---
title: Pillanatképek kezelése az Azure NetApp-fájlok használatával | Microsoft dokumentumok
description: Bemutatja, hogyan hozhat létre pillanatképeket egy kötethez, vagy hogyan állítható kontorát egy új kötetre az Azure NetApp Files használatával.
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
ms.date: 03/03/2020
ms.author: b-juche
ms.openlocfilehash: 48055a774808aea86452e8410b7e717f5019d172
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267899"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Pillanatképek kezelése az Azure NetApp-fájlok használatával

Az Azure NetApp-fájlok segítségével manuálisan hozhat létre egy kötet igény szerinti pillanatképét, vagy visszaállíthatja a pillanatképről egy új kötetre. Az Azure NetApp Files szolgáltatás nem hoz létre automatikusan kötetpillanatképeket.  

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Igény szerinti pillanatkép létrehozása kötethez

Csak igény szerint hozhat létre pillanatképeket. A pillanatkép-házirendek jelenleg nem támogatottak.

1.  A Kötet panelen kattintson a **Pillanatképek gombra.**

    ![Navigálás a pillanatképekre](../media/azure-netapp-files/azure-netapp-files-navigate-to-snapshots.png)

2.  Kattintson **a + Pillanatkép hozzáadása** elemre egy kötet igény szerinti pillanatképének létrehozásához.

    ![Pillanatkép hozzáadása](../media/azure-netapp-files/azure-netapp-files-add-snapshot.png)

3.  Az Új pillanatkép ablakban adja meg a létrehozandó új pillanatkép nevét.   

    ![Új pillanatkép](../media/azure-netapp-files/azure-netapp-files-new-snapshot.png)

4. Kattintson az **OK** gombra. 

## <a name="restore-a-snapshot-to-a-new-volume"></a>Pillanatkép visszaállítása új kötetre

Jelenleg csak egy új kötetre állíthatja vissza a pillanatképet. 
1. Lépjen a **Pillanatképek kezelése** panelre a Kötet panelről a pillanatképlista megjelenítéséhez. 
2. Válassza ki a visszaállítani kívánt pillanatképet.  
3. Kattintson a jobb gombbal a pillanatkép nevére, és válassza a **menüpont Visszaállítás új kötetére** parancsát.  

    ![Pillanatkép visszaállítása új kötetre](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Az Új kötet ablakban adja meg az új kötet adatait:  
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
        A megadott virtuális hálózatnak rendelkeznie kell az Azure NetApp-fájlok delegált alhálózatával. Az Azure NetApp-fájlok csak ugyanabból a virtuális hálózatból vagy egy virtuális hálózat, amely ugyanabban a régióban, mint a kötet a virtuális hálózat társviszony-létesítés. A kötetet a helyszíni hálózatról az Expressz útvonalon keresztül érheti el. 

    * **Alhálózat**  
        Adja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell az Azure NetApp Files szolgáltatásban. Új alhálózatot úgy hozhat létre, hogy az Alhálózat mezőben az Új létrehozása lehetőséget **választja.**  
   <!--
    ![Restored new volume](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
   -->

5. Kattintson az **OK** gombra.   
    Az új kötet, amelyre a pillanatkép vissza áll, megjelenik a Kötetek panelen.

## <a name="next-steps"></a>További lépések

[Az Azure NetApp Files tárhely-hierarchiájának ismertetése](azure-netapp-files-understand-storage-hierarchy.md)

---
title: NFS-kötet létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Ismerteti, hogyan lehet NFS-kötetet létrehozni a Azure NetApp Fileshoz.
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
ms.date: 7/9/2019
ms.author: b-juche
ms.openlocfilehash: 06733103980086fad0975514ae3489c3652e428a
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846745"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja az NFS-és SMBv3-köteteket. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ebből a cikkből megtudhatja, hogyan hozhat létre NFS-köteteket. Ha SMB-kötetet szeretne létrehozni, tekintse [meg az SMB-kötet létrehozása Azure NetApp Fileshoz](azure-netapp-files-create-volumes-smb.md)című témakört. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell Azure NetApp Files.  
[Alhálózat delegálása Azure NetApp Filesre](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>NFS-kötet létrehozása

1.  Kattintson a **kötetek** panelre a kapacitási készletek panelen. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a kötet létrehozása ablak.

3.  A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének egyedinek kell lennie az egyes kapacitási készleteken belül. Legalább három karakter hosszúnak kell lennie. Bármely alfanumerikus karaktert használhat.

    * **Kapacitási készlet**  
        Határozza meg azt a kapacitási készletet, amelyben létre szeretné hozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott vnet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a vnet érhető el, vagy egy olyan vnet, amely ugyanabban a régióban található, mint a kötet vnet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha nem delegált alhálózatot, a kötet létrehozása lapon kattintson az **új létrehozása** lehetőségre. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden vnet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson a **protokoll**elemre, majd válassza az **NFS** lehetőséget a kötethez tartozó protokoll típusaként.   
    * Itt adhatja meg a **fájl elérési útját** , amelyet az új kötet exportálási útvonalának létrehozásához fog használni. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.

        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

        A fájl elérési útjának egyedinek kell lennie az egyes előfizetésekben és az egyes régiókban. 

    * Szükség esetén [az NFS-kötet exportálási házirendjének konfigurálása](azure-netapp-files-configure-export-policy.md)

    ![NFS-protokoll meghatározása](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra az NFS-kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.


## <a name="next-steps"></a>További lépések  

* [Kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportálási szabályzat konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

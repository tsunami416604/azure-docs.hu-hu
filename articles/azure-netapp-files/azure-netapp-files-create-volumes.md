---
title: Kötet létrehozása az Azure NetApp Files számára | Microsoft Docs
description: A cikk azt ismerteti, hogyan hozható létre kötet az Azure NetApp Files számára.
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
ms.date: 12/17/2018
ms.author: b-juche
ms.openlocfilehash: 0d4629651e17c917720196c1e5f6152c50c2a33a
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57770266"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Kötet létrehozása az Azure NetApp Files számára

A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.  Egy kapacitáskészletben több kötetet is létrehozhat, de a kötetek teljes kapacitásfogyasztása nem haladhatja meg a készlet méretét. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitás készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Egy alhálózatot az Azure Files-NetApp delegálni.  
[Az Azure Files-NetApp alhálózat delegálása](azure-netapp-files-delegate-subnet.md)


## <a name="steps"></a>Lépések 
1.  Kattintson a **Kötetek** panelre a Kapacitáskészletek kezelése panelen. 
2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Ekkor megjelenik az Új kötet ablak.

3.  Az Új kötet ablakban kattintson a **Létrehozás** elemre, és töltse ki az alábbi mezőket:   
    * **Név**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúságúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Elérési út**  
        Adja meg az új kötet exportálási útvonalának létrehozásához használni kívánt fájlelérési utat. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.   
     
        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet.  

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott virtuális hálózathoz rendelkeznie kell egy alhálózatot az Azure Files-NetApp delegált. Az Azure Files-NetApp szolgáltatás csak az azonos virtuális hálózaton vagy a virtuális hálózaton lévő virtuális hálózatok közötti társviszony-létesítésen keresztül a kötet és ugyanabban a régióban is elérhető. A kötet a helyszíni hálózatból Express Route használatával is elérheti.   

    * **Alhálózat**  
        Adja meg az alhálózatot, amelyet a kötet használni kívánt.  
        Az alhálózatot, adja meg az Azure Files-NetApp delegálni. 
        
        Ha nem rendelkezik delegálta egy alhálózathoz, kattintson **új létrehozása** kötet létrehozása a lapon. Ezután az alhálózat létrehozásához lapon adja meg az alhálózati adatokat, és válassza ki **Microsoft.NetApp/volumes** és az alhálózat delegálja a NetApp Azure-fájlok. Vegye figyelembe, hogy az egyes virtuális hálózatok, csak egy alhálózatot az Azure Files-NetApp delegálható.   
 
        ![Új kötet](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)


4.  Kattintson az **OK** gombra. 
 
A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="next-steps"></a>További lépések  
* [Exportálási szabályzat konfigurálása kötethez (opcionális)](azure-netapp-files-configure-export-policy.md)
* [Virtuális hálózat integrációja Azure-szolgáltatások ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


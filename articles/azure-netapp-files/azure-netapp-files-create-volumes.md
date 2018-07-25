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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011092"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Kötet létrehozása az Azure NetApp Files számára

A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába.  Egy kapacitáskészletben több kötetet is létrehozhat, de a kötetek teljes kapacitásfogyasztása nem haladhatja meg a készlet méretét. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.  

[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Lépések 
1.  Kattintson a **Kötetek** panelre a Kapacitáskészletek kezelése panelen. 
2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Ekkor megjelenik az Új kötet ablak.

3.  Az Új kötet ablakban kattintson a **Létrehozás** elemre, és töltse ki az alábbi mezőket:   
    * **Név**      
        Adja meg a létrehozni kívánt kötet nevét.   
        A névnek egyedinek kell lennie az erőforráscsoporton belül. A névnek legalább 3 karakter hosszúságúnak kell lennie.  Bármilyen alfanumerikus karakter használható.

    * **Elérési út**  
        Adja meg az új kötet exportálási útvonalának létrehozásához használni kívánt fájlelérési utat. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.   
        A csatlakoztatási cél az NFS-szolgáltatás IP-címének végpontja. Ennek létrehozása automatikusan történik.    
        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet.  

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  
        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet. A megadott virtuális hálózatnak rendelkeznie kell konfigurált Azure NetApp Files szolgáltatással. Az Azure NetApp Files szolgáltatás csak a kötettel megegyező helyen található virtuális hálózatról érhető el.   

    ![Új kötet](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Kattintson az **OK** gombra. 
 
A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="next-steps"></a>További lépések  
[Exportálási szabályzat konfigurálása kötethez (opcionális)](azure-netapp-files-configure-export-policy.md)


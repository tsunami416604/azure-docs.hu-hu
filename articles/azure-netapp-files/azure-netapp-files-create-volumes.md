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
ms.topic: how-to-article
ms.date: 3/17/2019
ms.author: b-juche
ms.openlocfilehash: c2dd8b636223e0484e36eed9fcc616dc6f19cf3a
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58877975"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Kötet létrehozása az Azure NetApp Files számára

Minden kapacitás készlet legfeljebb 500 kötetek. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. NetApp Azure Files támogatja az SMBv3- és NFS-kötetek. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Egy alhálózatot az Azure Files-NetApp delegálni.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="create-an-nfs-volume"></a>Az NFS-kötet létrehozása

1.  Kattintson a **kötetek** panel kapacitás készletek paneljéről. 

    ![Navigáljon a kötetek](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    A létrehozás egy kötet ablak jelenik meg.

3.  A létrehozását egy kötet ablak, kattintson **létrehozás** és adjon meg információt a következő mezőket:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúságúnak kell lennie.  Minden olyan alfanumerikus karaktert használhat.

    * **Kapacitáskészlet**  
        Adja meg a kapacitás-készletet, hol szeretné létrehozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott virtuális hálózathoz rendelkeznie kell egy alhálózatot az Azure Files-NetApp delegált. Az Azure Files-NetApp szolgáltatás csak az azonos virtuális hálózaton vagy a virtuális hálózaton lévő virtuális hálózatok közötti társviszony-létesítésen keresztül a kötet és ugyanabban a régióban is elérhető. A kötet a helyszíni hálózatból Express Route használatával is elérheti.   

    * **Alhálózat**  
        Adja meg az alhálózatot, amelyet a kötet használni kívánt.  
        Az alhálózatot, adja meg az Azure Files-NetApp delegálni. 
        
        Ha nem rendelkezik delegálta egy alhálózathoz, kattintson **új létrehozása** kötet létrehozása a lapon. Ezután az alhálózat létrehozásához lapon adja meg az alhálózati adatokat, és válassza ki **Microsoft.NetApp/volumes** és az alhálózat delegálja a NetApp Azure-fájlok. Az egyes virtuális hálózatok csak egy alhálózatot az Azure Files-NetApp delegálható.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson a **protokoll**, majd **NFS** , a kötet protokoll típusát.   
    * Adja meg a **fájl elérési útja** kulcsindexet exportálás elérési útjának az új kötet létrehozásához. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.

        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet.  

    * Másik lehetőségként [házirend exportálása az NFS-kötet konfigurálása](azure-netapp-files-configure-export-policy.md)

    ![Adja meg az NFS-protokollt](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kattintson a **felülvizsgálat + létrehozás** , tekintse át a kötet.  Kattintson a **létrehozás** az NFS-kötet létrehozásához.

    A létrehozott kötet a kötetek lap jelenik meg. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="create-an-smb-volume"></a>Az SMB-kötet létrehozása

NetApp Azure Files SMBv3 köteteket támogatja. Az Active Directory-kapcsolatok létrehozása az SMB-kötet hozzáadása előtt kell. 

### <a name="create-an-active-directory-connection"></a>Az Active Directory-kapcsolat létrehozása

1. A NetApp fiókból, kattintson a **Active Directory kapcsolatok**, majd kattintson a **csatlakozzon**.  

    ![Az Active Directory-kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. Az Join Active Directory ablakban adja meg a következő információkat:

    * **Elsődleges DNS**   
        Ez a tartomány tartományvezérlő IP-címét az előnyben részesített Active Directory Domain Services használata az Azure NetApp fájlokkal. 
    * **Másodlagos DNS**  
        Ez a tartomány tartományvezérlő IP-címét a másodlagos Active Directory Domain Services használata az Azure NetApp fájlokkal. 
    * **Domain**  
        Ez a tartomány nevét az Active Directory Domain Services, amely csatlakozni szeretne.
    * **SMB-kiszolgáló (számítógép-fiók) előtag**  
        Ez az Active Directoryban, amely Azure NetApp fájlokat fogja használni kívánt új fiókok a számítógépfiók névelőtagjának.

        Írja be például az elnevezési szabványnak, amely a szervezet használja a fájlkiszolgálók esetén NAS-01, NAS-02..., NAS-045, majd volna "NAS" a előtag. 

        Igény szerint a szolgáltatás további számítógép-fiókot hoz létre az Active Directoryban.

    * **Szervezeti egység elérési útja**  
        Ez az a szervezeti egység (OU), ahol létrejön az SMB-kiszolgálói számítógépfiókhoz LDAP elérési útját. Azt jelenti, OU = az második szint, szervezeti egység = első szintjét. 
    * Hitelesítő adatait, beleértve a **felhasználónév** és **jelszó**

    ![Csatlakozás Active Directory-címtárhoz](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

3. Kattintson a **Csatlakozás** parancsra.  

    Megjelenik a létrehozott Active Directory-kapcsolatot.

    ![Az Active Directory-kapcsolatok](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

### <a name="add-an-smb-volume"></a>Az SMB-kötet hozzáadása

1. Kattintson a **kötetek** panel kapacitás készletek paneljéről. 

    ![Navigáljon a kötetek](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    A létrehozás egy kötet ablak jelenik meg.

3. A létrehozását egy kötet ablak, kattintson **létrehozás** és adjon meg információt a következő mezőket:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A névnek egyedinek kell lennie az erőforráscsoporton belül. Legalább három karakter hosszúságúnak kell lennie.  Minden olyan alfanumerikus karaktert használhat.

    * **Fájl elérési útja**  
        Adja meg az új kötet exportálási útvonalának létrehozásához használni kívánt fájlelérési utat. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.   
     
        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet.  

    * **Kapacitáskészlet**  
        Adja meg a kapacitás-készletet, hol szeretné létrehozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott virtuális hálózathoz rendelkeznie kell egy alhálózatot az Azure Files-NetApp delegált. Az Azure Files-NetApp szolgáltatás csak az azonos virtuális hálózaton vagy a virtuális hálózaton lévő virtuális hálózatok közötti társviszony-létesítésen keresztül a kötet és ugyanabban a régióban is elérhető. A kötet a helyszíni hálózatból Express Route használatával is elérheti.   

    * **Alhálózat**  
        Adja meg az alhálózatot, amelyet a kötet használni kívánt.  
        Az alhálózatot, adja meg az Azure Files-NetApp delegálni. 
        
        Ha nem rendelkezik delegálta egy alhálózathoz, kattintson **új létrehozása** kötet létrehozása a lapon. Ezután az alhálózat létrehozásához lapon adja meg az alhálózati adatokat, és válassza ki **Microsoft.NetApp/volumes** és az alhálózat delegálja a NetApp Azure-fájlok. Az egyes virtuális hálózatok csak egy alhálózatot az Azure Files-NetApp delegálható.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson a **protokoll** hajtsa végre a következő információkat:  
    * Válassza ki **SMB** , a kötet protokoll típusát. 
    * Válassza ki a **Active Directory** kapcsolat a legördülő listából.
    * Adja meg a nevét, a megosztott kötet **megosztásnév**.

    ![Adja meg az SMB protokoll](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kattintson a **felülvizsgálat + létrehozás** , tekintse át a kötet.  Kattintson a **létrehozás** az SMB-kötet létrehozásához.

    A létrehozott kötet a kötetek lap jelenik meg. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.

## <a name="next-steps"></a>További lépések  

* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportálási szabályzat konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md)
* [Virtuális hálózat integrációja Azure-szolgáltatások ismertetése](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

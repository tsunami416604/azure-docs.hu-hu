---
title: NFS-kötet létrehozása a Azure NetApp Fileshoz | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre NFS-köteteket Azure NetApp Files. Ismerje meg a szempontokat, például a használni kívánt verziót és az ajánlott eljárásokat.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: 2e1e6ad6625586e882551521111057a2a20f0fff
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513043"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp Files számára

Azure NetApp Files támogatja az NFS-t (NFSv3 és NFSv 4.1) és a SMBv3-köteteket. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ebből a cikkből megtudhatja, hogyan hozhat létre NFS-köteteket. Ha SMB-kötetet szeretne létrehozni, tekintse [meg az SMB-kötet létrehozása Azure NetApp Fileshoz](azure-netapp-files-create-volumes-smb.md)című témakört. 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitási készlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell Azure NetApp Files.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Megfontolandó szempontok 

* A használni kívánt NFS-verzió meghatározása  
  A NFSv3 sokféle használati esetet kezelhet, és általában a legtöbb vállalati alkalmazásban üzembe helyezhető. Ellenőriznie kell, hogy az alkalmazás melyik verziót (NFSv3 vagy NFSv 4.1) igényli, és a megfelelő verzió használatával hozza létre a kötetet. Ha például az [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)-t használja, a nfsv 4.1-es fájl zárolása ajánlott a NFSv3-en keresztül. 

* Biztonság  
  A UNIX módú BITS (olvasás, írás és végrehajtás) támogatása a NFSv3 és a NFSv 4.1 esetében érhető el. A gyökérszintű hozzáférés szükséges az NFS-ügyfélen az NFS-kötetek csatlakoztatásához.

* Helyi felhasználó/csoport és LDAP-támogatás a NFSv 4.1-hez  
  A NFSv 4.1 jelenleg csak a kötetek számára támogatja a gyökérszintű hozzáférést. Lásd: [a nfsv 4.1 alapértelmezett tartományának konfigurálása Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Ajánlott eljárás

* Győződjön meg arról, hogy a megfelelő csatlakoztatási utasításokat használja a kötethez.  Lásd: [kötetek csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Az NFS-ügyfélnek ugyanabban a VNet vagy összetartozó VNet kell lennie, mint a Azure NetApp Files kötetnek. A VNet kívülről való csatlakozás támogatott; azonban további késleltetést fog bevezetni, és csökkenti az általános teljesítményt.

* Győződjön meg arról, hogy az NFS-ügyfél naprakész, és futtatja az operációs rendszer legújabb frissítéseit.

## <a name="create-an-nfs-volume"></a>NFS-kötet létrehozása

1.  Kattintson a **kötetek** panelre a kapacitási készletek panelen. 

    ![A kötetek navigálása](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a kötet létrehozása ablak.

3.  A kötet létrehozása ablakban kattintson a **Létrehozás** gombra, és adja meg a következő mezők adatait:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötet nevének egyedinek kell lennie az egyes kapacitási készleteken belül. Legalább három karakter hosszúnak kell lennie. Bármely alfanumerikus karaktert használhat.   

        `default`A kötet neve nem használható.

    * **Kapacitási készlet**  
        Határozza meg azt a kapacitási készletet, amelyben létre szeretné hozni a kötetet.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Itt adhatja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott vnet rendelkeznie kell egy Azure NetApp Files delegált alhálózattal. A Azure NetApp Files szolgáltatás csak ugyanarról a vnet érhető el, vagy egy olyan vnet, amely ugyanabban a régióban található, mint a kötet vnet-társításon keresztül. Az Express Route használatával is elérheti a kötetet a helyszíni hálózatról.   

    * **Alhálózat**  
        Itt adhatja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell Azure NetApp Files. 
        
        Ha nem delegált alhálózatot, a kötet létrehozása lapon kattintson az **új létrehozása** lehetőségre. Ezután az alhálózat létrehozása lapon adja meg az alhálózati adatokat, majd válassza a **Microsoft. NetApp/kötetek** lehetőséget az alhálózat delegálásához Azure NetApp Files számára. Minden vnet csak egy alhálózat delegálható Azure NetApp Filesra.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Ha meglévő pillanatkép-szabályzatot szeretne alkalmazni a kötetre, kattintson a **speciális szakasz megjelenítése** lehetőségre a kibontásához, majd a legördülő menüben válassza ki a pillanatkép-szabályzatot. 

        A pillanatkép-szabályzat létrehozásával kapcsolatos információkért lásd: [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md).

        ![Speciális kijelölés megjelenítése](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Kattintson a **protokoll**elemre, majd hajtsa végre a következő műveleteket:  
    * Válassza az **NFS** lehetőséget a kötethez tartozó protokoll típusaként.   
    * Itt adhatja meg a **fájl elérési útját** , amelyet az új kötet exportálási útvonalának létrehozásához fog használni. A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.

        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

        A fájl elérési útjának egyedinek kell lennie az egyes előfizetésekben és az egyes régiókban. 

    * Válassza ki a kötet NFS-verzióját (**NFSv3** vagy **nfsv 4.1**).  
    * Szükség esetén [az NFS-kötet exportálási házirendjét is konfigurálhatja](azure-netapp-files-configure-export-policy.md).

    ![NFS-protokoll meghatározása](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. A kötet részleteinek áttekintéséhez kattintson a **felülvizsgálat + létrehozás** elemre.  Ezután kattintson a **Létrehozás** gombra az NFS-kötet létrehozásához.

    A létrehozott kötet megjelenik a kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.


## <a name="next-steps"></a>További lépések  

* [Az NFSv 4.1 alapértelmezett tartományának konfigurálása az Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportálási szabályzat konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [Ismerje meg az Azure-szolgáltatások virtuális hálózati integrációját](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

---
title: NFS-kötet létrehozása az Azure NetApp-fájlokhoz | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként hozhat létre NFS-kötetet az Azure NetApp-fájlokhoz.
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
ms.date: 12/01/2019
ms.author: b-juche
ms.openlocfilehash: 9e8817f802ca1d73ca0f6bfa2b32b1b14b37d7da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274085"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>NFS-kötet létrehozása az Azure NetApp Files számára

Az Azure NetApp Files támogatja az NFS (NFSv3 és NFSv4.1) és az SMBv3 köteteket. A kötet kapacitásfogyasztása beleszámít a készlet kiosztott kapacitásába. Ez a cikk bemutatja, hogyan hozhat létre NFS-köteteket. Ha SMB-kötetet szeretne létrehozni, olvassa [el az SMB-kötet létrehozása az Azure NetApp-fájlokhoz című témakört.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Előkészületek 
A cikk előfeltételeinek részeként korábban már be kellett állítania egy kapacitáskészletet.   
[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)   
Az alhálózatot delegálni kell az Azure NetApp-fájlokszámára.  
[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>Megfontolandó szempontok 

* Annak eldöntése, hogy melyik NFS-verziót használja  
  Az NFSv3 a használati esetek széles skáláját képes kezelni, és a legtöbb vállalati alkalmazásban gyakran alkalmazzák. Ellenőrizze, hogy milyen verzióra (NFSv3 vagy NFSv4.1) van szükség az alkalmazáshoz, és hozza létre a kötetet a megfelelő verzióval. Ha például [Apache ActiveMQ-t](https://activemq.apache.org/shared-file-system-master-slave)használ, az NFSv4.1 fájlzárolása az NFSv3-on keresztül ajánlott. 

* Biztonság  
  Az NFSv3 és az NFSv4.1 esetében a UNIX módú bitek (olvasás, írás és végrehajtás) támogatása érhető el. Az NFS-kötetek csatlakoztatásához gyökérszintű hozzáférés szükséges az NFS-ügyfélhez.

* Helyi felhasználó/csoport és LDAP-támogatás az NFSv4.1-hez  
  Jelenleg az NFSv4.1 csak a kötetekhez támogatja a gyökérhozzáférést. Lásd: [NFSv4.1 alapértelmezett tartomány konfigurálása az Azure NetApp-fájlokhoz.](azure-netapp-files-configure-nfsv41-domain.md) 

## <a name="best-practice"></a>Ajánlott eljárás

* Győződjön meg arról, hogy a megfelelő csatlakoztatási utasításokat használja a kötethez.  Lásd: [Kötet csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

* Az NFS-ügyfélnek ugyanabban a virtuális hálózatban vagy társviszony-létesített virtuális hálózatban kell lennie, mint az Azure NetApp Files kötetnek. A virtuális hálózaton kívülről való csatlakozás támogatott; azonban további késést vezet be, és csökkenti az általános teljesítményt.

* Győződjön meg arról, hogy az NFS-ügyfél naprakész, és az operációs rendszer legújabb frissítéseit futtatja.

## <a name="create-an-nfs-volume"></a>NFS-kötet létrehozása

1.  Kattintson a Kapacitáskészletek panel **Kötetek** paneljének elemre. 

    ![Navigálás a kötetekre](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  Kattintson a **+ Kötet létrehozása** lehetőségre egy kötet létrehozásához.  
    Megjelenik a Kötet létrehozása ablak.

3.  A Kötet létrehozása ablakban kattintson a **Létrehozás** és a következő mezők adatainak megadására:   
    * **Kötet neve**      
        Adja meg a létrehozni kívánt kötet nevét.   

        A kötetnevének minden kapacitáskészleten belül egyedinek kell lennie. Legalább három karakter hosszúnak kell lennie. Bármilyen alfanumerikus karaktert használhat.   

        Kötetnévként `default` nem használható.

    * **Kapacitáskészlet**  
        Adja meg azt a kapacitáskészletet, amelyhez a kötetet létre kívánja hozni.

    * **Kvóta**  
        Adja meg a kötet számára kiosztott logikai tárterület mennyiségét.  

        A **Rendelkezésre álló kvóta** mező a kiválasztott kapacitáskészletben fel nem használt terület mennyiségét mutatja, amely felhasználható egy új kötet létrehozása során. Az új kötet mérete nem haladhatja meg a rendelkezésre álló kvótát.  

    * **Virtuális hálózat**  
        Adja meg azt az Azure-beli virtuális hálózatot (VNet), amelyről el szeretné érni a kötetet.  

        A megadott virtuális hálózatnak rendelkeznie kell az Azure NetApp-fájlok delegált alhálózatával. Az Azure NetApp Files szolgáltatás csak ugyanabból a virtuális hálózatból vagy egy virtuális hálózatról érhető el, amely ugyanabban a régióban van, mint a kötet a virtuális hálózat társviszony-létesítésén keresztül. A kötetet a helyszíni hálózatról is elérheti az Expressz útvonalon keresztül.   

    * **Alhálózat**  
        Adja meg a kötethez használni kívánt alhálózatot.  
        A megadott alhálózatot delegálni kell az Azure NetApp-fájlokban. 
        
        Ha még nem delegált alhálózatot, kattintson az **Új létrehozása** gombra a Kötet létrehozása lapon. Ezután az Alhálózat létrehozása lapon adja meg az alhálózati adatokat, és válassza a **Microsoft.NetApp/kötetek** lehetőséget az Azure NetApp-fájlok alhálózatának delegálásához. Minden virtuális hálózatban csak egy alhálózat delegálható az Azure NetApp-fájlokszámára.   
 
        ![Kötet létrehozása](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Alhálózat létrehozása](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. Kattintson **a Protokoll**gombra, majd hajtsa végre a következő műveleteket:  
    * Válassza az **NFS-t** a kötet protokolltípusaként.   
    * Adja meg az új kötet exportálási útvonalának létrehozásához használt **fájlelérési utat.** A rendszer az exportálási útvonal használatával csatlakoztatja és éri el a kötetet.

        Az elérési út neve csak betűket, számokat és kötőjeleket („-”) tartalmazhat. A név 16–40 karakter hosszú lehet. 

        A fájl elérési útjának egyedinek kell lennie az egyes előfizetéseken és régiókon belül. 

    * Válassza ki a kötet NFS verzióját (**NFSv3** vagy **NFSv4.1).**  
    * Szükség esetén [konfigurálja az NFS-kötet exportálási házirendjeit.](azure-netapp-files-configure-export-policy.md)

    ![NFS protokoll megadása](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. Kattintson **a Véleményezés + Létrehozás gombra** a kötet részleteinek áttekintéséhez.  Ezután kattintson a **Létrehozás** gombra az NFS-kötet létrehozásához.

    A létrehozott kötet megjelenik a Kötetek lapon. 
 
    A kötetek a kapacitáskészletről öröklik az előfizetésre, az erőforráscsoportra és a helyre vonatkozó attribútumokat. A kötet üzembe helyezésének állapotát az Értesítések lapon követheti nyomon.


## <a name="next-steps"></a>További lépések  

* [A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportálási szabályzat konfigurálása NFS-kötethez](azure-netapp-files-configure-export-policy.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)
* [További információ az Azure-szolgáltatások virtuális hálózati integrációjáról](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)

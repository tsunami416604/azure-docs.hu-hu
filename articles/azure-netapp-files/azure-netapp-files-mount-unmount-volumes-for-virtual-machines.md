---
title: Az Azure NetApp-fájlok köteteinek csatlakoztatása virtuális gépekhez
description: Ismerje meg, hogyan csatlakoztathat vagy leoldhat egy kötetet Windows os virtuális gépekhez vagy Linuxos virtuális gépekhez az Azure-ban.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084940"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása 

Szükség esetén csatlakoztathat vagy leválaszthat egy kötetet Windows vagy Linux rendszerű virtuális gépekhez.  A Linux-alapú virtuális gépek csatlakoztatási utasításai elérhetők az Azure NetApp-fájlokban.  

1. Kattintson a **Kötetek** panelre, majd válassza ki azt a kötetet, amelyhez csatlakoztatni szeretné. 
2. Kattintson a Kiválasztott kötet **utasításoknak csatlakoztatása** elemre, majd kövesse az utasításokat a kötet csatlakoztatásához. 

    ![NFS csatlakoztatási utasítások](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Csatlakoztatási utasítások SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    NFSv4.1 használata esetén a következő paranccsal csatlakoztassa a fájlrendszert:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Ha azt szeretné, hogy egy NFS-kötet automatikusan csatlakoztatva, amikor egy Azure `/etc/fstab` virtuális gép indításakor vagy újraindítása, adjon hozzá egy bejegyzést a fájlhoz az állomáson. 

    Például:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`Az Azure NetApp Files kötet IP-címe a kötet tulajdonságai panelen található.
    * `$FILEPATH`Az Azure NetApp Files kötet exportálási útvonala.
    * `$MOUNTPOINT`az NFS-exportálás csatlakoztatására használt Linux-állomáson létrehozott könyvtár.

4. Ha nfs-sel szeretné csatlakoztatni a kötetet a Windows rendszerhez:

    a. Először csatlakoztassa a kötetet egy Unix vagy Linux virtuális gépre.  
    b. Futtasson egy `chmod 777` vagy `chmod 775` parancsot a kötethez.  
    c. Csatlakoztassa a kötetet az NFS-ügyfélen keresztül Windows rendszeren.

## <a name="next-steps"></a>További lépések

* [A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS – gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Hálózati fájlrendszer – áttekintés](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)

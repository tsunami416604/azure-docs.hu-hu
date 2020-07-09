---
title: Azure NetApp Files kötetek csatlakoztatása virtuális gépekhez
description: Megtudhatja, hogyan csatlakoztathat vagy leválaszthat köteteket Windows rendszerű virtuális gépekhez vagy linuxos virtuális gépekhez az Azure-ban.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/06/2020
ms.openlocfilehash: 4bfd90be2a469c5ab94172769729095069f53cd7
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045654"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása 

Szükség szerint csatlakoztathat vagy leválaszthat Windows-vagy Linux-alapú virtuális gépek kötetét.  A Linux rendszerű virtuális gépek csatlakoztatására vonatkozó utasítások a Azure NetApp Fileson érhetők el.  

> [!IMPORTANT] 
> Az NFS-kötetek eléréséhez legalább egy exportálási házirend szükséges.

1. Kattintson a **kötetek** panelre, majd válassza ki azt a kötetet, amelyhez csatlakoztatni kívánja. 
2. Kattintson a kijelölt kötet **csatlakoztatási utasításai** elemre, majd kövesse az utasításokat a kötet csatlakoztatásához. 

    ![Csatlakoztatási utasítások NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Csatlakoztatási utasítások SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Ha NFS-kötetet csatlakoztat, ügyeljen arra, hogy a `vers` parancsban található kapcsolóval `mount` megadhatja az NFS protokoll azon verzióját, amely megfelel a csatlakoztatni kívánt kötetnek. 
    * Ha a NFSv 4.1-et használja, használja a következő parancsot a fájlrendszer csatlakoztatásához:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Ha egy Azure-beli virtuális gép indításakor vagy újraindításakor automatikusan csatlakoztatni szeretné az NFS-kötetet, vegyen fel egy bejegyzést a `/etc/fstab` gazdagépen lévő fájlba. 

    Például:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`a kötet tulajdonságai panelen található Azure NetApp Files kötet IP-címe.
    * `$FILEPATH`a Azure NetApp Files kötet exportálási útvonala.
    * `$MOUNTPOINT`az NFS-exportálás csatlakoztatásához használt Linux-gazdagépen létrehozott könyvtár.

4. Ha csatlakoztatni szeretné a kötetet a Windowshoz az NFS használatával:

    a. Először csatlakoztassa a kötetet egy UNIX vagy Linux rendszerű virtuális gépre.  
    b. Futtasson egy `chmod 777` vagy `chmod 775` parancsot a köteten.  
    c. Csatlakoztassa a kötetet a Windows rendszer NFS-ügyfelén keresztül.

## <a name="next-steps"></a>További lépések

* [Az NFSv 4.1 alapértelmezett tartományának konfigurálása az Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS – gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Hálózati fájlrendszer – áttekintés](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)

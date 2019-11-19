---
title: Azure NetApp Files kötet csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez | Microsoft Docs
description: Ismerteti, hogyan lehet kötetet csatlakoztatni vagy leválasztani a virtuális gépekhez vagy a Linux rendszerű virtuális gépekhez.
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
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173583"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása 

Szükség szerint csatlakoztathat vagy leválaszthat Windows-vagy Linux-alapú virtuális gépek kötetét.  A Linux rendszerű virtuális gépek csatlakoztatására vonatkozó utasítások a Azure NetApp Fileson érhetők el.  

1. Kattintson a **kötetek** panelre, majd válassza ki azt a kötetet, amelyhez csatlakoztatni kívánja. 
2. Kattintson a kijelölt kötet **csatlakoztatási utasításai** elemre, majd kövesse az utasításokat a kötet csatlakoztatásához. 

    ![Csatlakoztatási utasítások NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Csatlakoztatási utasítások SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Ha a NFSv 4.1-et használja, használja a következő parancsot a fájlrendszer csatlakoztatásához: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Ha egy Azure-beli virtuális gép indításakor vagy újraindításakor automatikusan csatlakoztatni szeretné az NFS-kötetet, vegyen fel egy bejegyzést a gazdagép `/etc/fstab` fájljába. 

    Példa: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` a kötet tulajdonságai panelen található Azure NetApp Files kötet IP-címe.
    * `$FILEPATH` a Azure NetApp Files kötet exportálási útvonala.
    * `$MOUNTPOINT` az NFS-exportálás csatlakoztatásához használt Linux-gazdagépen létrehozott könyvtár.

## <a name="next-steps"></a>Következő lépések

* [A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS – gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)

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
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953097"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása 

Szükség szerint csatlakoztathat vagy leválaszthat Windows-vagy Linux-alapú virtuális gépek kötetét.  A Linux rendszerű virtuális gépek csatlakoztatására vonatkozó utasítások a Azure NetApp Fileson érhetők el.  

1. Kattintson a **kötetek** panelre, majd válassza ki azt a kötetet, amelyhez csatlakoztatni kívánja. 
2. Kattintson a kijelölt kötet **csatlakoztatási utasításai** elemre, majd kövesse az utasításokat a kötet csatlakoztatásához. 

    ![Csatlakoztatási utasítások NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Csatlakoztatási utasítások SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Ha a NFSv 4.1-et használja, használja a következő parancsot a fájlrendszer csatlakoztatásához:  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>Következő lépések

* [A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz](azure-netapp-files-configure-nfsv41-domain.md)
* Az Azure-beli virtuális gépek indításakor vagy újraindításakor megtekintheti, hogyan lehet automatikusan csatlakoztatni egy NFS-kötetet az NFS-vel kapcsolatos [Gyakori kérdések](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs) között.

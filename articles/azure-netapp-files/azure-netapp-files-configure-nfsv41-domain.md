---
title: Az NFSv4.1 alapértelmezett tartományának konfigurálása az Azure NetApp-fájlokhoz | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként konfigurálható az NFSv4.1 és az Azure NetApp Files használatával az NFSclient-ügyfél.
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
ms.openlocfilehash: 77178a23206eadae941794c92b8dd99fe2ca1e05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73906285"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>A NFSv 4.1 alapértelmezett tartományának konfigurálása a Azure NetApp Fileshoz

Az NFSv4 bevezeti a hitelesítési tartomány fogalmát. Az Azure NetApp Files jelenleg támogatja a csak gyökér szintű felhasználók leképezését a szolgáltatásból az NFS-ügyfélhez. Az NFSv4.1 funkció azure NetApp-fájlokkal való használatához frissítenie kell az NFS-ügyfelet.

## <a name="default-behavior-of-usergroup-mapping"></a>A felhasználó/csoport hozzárendelésének alapértelmezett viselkedése

A gyökérleképezés `nobody` alapértelmezés szerint a felhasználó számára történik, mert az NFSv4 tartomány `localdomain`beállítása . Ha egy Azure NetApp Files NFSv4.1 kötetet csatlakoztat gyökérként, a fájlengedélyek a következőképpen jelennek meg:  

![Az NFSv4.1-hez való felhasználói/csoportleképezés alapértelmezett viselkedése](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Ahogy a fenti példa `file1` is `root`mutatja, a `nobody` felhasználónak kell lennie, de alapértelmezés szerint le képezi.  Ebből a cikkből `file1` megtudhatja, hogyan állíthatja be a felhasználót a beállítására. `root`  

## <a name="steps"></a>Lépések 

1. A `/etc/idmapd.conf` fájl szerkesztése az NFS-ügyfélen.   
    Szüntesse meg `#Domain` a sor megjegyzését (azaz távolítsa el a `#` sort a sorból), és módosítsa az értéket `localdomain` a értékre. `defaultv4iddomain.com` 

    Kezdeti konfiguráció: 
    
    ![Az NFSv4.1 kezdeti konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Frissített konfiguráció:
    
    ![Az NFSv4.1 frissített konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Lekell választani a jelenleg csatlakoztatott NFS-köteteket.
3. Frissítse `/etc/idmapd.conf` a fájlt.
4. Indítsa `rpcbind` újra a szolgáltatást`service rpcbind restart`a gazdagépen ( ), vagy egyszerűen indítsa újra a gazdagépet.
5. Csatlakoztassa az NFS-köteteket szükség szerint.   

    Lásd: [Kötet csatlakoztatása vagy leválasztása Windows vagy Linux rendszerű virtuális gépekhez.](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 

A következő példa az eredményül kapott felhasználói/csoportváltozást mutatja be: 

![Az NFSv4.1 eredményül kapott konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Ahogy a példa is mutatja, a `nobody` felhasználó/csoport mostantól `root`a-ra változott.

## <a name="behavior-of-other-non-root-users-and-groups"></a>Más (nem legfelső szintű) felhasználók és csoportok viselkedése

Az Azure NetApp Files támogatja azokat a helyi felhasználókat (a gazdagépen helyileg létrehozott felhasználókat), akik nfsv4.1-kötetekben lévő fájlokhoz vagy mappákhoz társított engedélyekkel rendelkeznek. A szolgáltatás azonban jelenleg nem támogatja a felhasználók/csoportok leképezését több csomópont között. Ezért az egyik állomáson létrehozott felhasználók alapértelmezés szerint nem felelnek meg egy másik állomáson létrehozott felhasználóknak. 

A következő `Host1` példában három meglévő tesztfelhasználói fiókkal (`testuser01`, `testuser02`, , `testuser03`): 

![Az NFSv4.1 eredményül kapott konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

A `Host2`alkalmazásban vegye figyelembe, hogy a tesztfelhasználói fiókok nem lettek létrehozva, de ugyanaz a kötet mindkét állomásra csatlakoztatva van:

![Az NFSv4.1 eredményül kapott konfigurációja](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Következő lépés 

[Kötet Windows vagy Linux rendszerű virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)


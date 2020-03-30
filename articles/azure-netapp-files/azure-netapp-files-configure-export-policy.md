---
title: Az NFS-kötet exportálási házirendjének konfigurálása – Azure NetApp-fájlok
description: A cikk azt ismerteti, hogy miként konfigurálható az exportálási szabályzat az NFS-kötetekhez való hozzáférés szabályozására az Azure NetApp-fájlok használatával
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: b96fca3a5627a1c6c96c8db5c1c209a51c5e102a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551558"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportálási szabályzat konfigurálása NFS-kötethez

Konfigurálhat egy exportálási szabályzatot, amellyel szabályozhatja az Azure NetApp Files-kötethez való hozzáférést. Az Azure NetApp Files exportálási szabályzata csak az NFS-köteteket támogatja.  Az NFSv3 és az NFSv4 egyaránt támogatott. 

## <a name="steps"></a>Lépések 

1.  Kattintson a **Szabályzat exportálása** elemre az Azure NetApp-fájlok navigációs ablaktábláján. 

2.  Töltse ki az alábbi mezőket az exportálási szabályzat létrehozásához:   
    *  **Index**   
        Adja meg a szabály indexszámát.  
        Egy exportálási szabályzat legfeljebb öt szabályból állhat. A szabályok kiértékelése az indexszámok alapján felállított listában elfoglalt helyük alapján történik. Az alacsonyabb indexszámmal rendelkező szabályok lesznek először kiértékelve. Az 1-es indexszámmal rendelkező szabály például előbb lesz kiértékelve, mint a 2-es indexszámú szabály. 

    * **Engedélyezett ügyfelek**   
        Adja meg az értéket az alábbi formátumok egyikében:  
        * IPv4-cím, például: `10.1.12.24` 
        * IPv4-cím bitek számában kifejezett alhálózati maszkkal, például: `10.1.12.10/4`

    * **Hozzáférés**  
        Válasszon egyet az alábbi hozzáférési típusok közül:  
        * Nincs hozzáférés 
        * Olvasás és írás
        * Csak olvasási engedély

    ![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>További lépések 
* [Kötetek kezelése](azure-netapp-files-manage-volumes.md)
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)

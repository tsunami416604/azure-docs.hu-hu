---
title: Az NFS-kötet exportálási házirendjének konfigurálása – Azure NetApp Files
description: Leírja, hogyan konfigurálható az exportálási házirend az NFS-kötetekhez való hozzáférés vezérléséhez Azure NetApp Files használatával
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533204"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportálási szabályzat konfigurálása NFS-kötethez

Az exportálási házirend konfigurálásával szabályozhatja a Azure NetApp Files kötethez való hozzáférést. Azure NetApp Files exportálási házirend az NFS protokollt (NFSv3 és NFSv 4.1) és a kettős protokollt (NFSv3 és SMB) használó köteteket támogatja. 

Legfeljebb öt exportálási szabályzatot hozhat létre.

## <a name="steps"></a>Lépések 

1.  A kötetek lapon válassza ki azt a kötetet, amelyhez konfigurálni szeretné az exportálási szabályzatot, majd kattintson a **házirend exportálása**lehetőségre. 

    Az exportálási házirendet a kötet létrehozása során is konfigurálhatja.

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

    * **Írásvédett** és írási **/olvasási**  
        Ha a NFSv 4.1 használatával Kerberos-titkosítást használ, kövesse az [nfsv 4.1 Kerberos-titkosítás konfigurálása](configure-kerberos-encryption.md)című témakör utasításait.  A Kerberos teljesítményére gyakorolt hatásért lásd: a [Kerberos teljesítményére gyakorolt hatás a nfsv 4.1 rendszeren](configure-kerberos-encryption.md#kerberos_performance). 

        ![Kerberos biztonsági beállítások](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Gyökérszintű hozzáférés**  
        Annak megadása, hogy a `root` fiók hozzáférhet-e a kötethez.  Alapértelmezés szerint a gyökérszintű hozzáférés be van **kapcsolva**, és a `root` fiók hozzáfér a kötethez.

![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>További lépések 
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)

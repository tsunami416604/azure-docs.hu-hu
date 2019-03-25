---
title: Az Azure Files-NetApp használatával NFS-kötet exportálási vonatkozó házirend konfigurálása |} A Microsoft Docs
description: Ismerteti, hogyan lehet egy NFS-kötet Azure NetApp fájlokkal való hozzáférés szabályozásához export-házirend konfigurálása
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
ms.date: 03/20/2019
ms.author: b-juche
ms.openlocfilehash: 8cda5921a1aec86d28beabbd9cea5b07a203a0e8
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401489"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Az NFS-kötet exportálási vonatkozó házirend konfigurálása

Konfigurálhat egy exportálási szabályzatot, amellyel szabályozhatja az Azure NetApp Files-kötethez való hozzáférést. Exportálás házirend a rendszer csak az NFS-kötetek esetében támogatja. 

## <a name="steps"></a>Lépések 

1.  Kattintson az **Exportálási szabályzat létrehozása** panelre a Kötet kezelése panelen. 

2.  Töltse ki az alábbi mezőket az exportálási szabályzat létrehozásához:   
    *  **Index**   
        Adja meg a szabály indexszámát.  
        Egy exportálási szabályzat legfeljebb öt szabályból állhat. A szabályok kiértékelése az indexszámok alapján felállított listában elfoglalt helyük alapján történik. Az alacsonyabb indexszámmal rendelkező szabályok lesznek először kiértékelve. Az 1-es indexszámmal rendelkező szabály például előbb lesz kiértékelve, mint a 2-es indexszámú szabály. 

    * **Engedélyezett ügyfelek**   
        Adja meg az értéket az alábbi formátumok egyikében:  
        * IPv4-cím, például: `10.1.12.24` 
        * IPv4-cím bitek számában kifejezett alhálózati maszkkal, például: `10.1.12.10/4`

    * **Access (Hozzáférés)**  
        Válasszon egyet az alábbi hozzáférési típusok közül:  
        * Nincs hozzáférés 
        * Olvasás és írás
        * Csak olvasási engedély

    * **Protokollok**   
        Adja meg az exportálási szabályzathoz használni kívánt protokollt.   
        Az Azure Files-NetApp exportálási házirend jelenleg csak NFSv3 támogatja.

    ![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>További lépések 
* [Kötetek kezelése](azure-netapp-files-manage-volumes.md)
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)

---
title: NFS-kötet exportálási házirendjének konfigurálása Azure NetApp Files használatával | Microsoft Docs
description: Leírja, hogyan konfigurálható az exportálási házirend az NFS-kötetekhez való hozzáférés vezérléséhez Azure NetApp Files használatával
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674925"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportálási szabályzat konfigurálása NFS-kötethez

Konfigurálhat egy exportálási szabályzatot, amellyel szabályozhatja az Azure NetApp Files-kötethez való hozzáférést. Azure NetApp Files exportálási házirend csak az NFS-köteteket támogatja.  A NFSv3 és a Nfsv4 névleképezője egyaránt támogatott. 

## <a name="steps"></a>Lépések 

1.  Kattintson a **házirend exportálása** lehetőségre a Azure NetApp Files navigációs ablaktáblán. 

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

    ![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Következő lépések 
* [Kötetek kezelése](azure-netapp-files-manage-volumes.md)
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)

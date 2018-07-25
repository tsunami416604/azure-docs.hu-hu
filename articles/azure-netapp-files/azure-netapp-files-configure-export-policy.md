---
title: Exportálási szabályzat konfigurálása egy Azure NetApp Files-kötet esetén | Microsoft Docs
description: A cikk azt ismerteti, hogyan konfigurálhat exportálási szabályzatot és szabályozhatja a hozzáférést egy Azure NetApp Files-kötet esetén.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010956"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>Exportálási szabályzat konfigurálása kötethez (opcionális)

Konfigurálhat egy exportálási szabályzatot, amellyel szabályozhatja az Azure NetApp Files-kötethez való hozzáférést. 

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
        Az Azure NetApp Files jelenleg csak az NFSv3 protokollt támogatja.

    ![Exportálási szabályzat](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>További lépések 
* [Kötetek kezelése](azure-netapp-files-manage-volumes.md)
* [Kötet virtuális gépekhez való csatlakoztatása és leválasztása](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Pillanatképek kezelése](azure-netapp-files-manage-snapshots.md)

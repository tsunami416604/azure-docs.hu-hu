---
title: Kapacitáskészlet beállítása az Azure NetApp Files számára | Microsoft Docs
description: A cikk azt mutatja be, hogyan állíthat be kapacitáskészleteket, amelyekben aztán majd köteteket hozhat létre.
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
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 8f50b2ad34c705c8d3831d8243f136c41d750dc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60691098"
---
# <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

A beállított kapacitáskészletekben hozhat létre majd köteteket.  

## <a name="before-you-begin"></a>Előkészületek 

Már rendelkeznie kell egy létrehozott NetApp-fiókkal.   

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Lépések 

1. Nyissa meg a NetApp-fiók felügyeleti paneljét, majd a navigációs ablaktáblán kattintson a **Kapacitáskészletek**elemre.  
    
    ![Navigálás a kapacitáskészlethez](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Új kapacitáskészlet létrehozásához kattintson a **+ Készletek hozzáadása** elemre.   
    Ekkor megjelenik az Új kapacitáskészlet ablak.

3. Adja meg az alábbi, az új kapacitáskészletre vonatkozó információkat:  
   * **Név**  
     Adja meg a kapacitáskészlet nevét.  
     A kapacitáskészlet nevének a NetApp-fiókban egyedinek kell lennie.

   * **Szolgáltatási szint**   
     Ez a mező a kapacitáskészlet célteljesítményét mutatja.  
     Adja meg a kapacitáskészlet szolgáltatási szintjét: [**Prémium**](azure-netapp-files-service-levels.md#Premium) vagy [**Standard**](azure-netapp-files-service-levels.md#Standard).

   * **Méret**     
     Adja meg a megvásárolni kívánt kapacitáskészlet-méretet.        
     A kapacitáskészletek minimális mérete 4 TiB. A létrehozott kapacitáskészlet méretének a 4 TiB egy szorzatának kell lennie.   
      
     ![Új kapacitáskészlet](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések 

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- Tekintse meg az [Azure NetApp Files díjszabási lapját](https://azure.microsoft.com/pricing/details/storage/netapp/) a különböző szolgáltatási szintek áráról
- [Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

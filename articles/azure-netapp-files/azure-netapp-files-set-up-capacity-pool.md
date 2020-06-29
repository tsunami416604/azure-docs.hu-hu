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
ms.topic: how-to
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: d76af4901103b0eed8cd1cffac744f8fb41d9689
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483499"
---
# <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása

A beállított kapacitáskészletekben hozhat létre majd köteteket.  

## <a name="before-you-begin"></a>Előkészületek 

Már rendelkeznie kell egy létrehozott NetApp-fiókkal.   

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Lépések 

1. Lépjen a NetApp-fiókhoz tartozó felügyelet panelre, majd a navigációs ablaktáblán kattintson a **Kapacitási készletek**elemre.  
    
    ![Navigáljon a kapacitási készlethez](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Új kapacitáskészlet létrehozásához kattintson a **+ Készletek hozzáadása** elemre.   
    Ekkor megjelenik az Új kapacitáskészlet ablak.

3. Adja meg az alábbi, az új kapacitáskészletre vonatkozó információkat:  
   * **Name (Név)**  
     Adja meg a kapacitáskészlet nevét.  
     A kapacitáskészlet nevének a NetApp-fiókban egyedinek kell lennie.

   * **Szolgáltatási szint**   
     Ez a mező a kapacitáskészlet célteljesítményét mutatja.  
     A kapacitási készlet szolgáltatási szintjének megadása: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)vagy [**standard**](azure-netapp-files-service-levels.md#Standard).

   * **Méret**     
     Adja meg a megvásárolni kívánt kapacitáskészlet-méretet.        
     A kapacitáskészletek minimális mérete 4 TiB. A létrehozott kapacitáskészlet méretének a 4 TiB egy szorzatának kell lennie.   
      
     ![Új kapacitáskészlet](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések 

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- A különböző szolgáltatási szintek árának [Azure NetApp Files díjszabási oldalán](https://azure.microsoft.com/pricing/details/storage/netapp/) talál.
- [Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)

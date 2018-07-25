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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 0e9203f5b4e2a9043e242b804c82017cf6fc3ee1
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010803"
---
# <a name="set-up-a-capacity-pool"></a>Kapacitáskészlet beállítása
A beállított kapacitáskészletekben hozhat létre majd köteteket.  

## <a name="before-you-begin"></a>Előkészületek 
Már rendelkeznie kell egy létrehozott NetApp-fiókkal.   

[NetApp-fiók létrehozása](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Lépések 

1. Lépjen a NetApp-fiók felügyeleti paneljére, majd a navigációs ablaktáblán válassza a **Kapacitáskészletek** lehetőséget.

2. Új kapacitáskészlet létrehozásához kattintson a **+ Készletek hozzáadása** elemre.   
    Ekkor megjelenik az Új kapacitáskészlet ablak.

3. Adja meg az alábbi, az új kapacitáskészletre vonatkozó információkat:  
  * **Name (Név)**  
    Adja meg a kapacitáskészlet nevét.  
    A kapacitáskészlet nevének a NetApp-fiókban egyedinek kell lennie.

  * **Szolgáltatásszint**   
    Ez a mező a kapacitáskészlet célteljesítményét mutatja.  
    Jelenleg csak a Prémium szolgáltatásszint érhető el. 

  *  **Méret**     
      Adja meg a megvásárolni kívánt kapacitáskészlet-méretet.        
      A kapacitáskészletek minimális mérete 4 TiB. A létrehozott kapacitáskészlet méretének a 4 TiB egy szorzatának kell lennie.   
      
      ![Új kapacitáskészlet](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kattintson az **OK** gombra.

## <a name="next-steps"></a>További lépések 

1. [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)
2. [Exportálási szabályzat konfigurálása kötethez (opcionális)](azure-netapp-files-configure-export-policy.md)


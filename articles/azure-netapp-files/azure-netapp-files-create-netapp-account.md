---
title: NetApp-fiók létrehozása Azure NetApp Files-hozzáféréshez | Microsoft Docs
description: A cikk az Azure NetApp Files elérésének és a NetApp-fiókok létrehozásának módját ismerteti kapacitáskészletek beállításához és kötetek létrehozásához.
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
ms.openlocfilehash: ad8cc550ce69e4dc4c19a569718fa873a65b3620
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010344"
---
# <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása
A NetApp-fiókok létrehozásával lehetősége lesz kapacitáskészleteket beállítani, majd azt követően köteteket létrehozni. NetApp-fiókokat az Azure NetApp Files panelen hozhat létre.

## <a name="before-you-begin"></a>Előkészületek
Szerepelnie kell a Microsoft.NetApp Azure erőforrás-szolgáltató engedélyezési listáján, és konfigurálnia kellett az Azure NetApp Files szolgáltatás használatát.  

[Az Azure NetApp Files nyilvános előzetes verziójának regisztrációs oldala](https://aka.ms/nfspublicpreview). 

## <a name="steps"></a>Lépések 

1. Keresse meg az előzetes verziójú Azure Portal URL-címét az előzetes verzió meghívójában, és jelentkezzen be a portálra. 
2.  Az Azure NetApp Files panelt a következő módszerek egyikével érheti el:  
  * Keressen rá az **Azure NetApp Files** kifejezésre az Azure Portal keresőmezőjében.  
  * Kattintson a navigációs menü **Minden szolgáltatás** elemére, majd szűrjön az Azure NetApp Files kifejezésre.  

  Az Azure NetApp Filest „kedvencnek is jelölheti” a mellette lévő csillag ikonra kattintva. 

3. Új NetApp-fiók létrehozásához kattintson a **+ Hozzáadás** gombra.  
  Ekkor megjelenik az Új NetApp-fiók ablaka.  

4. Adja meg az alábbi információkat a NetApp-fiókra vonatkozóan: 
  * **Fióknév**  
    Adja meg az előfizetés egyedi nevét.
  *  **Előfizetés**  
    Válasszon ki egyet a meglévő előfizetések közül.
  * **Erőforráscsoport**   
    Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.
  * **Hely**  
    Válassza ki azt az Azure-régiót, ahol a fiókot és annak alárendelt erőforrásait létre szeretné hozni.  
    Az Azure NetApp Files szolgáltatás használata jelenleg csak az USA keleti régiójában támogatott.  

    ![Új NetApp-fiók](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kattintson a **Create** (Létrehozás) gombra.     
  A létrehozott NetApp-fiók megjelenik az Azure NetApp Files panelen. 

## <a name="next-steps"></a>További lépések  

1. [Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)
2. [Kötet létrehozása Azure NetApp Files-hoz](azure-netapp-files-create-volumes.md)
3. [Exportálási szabályzat konfigurálása kötethez (opcionális)](azure-netapp-files-configure-export-policy.md)

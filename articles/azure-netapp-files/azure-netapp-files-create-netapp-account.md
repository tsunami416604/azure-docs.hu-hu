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
ms.openlocfilehash: 47b9d25f8db2241bb578528780e28f43d56371e5
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963080"
---
# <a name="create-a-netapp-account"></a>NetApp-fiók létrehozása
A NetApp-fiókok létrehozásával lehetősége lesz kapacitáskészleteket beállítani, majd azt követően köteteket létrehozni. NetApp-fiókokat az Azure NetApp Files panelen hozhat létre.

## <a name="before-you-begin"></a>Előkészületek
Az előfizetést a NetApp erőforrás-szolgáltató és a nyilvános előzetes verziójú funkció használatával van regisztrálva.

[Regisztráljon a Azure NetApp-fájlok](azure-netapp-files-register.md)

## <a name="steps"></a>Lépések 

1. Jelentkezzen be az Azure portálra. 
2. Az Azure NetApp Files panelt a következő módszerek egyikével érheti el:  
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

    ![Új NetApp-fiók](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Kattintson a **Create** (Létrehozás) gombra.     
  A létrehozott NetApp-fiók megjelenik az Azure NetApp Files panelen. 

## <a name="next-steps"></a>További lépések  

[Kapacitáskészlet beállítása](azure-netapp-files-set-up-capacity-pool.md)


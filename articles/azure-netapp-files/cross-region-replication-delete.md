---
title: Replikálások törlése Azure NetApp Files régiók közötti replikációhoz | Microsoft Docs
description: Útmutató olyan replikációs kapcsolat törléséhez, amely már nem szükséges a forrás és a cél kötetek között.
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695707"
---
# <a name="delete-replications"></a>Replikációk törlése

A replikálási kapcsolatot a forrás és a cél kötetek között a kötetek replikálásának törlésével állíthatja le. A törlési műveletet a forrás vagy a cél kötetről is végrehajthatja. A törlési művelet csak a replikálás engedélyezését távolítja el; nem távolítja el a forrást vagy a célként megadott kötetet. 

## <a name="steps"></a>Lépések

1. A kötetek replikálásának törlése előtt győződjön meg arról, hogy a replikálási társítás megszakadt.    
    Lásd: [a replikációs kapcsolat](cross-region-replication-display-health-status.md) állapotának megjelenítése és a [replikálási társítás megszakítása](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. A kötetek replikálásának törléséhez válassza a forrás vagy a cél kötet **replikáció** elemét.  

2. Kattintson a **Törlés** gombra.    

3. A törlés megerősítéséhez írja be az **Igen értéket** , és kattintson a **Törlés** gombra.   

    ![Replikáció törlése](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Régiók közötti replikáció – replikálás](troubleshoot-cross-region-replication.md)


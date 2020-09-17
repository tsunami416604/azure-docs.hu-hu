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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708818"
---
# <a name="delete-replications"></a>Replikálások törlése

A replikálási kapcsolatot a forrás és a cél kötetek között a kötetek replikálásának törlésével állíthatja le. A törlési műveletet a forrás vagy a cél kötetről is végrehajthatja. A törlési művelet csak a replikálás engedélyezését távolítja el; nem távolítja el a forrást vagy a célként megadott kötetet. 

## <a name="steps"></a>Lépések

1. A kötetek replikálásának törléséhez válassza a forrás vagy a cél kötet **replikáció** elemét.  

2. Kattintson a **Törlés** gombra.    

3. A törlés megerősítéséhez írja be az **Igen értéket** , és kattintson a **Törlés**gombra.   

    ![Replikáció törlése](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Következő lépések  

* [Régiók közötti replikáció](cross-region-replication-introduction.md)
* [A régiók közötti replikáció használatára vonatkozó követelmények és szempontok](cross-region-replication-requirements-considerations.md)
* [Replikációs kapcsolat állapotának megjelenítése](cross-region-replication-display-health-status.md)
* [Régiók közötti replikáció – replikálás](troubleshoot-cross-region-replication.md)


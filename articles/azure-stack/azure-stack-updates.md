---
title: "Kezelheti a frissítéseket az Azure-verem áttekintés |} Microsoft Docs"
description: "További információk a frissítéskezelés integrált Azure verem rendszerekhez."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 3d0d5ea6cc3f3cc7bc0550b83dabbf0ae6af8a27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Kezelheti a frissítéseket a Azure verem – áttekintés

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A Microsoft Azure verem integrált rendszerek rendszeresen frissítési csomagokat ad ki. A Microsoft minden kiadása van kötegelt egyetlen frissítési csomag. Azure verem kezelőként könnyen importálhatja, telepítés és a figyelő a telepítési folyamat ezen a felügyeleti portálon-csomagok frissítése. 

Számítógépgyártó (OEM) hardvergyártójához is frissítéseket, például az illesztőprogram és a belső vezérlőprogram frissítések. Ezeket a frissítéseket a OEM hardver gyártójának külön csomag érkeznek, és a Microsoft-frissítésekből külön felügyelettel.

Hogy a rendszer a támogatás, mindig egy adott verziójához szint a frissített Azure verem. Győződjön meg arról, hogy tekintse át a [házirend karbantartási Azure verem](azure-stack-servicing-policy.md).

> [!NOTE]
> Azure verem szoftverfejlesztői készlet Azure verem frissítési csomagokat nem vonatkozik. A frissítési csomagok integrált rendszerek készültek.

## <a name="the-update-resource-provider"></a>A frissítés erőforrás-szolgáltató

Azure verem egy frissítés erőforrás-szolgáltató az alkalmazás a Microsoft szoftverfrissítések vezénylő tartalmazza. Az erőforrás-szolgáltató biztosítja, hogy minden fizikai gazdagép, Service Fabric-alkalmazások és futtatókörnyezetek, és minden infrastruktúra virtuális gépek és a kapcsolódó szolgáltatások frissítéseket alkalmazza.

Frissítések telepítése, mert a frissítési folyamat célként (például fizikai gazdagépek és virtuális gépek infrastruktúra) Azure verem különböző alrendszereket egyszerűen megtekintheti a magas szintű állapotát.

## <a name="plan-for-updates"></a>Frissítések tervezése

Határozottan javasoljuk, hogy a karbantartási műveleteket sem felhasználók értesítése, és a normál karbantartási időszak alatt munkaidőn kívüli lehetőség szerint úgy ütemezni. A karbantartási műveleteket hatással lehet a bérlői munkaterheléseket és a portál műveletek.

## <a name="using-the-update-tile-to-manage-updates"></a>A frissítés csempe használatával kezelheti a frissítéseket
Frissítések kezelése a felügyeleti portálon rendkívül egyszerű folyamat. A frissítés csempe az irányítópulton való lépjen Azure verem operátort:

- megtekintheti a fontos információkat, például a jelenlegi verziójával.
- Telepítse a frissítéseket, és figyelemmel az előrehaladást.
- Tekintse át a korábban telepített frissítések frissítési előzményeket.
 
## <a name="determine-the-current-version"></a>Határozza meg az aktuális verzió

A frissítés csempe Azure verem aktuális verzióját jeleníti meg. A frissítés csempe is elérheti a felügyeleti portálon az alábbi módszerek egyikével:

- Az irányítópult megtekintéséhez a jelenlegi verziót a **frissítés** csempére.
 
   ![Frissítések csempét alapértelmezett irányítópult](./media/azure-stack-updates/image1.png)
 
- Az a **régió felügyeleti** csempére, kattintson a régió nevét. Megtekintheti a jelenlegi verziót a **frissítés** csempére.

## <a name="next-steps"></a>Következő lépések

- [Az Azure verem karbantartása házirend](azure-stack-servicing-policy.md) 
- [Azure-készletben régió kezelése](azure-stack-region-management.md)     



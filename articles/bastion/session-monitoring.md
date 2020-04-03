---
title: Azure Bastion munkamenet figyelése és kezelése | Microsoft dokumentumok
description: Ebből a cikkből megtudhatja, hogyan választhat ki egy folyamatban lévő munkamenetet, és hogyan kapcsolhat le vagy törölheti azt.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619194"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Munkamenet-figyelés és -kezelés az Azure Bastion számára

Miután a megerősített szolgáltatás kivan építve és üzembe helyezve a virtuális hálózatban, használhatja azt zökkenőmentesen csatlakozni bármely virtuális gép ebben a virtuális hálózatban. Ahogy a felhasználók számítási feladatokhoz csatlakoznak, az Azure Bastion segítségével figyelheti a távoli munkameneteket, és gyors felügyeleti műveleteket tehet. Az Azure Bastion-munkamenet figyelése lehetővé teszi, hogy megtekintse, mely felhasználók melyik virtuális gépekhez csatlakoznak. Ez azt mutatja, az IP, hogy a felhasználó csatlakozik, mennyi ideig voltak csatlakoztatva, és mikor csatlakozik. A munkamenet-kezelési élmény lehetővé teszi, hogy kiválasszon egy folyamatban lévő munkamenetet, és kényszerítse le vagy törölje a munkamenetet, hogy leválassza a felhasználót a folyamatban lévő munkamenetről.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Távoli munkamenetek figyelése

1. Az [Azure Portalon](https://portal.azure.com)keresse meg az Azure Bastion erőforrást, és válassza **a Munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![Ülés](./media/session-monitoring/sessions.png)
2. A **Munkamenetek** lapon a folyamatban lévő távoli munkamenetek láthatók a jobb oldalon.

   ![munkamenet megtekintése](./media/session-monitoring/view-session.png)
3. A Távoli munkamenetek frissített listájának megtekintéséhez válassza a **Frissítés** lehetőséget. Amikor a Frissítés lehetőséget választja, az Azure Bastion lekéri a legújabb figyelési információkat, és frissíti azokat a portálon.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Engedélyezze a 4443-as portot a Gateway Manager bejövő forgalmához a munkamenet-figyelés működéséhez.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Folyamatban lévő távoli munkamenet törlése vagy leválasztásának kényszerítése

Kiválaszthatja a munkamenet(ek) készletét, és kényszerítheti leőket. A következő lépések bemutatják, hogyan lehet törölni a távoli munkameneteket:

1. Keresse meg az Azure Bastion erőforrást, és válassza **a Munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![navigate](./media/session-monitoring/navigate.png)
2. Miután kiválasztotta a Munkamenetek lehetőséget, megjelenik a távoli munkamenetek listája.

   ![munkamenetek listázása](./media/session-monitoring/list.png)
3. Jelöljön ki egy adott távoli munkamenetet, majd jelölje ki a három három pontot a munkamenetsor jobb oldalán, majd kattintson a **Törlés gombra.**

   ![delete](./media/session-monitoring/delete.png)
4. Ha a Törlés lehetőséget választja, a távoli munkamenet megszakad, és a távoli munkamenetben megjelenik a "Megszakadt a kapcsolat" üzenet.

   ![Húzza ki](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>További lépések

Olvassa el a [Bástya GYIK](bastion-faq.md).
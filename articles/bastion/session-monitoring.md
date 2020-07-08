---
title: Azure Bastion-munkamenet figyelése és kezelése | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan választhat ki egy folyamatban lévő munkamenetet, és hogyan kényszerítheti le vagy törölheti azt.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: 5974ebe7960eec1ca3bb8610f66061395fea64d6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744102"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Munkamenet-figyelés és-felügyelet az Azure Bastion szolgáltatásban

A megerősített szolgáltatás a virtuális hálózatban való üzembe helyezése és telepítése után zökkenőmentesen csatlakozhat a virtuális hálózat bármely virtuális GÉPHEZ. Ahogy a felhasználók munkaterhelésekhez csatlakoznak, az Azure Bastion használatával figyelheti a távoli munkameneteket, és gyors felügyeleti műveleteket hajthat végre. Az Azure megerősített munkamenet-figyelési funkcióval megtekintheti, hogy mely felhasználók csatlakoznak a virtuális gépekhez. Azt az IP-címet jeleníti meg, amelyhez a felhasználó csatlakozik, mennyi ideig csatlakoztatva van, és mikor kapcsolódtak. A munkamenet-kezelési élmény lehetővé teszi, hogy egy folyamatban lévő munkamenetet válasszon ki, és kényszerítse a kapcsolat bontását, vagy törölje a munkamenetet, hogy leválassza a felhasználót a folyamatban lévő munkamenetből.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Távoli munkamenetek figyelése

1. A [Azure Portal](https://portal.azure.com)navigáljon az Azure Bastion-erőforráshoz, és válassza a **munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![munkamenetek](./media/session-monitoring/sessions.png)
2. A **munkamenetek** lapon a jobb oldalon láthatja a folyamatban lévő távoli munkameneteket.

   ![munkamenet megtekintése](./media/session-monitoring/view-session.png)
3. Válassza a **frissítés** lehetőséget a távoli munkamenetek frissített listájának megtekintéséhez. Ha a frissítés lehetőséget választja, az Azure Bastion beolvassa a legfrissebb figyelési információkat, és frissíti azt a portálon.

   ![refresh](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Folyamatban lévő távoli munkamenet törlése vagy kényszerített leválasztása

Kiválaszthatja a munkamenet (ek) készletét, és kényszerítheti a leválasztást. A következő lépések bemutatják, hogyan törölhet távoli munkameneteket:

1. Navigáljon az Azure Bastion-erőforráshoz, és válassza a **munkamenetek** lehetőséget az Azure Bastion oldalon.

   ![navigate](./media/session-monitoring/navigate.png)
2. A munkamenetek kiválasztása után megtekintheti a távoli munkamenetek listáját.

   ![munkamenetek listázása](./media/session-monitoring/list.png)
3. Válasszon ki egy adott távoli munkamenetet, majd válassza ki a három ellipszist a munkamenet-sor jobb oldali végén, majd válassza a **Törlés**lehetőséget.

   ![delete](./media/session-monitoring/delete.png)
4. Ha a Törlés lehetőséget választja, a távoli munkamenet le lesz választva, és a felhasználó "megszakadt" üzenet jelenik meg a távoli munkamenetben.

   ![szétkapcsol](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>További lépések

Olvassa el a [megerősített GYIK](bastion-faq.md)-t.

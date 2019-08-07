---
title: A Fusion engedélyezése az Azure Sentinel előzetes verziójában | Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti a fúziót az Azure Sentinelben.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: f90bb86632886d8a2ca6c6e8c60d3b79f5d0b9e9
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780443"
---
# <a name="enable-fusion"></a>Egyesítés engedélyezése

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure Sentinel Machine Learning az elejétől kezdve beépített. A rendszerek elsajátítása és a biztonsági elemzők, a biztonsági adatszakértők és a mérnökök számára is hatékony volt. Az egyik ilyen innováció az Azure Sentinel Fusion, amely különösen a riasztások fáradtságának csökkentésére készült.

A Fusion Graph-alapú gépi tanulási algoritmusokat használ a különböző termékekből, például Azure AD Identity Protectionokból és Microsoft Cloud App Securityokból származó alacsonyabb hűségű rendellenes tevékenységek millióinak összekapcsolásához, hogy azok kezelhető számú érdekes biztonsági incidensek.

## <a name="enable-fusion"></a>Egyesítés engedélyezése

1. A Azure Portal Cloud Shell megnyitásához válassza a ikont.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Az itt megjelenő **üdvözöljük Cloud Shell** Windows rendszerben válassza a PowerShell lehetőséget.

3.  Válassza ki azt az előfizetést, amelyen üzembe helyezte az Azure Sentinelt, és **hozzon létre**egy tárolót.

4. Miután elvégezte a hitelesítést, és létrehozta az Azure-meghajtót, futtassa a következő parancsokat a parancssorban:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>A Fusion letiltása

Kövesse a fenti eljárást, és futtassa a következő parancsot:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>A Fusion állapotának megtekintése

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtanulta, hogyan engedélyezheti a fúziót az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).


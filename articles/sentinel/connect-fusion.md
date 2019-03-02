---
title: Az Azure-on Előzetesben Sentinel-fusion engedélyezése |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezheti az Azure-Sentinel fusion.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 82becf50-6628-47e4-b3d7-18d7d72d505f
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 55d569d4a993a725137d7bfab37c113147fe81ef
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242462"
---
# <a name="enable-fusion"></a>Egyesítés engedélyezése

> [!IMPORTANT]
> Az Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Azure-Sentinel Machine Learning szolgáltatás beépített közvetlenül az elejétől. Alkalmazkodva alakítottuk átgondolt a rendszer a célja, hogy adatbiztonsági elemzők, adatelemzők biztonsági és mérnökök hatékony gépi Tanulási innovációkat. Egy ilyen innováció Azure Sentinel-Fusion beépített, különösen a riasztási fáradás csökkentése érdekében.

Fusion működő graph gépi tanulási algoritmusok használatával korrelációját, ha alacsonyabb pontosságú rendellenes tevékenységek a különböző termékek, például az Azure AD Identity Protection millió, és a Microsoft Cloud App Security, úgy, hogy kezelhető számos között biztonsági esetek érdekes.

## <a name="enable-fusion"></a>Egyesítés engedélyezése

1. Az Azure Portalon válassza ki a ikonra kattintva nyissa meg a Cloud Shellben.
  ![Cloud Shell](./media/connect-fusion/cloud-shell.png)

2.  Az a **üdvözli a Cloud Shell** , amely megnyitja az alábbi windows PowerShell válassza ki.

3.  Válassza ki az előfizetést, amelyen üzembe helyezett Azure Sentinel, és **tároló létrehozása**.

4. Miután végzett a hitelesítéssel és az Azure-meghajtó épül, a parancssorban futtassa a következő parancsokat:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=true --subscription "{Subscription Guid}"

## <a name="disable-fusion"></a>Fusion Tables letiltása

Kövesse a fent ismertetett eljárást, és futtassa a következő parancsot:

            az resource update --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --set properties.IsEnabled=false --subscription "{Subscription Guid}"

## <a name="view-the-status-of-fusion"></a>Fusion Tables állapotának megtekintése

            az resource show --ids /subscriptions/{Subscription Guid}/resourceGroups/{Log analytics resource Group Name}/providers/Microsoft.OperationalInsights/workspaces/{Log analytics workspace Name}/providers/Microsoft.SecurityInsights/settings/Fusion --api-version 2019-01-01-preview --subscription "{Subscription Guid}"


## <a name="next-steps"></a>További lépések

Ebben a dokumentumban megtudhatta, hogyan engedélyezése az Azure-Sentinel Fusion. Azure-Sentinel kapcsolatos további információkért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan [betekintést nyerhet az adatok és a potenciális fenyegetések](quickstart-get-visibility.md).
- Első lépések [Azure Sentinel-fenyegetések észlelése](tutorial-detect-threats.md).


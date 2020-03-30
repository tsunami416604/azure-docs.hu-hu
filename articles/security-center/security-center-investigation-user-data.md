---
title: Az Azure Security Center-vizsgálat során talált felhasználói adatok kezelése
description: " Ismerje meg, hogyan kezelheti az Azure Security Center vizsgálati funkciójában található felhasználói adatokat. "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979246"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Az Azure Security Center-vizsgálat során talált felhasználói adatok kezelése
Ez a cikk az Azure Security Center vizsgálati szolgáltatásában található felhasználói adatok kezelésével kapcsolatos információkat tartalmaz. A vizsgálati adatokat az [Azure Monitor naplói](../log-analytics/log-analytics-overview.md) tárolják, és a Security Centerben teszik elérhetővé. A felhasználói adatok kezelése magában foglalja az adatok törlését vagy exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Személyes adatok keresése és azonosítása
Az Azure Portalon a Security Center [vizsgálati funkciójával](../security-center/security-center-investigation.md) személyes adatokat kereshet. A vizsgálati funkció a **Biztonsági riasztások**szolgáltatás ban érhető el.

A vizsgálati funkció az összes entitást, felhasználói információt és adatot jeleníti meg az **Entitások** lapon.

## <a name="securing-and-controlling-access-to-personal-information"></a>Személyes információk védelme és a hozzáférés szabályozása
A Reader, Owner, Contributor vagy Account Administrator szerepkörrel rendelkező biztonsági központ-felhasználó hozzáférhet az ügyféladatokhoz az eszközön belül.

Az Olvasó, a tulajdonos és a közreműködői szerepkörökről az [Azure szerepköralapú hozzáférés-vezérlésének beépített szerepkörei](../role-based-access-control/built-in-roles.md) című témakörben olvashat bővebben. Ha többet szeretne megtudni a Fiókfelügyelő szerepkörről, tekintse meg az [Azure-előfizetés-rendszergazdákat.](../cost-management-billing/manage/add-change-subscription-administrator.md)

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A Tulajdonos, Közreműködő vagy fiókfelügyelő szerepkörrel elosztott biztonsági központ-felhasználó törölheti a vizsgálati adatokat.

A vizsgálat törléséhez kérelmet `DELETE` küldhet az Azure Resource Manager REST API-nak:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A `incidentName` bemenet megtalálható az összes incidens `GET` felsorolásával egy kérés használatával:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Tulajdonos, Közreműködő vagy fiókadminisztrátor szerepkörrel elosztott biztonsági központ-felhasználó exportálhatja a vizsgálati adatokat. A vizsgálati adatok exportálásához nyissa meg az **Entitások** lapot a vonatkozó információk másolásához és beillesztéséhez.

## <a name="next-steps"></a>További lépések
A felhasználói adatok kezeléséről a [Felhasználói adatok kezelése az Azure Security Centerben](security-center-privacy.md)című témakörben talál további információt.
Ha többet szeretne tudni a személyes adatok azure-figyelőnaplókban való törléséről, olvassa [el a Személyes adatok exportálása és törlése.](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)

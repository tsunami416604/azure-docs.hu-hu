---
title: Felhasználói adatokat az Azure Security Center vizsgálat található |} A Microsoft Docs
description: " Ismerje meg, hogyan kezelheti a felhasználói adatokat az Azure Security Center vizsgálati funkciójáról található. "
services: operations-management-suite
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: rkarlin
ms.openlocfilehash: 81bd34cdbe35f3e12d5afddc929b0fac7631f4cc
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113073"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>Az Azure Security Center vizsgálat található felhasználói adatok kezelése
Ez a cikk az Azure Security Center vizsgálati funkciójáról található felhasználói adatok kezelése ismertetése. Vizsgálat adatok tárolása [Azure Log Analytics](../log-analytics/log-analytics-overview.md) és a Security Centerben elérhető. Felhasználói adatok kezelése lehetőség törölje vagy exportálhat adatokat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Keresse meg, majd a személyes adatok azonosítása
Az Azure Portalon is használhatja a Security Center [vizsgálati funkciójáról](../security-center/security-center-investigation.md) személyes adatok kereséséhez. A vizsgálat funkció érhető el **biztonsági riasztások**.

A vizsgálati funkciójáról jeleníti meg minden entitás, felhasználói adatok és az adatok a **entitások** fülre.

## <a name="securing-and-controlling-access-to-personal-information"></a>Biztonságossá tétele és a személyes információkhoz való hozzáférés szabályozása
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája hozzáférhet a vásárlói adatokat az eszköz belül.

Lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md) tudhat meg többet az olvasó, a tulajdonos és közreműködő szerepkört. Lásd: [Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) további információ a fiók rendszergazdai szerepkörrel.

## <a name="deleting-personal-data"></a>Személyes adatok törléséről
A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy a fiók rendszergazdája törölheti a vizsgálat információkat.

Törli a vizsgálatot, küldhet egy `DELETE` kérést az Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A `incidentName` használatával minden incidens listázásával található bemenet egy `GET` kérelem:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy a fiók rendszergazdája exportálhatja a vizsgálat információkat. Vizsgálat információk exportálásához nyissa meg a **entitások** lapon másolja és illessze be a vonatkozó információkat.

## <a name="next-steps"></a>További lépések
Felhasználói adatok kezelésével kapcsolatos további információkért lásd: [felhasználói adatokat az Azure Security Centerben](security-center-privacy.md).
Személyes adatokat a Log Analytics törlésével kapcsolatos további tudnivalókért lásd: [és törlése a személyes adatok exportálása](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data).

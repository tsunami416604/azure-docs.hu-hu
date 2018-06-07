---
title: Felhasználói adatokat az Azure Security Center vizsgálat található |} Microsoft Docs
description: " Tudnivalók a felhasználói adatokat az Azure Security Center vizsgálat szolgáltatás található. "
services: operations-management-suite
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 6685db4eeda72928753c74c64b4b26539ccb1eb9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655225"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>A vizsgálat során az Azure Security Center található felhasználói adatok kezelése
Ez a cikk bemutatja, hogy miként kezelheti a felhasználói adatokat az Azure Security Center vizsgálat szolgáltatás található. Vizsgálat adat [Azure Naplóelemzés](../log-analytics/log-analytics-overview.md) és a Security Center felfedett. Felhasználói adatok kezelése lehetővé teszi a törlésére, vagy exportálhatja az adatokat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>Keres, és a személyes adatok azonosítása
Az Azure-portálon a Security Center használható [vizsgálat szolgáltatás](../security-center/security-center-investigation.md) személyes adatok kereséséhez. A vizsgálat funkció érhető el a **biztonsági riasztások**.

A vizsgálat funkció jeleníti meg az összes entitásokat, felhasználói adatok és az adatok a **entitások** fülre.

## <a name="securing-and-controlling-access-to-personal-information"></a>Védelme és személyes információkhoz való hozzáférés vezérlése
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a Fiókadminisztrátor férhet hozzá a felhasználói adatokhoz belül az eszköz.

Lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../role-based-access-control/built-in-roles.md) további információt az olvasó, a tulajdonos és közreműködő szerepkört. Lásd: [Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) további információt a fiók rendszergazdai szerepkörrel.

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy a fiók rendszergazda törölheti a vizsgálat információkat.

Törli a vizsgálatot, elküldheti a `DELETE` kérelmet az Azure Resource Manager REST API:

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

A `incidentName` bemeneti található összes incidens használja listáját egy `GET` kérelem:

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy Fiókadminisztrátort exportálhatja a vizsgálat információkat. Vizsgálat információk exportálásához nyissa meg a **entitások** lapon, a vonatkozó információk másolását és beillesztését.

## <a name="next-steps"></a>További lépések
Felhasználói adatok kezelésével kapcsolatos további információkért lásd: [felhasználói adatokat az Azure Security Centerben](security-center-privacy.md).

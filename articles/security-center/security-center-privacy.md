---
title: Felhasználói adatokat az Azure Security Centerben |} A Microsoft Docs
description: " Ismerje meg, hogyan kezelheti a felhasználói adatokat az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: fcaac3d248b676e4b7b1fe0344b54e52ce1fb558
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302673"
---
# <a name="manage-user-data-in-azure-security-center"></a>Felhasználói adatokat az Azure Security Centerben
Ez a cikk ismerteti, hogyan kezelheti a felhasználói adatokat az Azure Security Centerben. Felhasználói adatok kezelése lehetőség eléréséhez, törléséhez vagy exportálhat adatokat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája hozzáférhet a vásárlói adatokat az eszköz belül. Lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md) tudhat meg többet az olvasó, a tulajdonos és közreműködő szerepkört. Lásd: [Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) további információ a fiók rendszergazdai szerepkörrel.

## <a name="searching-for-and-identifying-personal-data"></a>Keresse meg, majd a személyes adatok azonosítása
A Security Center felhasználó megtekintheti a személyes adataikat az Azure Portalon keresztül. A Security Center csak tárolja a biztonsági kapcsolattartói adatok, például az e-mail-címeket és telefonszámokat. Lásd: [az Azure Security Center biztonsági kapcsolattartói adatok megadása](security-center-provide-security-contact-details.md) további információt.

Az Azure Portalon a felhasználó megtekintheti a Security Center használatával csak az time VM access funkció engedélyezett IP-konfigurációk. További információkért lásd: [A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáféréssel](security-center-just-in-time.md).

Az Azure Portalon a felhasználók megtekinthetik az IP-címek és a támadó részletek többek között a Security Center által biztosított biztonsági riasztásokat. Lásd: [kezelése és válaszadás a biztonsági riasztásokra az Azure Security Center](security-center-managing-and-responding-alerts.md) további információt.

## <a name="classifying-personal-data"></a>Személyes adatok besorolása
Nem kell besorolni a Security Center biztonsági kapcsolattartó szolgáltatás található személyes adatok. Az adatok mentve egy e-mail-cím (vagy több e-mail-cím) és egy telefonszám. [Lépjen kapcsolatba az adatok](security-center-provide-security-contact-details.md) a Security Center által.

Nem kell az IP-címek besorolását, és a Security Center által mentett portszámokat [szerinti](security-center-just-in-time.md) funkció.

Csak a felhasználó a rendszergazda szerepkörrel osztályozhat személyes adatokat [riasztások](security-center-managing-and-responding-alerts.md) a Security Centerben.

## <a name="securing-and-controlling-access-to-personal-data"></a>Biztonságossá tétele és a személyes adatokhoz való hozzáférés szabályozása
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája hozzáférhet [biztonsági kapcsolattartói adatok](security-center-provide-security-contact-details.md).

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája hozzáférhet a [szerinti](security-center-just-in-time.md) házirendeket.

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája tekintheti meg azok [riasztások](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Személyes adatok frissítése
A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy a fiók rendszergazdája frissítheti [biztonsági kapcsolattartói adatok](security-center-provide-security-contact-details.md) az Azure Portalon keresztül.

A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy a fiók rendszergazdája frissítheti azok [csak az idő házirendek](security-center-just-in-time.md).

Egy fiók rendszergazdai riasztás incidensek nem szerkeszthető. Egy [riasztásokat szinkronizáló](security-center-managing-and-responding-alerts.md) számít a biztonsági adatokat, és csak olvasható.

## <a name="deleting-personal-data"></a>Személyes adatok törléséről
A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy törölhetik Fiókadminisztrátort [biztonsági kapcsolattartói adatok](security-center-provide-security-contact-details.md) az Azure Portalon keresztül.

A Security Center felhasználói szerepkörrel a tulajdonos, közreműködő, vagy a fiók rendszergazdája törölheti a [csak az idő házirendek](security-center-just-in-time.md) az Azure Portalon keresztül.

A Security Center felhasználó riasztási incidensek nem lehet törölni. Biztonsági igényeinek, mert egy [riasztásokat szinkronizáló](security-center-managing-and-responding-alerts.md) számít a csak olvasható adatok.

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája is exportálhat [biztonsági kapcsolattartói adatok](security-center-provide-security-contact-details.md) szerint:

- A másolási végrehajtása az Azure Portalról
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

A Security Center a fiók rendszergazdája szerepkörrel exportálhatják a [csak az idő házirendek](security-center-just-in-time.md) tartalmazó IP-címeinek:

- A másolási végrehajtása az Azure Portalról
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Egy fiók rendszergazdája által a riasztási adatok exportálásához:

- A másolási végrehajtása az Azure Portalról
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Lásd: [lekérése a biztonsági riasztások (gyűjtemény beolvasása)](https://msdn.microsoft.com/library/mt704050.aspx) további információt.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>A személyes adatok használatát a profilkészítés vagy hozzájárulása nélkül marketing korlátozása
A Security Center a felhasználó törlésével vesznek dönthet a [biztonsági kapcsolattartói adatok](security-center-provide-security-contact-details.md).

[Csak az idő adatok](security-center-just-in-time.md) számít nem alkalmas adatokat, és őrződnek meg egy 30 napos időszak.

[Riasztási adatokat](security-center-managing-and-responding-alerts.md) biztonsági adatok számít, és a két évig őrzi meg a rendszer.

## <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés
A Naplók biztonsági kapcsolattartó, igény szerint, és riasztást küldjön, frissítések karbantartása [Azure-tevékenységnaplóinak](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>További lépések
Felhasználói adatok kezelésével kapcsolatos további információkért lásd: [felhasználói adatokat az Azure Security Center vizsgálat található](security-center-investigation-user-data.md).

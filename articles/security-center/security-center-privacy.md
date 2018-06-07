---
title: Felhasználói adatokat az Azure Security Centerben |} Microsoft Docs
description: " Tudnivalók a felhasználói adatokat az Azure Security Centerben. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 2495bae5c63cdafe049ec39f71ab53106c5f2df7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655229"
---
# <a name="manage-user-data-in-azure-security-center"></a>Felhasználói adatokat az Azure Security Centerben
Ez a cikk bemutatja, hogyan kezelheti a felhasználói adatokat az Azure Security Centerben. Felhasználói adatok kezelése lehetővé teszi a hozzáférést, törölje vagy exportálhatja az adatokat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a Fiókadminisztrátor férhet hozzá a felhasználói adatokhoz belül az eszköz. Lásd: [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörök](../role-based-access-control/built-in-roles.md) további információt az olvasó, a tulajdonos és közreműködő szerepkört. Lásd: [Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) további információt a fiók rendszergazdai szerepkörrel.

## <a name="searching-for-and-identifying-personal-data"></a>Keres, és a személyes adatok azonosítása
A Security Center felhasználó megtekintheti a személyes adataik az Azure portálon keresztül. A Security Center biztonsági kapcsolattartási adatait, például az e-mail-címeket és telefonszámokat csak tárolja. Lásd: [adja meg az Azure Security Centerben a biztonsági kapcsolattartási adatait](security-center-provide-security-contact-details.md) további információt.

Az Azure-portálon a felhasználók megtekinthetik a Security Center csak idő VM hozzáférési funkciónak használatával engedélyezett IP-konfigurációk. További információkért lásd: [A virtuális gépekhez való hozzáférés kezelése igény szerinti hozzáféréssel](security-center-just-in-time.md).

Az Azure portálon a felhasználók megtekinthetik az IP-címek és a támadó részletek többek között a Security Center által biztosított biztonsági riasztások. Lásd: [kezelése és az Azure Security Centerben a biztonsági riasztásokra való reagálásról](security-center-managing-and-responding-alerts.md) további információt.

## <a name="classifying-personal-data"></a>Személyes adatok besorolása
Nem található a Security Center biztonsági kapcsolattartási szolgáltatás személyes adatok osztályozására szolgáló igényelnek. A mentett adatok egy e-mail címet (vagy több e-mail cím) és telefonszámát. [Lépjen kapcsolatba az adatok](security-center-provide-security-contact-details.md) Security Center érvényesíti.

Nem kell az IP-címek besorolásához, és a Security Center által mentett portszámokat [JIT](security-center-just-in-time.md) szolgáltatás.

Csak a felhasználó a rendszergazda szerepkörrel sorolhatja be a személyes adatok által [riasztások megtekintése](security-center-managing-and-responding-alerts.md) a biztonsági központban.

## <a name="securing-and-controlling-access-to-personal-data"></a>Védelme és személyes adatokhoz való hozzáférés vezérlése
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a Fiókadminisztrátor férhet [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md).

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája hozzáférhet a [JIT](security-center-just-in-time.md) házirendek.

A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdája tekintheti a [riasztások](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Személyes adatok frissítése
A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy a fiók rendszergazdája frissítheti [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md) az Azure-portálon.

A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy a fiók rendszergazdája frissítheti az [csak az idő házirendek](security-center-just-in-time.md).

A fiók rendszergazda figyelmeztetési események nem szerkeszthető. Egy [a riasztásokat szinkronizáló](security-center-managing-and-responding-alerts.md) tekinthető biztonsági adatokat, és csak olvasható.

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy törölheti a Fiókadminisztrátor [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md) az Azure-portálon.

A Security Center felhasználói szerepkörrel tulajdonos, közreműködő, vagy a fiók rendszergazda törölheti a [csak az idő házirendek](security-center-just-in-time.md) az Azure-portálon.

A Security Center felhasználó figyelmeztetési események nem törölhető. Biztonsági igényeket elégíti ki, mert egy [a riasztásokat szinkronizáló](security-center-managing-and-responding-alerts.md) csak adatolvasási számít.

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Security Center felhasználó olvasó, a tulajdonos, közreműködő szerepkörrel, vagy a fiók rendszergazdájához exportálhatja [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md) szerint:

- A másolási végrehajtása az Azure-portálon
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

A Security Center felhasználói fiók rendszergazdai szerepkörrel exportálhatja a [csak az idő házirendek](security-center-just-in-time.md) az IP-cím tartalmazó nyújtja:

- A másolási végrehajtása az Azure-portálon
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

A fiók rendszergazda exportálhatja a riasztás részletei:

- A másolási végrehajtása az Azure-portálon
- Az Azure REST API-hívás, GET HTTP végrehajtása:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Lásd: [biztonsági riasztások beolvasása (GET gyűjtemény)](https://msdn.microsoft.com/library/mt704050.aspx) további információt.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>A személyes adatok profilkészítési vagy hozzájárulása nélkül marketing használatának korlátozása
A Security Center felhasználó törlésével nem vesznek választhat a [biztonsági kapcsolattartási adatok](security-center-provide-security-contact-details.md).

[Csak a időre vonatkozó adatok](security-center-just-in-time.md) nem azonosítható adatok minősül, és 30 napig őrzi meg.

[Riasztási adatokat](security-center-managing-and-responding-alerts.md) tekinthető biztonsági adatokat, és a két évig őrzi meg.

## <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés
Naplók biztonsági kapcsolódási, igény szerint, és riasztást küldjön, frissítések karbantartása [Azure tevékenységi naplóit](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>További lépések
Felhasználói adatok kezelésével kapcsolatos további információkért lásd: [felhasználói adatokat az Azure Security Center vizsgálat található](security-center-investigation-user-data.md).

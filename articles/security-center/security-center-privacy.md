---
title: Felhasználói adatkezelés Azure Security Centerban | Microsoft Docs
description: " Megtudhatja, hogyan kezelheti Azure Security Center felhasználói információit. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: aab2535eb44ca5007859c71bf1f8142b26de7d14
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996737"
---
# <a name="manage-user-data-in-azure-security-center"></a>Felhasználói adatkezelés Azure Security Center
Ez a cikk azt ismerteti, hogyan kezelheti a felhasználói adatokat Azure Security Centerban. A felhasználói adatkezelési funkció magában foglalja az adatelérését, törlését és exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó az eszközön belül férhet hozzá az ügyféladatok eléréséhez. Az olvasói, a tulajdonosi és a közreműködő szerepkörökkel kapcsolatos további tudnivalókért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés beépített szerepköreit](../role-based-access-control/built-in-roles.md) . Az [Azure-előfizetés rendszergazdái](../billing/billing-add-change-azure-subscription-administrator.md) további információkat tudhatnak meg a fiók rendszergazdai szerepköréről.

## <a name="searching-for-and-identifying-personal-data"></a>Személyes adatkeresés és-azonosítás
A Security Center felhasználók a Azure Portal keresztül tekinthetik meg a személyes adatfájljaikat. Security Center csak a biztonsági kapcsolattartási adatokat, például az e-mail-címeket és a telefonszámokat tárolja. További információért lásd: a [biztonsági kapcsolattartási adatok megadása Azure Security Center](security-center-provide-security-contact-details.md) .

A Azure Portalban a felhasználók az engedélyezett IP-konfigurációkat Security Center az igény szerinti virtuálisgép-hozzáférési funkciójával tekinthetik meg. További információért lásd: [virtuális gépek hozzáférésének kezelése igény szerint](security-center-just-in-time.md) .

A Azure Portal a felhasználók megtekinthetik Security Center által biztosított biztonsági riasztásokat, beleértve az IP-címeket és a támadó adatait. További információért lásd: a [biztonsági riasztások kezelése és válaszadás a Azure Security Center](security-center-managing-and-responding-alerts.md) .

## <a name="classifying-personal-data"></a>Személyes adatosztályozás
Nem szükséges a Security Center biztonsági kapcsolattartási szolgáltatásában található személyes adatosztályozás. A mentett adatmennyiség egy e-mail-cím (vagy több e-mail-cím) és egy telefonszám. A [kapcsolattartási adatforrásokat](security-center-provide-security-contact-details.md) a Security Center érvényesíti.

Nem kell besorolnia a [Security Center igény szerinti funkciója által](security-center-just-in-time.md) mentett IP-címeket és portszámokat.

A riasztások Security Centerban való [megtekintésével](security-center-managing-and-responding-alerts.md) csak a rendszergazda szerepkörhöz rendelt felhasználó tud kategorizálni személyes adatmennyiséget.

## <a name="securing-and-controlling-access-to-personal-data"></a>Személyes adatelérés biztosítása és szabályozása
Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó hozzáférhet a [biztonsági kapcsolattartási adathoz](security-center-provide-security-contact-details.md).

Egy Security Center felhasználó hozzárendelte az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája szerepkört az igény [szerinti szabályzatokhoz](security-center-just-in-time.md) .

Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó megtekintheti a [riasztásokat](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Személyes adatok frissítése
A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználók a Azure Portal használatával frissíthetik a [biztonsági kapcsolattartási](security-center-provide-security-contact-details.md) adatvédelmet.

A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználó frissítheti az igény szerinti [házirendeket](security-center-just-in-time.md).

A fiók rendszergazdája nem szerkesztheti a riasztási incidenseket. A [riasztási incidensek](security-center-managing-and-responding-alerts.md) biztonsági adatként számítanak, és csak olvashatók.

## <a name="deleting-personal-data"></a>Személyes adatok törléséről
A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználók a Azure Portal használatával törölhetik a [biztonsági kapcsolattartási](security-center-provide-security-contact-details.md) adatvédelmet.

A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználó a Azure Portal használatával törölheti az igény szerinti [házirendeket](security-center-just-in-time.md) .

Egy Security Center felhasználó nem törölhet riasztási incidenseket. Biztonsági igények miatt a [riasztási incidens](security-center-managing-and-responding-alerts.md) csak olvasható adatként szerepel.

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó a következőket teheti a [biztonsági kapcsolattartási adat](security-center-provide-security-contact-details.md) exportálásával:

- Másolat végrehajtása a Azure Portal
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

A rendszergazda szerepkört hozzárendelő Security Center felhasználók az IP-címeket tartalmazó igény szerinti [szabályzatokat](security-center-just-in-time.md) az alábbiak szerint exportálhatók:

- Másolat végrehajtása a Azure Portal
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

A fiók rendszergazdája a következőket végezheti el a riasztás részleteinek exportálásával:

- Másolat végrehajtása a Azure Portal
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

További információt a [biztonsági riasztások beolvasása (gyűjtemény BEolvasása)](https://msdn.microsoft.com/library/mt704050.aspx) című témakörben talál.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Személyes adatfelhasználás korlátozása profilkészítés vagy marketing jóváhagyás nélkül
Egy Security Center felhasználó dönthet úgy, hogy törli a [biztonsági kapcsolattartási adatvédelmet](security-center-provide-security-contact-details.md).

Az igény szerinti [adatok](security-center-just-in-time.md) nem azonosítható adatoknak minősülnek, és 30 napig őrzik meg őket.

A [riasztási adat](security-center-managing-and-responding-alerts.md) biztonsági adatnak minősül, és két évig őrzi meg a rendszer.

## <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés
A biztonsági kapcsolattartási naplókat, az igény szerinti és a riasztási frissítéseket a rendszer az [Azure-tevékenységek naplóiban](../azure-monitor/platform/activity-logs-overview.md)tartja karban.

## <a name="next-steps"></a>További lépések
A felhasználói adatok kezelésével kapcsolatos további információkért lásd: [Azure Security Center vizsgálatban talált felhasználói adatok kezelése](security-center-investigation-user-data.md).

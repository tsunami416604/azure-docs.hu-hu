---
title: Felhasználói adatok kezelése az Azure Security Centerben | Microsoft dokumentumok
description: Ismerje meg, hogyan kezelheti a felhasználói adatokat az Azure Security Centerben. A felhasználói adatok kezelése magában foglalja az adatok elérését, törlését vagy exportálását.
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
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585974"
---
# <a name="manage-user-data-in-azure-security-center"></a>Felhasználói adatok kezelése az Azure Security Centerben
Ez a cikk arról tartalmaz, hogyan kezelheti a felhasználói adatokat az Azure Security Centerben. A felhasználói adatok kezelése magában foglalja az adatok elérését, törlését vagy exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

A Reader, Owner, Contributor vagy Account Administrator szerepkörrel rendelkező biztonsági központ-felhasználó hozzáférhet az ügyféladatokhoz az eszközön belül. Ha többet szeretne megtudni a Fiókfelügyelő szerepkörről, olvassa [el az Azure szerepköralapú hozzáférés-vezérlés beépített szerepköreit,](../role-based-access-control/built-in-roles.md) ha többet szeretne megtudni az Olvasó, a tulajdonos és a közreműködői szerepkörökről. Lásd: [Azure-előfizetés-rendszergazdák](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Személyes adatok keresése és azonosítása
A Security Center-felhasználók megtekinthetik a személyes adataikat az Azure Portalon keresztül. A Security Center csak a biztonsági kapcsolattartási adatokat tárolja, például az e-mail címeket és a telefonszámokat. További információ: [Biztonsági kapcsolattartási adatok biztosítása az Azure Security Centerben.](security-center-provide-security-contact-details.md)

Az Azure Portalon a felhasználó megtekintheti az engedélyezett IP-konfigurációk a Security Center just-in-time virtuális gép hozzáférési szolgáltatás használatával. További információt a [Virtuálisgép-hozzáférés kezelése just-in-time használatával című témakörben](security-center-just-in-time.md)talál.

Az Azure Portalon a felhasználó megtekintheti a Security Center által biztosított biztonsági riasztásokat, beleértve az IP-címeket és a támadó adatait. További információt a [Biztonsági riasztások kezelése és megválaszolása az Azure Security Centerben című témakörben talál.](security-center-managing-and-responding-alerts.md)

## <a name="classifying-personal-data"></a>A személyes adatok osztályozása
Nem kell osztályoznia a Security Center biztonsági kapcsolattartó funkciójában található személyes adatokat. A mentett adatok egy e-mail cím (vagy több e-mail cím) és egy telefonszám. [A kapcsolattartási adatokat](security-center-provide-security-contact-details.md) a Security Center ellenőrzi.

Nem kell osztályoznia a Security Center [just-in-time](security-center-just-in-time.md) szolgáltatása által mentett IP-címeket és portszámokat.

Csak a rendszergazdai szerepkörrel hozzárendelt felhasználó osztályozhatja a személyes adatokat a Biztonsági központban található [riasztások megtekintésével.](security-center-managing-and-responding-alerts.md)

## <a name="securing-and-controlling-access-to-personal-data"></a>A személyes adatokhoz való hozzáférés biztosítása és ellenőrzése
A Reader, Owner, Contributor vagy Account Administrator szerepkörrel rendelkező biztonsági központ-felhasználó hozzáférhet a [biztonsági kapcsolattartási adatokhoz.](security-center-provide-security-contact-details.md)

A Reader, Owner, Contributor vagy Account Administrator szerepkörrel rendelkező biztonsági központ-felhasználó hozzáférhet [a just-in-time](security-center-just-in-time.md) szabályzataikhoz.

A Biztonsági központ olvasói, tulajdonosi, közreműködői vagy fiókadminisztrátori szerepkörrel bíró felhasználója megtekintheti [a riasztásokat.](security-center-managing-and-responding-alerts.md)

## <a name="updating-personal-data"></a>Személyes adatok frissítése
A Security Center-felhasználó a tulajdonos, közreműködő vagy a fiókadminisztrátor szerepköréhez rendelt rendszergazda az Azure Portalon keresztül frissítheti a [biztonsági kapcsolattartási adatokat.](security-center-provide-security-contact-details.md)

A Tulajdonos, közreműködő vagy fiókfelügyelő szerepkörrel elosztott biztonsági központ-felhasználó frissítheti [a just-in-time házirendjeit.](security-center-just-in-time.md)

A fiókadminisztrátorok nem szerkeszthetik a riasztási incidenseket. A [riasztási incidens](security-center-managing-and-responding-alerts.md) biztonsági adatnak minősül, és csak olvasható.

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A Tulajdonos, közreműködő vagy fiókrendszergazda szerepkörrel hozzárendelt Security Center-felhasználó törölheti a [biztonsági kapcsolattartóadatait](security-center-provide-security-contact-details.md) az Azure Portalon keresztül.

A Tulajdonos, közreműködő vagy fiókfelügyelő szerepkörrel hozzárendelt security center-felhasználó törölheti a [just-in-time szabályzatokat](security-center-just-in-time.md) az Azure Portalon keresztül.

A Security Center felhasználói nem törölhetik a riasztási incidenseket. Biztonsági okokból a [riasztási incidens](security-center-managing-and-responding-alerts.md) csak olvasható adatnak minősül.

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
A Reader, Owner, Contributor vagy Account Administrator szerepkörrel elosztott biztonsági kapcsolattartó-felhasználók a következő képpen exportálhatják [a biztonsági kapcsolattartóadatait:](security-center-provide-security-contact-details.md)

- Másolás az Azure Portalról
- Executing the Azure REST API call, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

A Fiókfelügyelő szerepkörrel hozzárendelt Biztonsági központ-felhasználó exportálhatja az IP-címeket tartalmazó [just-in-time házirendeket:](security-center-just-in-time.md)

- Másolás az Azure Portalról
- Executing the Azure REST API call, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

A fiókadminisztrátorok a következők szerint exportálhatják a riasztás részleteit:

- Másolás az Azure Portalról
- Executing the Azure REST API call, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

További információt a [Biztonsági riasztások (GET Gyűjtemény) begyűjtése című témakörben talál.](https://msdn.microsoft.com/library/mt704050.aspx)

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>A személyes adatok profilalkotásvagy marketing célú felhasználásának korlátozása hozzájárulás nélkül
A Security Center felhasználói leiratkozhatnak a [biztonsági kapcsolattartási adataik](security-center-provide-security-contact-details.md)törlésével.

[A just-in-time adatok](security-center-just-in-time.md) nem azonosítható adatoknak minősülnek, és 30 napig őrződnek meg.

[A riasztási adatok](security-center-managing-and-responding-alerts.md) biztonsági adatnak minősülnek, és két évig megőrződnek.

## <a name="auditing-and-reporting"></a>Könyvvizsgálat és jelentéstétel
A biztonsági kapcsolattartó, a just-in-time és a riasztási frissítések naplói az [Azure-tevékenységnaplókban](../azure-monitor/platform/platform-logs-overview.md)találhatók.
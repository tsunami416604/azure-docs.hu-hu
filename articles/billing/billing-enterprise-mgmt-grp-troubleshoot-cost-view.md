---
title: Hibaelhárítás Azure vállalati költségek nézet
description: Megtudhatja, hogyan lehetséges, hogy az Azure Portalon szervezeti költség nézetekkel kapcsolatos problémák megoldásához.
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491273"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Vállalati költségek nézet hibaelhárítása

Vállalati belépéseket, belül számos beállítás, amely a felhasználót a regisztrációs belül nem jelenik meg a költségek tartoznak.  Ezek a beállítások a beléptetés adminisztrátora kezeli. Vagy, ha a regisztráció nem vásárolta meg közvetlenül a Microsoft, a beállításokat a partner által kezelt.  Ezen cikk segítségével megtudhatja, Mik azok a beállítások, és hogy azok hatással a regisztráció. Ezek a beállítások az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök függetlenek egymástól.

## <a name="enable-access-to-costs"></a>Engedélyezze a hozzáférést a költségek

Nem engedélyezett, üzenetet látja, vagy *"költség nézetek le vannak tiltva a regisztrációs."* Ha a költségadatok keres?
![Képernyőkép a "nem engedélyezett" előfizetéshez tartozó aktuális költség mezőben.](media/billing-enterprise-mgmt-groups/unauthorized.png)

A következő okok valamelyike lehet:

1. Az Azure enterprise-partneren keresztül vásárolt, és a partner nem kiadás díjszabása még. Lépjen kapcsolatba a partnerrel az árképzés belül beállítás frissítése a [az Enterprise portal](https://ea.azure.com).
2. Ha már a közvetlen nagyvállalati szerződéssel rendelkező ügyfelek, van néhány lehetőségeket:
    * Ön a fiók tulajdonosa és a regisztráció rendszergazdájához le van tiltva a **AO költségek megtekintése** beállítás.  
    * A részleg rendszergazdája, és a regisztráció rendszergazdájához le van tiltva a **DA költségek megtekintése** beállítás.
    * Lépjen kapcsolatba a regisztrációs rendszergazdától kérhet hozzáférést. A regisztrációs adminisztrátoránál frissítheti a beállításokat a [az Enterprise portal](https://ea.azure.com/manage/enrollment).

      ![Képernyőkép a vállalati portál beállításait nézet eredő díjakat kiszámlázzuk.](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Eszköz nem érhető el.

Ha hibaüzenetet kap **az objektum nem érhető el** amikor megpróbál hozzáférni egy előfizetést, vagy a felügyeleti csoportban, majd nem rendelkezik a megfelelő szerepkört ezen elem megtekintéséhez.  

!["Az eszköz nem érhető el" üzenet megjelenítő képernyőkép.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Hozzáférés kérése az Azure előfizetés vagy a felügyeleti csoport rendszergazdája. További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések
- Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

---
title: Azure vállalati költségek nézet hibaelhárítása |} A Microsoft Docs
description: Megtudhatja, hogyan lehetséges, hogy az Azure Portalon szervezeti költség nézetekkel kapcsolatos problémák megoldásához.
author: rthorn17
manager: adpick
editor: ''
ms.assetid: ''
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/22/2017
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: d35996b16d615a198b9a6039386f6b295172f388
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54900187"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Vállalati költségek nézet hibaelhárítása

Vállalati belépéseket, belül számos beállítás, amely a felhasználót a regisztrációs belül nem jelenik meg a költségek tartoznak.  Ezek a beállítások a beléptetés adminisztrátora kezeli. Vagy, ha a regisztráció nem vásárolta meg közvetlenül a Microsoft, a beállításokat a partner által kezelt.  Ezen cikk segítségével megtudhatja, Mik azok a beállítások, és hogy azok hatással a regisztráció. Ezek a beállítások az Azure szerepköralapú hozzáférés-vezérlés (RBAC) szerepkörök függetlenek egymástól.

## <a name="enabling-access-to-costs"></a>Költségek való hozzáférés engedélyezése

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

Ha "az eszköz nem érhető el" hibaüzenetet kap amikor megpróbál hozzáférni egy előfizetést vagy felügyeleti csoportot, akkor nem rendelkezik a megfelelő szerepkört ezen elem megtekintéséhez.  

!["Az eszköz nem érhető el" üzenet megjelenítő képernyőkép.](media/billing-enterprise-mgmt-groups/asset-not-found.png)

Hozzáférés kérése az Azure előfizetés vagy a felügyeleti csoport rendszergazdája. További információkért lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../role-based-access-control/role-assignments-portal.md).

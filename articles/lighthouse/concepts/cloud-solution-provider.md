---
title: Az Azure Lighthouse és a Cloud Solution Provider program
description: Az Azure-beli delegált erőforrás-kezelés használatakor fontos figyelembe venni a biztonsági és hozzáférés-vezérlést.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012121"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Az Azure Lighthouse és a Cloud Solution Provider program

Ha Ön CSP- [(felhőalapú szolgáltatói)](https://docs.microsoft.com/partner-center/csp-overview) partner, akkor már hozzáférhet az ÜGYFELEKNEK a CSP programon keresztül létrehozott Azure-előfizetésekhez a (z [) (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkció használatával. Ez a hozzáférés lehetővé teszi az ügyfelek előfizetésének közvetlen támogatását, konfigurálását és felügyeletét.

Az Azure Lighthouse használatával az Azure-beli delegált erőforrás-kezelést és az AOBO-et is használhatja. Ez segít javítani a biztonságot, és csökkenti a szükségtelen hozzáférést azáltal, hogy részletesebb engedélyeket biztosít a felhasználók számára. Emellett nagyobb hatékonyságot és méretezhetőséget is biztosít, mivel a felhasználók több ügyfél-előfizetésben is működhetnek, ha egyetlen bejelentkezést használnak a bérlőben.

## <a name="administer-on-behalf-of-aobo"></a>Felügyelet a következő nevében (AOBO)

A AOBO esetében a bérlő [rendszergazdai ügynök](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkörrel rendelkező felhasználója a CSP-program keretében létrehozott Azure-előfizetésekhez is hozzáférhet. Minden olyan felhasználónak, akinek hozzáférésre van szüksége bármely ügyfél előfizetéséhez, a csoport tagjának kell lennie. A AOBO nem teszi lehetővé a rugalmasságot olyan különálló csoportok létrehozására, amelyek különböző ügyfelekkel működnek, vagy különböző szerepköröket engedélyeznek a csoportok vagy felhasználók számára.

![Bérlői felügyelet az AOBO használatával](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure delegált erőforrás-kezelés használatával különböző csoportokat rendelhet hozzá különböző ügyfelekhez vagy szerepkörökhöz, ahogy az alábbi ábrán is látható. Mivel a felhasználók a megfelelő szintű hozzáféréssel rendelkeznek az Azure-beli delegált erőforrás-kezelésen keresztül, csökkentheti a rendszergazdai ügynök szerepkörrel rendelkező felhasználók számát (és így teljes AOBO-hozzáféréssel rendelkezik). Ez segít a biztonság javításában azáltal, hogy korlátozza az ügyfelek erőforrásainak szükségtelen hozzáférését. Emellett nagyobb rugalmasságot biztosít több ügyfél felügyeletéhez.

A CSP programon keresztül létrehozott előfizetés bevezetése az [előfizetés Azure-beli delegált erőforrás-kezelésre való](../how-to/onboard-customer.md)bevezetésében ismertetett lépéseket követi. A bérlő rendszergazdai ügynök szerepkörrel rendelkező felhasználója elvégezheti ezt a bevezetést.

![Bérlői felügyelet az AOBO és az Azure által delegált erőforrás-kezelés használatával](../media/csp-2.jpg)

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Ismerje meg, hogyan lehet előfizetést bevezetni [Az Azure-beli delegált erőforrás-kezelésbe](../how-to/onboard-customer.md).
- Tudnivalók a [Cloud Solution Provider programról](https://docs.microsoft.com/partner-center/csp-overview).

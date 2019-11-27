---
title: A felhőszolgáltatói program megfontolandó szempontjai
description: A CSP-partnerek esetében az Azure-beli delegált erőforrás-kezelés a részletes engedélyek engedélyezésével segíti a biztonságot és a szabályozást.
ms.date: 10/23/2019
ms.topic: conceptual
ms.openlocfilehash: 7e1e371d8c31c45828ee0565545cb40145b40e92
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74463957"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Az Azure Lighthouse és a Cloud Solution Provider program

Ha Ön CSP- [(felhőalapú szolgáltatói)](https://docs.microsoft.com/partner-center/csp-overview) partner, akkor már hozzáférhet az ÜGYFELEKNEK a CSP programon keresztül létrehozott Azure-előfizetésekhez a (z [) (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkció használatával. Ez a hozzáférés lehetővé teszi az ügyfelek előfizetésének közvetlen támogatását, konfigurálását és felügyeletét.

Az [Azure Lighthouse](../overview.md)használatával az Azure-beli delegált erőforrás-kezelést és az AOBO-et is használhatja. Ez segít javítani a biztonságot, és csökkenti a szükségtelen hozzáférést azáltal, hogy részletesebb engedélyeket biztosít a felhasználók számára. Emellett nagyobb hatékonyságot és méretezhetőséget is biztosít, mivel a felhasználók több ügyfél-előfizetésben is működhetnek, ha egyetlen bejelentkezést használnak a bérlőben.

> [!TIP]
> Az ügyfelek erőforrásainak védelme érdekében mindenképpen tekintse át és kövesse az [ajánlott biztonsági eljárásokat](recommended-security-practices.md) a [partneri biztonsági követelményekkel](https://docs.microsoft.com/partner-center/partner-security-requirements)együtt.

## <a name="administer-on-behalf-of-aobo"></a>Felügyelet a következő nevében (AOBO)

A AOBO esetében a bérlő [rendszergazdai ügynök](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkörrel rendelkező felhasználója a CSP-program keretében létrehozott Azure-előfizetésekhez is hozzáférhet. Minden olyan felhasználónak, akinek hozzáférésre van szüksége bármely ügyfél előfizetéséhez, a csoport tagjának kell lennie. A AOBO nem teszi lehetővé a rugalmasságot olyan különálló csoportok létrehozására, amelyek különböző ügyfelekkel működnek, vagy különböző szerepköröket engedélyeznek a csoportok vagy felhasználók számára.

![Bérlői felügyelet az AOBO használatával](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure delegált erőforrás-kezelés használatával különböző csoportokat rendelhet hozzá különböző ügyfelekhez vagy szerepkörökhöz, ahogy az alábbi ábrán is látható. Mivel a felhasználók a megfelelő szintű hozzáféréssel rendelkeznek az Azure-beli delegált erőforrás-kezelésen keresztül, csökkentheti a rendszergazdai ügynök szerepkörrel rendelkező felhasználók számát (és így teljes AOBO-hozzáféréssel rendelkezik). Ez segít a biztonság javításában azáltal, hogy korlátozza az ügyfelek erőforrásainak szükségtelen hozzáférését. Emellett nagyobb rugalmasságot biztosít több ügyfél felügyeletéhez.

A CSP programon keresztül létrehozott előfizetés bevezetése az [előfizetés Azure-beli delegált erőforrás-kezelésre való bevezetésében](../how-to/onboard-customer.md)ismertetett lépéseket követi. A bérlő rendszergazdai ügynök szerepkörrel rendelkező felhasználója elvégezheti ezt a bevezetést.

![Bérlői felügyelet az AOBO és az Azure által delegált erőforrás-kezelés használatával](../media/csp-2.jpg)

> [!NOTE]
> A [Azure Portal **saját ügyfelek** lapján](../how-to/view-manage-customers.md) már szerepel a **Cloud Solution Provider (előzetes verzió)** szakasz, amely megjeleníti a számlázási adatokat és erőforrásokat a [Microsoft ügyfél-szerződést (MCA) aláírt](https://docs.microsoft.com/partner-center/confirm-customer-agreement) CSP-ügyfelek számára, és az Azure-csomag alatt található. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](https://docs.microsoft.com/azure/billing/mpa-overview).
>
> A CSP-ügyfelek ebben a szakaszban jelenhetnek meg, függetlenül attól, hogy az Azure-beli delegált erőforrás-kezelésre kerültek-e elő. Ha van, akkor a **Customers (ügyfelek** ) szakaszban is megjelennek az ügyfelek és a [delegált erőforrások megtekintése és kezelése](../how-to/view-manage-customers.md)című témakörben leírtak szerint.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Ismerje meg, hogyan lehet [előfizetést bevezetni az Azure-beli delegált erőforrás-kezelésbe](../how-to/onboard-customer.md).
- Tudnivalók a [Cloud Solution Provider programról](https://docs.microsoft.com/partner-center/csp-overview).

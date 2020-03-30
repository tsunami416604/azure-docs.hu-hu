---
title: A felhőszolgáltatói program megfontolandó szempontjai
description: A CSP-partnerek számára az Azure delegált erőforrás-kezelése a részletes engedélyek engedélyezésével javítja a biztonságot és a felügyeletet.
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: 66ea74751f12a499a1e2d9e083497da31746e3c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456911"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Az Azure Világítótorony és a felhőszolgáltató program

Ha Ön [csp (cloud solution provider)](https://docs.microsoft.com/partner-center/csp-overview) partner, a CsP-programon keresztül már elérheti az ügyfelek számára létrehozott Azure-előfizetéseket a [Felügyelet a nevében (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkció használatával. Ez a hozzáférés lehetővé teszi, hogy közvetlenül támogassa, konfigurálja és kezelje az ügyfelek előfizetéseit.

Az [Azure Lighthouse](../overview.md)segítségével használhatja az Azure delegált erőforrás-kezelése mellett AOBO. Ez segít a biztonság javításában, és csökkenti a szükségtelen hozzáférést azáltal, hogy részletesebb engedélyeket engedélyez a felhasználók számára. Emellett nagyobb hatékonyságot és méretezhetőséget tesz lehetővé, mivel a felhasználók több ügyfél-előfizetésen keresztül is dolgozhatnak egyetlen bejelentkezéssel a bérlőben.

> [!TIP]
> Az ügyfelek erőforrásainak védelme érdekében mindenképpen tekintse át és kövesse az [ajánlott biztonsági gyakorlatokat](recommended-security-practices.md) a [partner biztonsági követelményeivel](https://docs.microsoft.com/partner-center/partner-security-requirements)együtt.

## <a name="administer-on-behalf-of-aobo"></a>Az (AOBO) nevében történő ügyintézés

Az AOBO-val a bérlőben a [Felügyeleti ügynök](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkörrel rendelkező felhasználók AOBO-hozzáféréssel rendelkeznek a CSP-programon keresztül létrehozott Azure-előfizetésekhez. Minden olyan felhasználónak, akinek hozzáférésre van szüksége az ügyfelek előfizetéseihez, ennek a csoportnak a tagja kell, hogy legyen. Az AOBO nem teszi lehetővé a rugalmasságot, hogy különböző csoportokat hozzon létre, amelyek különböző ügyfelekkel működnek, vagy különböző szerepköröket engedélyezhet csoportok vagy felhasználók számára.

![Bérlői felügyelet az AOBO használatával](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure delegált erőforrás-kezelése használatával különböző csoportokat rendelhet különböző ügyfelekhez vagy szerepkörökhöz, ahogy az az alábbi ábrán látható. Mivel a felhasználók megfelelő szintű hozzáféréssel rendelkeznek az Azure delegált erőforrás-kezelése révén, csökkentheti a rendszergazdai ügynök szerepkörrel rendelkező felhasználók számát (és így teljes AOBO-hozzáféréssel rendelkezik). Ez növeli a biztonságot azáltal, hogy korlátozza az ügyfelek erőforrásaihoz való szükségtelen hozzáférést. Emellett nagyobb rugalmasságot biztosít több ügyfél nagy méretekben való kezeléséhez.

A kriptaprogramon keresztül létrehozott előfizetés bevezetése az [Azure delegált erőforrás-kezelésére való előfizetés onboardban](../how-to/onboard-customer.md)leírt lépéseit követi. Bármely felhasználó, aki rendelkezik a rendszergazdai ügynök szerepköra a bérlőben elvégezheti ezt a bevezetést.

![Bérlői felügyelet az AOBO és az Azure delegált erőforrás-kezelése használatával](../media/csp-2.jpg)

> [!NOTE]
> [ **Az** Azure Portal On Customers lap](../how-to/view-manage-customers.md) most már tartalmaz egy **felhőszolgáltatót (előzetes verzió)** című szakaszt, amely megjeleníti a számlázási adatokat és az erőforrásokat [a Microsoft ügyfélszerződést (MCA) aláíró](https://docs.microsoft.com/partner-center/confirm-customer-agreement) és az [Azure-csomag alatt álló](https://docs.microsoft.com/partner-center/azure-plan-get-started)csp-ügyfelek számára. További információ: [Ismerkedés a Microsoft Partner-szerződés számlázási fiókjával.](../../billing/mpa-overview.md)
>
> A CSP-ügyfelek ebben a szakaszban megjelenhetnek, függetlenül attól, hogy az Azure delegált erőforrás-kezeléséhez is be vannak-e táblázva. Ha van, akkor a **Vevők** szakaszban is megjelennek, ahogy azt az [Ügyfelek és a delegált erőforrások megtekintése és kezelése](../how-to/view-manage-customers.md)című szakaszban leírtak szerint. Hasonlóképpen a CSP-ügyfél nem kell megjelennie a **Felhőalapú megoldásszolgáltató (előzetes verzió)** szakaszában **az ügyfeleim** annak érdekében, hogy az Azure delegált erőforrás-kezeléséhez.

## <a name="next-steps"></a>További lépések

- További információ a [bérlők közötti felügyeleti élményekről.](cross-tenant-management-experience.md)
- Ismerje meg, hogyan [lehet az Azure delegált erőforrás-kezelésére vonatkozó előfizetést beszállni.](../how-to/onboard-customer.md)
- További információ a [Felhőszolgáltató programról.](https://docs.microsoft.com/partner-center/csp-overview)

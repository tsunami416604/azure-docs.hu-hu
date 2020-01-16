---
title: Az ingyenes Azure-szolgáltatás használatának monitorozása és nyomon követése
description: Tudja meg, hogyan ellenőrizheti az ingyenes szolgáltatások használatát az Azure Portalon.
author: amberbhargava
manager: amberb
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 83937e31d844ba0cc8efc17f1ecefa2ad6e7a7c1
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75992828"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Tekintse meg az ingyenes Azure-fiókhoz kapcsolódó ingyenes szolgáltatások használati adatait

Az ingyenes Azure-fiókjához tartozó ingyenes szolgáltatásokat díjmentesen veheti igénybe, amennyiben nem lépi túl a szolgáltatások korlátait. Annak érdekében, hogy a korlátokon belül maradjon, használhatja az Azure Portalt az ingyenes szolgáltatáshasználat nyomon követéséhez.

## <a name="check-usage-in-the-azure-portal"></a>A használati adatok ellenőrzése az Azure Portalon

1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com).

2.  Keressen rá az **Előfizetések** kifejezésre.

    ![Képernyőkép az „előfizetések” kifejezés a portálon történő kereséséről](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Válassza ki azt az előfizetést, amelyet az ingyenes Azure-fiókja regisztrációjakor hozott létre.

4.  Görgessen le, és keresse meg az ingyenes szolgáltatások használatát ismertető táblázatot.

    ![Ingyenes szolgáltatások használatát megjelenítő képernyőkép](./media/check-free-service-usage/subscription-usage-free-services.png)

    A táblázat az alábbi oszlopokat tartalmazza:

* **Fogyasztásmérő:** Meghatározza a felhasznált szolgáltatás mértékegységét.
* **Használat/korlát:** Az aktuális havi használat és korlát a mérőhöz.
* **Állapot:** A szolgáltatás használati állapota. A használati adatai alapján a következő állapotok egyike lehet:
  * **Nincs használatban:** Nem használta a mérőszámot, vagy a mérő használata nem érte el a számlázási rendszerét.
  * **Túllépte a \<dátum >:** Túllépte a (z) \<dátum > a mérőre vonatkozó korlátot.
  * Nem **valószínű, hogy meghaladja a** következőt: Nem valószínű, hogy túllépi a mérő korlátját.
  * **Meghaladja a \<dátumot >:** Valószínű, hogy túllépi a mérőszám határértékét \<dátum >.

> [!IMPORTANT]
>
> Az ingyenes szolgáltatások csak ahhoz az előfizetéshez érhetők el, amelyet az ingyenes Azure-fiókja regisztrációjakor hozott létre. Ha nem látja az ingyenes szolgáltatások táblázatát az előfizetés áttekintési oldalán, nem érhetők el ingyenes szolgáltatások az előfizetéshez.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések
- [Ingyenes Azure-fiók frissítése](upgrade-azure-subscription.md)

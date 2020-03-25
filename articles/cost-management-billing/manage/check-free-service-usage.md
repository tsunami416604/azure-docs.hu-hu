---
title: Az ingyenes Azure-szolgáltatás használatának monitorozása és nyomon követése
description: Tudja meg, hogyan ellenőrizheti az ingyenes szolgáltatások használatát az Azure Portalon.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 0a62deecc6f70bb21059ae3ddda9eea173de65fe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77199806"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Tekintse meg az ingyenes Azure-fiókhoz kapcsolódó ingyenes szolgáltatások használati adatait

Az ingyenes Azure-fiókjához tartozó ingyenes szolgáltatásokat díjmentesen veheti igénybe, amennyiben nem lépi túl a szolgáltatások korlátait. Annak érdekében, hogy a korlátokon belül maradjon, használhatja az Azure Portalt az ingyenes szolgáltatáshasználat nyomon követéséhez.

## <a name="check-usage-in-the-azure-portal"></a>A használati adatok ellenőrzése az Azure Portalon

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  Keressen rá az **Előfizetések** kifejezésre.

    ![Képernyőkép az „előfizetések” kifejezés a portálon történő kereséséről](./media/check-free-service-usage/billing-search-subscriptions.png)

3.  Válassza ki azt az előfizetést, amelyet az ingyenes Azure-fiókja regisztrációjakor hozott létre.

4.  Görgessen le, és keresse meg az ingyenes szolgáltatások használatát ismertető táblázatot.

    ![Ingyenes szolgáltatások használatát megjelenítő képernyőkép](./media/check-free-service-usage/subscription-usage-free-services.png)

    A táblázat az alábbi oszlopokat tartalmazza:

* **Mérő:** Azonosítja a mértékegységet a használt szolgáltatás esetében.
* **Használat/korlát:** A mérőszám aktuális havi használata és korlátja.
* **Állapot:** A szolgáltatás használati állapota. A használati adatai alapján a következő állapotok egyike lehet:
  * **Nincs használatban:** Nem használta a mérőt, vagy a mérő használata nem érte el a számlázási rendszer szintjét.
  * **Túllépés időpontja: \<dátum>:** A megadott \<dátumon> túllépte a mérőszám korlátját.
  * **Valószínűleg nem lépi túl:** Valószínűleg nem fogja túllépni a mérőszám korlátját.
  * **Túllépés: \<dátum>:** Valószínűleg a megadott \<dátumon> túl fogja lépni a mérőszám korlátját.

> [!IMPORTANT]
>
> Az ingyenes szolgáltatások csak ahhoz az előfizetéshez érhetők el, amelyet az ingyenes Azure-fiókja regisztrációjakor hozott létre. Ha nem látja az ingyenes szolgáltatások táblázatát az előfizetés áttekintési oldalán, nem érhetők el ingyenes szolgáltatások az előfizetéshez.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Ingyenes Azure-fiók frissítése](upgrade-azure-subscription.md)

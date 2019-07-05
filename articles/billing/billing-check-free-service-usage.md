---
title: Figyelje és kövesse az Azure ingyenes szolgáltatás használata
description: Ismerje meg, hogyan ellenőrizheti az ingyenes szolgáltatás használata az Azure portal és a használati CSV-fájl.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 3543bed7f699fd149ca7f2a6f61e9eb5aad5f1a3
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491425"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Ellenőrizze az Azure ingyenes fiókhoz tartozó ingyenes szolgáltatás használata

Ön nem kell fizetnie szolgáltatásokért díjmentesen ingyenes Azure-fiókra, kivéve, ha túllépi a szolgáltatások korlátozásait. Megőrzési ideje a korlátokat, használhatja az Azure portal vagy a használati fájlban figyeléséhez és nyomon követéséhez az ingyenes szolgáltatás használata.

## <a name="check-usage-in-the-azure-portal"></a>Ellenőrizze a használatot az Azure Portalon

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  A bal oldali navigációs területen jelölje ki a **minden szolgáltatás**.

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki a létrehozott való regisztráció során az ingyenes fiók előfizetést.

    ![Előfizetések bemutató képernyőkép](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Az Áttekintés szakaszban jeleníti meg az előfizetéssel kapcsolatos alapvető adatokat. Például előfizetés-azonosító, ajánlat és az előfizetés neve. Ha a lejár a fiókjához tartozó ingyenes kreditekkel is talál információt.

    ![Előfizetés alapvető információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Görgessen le a jelenlegi és az előre jelzett költség található információ. A költségek magában foglalja a szolgáltatás használata nem találhatók meg az ingyenes fiók és a ingyenesen használhatja a szolgáltatásokat meghaladó használat.

    ![Előfizetési költség információkat bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Az Áttekintés szakasz utolsó része belefoglalja rendelkezik egy ingyenes szolgáltatás használati megjelenítő táblázat.

    ![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A táblázatban szerepel a következő oszlopokat:

* **Fogyasztásmérő neve:** A rendszer, elérheti az érték mértékegységét azonosítja. Fogyasztásmérő leképezés szolgáltatás kapcsolatos további információkért lásd: [mérőszám leképezés ingyenes szolgáltatás ismertetése](billing-understand-free-service-meter-mapping.md).
* **Használat/korlát:** Aktuális havi használat és a mértékegységek korlátot. Ezeket az adatokat az állapotsorban is megkeresheti.
* **Állapot:** A mérőszámok használati állapota. A használati minta alapján, akkor az alábbi alapszabály egyikét:
  * **Nincs használatban:** Még nem használta az a fogyasztásmérő, vagy a mérőszám a használata még nem elérte a számlázási rendszerhez.
  * **Túllépte a \<dátum >:** Túllépte a korlátot, a mértékegységek a \<dátum >.
  * **Valószínűleg nem lépi túl:** Most már valószínűleg nem lépi túl a korlátot, a mértékegységek.
  * **Túllépés \<dátum >:** Valószínűleg meghaladja a korlátot, a mérő az Ön \<dátum >.

## <a name="check-usage-with-the-usage-file"></a>Ellenőrizze a használatot és a használatot részletező fájl

A használati fájlban az Azure-előfizetés részletes információkat szolgáltat. Az Azure Account Center a havi és napi használati fájl is letölthető. Ismerje meg, hogyan a használatot részletező fájl letöltése és megismerése a hozzáférés szükséges, lásd: [első számlák és használati adatok](billing-download-azure-invoice-daily-usage-date.md). A használatot részletező fájl oszlopai kapcsolatos további információkért lásd: [a használat feltételeinek értelmezése](billing-understand-your-usage.md).

A használatot részletező fájl rendelkezik egyaránt ingyenes vagy fizetős szolgáltatás méretkihasználtsági adatait. Ingyenes szolgáltatás mérőszámok kellene **ingyenes** hozzáfűzi a végén a fogyasztásmérő nevét. Ingyenes mérőszámok, nyissa meg a fájlt az excel- és szűrő keresése a **mérőszám kategóriája oszlop** szöveg rendelkező cellák **– ingyenes** (használata Szövegszűrők &rarr; tartalmazza szűrő).


![Ingyenes szolgáltatások használatának bemutató képernyőkép](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Az előfizetés frissítése](billing-upgrade-azure-subscription.md)

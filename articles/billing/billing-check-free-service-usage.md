---
title: Az ingyenes Azure-szolgáltatás használatának monitorozása és nyomon követése
description: Tudja meg, hogyan ellenőrizheti az ingyenes szolgáltatások használatát az Azure Portalon és a használati CSV-fájlban.
author: amberbhargava
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ae8b26f0032c6659da18b822f4f8568995eff82b
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709769"
---
# <a name="check-free-service-usage-included-with-your-azure-free-account"></a>Az ingyenes Azure-fiókhoz járó ingyenes szolgáltatások ellenőrzése

Az ingyenes Azure-fiókhoz tartozó ingyenes szolgáltatásokat díjmentesen veheti igénybe, amennyiben nem lépi túl a szolgáltatások korlátait. Annak érdekében, hogy a korlátokon belül maradjon, használhatja az Azure Portalt vagy a használati fájlját az ingyenes szolgáltatáshasználat monitorozásához és nyomon követéséhez.

## <a name="check-usage-in-the-azure-portal"></a>A használati adatok ellenőrzése az Azure Portalon

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2.  A navigációs terület bal oldalán válassza ki a **Minden szolgáltatás** elemet.

3.  Válassza az **Előfizetések** lehetőséget.

4.  Válassza ki azt az előfizetést, amelyet az ingyenes fiók regisztrációjakor hozott létre.

    ![Az összes előfizetést megjelenítő képernyőkép](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Az áttekintési szakasz lényeges információkat jelenít meg az előfizetéséről. Ilyen például az előfizetés azonosítója, az ajánlat típusa és az előfizetés neve. Ezek az információk az ingyenes fiókkreditek lejárta után is elérhetők.

    ![Az előfizetés lényeges információit megjelenítő képernyőkép](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Görgessen le az aktuális és előre jelzett díjakkal kapcsolatos információkért. A költség tartalmazza az ingyenes fiókba nem beletartozó szolgáltatások használatát és az ingyenes szolgáltatások korlátait meghaladó használatot.

    ![Az előfizetés költségeivel kapcsolatos információkat megjelenítő képernyőkép](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Az áttekintési szakasz utolsó része egy táblázat, amely az ingyenes szolgáltatáshasználatot mutatja be.

    ![Ingyenes szolgáltatások használatát megjelenítő képernyőkép](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    A táblázat az alábbi oszlopokat tartalmazza:

* **Mérő neve:** Megadja a használt fogyasztásmérő mértékegységét. Ha többet szeretne megtudni a szolgáltatásmérő-társításról, olvassa el [az ingyenes szolgáltatásmérő-társítások ismertetését](billing-understand-free-service-meter-mapping.md).
* **Használat/korlát:** A mérőszám aktuális havi használata és korlátja. Ezeket az információkat megtalálja az állapotsávon is.
* **Állapot:** A mérő használati állapota. A használati mintája alapján a következő állapotok egyike lehet:
  * **Nincs használatban:** Nem használta a mérőt, vagy a mérő használata nem érte el a számlázási rendszer szintjét.
  * **Túllépés időpontja: \<dátum>:** A megadott \<dátumon> túllépte a mérőszám korlátját.
  * **Valószínűleg nem lépi túl:** Valószínűleg nem fogja túllépni a mérőszám korlátját.
  * **Túllépés: \<dátum>:** Valószínűleg a megadott \<dátumon> túl fogja lépni a mérőszám korlátját.

## <a name="check-usage-with-the-usage-file"></a>Használat ellenőrzése a használati fájllal

A használati fájlja részletes információt nyújt az Azure-előfizetéséről. Havi vagy napi használati fájlját letöltheti az Azure Fiókközpontból. A használati fájl letöltéséhez és a szükséges hozzáférés megismeréséhez lásd a [számla és használat beszerzését](billing-download-azure-invoice-daily-usage-date.md) ismertető szakaszt. A használati fájlban található oszlopok megismeréséhez lásd [a használat feltételeinek értelmezését](billing-understand-your-usage.md).

A használati fájl mind az ingyenes, mind a fizetős szolgáltatások használati információit tartalmazza. Az ingyenes szolgáltatásmérők neve után az **Ingyenes** kifejezés áll. Az ingyenes mérők megkereséséhez nyissa meg a fájlt az Excelben, és szűrje a **Fogyasztásmérő kategóriája oszlop** tartalmát olyan cellákra, amelyek az **– Ingyenes** szöveget tartalmazzák (ehhez használja a Szövegszűrők &rarr; Tartalmazza szűrőt).


![Ingyenes szolgáltatások használatát megjelenítő képernyőkép](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések
- [Váltás magasabb szintű előfizetésre](billing-upgrade-azure-subscription.md)

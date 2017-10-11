---
title: "Az Azure piactér kifizetés reporting megértése |} Microsoft Docs"
description: "Ismerje meg, hogyan lehet áttekinteni és az Azure piactér kifizetés jelentés betöltési."
services: marketplace-publishing
documentationcenter: na
author: v-jeana
manager: lakoch
editor: 
ms.assetid: 3e99aefe-abeb-414c-8689-15352d25aefd
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: v-jeana; hascipio; v-dabosl
ms.openlocfilehash: 5a89e9ba4376d0c4f49feb3783692e28a28902a2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="understand-your-azure-marketplace-payout-reports"></a>Az Azure piactér kifizetés jelentések ismertetése
## <a name="access-and-view-your-payout-reports"></a>Hozzáférés és a kifizetés jelentések megtekintése
Amíg a Microsoft fejlesztői központban átmenet néhány kifizetés jelentések érhetők el a Dev Center webhely https://dev.windows.com/en-us, míg mások is találhatók meg a közzétételi Portáljára https://publish.windowsazure.com.

Kifizetés reporting most rendelkezésre áll a **Dev Center webhely** bármely piactér ajánlatokról társított modern payouts; ez jelenleg tartalmazza:

* Virtuális gépek
* B + C ajánlatok
* Adatok és a fejlesztői szolgáltatások kínált EA alatt

Kifizetés reporting marad a **közzétételi Portáljára** számára:

* Adatok és a fejlesztői szolgáltatások kínált webes közvetlen (amely a továbbra is a hagyományos kifizetés rendszer) alatt.

Jelentések negyedév lezárása után rendelkezésre álló 45 nap, és bármely visszatérítés kell számítani.

### <a name="access-payout-reports-in-dev-center"></a>A fejlesztői központjában Access kifizetés jelentések
1. Https://dev.windows.com/en-us, navigáljon a fejlesztői központban.
2. Kattintson a **irányítópult**.

    ![LandingPageDashboardHighlight][1]
3. Kattintson a **kifizetés összegzés**.

    ![DashboardPayoutSummary][2]

## <a name="view-your-payout-reports-in-dev-center"></a>A fejlesztői központjában kifizetés jelentések megtekintése
A negyedév kifizetés jelentés negyedévben történt összes tranzakció rögzíti.

* A fenntartott mennyiség azt jelzi, hogy a jövőbeli fizetési ciklus (pl. ezt a mennyiséget helyezi át a következő hónap jövőbeli fizetési) kívül van keletkezhetnek fizetési.  Ez a mennyiség általában $0 lesz (kivéve, ha az ügyfél és az előzetes fizet).
* A közeljövőben fizetési vagy a legutóbbi fizetési **részleteinek megtekintéséhez** hivatkozásokra, hogy ezek payouts megjegyzést.
* Kattintson a **fizetési utasítások** alkalmazás vagy termék a hibákra vonatkozó részletes adatainak megtekintéséhez.
* Kattintson a **nézet** hivatkozásra kattintva megtekintheti az egyes utasításokat.

    ![PayoutSummaryUpcomingMostRecentLinksStatement][3]
* Használja a **halad lebontása** szűrő az egyes utasítás több apps/termék megtekintéséhez, ha vannak ilyenek alján.

    ![PayoutSummaryPaymentStatementsFilterControl][4]

## <a name="view-your-payout-reports-in-publishing-portal"></a>A közzétételi Portáljára a kifizetés jelentések megtekintése
A negyedév kifizetés jelentés negyedévben történt összes tranzakció rögzíti.

1. Keresse meg a közzétételi portálon, a https://publish.windowsazure.com.
2. Az a **közzétevők** kattintson **kifizetés jelentések**.
3. Kattintson a legördülő listán a negyedéves kifizetés összes elérhető jelentések megjelenítéséhez.

    ![accessingpayoutreport][5]

### <a name="read-your-payout-reports"></a>Kifizetés jelentések olvasása
A negyedév kifizetés jelentés negyedévben történt összes tranzakció rögzíti.

* Ha egy adott negyedév kapcsolódó tételek keres, válassza ki a kifizetés jelentést, hogy a legördülő lista a negyedév. Például ha érdekli tételek a 2015. június áprilisban, választhat adott dátumtartományon belül a legördülő listán.
* Ha egy adott negyedév kapcsolódó payouts részleteit keres, válassza ki a kifizetés jelentést az ezt követő negyedév. Például ha érdekli a payouts a 2015. június áprilisban, ezek az összegek megjelennek a későbbi kifizetés jelentés júliusi a 2015. szeptember.
  ![readingpayoutreport][6]
* A pénzügyi összefoglaló panelen látható kiegyensúlyozza, kreditek, tartozik kategória szerint.
* Tételek megjelenítése az egyes tranzakciók.

## <a name="definitions"></a>Meghatározások
**Pénzügyi összefoglaló panel:**

![financialdefinitions][7]

**Tételek:**

![ledgerdefinitions][8]

## <a name="payout-questions"></a>Kifizetés kérdések
Ha a payouts kapcsolatos kérdése van, forduljon a támogatási csapat.

![payoutquestions][9]

1. Nyissa meg a támogatási lapokat.
2. Válassza ki **Payouts**.
3. Válassza ki **kifizetés kapcsolódó lekérdezések**.
4. Kattintson a **indítási kérésre**.

## <a name="next-steps"></a>Következő lépések
Más támogatási lekérdezések esetén jelentkezzen egy problémáját <https://portal.azure.com>.

[1]: ./media/marketplace-publishing-report-payout/LandingPage-DashboardHighlight.png
[2]: ./media/marketplace-publishing-report-payout/Dashboard-PayoutSummary.png
[3]: ./media/marketplace-publishing-report-payout/PayoutSummary-UpcomingOrMostRecentPaymentLinksSingleStatementLink.png
[4]: ./media/marketplace-publishing-report-payout/PayoutSummary-PaymentStatements-SingleStatement-FilterControl.png
[5]: ./media/marketplace-publishing-report-payout/accessingpayoutreport.png
[6]: ./media/marketplace-publishing-report-payout/readingpayoutreport.png
[7]: ./media/marketplace-publishing-report-payout/financialdefinitions.png
[8]: ./media/marketplace-publishing-report-payout/ledgerdefinitions.png
[9]: ./media/marketplace-publishing-report-payout/payoutquestions.png

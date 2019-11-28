---
title: A Microsoft Partnerszerződés számláján szereplő díjak ismertetése – Azure
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a számláján szereplő díjak.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2019
ms.author: banders
ms.openlocfilehash: 9994ef6da379d0063930912a4d198b81623ddbe8
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74223699"
---
# <a name="understand-charges-on-your-microsoft-partner-agreement-invoice"></a>A Microsoft Partnerszerződés számláján szereplő díjak ismertetése

 A rendszer minden hónapban létrehoz egy számlát az egyes számlázási profilokhoz a Microsoft Partnerszerződéshez tartozó számlázási fiókban. A számla az előző hónap összes ügyfél által fizetendő díját tartalmazza. Az egyes tranzakciók Azure Portalon történő elemzésével értelmezheti a számlán feltüntetett díjakat. A számláit az Azure Portalon is megtekintheti. További tudnivalókért lásd [a számlák az Azure Portalról való letöltését](billing-download-azure-invoice.md) ismertető cikket.

Ez a cikk a Microsoft Partnerszerződéshez tartozó számlázási fiókra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-partnerszerződéshez](#check-access-to-a-microsoft-partner-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Egy számlához tartozó tranzakciók megtekintése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

   <!--  ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Válassza a bal oldalon található **Összes tranzakció** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók, számlázási profil vagy ügyfél kiválasztása után tudja kiválasztani a **Minden tranzakció** lehetőséget.

4. Az Összes tranzakció lapon az alábbi információk jelennek meg:

    <!-- ![Screenshot that shows the billed transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-all-transactions.png) -->

    |Oszlop  |Meghatározás  |
    |---------|---------|
    |Dátum     | A tranzakció dátuma  |
    |Számlaazonosító     | Annak a számlának az azonosítója, amelyen a tranzakció számlázva lett. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ |
    |Tranzakció típusa     |  A tranzakció típusa, például vásárlás, lemondás vagy a használati díjak befizetése  |
    |Termékcsalád     | Termékkategória, mint például a számítási szolgáltatás a virtuális gépek esetén, vagy az adatbázis az Azure SQL-adatbázis esetén|
    |Termékváltozat     | A termék példányát azonosító egyedi kód |
    |Mennyiség     |  A tranzakció összege      |
    |Számlázási profil     | A tranzakció ezen számlázási profil számláján jelenik meg |

5. A számla azonosítójára keresve szűrje ki a számlához tartozó tranzakciókat.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez

A díjak összegei a számlázásig becsült, függőben lévő összegek. A Microsoft Partnerszerződése számlázási profiljához tartozó, függőben lévő díjakat megtekintheti az Azure Portalon a következő számla összegének megbecsléséhez. A függőben lévő díjak becsült összegek, és nem tartalmazzák az adót. A következő számlán feltüntetett díjak eltérhetnek a függőben lévő díjaktól.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

A függőben lévő díjak azonosításkor a díjakat kitevő egyes tranzakciók elemzésével értelmezheti a díjakat. Ezen a ponton a függőben lévő használati díjak nem jelennek meg az Összes tranzakció lapon. A függőben lévő használati díjakat az Azure-előfizetések oldalán tekintheti meg. További információért tekintse meg a [függőben lévő használati díjak megtekintését](#view-pending-usage-charges) ismertető részt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

   <!-- ![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png) -->

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza a bal oldalon található **Összes tranzakció** lehetőséget.

5. Keressen rá a *függőben* kifejezésre. Használja az **Időtartomány** szűrőt a jelenlegi vagy az előző hónap függőben lévő díjainak megtekintéséhez.

   <!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Függőben lévő díjak megtekintése ügyfelenként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza ki az **Ügyfelek** elemet a lap bal oldalán.

    <!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

5. Az Ügyfelek lap megjeleníti a számlázási profilhoz tartozó egyes ügyfelek aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza ki az **Azure-előfizetések** elemet a lap bal oldalán.

5. Az Azure-előfizetések oldala megjeleníti a számlázási profilban szereplő egyes előfizetések aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Az Azure használati díjainak elemzése

A használatalapú díjak elemzéséhez használja az Azure használati adatait és díjait tartalmazó CSV-fájlt. Letöltheti az [Azure használati adatait és díjait CSV formátumban az Azure Portalról](billing-download-azure-daily-usage.md).

Az Azure használati adatait és díjait tartalmazó fájlt szűrheti a PDF formátumú számlában szereplő egyes termékekre vonatkozó használati díjak egyeztetéséhez. Egy adott termék részletes használati díjainak megtekintéséhez szűrje a **product** oszlopot az Azure használati adatait és díjait tartalmazó CSV-fájlban, hogy csak annak a terméknek a neve szerepeljen.

A **customerName** oszlopot is szűrheti az Azure használati adatait és díjait tartalmazó CSV-fájlban az egyes ügyfelekre vonatkozó napi használati díjak megtekintéséhez. Ha meg szeretné tekinteni a napi használati díjakat Azure-előfizetésenként, szűrje a **subscriptionName** oszlopot.


## <a name="pay-your-bill"></a>A számla befizetése

A számla befizetésére vonatkozó utasításokat a számla alján találja. Fizethet átutalással vagy csekkel a számlázási ciklus végét követő 60 napon belül.

Ha már befizette számláját, a befizetés állapotát az Azure Portalon található Számlák oldalon ellenőrizheti.

## <a name="check-access-to-a-microsoft-partner-agreement"></a>Microsoft-partnerszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mpa](../../includes/billing-check-mpa.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a részletes használati adatokkal kapcsolatos további információkért lásd:

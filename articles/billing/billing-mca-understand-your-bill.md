---
title: A Microsoft-ügyfélszerződés számlázási díjai – Azure
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a számláján szereplő díjak.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: b5dfffecbd2908e987b76f29b14937f0e50ce64f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75388992"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>A Microsoft-ügyfélszerződés számláján szereplő díjak ismertetése

Az egyes tranzakciók elemzésével értelmezheti a számlán feltüntetett díjakat. A rendszer minden hónapban létrehoz egy számlát az egyes számlázási profilokhoz a Microsoft-ügyfélszerződéshez tartozó számlázási fiókban. A számla az előző hónap összes díját tartalmazza. A számláit az Azure Portalon tekintheti meg. További információért lásd a [Microsoft-ügyfélszerződéshez tartozó számlák letöltése](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement) című részt.

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Egy számlához tartozó tranzakciók megtekintése az Azure Portalon

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza a bal oldalon található **Összes tranzakció** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók, számlázási profil vagy számlaszakasz kiválasztása után tudja kiválasztani ki az **Összes tranzakció** lehetőséget.

4. Az Összes tranzakció lapon az alábbi információk jelennek meg:

    ![A kiszámlázott tranzakciók listáját megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Oszlop  |Meghatározás  |
    |---------|---------|
    |Dátum     | A tranzakció dátuma  |
    |Számlaazonosító     | Annak a számlának az azonosítója, amelyen a tranzakció számlázva lett. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ |
    |Tranzakció típusa     |  A tranzakció típusa, például vásárlás, lemondás vagy a használati díjak befizetése  |
    |Termékcsalád     | Termékkategória, mint például a számítási szolgáltatás a virtuális gépek esetén, vagy az adatbázis az Azure SQL-adatbázis esetén|
    |Termékváltozat     | A termék példányát azonosító egyedi kód |
    |Mennyiség     |  A tranzakció összege      |
    |Számlázási szakasz     | A tranzakció a számlázási profil számlájának ezen szakaszában jelenik meg |
    |Számlázási profil     | A tranzakció ezen számlázási profil számláján jelenik meg |

5. A számla azonosítójára keresve szűrje ki a számlához tartozó tranzakciókat.

### <a name="view-transactions-by-invoice-sections"></a>Az egyes számlaszakaszok tranzakcióinak megtekintése

A számlaszakaszokkal rendezheti a számlázási profil számlájának költségeit. További információért lásd a [számlaszakaszokat ismertető részt](billing-mca-overview.md#invoice-sections). Amikor létrejön egy számla, a számlázási profil összes szakaszának összes díja megjelenik a számlán.

Az alábbi képen egy mintaszámla Könyvelési osztály szakasza és az abban található díjak láthatók.

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Ha beazonosította egy adott számlaszakasz díjait, megtekintheti a tranzakciókat az Azure Portalon a díjak értelmezéséhez.

1. Egy számla tranzakcióinak megtekintéséhez nyissa meg az Összes tranzakció lapot az Azure Portalon. További információért tekintse meg az [Egy számlához tartozó tranzakciók megtekintése az Azure Portalon](#view-transactions-for-an-invoice-in-the-azure-portal) című szakaszt.

2. Szűrjön rá a számlaszakasz nevére a tranzakciók megtekintéséhez.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez

A Microsoft-ügyfélszerződéshez tartozó számlázási fiókban a díjak összegei a számlázásig becsült, függőben lévő összegek. A függőben lévő díjakat megtekintheti az Azure Portalon a következő számla összegének megbecsléséhez. A függőben lévő díjak becsült összegek, és nem tartalmazzák az adót. A következő számlán feltüntetett díjak eltérhetnek a függőben lévő díjaktól.

### <a name="view-summary-of-pending-charges"></a>A függőben lévő díjak összegzésének megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza az **Összegzés** lehetőséget a képernyő felső részén.

5. A díjak szakasz az aktuális hónap és az utolsó hónap díjait jeleníti meg.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak, és a következő számlán fognak megjelenni.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

A függőben lévő díjak azonosításkor a díjakat kitevő egyes tranzakciók elemzésével értelmezheti a díjakat. Ezen a ponton a függőben lévő használati díjak nem jelennek meg az Összes tranzakció lapon. A függőben lévő használati díjakat az Azure-előfizetések oldalán tekintheti meg. További információért tekintse meg a [függőben lévő használati díjak megtekintését](#view-pending-usage-charges) ismertető részt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza a bal oldalon található **Összes tranzakció** lehetőséget.

5. Keressen rá a *függőben* kifejezésre. Használja az **Időtartomány** szűrőt a jelenlegi vagy az előző hónap függőben lévő díjainak megtekintéséhez.

   ![A függőben lévő tranzakciók listáját megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keressen rá a *Költségkezelés + számlázás* kifejezésre.

   ![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

4. Válassza ki az **Összes előfizetés** elemet az oldal bal oldalán.

5. Az Azure-előfizetések oldala megjeleníti a számlázási profilban szereplő egyes előfizetések aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

    ![A számlázási profil Azure-előfizetéseinek listáját megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Az Azure használati díjainak elemzése

A használatalapú díjak elemzéséhez használja az Azure használati adatait és díjait tartalmazó CSV-fájlt. A fájlt letöltheti egy számlához vagy a függőben lévő díjakhoz is. További információ: [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Részletes használat megtekintése számlaszakasz szerint

Az Azure használati adatait és díjait tartalmazó fájlt szűrheti a számlaszakaszokra vonatkozó használati díjak egyeztetéséhez.

Az alábbiak végigvezetik a Könyvelési osztály számlaszakasz számítási díjainak egyeztetési lépésein:

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | PDF-formátumú számla | Az Azure használati adatait és díjait tartalmazó CSV-fájl |
 | --- | --- |
 |Könyvelési osztály |invoiceSectionName |
 |Használati díjak – Microsoft Azure-csomag |productOrderName |
 |Compute |serviceFamily |

1. Szűrje a CSV-fájl **invoiceSectionName** oszlopát a **Könyvelési osztály** értékre.
2. Szűrje a CSV-fájl **productOrderName** oszlopát a **Microsoft Azure-csomag** értékre.
3. Szűrje a CSV-fájl **serviceFamily** oszlopát a **Microsoft.Compute** értékre.

![A számlaszakasz szerint szűrt, használati adatokat és díjakat tartalmazó fájlt megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)


### <a name="view-detailed-usage-by-subscription"></a>Részletes használat megtekintése előfizetés szerint

Az Azure használati adatait és díjait tartalmazó CSV-fájlt szűrheti az előfizetésekre vonatkozó használati díjak egyeztetéséhez. A számlaprofil összes előfizetésének megtekintéséhez lásd a [függőben lévő használati díjak megtekintését](#view-pending-usage-charges) ismertető részt.

Egy előfizetés díjainak azonosításakor használja az Azure használati adatait és díjait tartalmazó CSV-fájlt a díjak elemzéséhez.

A következő kép az Azure Portalon szereplő előfizetések listáját jeleníti meg.

![A számlázási profil Azure-előfizetéseinek listáját megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Szűrje az Azure használati adatait és díjait tartalmazó CSV-fájl **subscriptionName** oszlopát a **WA_Subscription** értékre a WA_Subscription részletes használati díjainak megtekintéséhez.

![Az előfizetés szerint szűrt, használati adatokat és díjakat tartalmazó fájlt megjelenítő képernyőkép](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>A számla befizetése

A számla befizetésére vonatkozó utasításokat a számla alján találja. [Útmutató a fizetéshez](billing-mca-understand-your-invoice.md#how-to-pay).

Ha már befizette számláját, a befizetés állapotát az Azure Portalon található Számlák oldalon ellenőrizheti.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlájával és a részletes használati adatokkal kapcsolatos további információkért lásd:

- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft-ügyfélszerződéshez tartozó számlán szereplő kifejezések ismertetése](billing-mca-understand-your-invoice.md)
- [A Microsoft-ügyfélszerződéshez tartozó, használati adatokat tartalmazó CSV-fájlban szereplő kifejezések ismertetése](billing-mca-understand-your-usage.md)

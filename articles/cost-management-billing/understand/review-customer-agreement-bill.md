---
title: A Microsoft Ügyfélszerződéshez tartozó számla áttekintése – Azure
description: Megtudhatja, hogyan tekintheti át a számlát és az erőforrás-használatot, valamint hogyan ellenőrizheti a Microsoft Ügyfélszerződéshez tartozó számla tételeit.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 21579c7c3acd726ac8958768238631333cf63c39
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84695422"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Oktatóanyag: A Microsoft Ügyfélszerződéshez tartozó számla áttekintése

Az egyes tranzakciók elemzésével áttekintheti a számlán szereplő tételeket. A rendszer minden hónapban létrehoz egy számlát az egyes számlázási profilokhoz a Microsoft-ügyfélszerződéshez tartozó számlázási fiókban. A számla az előző hónap összes díját tartalmazza. A számláit megtekintheti az Azure Portalon, és összehasonlíthatja a díjtételeket a használatrészletező fájlban szereplő adatokkal.

Ez az oktatóanyag csak azokra az Azure-ügyfelekre vonatkozik, akik rendelkeznek Microsoft Ügyfélszerződéssel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A kiszámlázott tranzakciók áttekintése az Azure Portalon
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

## <a name="prerequisites"></a>Előfeltételek

Számlázási fiókkal kell rendelkeznie egy Microsoft Ügyfélszerződéshez kapcsolódóan.

Hozzáféréssel kell rendelkeznie egy Microsoft Ügyfélszerződéshez. A számlázási profil tulajdonosának, közreműködőjének, olvasójának vagy számlakezelőjének kell lennie a számlázási és használati adatok megtekintéséhez. A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információért lásd a [számlázási profil szerepköreit és feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

30 napnál több időnek kell eltelnie el az Azure-előfizetés életbe lépése óta. Az Azure a számlázási időszak végén küld Önnek számlát.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése

A szerződéstípusra kattintva megállapíthatja, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz.

Az Azure Portalon írja be a *költségkezelés + számlázás* szöveget a keresőmezőbe, majd válassza a **Költségkezelés + Számlázás** lehetőséget.

![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Ha csak egy számlázási hatókörhöz rendelkezik hozzáféréssel, válassza a bal oldalon található **Tulajdonságok** elemet. Akkor fér hozzá a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz, ha a számlázási fiók típusa **Microsoft-ügyfélszerződés**.

![Képernyőkép a Tulajdonságok lapon található Microsoft Ügyfélszerződésről](./media/review-customer-agreement-bill/billing-mca-property.png)

Ha több számlázási hatókörhöz van hozzáférése, a számlázási fiók oszlopában ellenőrizheti a fiók típusát. Akkor fér hozzá a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz, ha bármelyik hatókör számlázási fiókjának típusa **Microsoft-ügyfélszerződés**.

![Képernyőkép a Számlázási fiókok listája lapon található Microsoft Ügyfélszerződésről](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>A kiszámlázott tranzakciók áttekintése az Azure Portalon

Az Azure Portalon válassza a bal oldalon található **Összes tranzakció** lehetőséget. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók, számlázási profil vagy számlaszakasz kiválasztása után tudja kiválasztani ki az **Összes tranzakció** lehetőséget.

Az Összes tranzakció lapon az alábbi információk jelennek meg:

![A kiszámlázott tranzakciók listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

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

A számla azonosítójára keresve szűrje ki a számlához tartozó tranzakciókat.

### <a name="view-transactions-by-invoice-sections"></a>Az egyes számlaszakaszok tranzakcióinak megtekintése

A számlaszakaszokkal rendezheti a számlázási profil számlájának költségeit. További tudnivalók. Amikor létrejön egy számla, a számlázási profil összes szakaszának összes díja megjelenik a számlán.

Az alábbi képen egy mintaszámla Könyvelési osztály szakasza és az abban található díjak láthatók.

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/review-customer-agreement-bill/invoicesection-details.png)

Ha beazonosította egy adott számlaszakasz díjait, megtekintheti a tranzakciókat az Azure Portalon a díjak értelmezéséhez.

Egy számla tranzakcióinak megtekintéséhez nyissa meg az Összes tranzakció lapot az Azure Portalon.

Szűrjön rá a számlaszakasz nevére a tranzakciók megtekintéséhez.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez

A Microsoft Ügyfélszerződéshez tartozó számlázási fiókban a díjak összegei a számlázásig becsült, függőben lévő összegek. A függőben lévő díjakat megtekintheti az Azure Portalon a következő számla összegének megbecsléséhez. A függőben lévő díjak becsült összegek, és nem tartalmazzák az adót. A következő számlán feltüntetett díjak eltérhetnek a függőben lévő díjaktól.

### <a name="view-summary-of-pending-charges"></a>A függőben lévő díjak összegzésének megtekintése

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza az **Összegzés** lehetőséget a képernyő felső részén.

A díjak szakasz az aktuális hónap és az utolsó hónap díjait jeleníti meg.

![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak, és a következő számlán fognak megjelenni.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

A függőben lévő díjak azonosításkor a díjakat kitevő egyes tranzakciók elemzésével értelmezheti a díjakat. Ezen a ponton a függőben lévő használati díjak nem jelennek meg az Összes tranzakció lapon. A függőben lévő használati díjakat az Azure-előfizetések oldalán tekintheti meg.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Az Azure Portalon írja be a *költségkezelés + számlázás* szöveget a keresőmezőbe, majd válassza a **Költségkezelés + Számlázás** lehetőséget.

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza a bal oldalon található **Összes tranzakció** lehetőséget.

Keressen rá a *függőben* kifejezésre. Használja az **Időtartomány** szűrőt a jelenlegi vagy az előző hónap függőben lévő díjainak megtekintéséhez.

![A függőben lévő tranzakciók listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Az Azure Portalon írja be a *költségkezelés + számlázás* szöveget a keresőmezőbe, majd válassza a **Költségkezelés + Számlázás** lehetőséget.

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza ki az **Összes előfizetés** elemet az oldal bal oldalán.

Az Azure-előfizetések oldala megjeleníti a számlázási profilban szereplő egyes előfizetések aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

![A számlázási profil Azure-előfizetéseinek listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Az Azure használati díjainak elemzése

A használatalapú díjak elemzéséhez használja az Azure használati adatait és díjait tartalmazó CSV-fájlt. A fájlt letöltheti egy számlához vagy a függőben lévő díjakhoz is.

### <a name="download-your-invoice-and-usage-details"></a>A számla és a használati adatok letöltése

A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt a Költségkezelés + Számlázás lapon. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát. Kattintson a sor végén található letöltési szimbólumra vagy három pontra (...). A **Letöltés** mezőben töltse le a használati adatokat tartalmazó fájlt és a számlát.

### <a name="view-detailed-usage-by-invoice-section"></a>Részletes használat megtekintése számlaszakasz szerint

Az Azure használati adatait és díjait tartalmazó fájlt szűrheti a számlaszakaszokra vonatkozó használati díjak egyeztetéséhez.

Az alábbi információk végigvezetik a Könyvelési osztály számlaszakasz számítási díjainak egyeztetési lépésein:

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF-formátumú számla | Az Azure használati adatait és díjait tartalmazó CSV-fájl |
| --- | --- |
|Könyvelési osztály |invoiceSectionName |
|Használati díjak – Microsoft Azure-csomag |productOrderName |
|Compute |serviceFamily |

Szűrje a CSV-fájl **invoiceSectionName** oszlopát a **Könyvelési osztály** értékre. Ezután szűrje a CSV-fájl **productOrderName** oszlopát a **Microsoft Azure-csomag** értékre. A következő lépésben szűrje a CSV-fájl **serviceFamily** oszlopát a **Microsoft.Compute** értékre.

![A számlaszakasz szerint szűrt, használati adatokat és díjakat tartalmazó fájlt megjelenítő képernyőkép](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-invoice-section.png)

### <a name="view-detailed-usage-by-subscription"></a>Részletes használat megtekintése előfizetés szerint

Az Azure használati adatait és díjait tartalmazó CSV-fájlt szűrheti az előfizetésekre vonatkozó használati díjak egyeztetéséhez. Egy előfizetés díjainak azonosításakor használja az Azure használati adatait és díjait tartalmazó CSV-fájlt a díjak elemzéséhez.

A következő kép az Azure Portalon szereplő előfizetések listáját jeleníti meg.

![A számlázási profil Azure-előfizetéseinek listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list-highlighted.png)

Szűrje az Azure használati adatait és díjait tartalmazó CSV-fájl **subscriptionName** oszlopát a **WA_Subscription** értékre a WA_Subscription részletes használati díjainak megtekintéséhez.

![Az előfizetés szerint szűrt, használati adatokat és díjakat tartalmazó fájlt megjelenítő képernyőkép](./media/review-customer-agreement-bill/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>A számla befizetése

A számla befizetésére vonatkozó utasításokat a számla alján találja. [Útmutató a fizetéshez](mca-understand-your-invoice.md#how-to-pay).

Ha már befizette számláját, a befizetés állapotát az Azure Portalon található Számlák oldalon ellenőrizheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A kiszámlázott tranzakciók áttekintése az Azure Portalon
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

A költségelemzés használatának megkezdéséhez tekintse át a gyorsútmutatót.

> [!div class="nextstepaction"]
> [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)

---
title: Tekintse át a Microsoft Customer szerződése számláját – Azure
description: Megtudhatja, hogyan tekintheti át a számla-és erőforrás-használatot, valamint ellenőrizheti a Microsoft ügyfél-szerződési számla díját.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: 3cbc4ab4f0e2ad18c7d1d430ed5501e23b5f5f4d
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987067"
---
# <a name="tutorial-review-your-microsoft-customer-agreement-invoice"></a>Oktatóanyag: a Microsoft Customer Agreement-számla áttekintése

Az egyes tranzakciók elemzésével áttekintheti a számlán felszámított díjakat. A rendszer minden hónapban létrehoz egy számlát az egyes számlázási profilokhoz a Microsoft-ügyfélszerződéshez tartozó számlázási fiókban. A számla az előző hónap összes díját tartalmazza. A számlákat a Azure Portalban tekintheti meg, és összehasonlíthatja a használati adatok fájljának díját.

Ez az oktatóanyag csak olyan Azure-ügyfelekre vonatkozik, akik Microsoft-szerződéssel rendelkeznek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számlázott tranzakciók ellenőrzése a Azure Portal
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

## <a name="prerequisites"></a>Előfeltételek

A Microsoft-ügyfél szerződéséhez számlázási fiókkal kell rendelkeznie.

Hozzáféréssel kell rendelkeznie egy Microsoft-ügyfél szerződéshez. Számlázási és használati információk megtekintéséhez számlázási profil tulajdonosának, közreműködőnek, olvasójának vagy számlafogadó-kezelőnek kell lennie. A Microsoft-ügyfélszerződések számlázási szerepköreivel kapcsolatos további információért lásd a [számlázási profil szerepköreit és feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

Az Azure-ra előfizetett naptól számítva 30 napnál hosszabbnak kell lennie. Az Azure a számlázási időszak végén küld Önnek számlát.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése

A szerződéstípusra kattintva megállapíthatja, hogy rendelkezik-e hozzáféréssel a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz.

A Azure Portal írja be a *Cost Management + számlázás* kifejezést a keresőmezőbe, majd válassza a **Cost Management + számlázás**lehetőséget.

![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/review-customer-agreement-bill/billing-search-cost-management-billing.png)

Ha csak egy számlázási hatókörhöz rendelkezik hozzáféréssel, válassza a bal oldalon található **Tulajdonságok** elemet. Akkor fér hozzá a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz, ha a számlázási fiók típusa **Microsoft-ügyfélszerződés**.

![Képernyőkép, amely a Microsoft ügyfél-szerződést mutatja a Tulajdonságok lapon](./media/review-customer-agreement-bill/billing-mca-property.png)

Ha több számlázási hatókörhöz van hozzáférése, a számlázási fiók oszlopában ellenőrizheti a fiók típusát. Akkor fér hozzá a Microsoft-ügyfélszerződéshez tartozó számlázási fiókhoz, ha bármelyik hatókör számlázási fiókjának típusa **Microsoft-ügyfélszerződés**.

![Képernyőkép, amely a Microsoft ügyfél-szerződést mutatja a számlázási fiókok listája oldalon](./media/review-customer-agreement-bill/billing-mca-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Számlázott tranzakciók ellenőrzése a Azure Portal

A Azure Portal válassza a lap bal oldalán található **összes tranzakció** elemet. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók, számlázási profil vagy számlaszakasz kiválasztása után tudja kiválasztani ki az **Összes tranzakció** lehetőséget.

Az Összes tranzakció lapon az alábbi információk jelennek meg:

![A kiszámlázott tranzakciók listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-billed-transactions-list.png)

|Column  |Meghatározás  |
|---------|---------|
|Dátum     | A tranzakció dátuma  |
|Számlaazonosító     | Annak a számlának az azonosítója, amelyen a tranzakció számlázva lett. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ |
|Tranzakció típusa     |  A tranzakció típusa, például vásárlás, lemondás vagy a használati díjak befizetése  |
|Termékcsalád     | Termékkategória, mint például a számítási szolgáltatás a virtuális gépek esetén, vagy az adatbázis az Azure SQL-adatbázis esetén|
|Termékváltozat     | A termék példányát azonosító egyedi kód |
|Mennyiség     |  A tranzakció összege      |
|Számlázási szakasz     | A tranzakció a számlázási profil számlájának ezen szakaszában jelenik meg |
|Számlázási profil     | A tranzakció ezen számlázási profil számláján jelenik meg |

A számlázási tranzakciók szűréséhez keresse meg a számla AZONOSÍTÓját.

### <a name="view-transactions-by-invoice-sections"></a>Az egyes számlaszakaszok tranzakcióinak megtekintése

A számlaszakaszokkal rendezheti a számlázási profil számlájának költségeit. További információ. Amikor létrejön egy számla, a számlázási profil összes szakaszának összes díja megjelenik a számlán.

Az alábbi képen egy mintaszámla Könyvelési osztály szakasza és az abban található díjak láthatók.

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/review-customer-agreement-bill/invoicesection-details.png)

Ha beazonosította egy adott számlaszakasz díjait, megtekintheti a tranzakciókat az Azure Portalon a díjak értelmezéséhez.

Egy számla tranzakcióinak megtekintéséhez nyissa meg az Összes tranzakció lapot az Azure Portalon.

Szűrjön rá a számlaszakasz nevére a tranzakciók megtekintéséhez.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez

A Microsoft-ügyfél szerződésének számlázási fiókjában a díjak becsültek, és a rendszer függőben lévőnek számít, amíg nem számláznak. A függőben lévő díjakat megtekintheti az Azure Portalon a következő számla összegének megbecsléséhez. A függőben lévő díjak becsült összegek, és nem tartalmazzák az adót. A következő számlán feltüntetett díjak eltérhetnek a függőben lévő díjaktól.

### <a name="view-summary-of-pending-charges"></a>A függőben lévő díjak összegzésének megtekintése

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza a képernyő felső részén található **Összefoglalás** lapot.

A díjak szakasz az aktuális hónap és az utolsó hónap díjait jeleníti meg.

![Képernyőkép az Azure Portal keresőmezőjéről a költségkezelés + számlázás keresőkifejezéssel](./media/review-customer-agreement-bill/mca-billing-profile-summary.png)

Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak, és a következő számlán fognak megjelenni.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

A függőben lévő díjak azonosításkor a díjakat kitevő egyes tranzakciók elemzésével értelmezheti a díjakat. Ezen a ponton a függőben lévő használati díjak nem jelennek meg az Összes tranzakció lapon. A függőben lévő használati díjakat az Azure-előfizetések oldalán tekintheti meg.

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

A Azure Portal írja be a *Cost Management + számlázás* kifejezést a keresőmezőbe, majd válassza a **Cost Management + számlázás**lehetőséget.

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza a bal oldalon található **Összes tranzakció** lehetőséget.

Keressen rá a *függőben* kifejezésre. Használja az **Időtartomány** szűrőt a jelenlegi vagy az előző hónap függőben lévő díjainak megtekintéséhez.

![A függőben lévő tranzakciók listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

Jelentkezzen be az [Azure portálra](https://portal.azure.com).

A Azure Portal írja be a *Cost Management + számlázás* kifejezést a keresőmezőbe, majd válassza a **Cost Management + számlázás**lehetőséget.

Válasszon ki egy számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza ki az **Összes előfizetés** elemet az oldal bal oldalán.

Az Azure-előfizetések oldala megjeleníti a számlázási profilban szereplő egyes előfizetések aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

![A számlázási profil Azure-előfizetéseinek listáját megjelenítő képernyőkép](./media/review-customer-agreement-bill/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Az Azure használati díjainak elemzése

A használatalapú díjak elemzéséhez használja az Azure használati adatait és díjait tartalmazó CSV-fájlt. A fájlt letöltheti egy számlához vagy a függőben lévő díjakhoz is.

### <a name="download-your-invoice-and-usage-details"></a>A számla és a használati adatok letöltése

A hozzáféréstől függően előfordulhat, hogy ki kell választania egy számlázási fiókot vagy számlázási profilt Cost Management + számlázásban. A bal oldali menüben válassza a **Számlázás** területen lévő **Számlák** elemet. A számlák rácsos elrendezésében keresse meg a letölteni kívánt számla sorát. A sor végén kattintson a letöltés szimbólumra vagy a három pontra (...). A **Letöltés** mezőben töltse le a használati adatokat tartalmazó fájlt és a számlát.

### <a name="view-detailed-usage-by-invoice-section"></a>Részletes használat megtekintése számlaszakasz szerint

Az Azure használati adatait és díjait tartalmazó fájlt szűrheti a számlaszakaszokra vonatkozó használati díjak egyeztetéséhez.

A következő információk végigvezetik a könyvelési osztály számlázásával kapcsolatos számítási díjak összeegyeztetésén:

![Szemléltető kép, amelyen a számlaszakasz-információk szerint jelennek meg a részletes adatok](./media/review-customer-agreement-bill/invoicesection-details.png)

| PDF-formátumú számla | Az Azure használati adatait és díjait tartalmazó CSV-fájl |
| --- | --- |
|Könyvelési osztály |invoiceSectionName |
|Használati díjak – Microsoft Azure-csomag |productOrderName |
|Számítási szolgáltatások |serviceFamily |

Szűrje a CSV-fájl **invoiceSectionName** oszlopát a **nyilvántartási osztályba**. Ezután szűrje a CSV-fájl **productOrderName** oszlopát **Microsoft Azure tervre**. Ezután szűrje a CSV-fájl **serviceFamily** oszlopát a **Microsoft. számítás**elemre.

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

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számlázott tranzakciók ellenőrzése a Azure Portal
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

Fejezze be a gyors üzembe helyezési útmutatót a Cost Analysis használatának megkezdéséhez.

> [!div class="nextstepaction"]
> [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)

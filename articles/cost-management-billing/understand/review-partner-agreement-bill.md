---
title: A Microsoft partneri szerződés számlájának áttekintése – Azure
description: Megtudhatja, hogyan tekintheti át a számla-és erőforrás-használatot, valamint ellenőrizheti a Microsoft partneri szerződés díját.
author: jureid
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: banders
ms.openlocfilehash: e3823a9eb2e0713f7f42e4e02808ef957efc5944
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990839"
---
# <a name="tutorial-review-your-microsoft-partner-agreement-invoice"></a>Oktatóanyag: a Microsoft partneri szerződéssel foglalkozó számla áttekintése

 A rendszer minden hónapban létrehoz egy számlát az egyes számlázási profilokhoz a Microsoft Partnerszerződéshez tartozó számlázási fiókban. A számla az előző hónap összes ügyfél által fizetendő díját tartalmazza. Az egyes tranzakciók Azure Portalon történő elemzésével értelmezheti a számlán feltüntetett díjakat. A Azure Portal is megtekintheti a számlákat, és összehasonlíthatja a használati adatok fájljának díját.

További tudnivalókért lásd [a számlák az Azure Portalról való letöltését](download-azure-invoice.md) ismertető cikket.

Ez az oktatóanyag csak a Microsoft partneri szerződéssel rendelkező Azure-partnerekre vonatkozik.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számlázott tranzakciók ellenőrzése a Azure Portal
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

## <a name="prerequisites"></a>Előfeltételek

A Microsoft partneri szerződéshez hozzáféréssel kell rendelkeznie a számlázási fiókhoz.

Az Azure-ra előfizetett naptól számítva 30 napnál hosszabbnak kell lennie. Az Azure a számlázási időszak végén küld Önnek számlát.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése

A szerződéstípusra kattintva megállapíthatja, hogy rendelkezik-e hozzáféréssel a Microsoft-partnerszerződéshez tartozó számlázási fiókhoz.

A Azure Portal írja be a *Cost Management + számlázás* kifejezést a keresőmezőbe, majd válassza a **Cost Management + számlázás**lehetőséget.

![Képernyőkép az Azure Portal keresőmezőjéről](./media/review-partner-agreement-bill/billing-search-cost-management-billing.png)

Ha csak egy számlázási hatókörhöz rendelkezik hozzáféréssel, válassza a bal oldalon található **Tulajdonságok** elemet. Akkor fér hozzá a Microsoft-partnerszerződéshez tartozó számlázási fiókhoz, ha a számlázási fiók típusa **Microsoft-partnerszerződés**.

![A Microsoft partneri szerződést bemutató képernyőkép a tulajdonságok oldalon](./media/review-partner-agreement-bill/billing-account-properties-partner-agreement.png)

Ha több számlázási hatókörhöz van hozzáférése, a számlázási fiók oszlopában ellenőrizheti a fiók típusát. Akkor fér hozzá a Microsoft-partnerszerződéshez tartozó számlázási fiókhoz, ha bármelyik hatókör számlázási fiókjának típusa **Microsoft-partnerszerződés**.

![A Microsoft partneri szerződést bemutató képernyőkép a számlázási fiókok listája oldalon](./media/review-partner-agreement-bill/mpa-in-the-list.png)

## <a name="review-invoiced-transactions-in-the-azure-portal"></a>Számlázott tranzakciók ellenőrzése a Azure Portal

Cost Management + számlázás lapon válassza a lap bal oldalán található **összes tranzakció** elemet. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási fiók, számlázási profil vagy ügyfél kiválasztása után tudja kiválasztani a **Minden tranzakció** lehetőséget.

Az Összes tranzakció lapon az alábbi információk jelennek meg:

![A kiszámlázott tranzakciók listáját megjelenítő képernyőkép](./media/review-partner-agreement-bill/all-transactions.png)

|Column  |Meghatározás  |
|---------|---------|
|Dátum     | A tranzakció dátuma  |
|Számlaazonosító     | Annak a számlának az azonosítója, amelyen a tranzakció számlázva lett. Ha támogatási kérést nyújt be, ossza meg az azonosítót az Azure-támogatással, hogy a támogatási kérésre gyorsabban kapjon választ |
|Tranzakció típusa     |  A tranzakció típusa, például vásárlás, lemondás vagy a használati díjak befizetése  |
|Termékcsalád     | Termékkategória, mint például a számítási szolgáltatás a virtuális gépek esetén, vagy az adatbázis az Azure SQL-adatbázis esetén|
|Termék SDKU     | A termék példányát azonosító egyedi kód |
|Mennyiség     |  A tranzakció összege      |
|Számlázási profil     | A tranzakció ezen számlázási profil számláján jelenik meg |

A számlázási tranzakciók szűréséhez keresse meg a számla AZONOSÍTÓját.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez

A díjak összegei a számlázásig becsült, függőben lévő összegek. A Microsoft Partnerszerződése számlázási profiljához tartozó, függőben lévő díjakat megtekintheti az Azure Portalon a következő számla összegének megbecsléséhez. A függőben lévő díjak becsült összegek, és nem tartalmazzák az adót. A következő számlán feltüntetett díjak eltérhetnek a függőben lévő díjaktól.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

A függőben lévő díjak azonosításkor a díjakat kitevő egyes tranzakciók elemzésével értelmezheti a díjakat. Ezen a ponton a függőben lévő használati díjak nem jelennek meg az Összes tranzakció lapon. A függőben lévő használati díjakat az Azure-előfizetések oldalán tekintheti meg.

Cost Management és számlázás lapon válassza ki a számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza a bal oldalon található **Összes tranzakció** lehetőséget.

Keressen rá a *függőben* kifejezésre. Használja az **Időtartomány** szűrőt a jelenlegi vagy az előző hónap függőben lévő díjainak megtekintéséhez.

<!-- ![Screenshot that shows the pending transactions list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-pending-transactions.png) -->

### <a name="view-pending-charges-by-customer"></a>Függőben lévő díjak megtekintése ügyfelenként

Cost Management és számlázás lapon válassza ki a számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza ki az **Ügyfelek** elemet a lap bal oldalán.

<!-- ![screenshot of billing profile customers list](./media/billing-mpa-understand-your-bill/mpa-billing-profile-customers.png) -->

Az Ügyfelek lap megjeleníti a számlázási profilhoz tartozó egyes ügyfelek aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

Cost Management és számlázás lapon válassza ki a számlázási profilt. A hozzáférésétől függően előfordulhat, hogy ki kell választania egy számlázási fiókot. A számlázási fiókon belül válassza ki a **Számlázási profilok** lehetőséget, majd a számlázási profilt.

Válassza ki az **Azure-előfizetések** elemet a lap bal oldalán. Az Azure-előfizetések oldala megjeleníti a számlázási profilban szereplő egyes előfizetések aktuális és utolsó havi díjait. Az aktuális hónap díjai az aktuális hónap függőben lévő díjai, és számlázásuk a havi számla létrehozásakor történik. Ha az előző havi számla még nincs létrehozva, a múlt havi díjak is függőben vannak.

<!--     ![Screenshot that shows the Azure subscriptions list for MPA billing profile](./media/billing-mpa-understand-your-bill/mpa-billing-profile-subscriptions-list.png) -->

## <a name="analyze-your-azure-usage-charges"></a>Az Azure használati díjainak elemzése

A használatalapú díjak elemzéséhez használja az Azure használati adatait és díjait tartalmazó CSV-fájlt. Az Azure használati adatait és díjait tartalmazó fájlt szűrheti a PDF formátumú számlában szereplő egyes termékekre vonatkozó használati díjak egyeztetéséhez. Egy adott termék részletes használati díjainak megtekintéséhez szűrje a **product** oszlopot az Azure használati adatait és díjait tartalmazó CSV-fájlban, hogy csak annak a terméknek a neve szerepeljen.

A **customerName** oszlopot is szűrheti az Azure használati adatait és díjait tartalmazó CSV-fájlban az egyes ügyfelekre vonatkozó napi használati díjak megtekintéséhez. Ha meg szeretné tekinteni a napi használati díjakat Azure-előfizetésenként, szűrje a **subscriptionName** oszlopot.

## <a name="pay-your-bill"></a>A számla befizetése

A számla befizetésére vonatkozó utasításokat a számla alján találja. Fizethet átutalással vagy csekkel a számlázási ciklus végét követő 60 napon belül.

Ha már befizette számláját, a befizetés állapotát az Azure Portalon található Számlák oldalon ellenőrizheti.

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számlázott tranzakciók ellenőrzése a Azure Portal
> * Függőben lévő díjak áttekintése a következő számla összegének megbecsléséhez
> * Az Azure használati díjainak elemzése

Ismerkedjen meg Azure Cost Management partnerekkel való használatával.

> [!div class="nextstepaction"]
> [Ismerkedjen meg Azure Cost Management partnerekkel](../costs/get-started-partners.md)

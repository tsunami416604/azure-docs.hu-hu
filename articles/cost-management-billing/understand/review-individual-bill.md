---
title: Egyéni Azure-számla áttekintése
description: Megtudhatja, hogyan értelmezheti a számla-és erőforrás-használatot, és ellenőrizheti az egyes Azure-előfizetések díját.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2019
ms.author: banders
ms.openlocfilehash: 1249758e5dd5b7e823c11890e57549195da22529
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75987347"
---
# <a name="tutorial-review-your-individual-azure-bill"></a>Oktatóanyag: egyéni Azure-számla áttekintése

Ez a cikk segítséget nyújt az Azure-számla megismerésében és áttekintésében. Az egyes számlázási időszakok esetében általában e-mailben kell számlát kapnia. A számla az Azure-számla ábrázolása. A számlán ugyanazok a költségadatok érhetők el, mint a Azure Portal. Ebben az oktatóanyagban összehasonlítjuk a számláját a részletes napi használati fájllal és a Azure Portal a Cost Analysis szolgáltatással.

Ez az oktatóanyag csak az egyéni előfizetéssel rendelkező Azure-ügyfelekre vonatkozik. A közös egyéni előfizetések az Azure-webhelyről közvetlenül vásárolt utólagos elszámolású díjszabással rendelkeznek.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számlázott díjak összehasonlítása a használati fájllal
> * Díjak és használat összehasonlítása a Cost Analysis szolgáltatásban

## <a name="prerequisites"></a>Előfeltételek

Fizetős *Microsoft Online Services-program* számlázási fiókkal kell rendelkeznie. A fiók akkor jön létre, amikor az Azure-webhelyről regisztrál az Azure-ra. Ha például egy [fiókja](https://azure.microsoft.com/offers/ms-azr-0003p/) utólagos elszámolású díjszabással rendelkezik, vagy ha Ön [Visual Studio-előfizető](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

Az [ingyenes Azure-fiókokhoz](https://azure.microsoft.com/offers/ms-azr-0044p/) tartozó számlák csak akkor jönnek létre, ha túllépik a havi kreditet.

Az Azure-ra előfizetett naptól számítva 30 napnál hosszabbnak kell lennie. Az Azure a számlázási időszak végén küld Önnek számlát.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

- Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="compare-invoiced-charges-with-usage-file"></a>Számlázott díjak összehasonlítása a használati fájllal

<a name="charges"></a>

A használat és a költségek összehasonlításának első lépése a számla és a használati fájlok letöltése. A részletes használatot tartalmazó CSV-fájl a számlázási időszak és a napi használat szerint jeleníti meg a díjakat. Nem tartalmaz adózási információt. A fájlok letöltéséhez fiók-rendszergazdának kell lennie, vagy tulajdonosi szerepkörrel kell rendelkeznie.

A Azure Portal írja be a keresőmezőbe az *előfizetések* kifejezést, majd kattintson az [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)elemre.

![Az előfizetések között navigáljon](./media/review-individual-bill/navigate-subscriptions.png)

Az előfizetések listájában kattintson az előfizetésre.

A **számlázás**alatt kattintson a **számlák**elemre.

A számlák listájában keresse meg a letölteni kívánt listát, majd kattintson a letöltés szimbólumra. Előfordulhat, hogy módosítania kell a TimeSpan a régebbi számlák megtekintéséhez. Eltarthat néhány percig a használati adatok fájljának és számlájának létrehozásához.

![A számlázási időszakokat, a letöltési lehetőséget és az egyes számlázási időszakok teljes díját ábrázoló képernyőkép](./media/review-individual-bill/download-invoice.png)

A használat és díjak letöltése ablakban kattintson a **CSV letöltése** és a **számla letöltése**elemre.

![Képernyőkép a Számlák és használati adatok letöltése oldalról](./media/review-individual-bill/usageandinvoice.png)

Ha **nem érhető el** , több oka is van annak, hogy nem látja a használati adatokat vagy a számlát:

- 30 napnál kevesebb telt el az Azure-előfizetés életbe lépése óta.
- Nincs használat a számlázási időszakra vonatkozóan.
- Még nem jön létre számla. Várja meg a számlázási időszak végét.
- Nem rendelkezik engedéllyel a számlák megtekintéséhez. Előfordulhat, hogy nem látja a régi számlákat, kivéve, ha Ön a fiók rendszergazdája. További információ a számlázási információhoz való hozzáférésről: [Az Azure-beli számlázáshoz való hozzáférés kezelése szerepkörök használatával](../manage/manage-billing-access.md).
- Ha ingyenes próbaverziós előfizetéssel rendelkezik, vagy nem haladta meg az előfizetésében foglalt jóváírás havi keretét, csak abban az esetben fog számlát kapni, ha Microsoft-ügyfélszerződéssel rendelkezik.

Ezután tekintse át a díjakat. A számla az adók és a használati díjak értékét jeleníti meg.

![Példa Azure-számlára](./media/review-individual-bill/invoice-usage-charge.png)

Nyissa meg a letöltött CSV-használati fájlt. A fájl végén adja meg a *Cost* oszlop összes elemének értékét.

![Példa az összefoglalt költségeket tartalmazó használati fájlra](./media/review-individual-bill/usage-file-usage-charges.png)

 Az összefoglalt *költség* értékének pontosan egyeznie kell a számla *használati* költségeivel.

A használati díjak a mérési szinten jelennek meg. A következő kifejezések a számlában és a részletes használati fájlban is ugyanazt a dolgot jelentik. A számlán fellelhető számlázási ciklus például megegyezik a részletes használati fájlban feltüntetett számlázási időszakkal.

| Számla (PDF) | Részletes használat (CSV)|
| --- | --- |
|Billing Cycle (Számlázási ciklus) | BillingPeriodStartDate BillingPeriodEndDate |
|Név |Mérőkategória |
|Type (Típus) |Meter Subcategory (Mérési alkategória) |
|Erőforrás |MeterName |
|Region (Régió) |MeterRegion |
|Consumed (Felhasznált mennyiség) | Mennyiség |
|Alap |Included Quantity (Bennefoglalt mennyiség) |
|Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |
|Rate (Egységár) | EffectivePrice|
| Value (Díj) | Költség |

A számla **használati díjak** szakasza a számlázási időszak során felhasznált egyes mérőszámok teljes értékét (költségét) jeleníti meg. Az alábbi képen például az Azure Storage szolgáltatás használati díja látható a *P10-lemezek* erőforráshoz.

![A számlán feltüntetett használati díjak](./media/review-individual-bill/invoice-usage-charges.png)

A CSV-használati fájlban a számla alapján megjelenő megfelelő erőforrásra a *MeterName* szerinti szűrést. Ezután összesítse az oszlopban szereplő elemek *költségérték* értékét. Íme egy példa.

![A használati fájl összefoglalt értéke MeterName](./media/review-individual-bill/usage-file-usage-charge-resource.png)

Az összefoglalt *költség* értékének pontosan egyeznie kell a számlán felszámított egyes erőforrások *használati díjaival* .

További információ: [Az Azure-beli számla megismerése](understand-invoice.md) és [Az Azure részletes használatának megismerése](understand-usage.md).

## <a name="compare-charges-and-usage-in-cost-analysis"></a>Díjak és használat összehasonlítása a Cost Analysis szolgáltatásban

A Azure Portal költségeinek elemzése a díjak ellenőrzésében is segítséget nyújt. A számlázott használat és díjak gyors áttekintéséhez válassza ki az előfizetést a Azure Portal [előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) . Ezután kattintson a **Cost Analysis** elemre, majd a nézetek listában kattintson a **számla részletei**elemre.

![Példa a számla részleteinek kiválasztására](./media/review-individual-bill/cost-analysis-select-invoice-details.png)

Ezután a dátumtartomány listából válasszon ki egy időszakot a számlához. Adjon hozzá egy szűrőt a számla számához, majd válassza ki azt a InvoiceNumber, amely megegyezik a számlán szereplővel. A Cost Analysis a számlázott elemek részletes adatait jeleníti meg.

![Példa a számlázott Cost-részletekre a Cost Analysis szolgáltatásban](./media/review-individual-bill/cost-analysis-service-usage-charges.png)

A Cost Analysis szolgáltatásban megjelenő költségeknek pontosan meg kell egyezniük a számlán felszámított egyes erőforrások *használati díjaival* .

![A számlán feltüntetett használati díjak](./media/review-individual-bill/invoice-usage-charges.png)

## <a name="external"></a>A külső szolgáltatásokat külön számlázzuk

A külső szolgáltatások vagy a piactér díjai olyan erőforrásokra vonatkoznak, amelyeket külső szoftverszállítók készítettek. Ezek az erőforrások az Azure Marketplace-ből érhetők el. A Barracuda-tűzfal például egy harmadik fél által kínált Azure Marketplace-erőforrás. A tűzfal és a hozzá tartozó mérőszámok összes díja külső szolgáltatási díjként jelenik meg.

A külső szolgáltatási díjakat külön számlázzuk. Ezek a díjak nincsenek feltüntetve az Azure-számlán. További tudnivalókért lásd [az Azure külső szolgáltatásaival kapcsolatos díjak ismertetését](understand-azure-marketplace-charges.md).

### <a name="resources-billed-by-usage-meters"></a>A használat mérésére szolgáló eszközök által számlázott erőforrások

Az Azure nem közvetlenül az erőforrásköltségek alapján számláz. Az erőforrások költségeit egy vagy több mérőszám alapján számítjuk ki. A mérőszámokkal az erőforrás kihasználtságát mérjük az erőforrás teljes élettartama során. Ezen mérőszámok alapján számítjuk ki a számlán szereplő költséget.

Ha egyetlen Azure-erőforrást hoz létre, például egy virtuális gépet, akkor a rendszer egy vagy több fogyasztásmérő-példányt hoz létre. A mérőszámok az erőforrás adott idő alatti kihasználtságát mérik. Minden mérőeszköz használati adatokat vesz fel, amelyek alapján az Azure kiszámítja a számlán szereplő összeget.

Az Azure-ban létrehozott egyetlen virtuális gép használatának nyomon követéséhez például a következő mérőeszközök hozhatók létre:

- Compute Hours – számítási óraszám
- IP-cím szolgáltatásórái
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard felügyelt lemez
- Standard felügyeltlemez-műveletek
- Standard IO-lemez
- Standard IO – blokkblob-olvasás
- Standard IO – blokkblob-írás
- Szabványos IO – blokkblob-törlés

A virtuális gép létrehozásakor minden mérőeszköz elkezd használati adatokat szolgáltatni. Ezt a használatot és a mérőeszköz díjszabását az Azure-mérési rendszerben követjük nyomon.

Megtekintheti a számla kiszámításához használt mérőszámokat a használati CSV-fájlban.

## <a name="payment"></a>A számla befizetése

Ha fizetési módként állít be bankkártyát, a rendszer a számlázási időszak lejárta után 10 napon belül automatikusan kiszámlázza a befizetést. A hitelkártya-kivonatban a költségsorban a következő szerepel: **MSFT Azure**.

A megterhelt hitelkártya megváltoztatásához tekintse meg a [hitelkártya az Azure-ban történő hozzáadását, frissítését vagy eltávolítását](../manage/change-credit-card.md) ismertető szakaszt.

[Számlás fizetés](../manage/pay-by-invoice.md) esetén küldje az utalást a számla alján megadott helyre.

Az utalás állapotának ellenőrzéséhez [hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számlázott díjak összehasonlítása a használati fájllal
> * Díjak és használat összehasonlítása a Cost Analysis szolgáltatásban

Fejezze be a gyors üzembe helyezési útmutatót a Cost Analysis használatának megkezdéséhez.

> [!div class="nextstepaction"]
> [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md)

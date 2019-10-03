---
title: Az Azure számlázásának ismertetése
description: Ismerje meg, hogyan olvashatók le és értelmezhetők az Azure-előfizetésre vonatkozó használati adatok és a hozzá tartozó számla.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: ceeacf216b1d3258aa73d86c613d28e00e9480bb
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718618"
---
# <a name="understand-your-microsoft-azure-bill"></a>A Microsoft Azure-számla ismertetése
Az Azure-számla értelmezéséhez hasonlítsa össze a számlát a részletes napi használati adatok fájljával és a költségkezelési jelentésekkel az Azure Portalon.

Ez a cikk a következő ügyfelekre nem vonatkozik:
- Nagyvállalati Szerződéssel rendelkező Azure-ügyfelek (EA-ügyfelek). Ha Ön Nagyvállalati Szerződéssel rendelkező ügyfél, tekintse meg a [nagyvállalati szerződéssel rendelkező Azure-ügyfelek számára készült számlaismertető](billing-understand-your-bill-ea.md) szakaszt.
- [Microsoft-ügyfélszerződéssel](#check-access-to-a-microsoft-customer-agreement) rendelkező Azure-ügyfelek. Ha Microsoft-ügyfélszerződéssel rendelkezik, tekintse meg a [Microsoft-ügyfélszerződés számláján szereplő Azure-díjak ismertetését](billing-mca-understand-your-bill.md).

Ha meg szeretné érteni az Azure Cloud Solution Provider (az Azure CSP) program számlázásának működését, beleértve a számlázási ciklust, a díjszabást és a használatot, tekintse meg [az Azure CSP számlázásának áttekintését](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>Díjak felülvizsgálata

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Ha olyan díjat lát a számláján, amelyről többet szeretne tudni, összevetheti a használatot és a költségeket a használati adatok fájljával vagy az Azure Portallal.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>1\. módszer: Használat és költségek összevetése a használati adatok fájljával

A részletes használatot tartalmazó CSV-fájl a számlázási időszak és a napi használat szerint jeleníti meg a díjakat. A fájl letöltéséhez vagy megtekintéséhez tekintse meg az [Azure számlázási és napi használati adatainak lekérését](billing-download-azure-invoice-daily-usage-date.md) ismertető szakaszt.

A használati díjak a mérési szinten jelennek meg. A következő kifejezések a számlában és a részletes használati fájlban is ugyanazt a dolgot jelentik. A számlán fellelhető számlázási ciklus például megegyezik a részletes használati fájlban feltüntetett számlázási időszakkal.

 | Számla (PDF) | Részletes használat (CSV)|
 | --- | --- |
|Billing Cycle (Számlázási ciklus) | Billing Period (Számlázási időszak) |
 |Name (Név) |Meter Category (Mérési kategória) |
 |Típus |Meter Subcategory (Mérési alkategória) |
 |Erőforrás |Meter Name (Mérés neve) |
 |Régió |Meter Region (Mérési régió) |
 |Consumed (Felhasznált mennyiség) |Consumed Quantity (Felhasznált mennyiség) |
 |Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
 |Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |

A számla **Használati díjak** szakasza a számlázási időszakban feltüntetett egyes mérőszámok teljes fogyasztási értékét jeleníti meg. Az alábbi képen például az Azure Scheduler szolgáltatás használati díja látható.

![A számlán feltüntetett használati díjak](./media/billing-understand-your-bill/1.png)

A részletes használatot bemutató CSV-fájl **Kivonat** szakasza ugyanazt a díjat mutatja. Mind a *Felhasznált* mennyiség, mind az *Érték* megegyezik a számlán szereplő adatokkal.

![A CSV-fájlban feltüntetett használati díjak](./media/billing-understand-your-bill/2.png)

A díj napi lebontásának megtekintéséhez lépjen a CSV-fájl **Napi használat** szakaszához. A *Fogyasztásmérő kategóriája* alatt szűrje a találatokat a *Scheduler* szerint. Láthatja, hogy a mérők mely napokon voltak használatban, és mennyi lett belőlük felhasználva. Összehasonlításképpen az *Erőforrásokkal* és az *Erőforráscsoportokkal* kapcsolatos információk is láthatók. A *Felhasznált* értékek összegének meg kell egyeznie a számlán láthatókkal.

![A CSV-fájl Napi használat szakasza](./media/billing-understand-your-bill/3.png)

A napi költségek kiszámításához szorozza össze a *Felhasznált* mennyiséget a **Kivonat** szakaszban feltüntetett *Díjak* értékkel.

További tudnivalókért lásd:

- [Az Azure-számlák ismertetése](billing-understand-your-invoice.md)
- [Az Azure részletes használatának ismertetése](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>2\. lehetőség: A használat és a költségek összevetése az Azure Portalon

Az Azure Portallal a díjakat is ellenőrizheti. Ha gyors áttekintést szeretne kapni a számlázott használatról és díjakról, tekintse meg a költségkezelési diagramokat.

1. Az Azure Portalon lépjen az [Előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) oldalra.
1. Válassza ki az előfizetés > **Költségelemzés** menüpontot.
1. Szűrjön **Időtartomány** szerint.
1. Folytatva az előző példát, az Azure Scheduler szolgáltatás használati díját fogja látni.

   ![Költségelemzés nézet az Azure Portalon](./media/billing-understand-your-bill/4.png)

1. A napi költséglebontás megtekintéséhez válassza ki a díjat megjelenítő sort.

   ![Költségelőzmények nézet az Azure Portalon](./media/billing-understand-your-bill/5.png)

További tudnivalókért lásd [az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzését](billing-getting-started.md#costs) ismertető szakaszt.

## <a name="external"></a>A külső szolgáltatásokat külön számlázzuk

A külső szolgáltatások vagy a piactér díjai olyan erőforrásokra vonatkoznak, amelyeket külső szoftverszállítók készítettek. Ezek az erőforrások az Azure Marketplace-ből érhetők el. A Barracuda-tűzfal például egy harmadik fél által kínált Azure Marketplace-erőforrás. A tűzfal és a hozzá tartozó mérőszámok összes díja külső szolgáltatási díjként jelenik meg.

A külső szolgáltatási díjakat külön számlázzuk. Ezek a díjak nincsenek feltüntetve az Azure-számlán. További tudnivalókért lásd [az Azure külső szolgáltatásaival kapcsolatos díjak ismertetését](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>A használat mérésére szolgáló eszközök által számlázott erőforrások

Az Azure nem közvetlenül az erőforrásköltségek alapján számláz. Az erőforrások költségeit egy vagy több mérőszám alapján számítjuk ki. A mérőszámokkal az erőforrás kihasználtságát mérjük az erőforrás teljes élettartama során. Ezen mérőszámok alapján számítjuk ki a számlán szereplő költséget.

Ha például egyetlen Azure-erőforrást hoz létre, mint például egy virtuális gépet, akkor arra egy vagy több mérőegységet is létrehozunk. A mérőszámok az erőforrás adott idő alatti kihasználtságát mérik. Minden mérőeszköz használati adatokat vesz fel, amelyek alapján az Azure kiszámítja a számlán szereplő összeget.

Az Azure-ban létrehozott egyetlen virtuális gép használatának nyomon követéséhez például a következő mérőeszközök hozhatók létre:

- Üzemóra
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

## <a name="payment"></a>A számla befizetése

Ha hitelkártyás fizetést állít be fizetési módként, a rendszer a számlázási időszak lejárta után 10 napon belül automatikusan felszámítja az összeget. A hitelkártya-kivonatban a költségsorban a következő szerepel: **MSFT Azure**.

A megterhelt hitelkártya megváltoztatásához tekintse meg a [hitelkártya az Azure-ban történő hozzáadását, frissítését vagy eltávolítását](billing-how-to-change-credit-card.md) ismertető szakaszt.

[Számlás fizetés](billing-how-to-pay-by-invoice.md) esetén küldje az utalást a számla alján megadott helyre.

Az utalás állapotának ellenőrzéséhez [hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tippek a költségkezeléshez

- Költségbecslés a következővel:
  - [Azure-díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
  - [Teljes bekerülési költség kalkulátora](https://aka.ms/azure-tco-calculator)
  - [Az egyes szolgáltatásokra vonatkozó részletes díjszabási információk](https://azure.microsoft.com/pricing/)
- [A használat és a költségek rendszeres áttekintése az Azure Portalon](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Részletek

- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft Azure-számla kifejezéseinek értelmezése](billing-understand-your-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek értelmezése](billing-understand-your-usage.md)
- [Költségkezelés az Azure Portalon](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Az Azure-számlázással és -költségkezeléssel kapcsolatos váratlan költségek megelőzése](billing-getting-started.md#costs)

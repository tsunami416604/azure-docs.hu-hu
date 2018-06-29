---
title: A számlázási megismerése az Azure-bA |} Microsoft Docs
description: Elolvastam és megértettem a használati és az Azure-előfizetéshez tartozó számlázási útmutató
services: ''
documentationcenter: ''
author: tonguyen10
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/14/2018
ms.author: tonguyen
ms.openlocfilehash: 689ea9e0d029bb65bc579fc914c6ed3073b4a96b
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064001"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>A Microsoft Azure-hoz kapcsolódó számlák magyarázata
Az Azure számlázásának ismertetése, hasonlítsa össze a számla a részletes napi használati fájl és a költségek jelentések az Azure portálon.

>[!NOTE]
>Ez a cikk nem alkalmazza a nagyvállalati szerződés (EA) vonatkozik. Ha Ön egy EA ügyfél [a vállalati portál számla dokumentációjában található.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

Lásd: a számla PDF és a részletes napi használati fájl CSV letölthető másolatának beszerzéséhez [beolvasni a számlázási számla és a napi használati adatok Azure](billing-download-azure-invoice-daily-usage-date.md). 

Részletes feltételeket és a számla és a napi használat fájl részletes leírását lásd: [a Microsoft Azure számlán feltételek megismeréséhez](billing-understand-your-invoice.md) és [megértése feltételeket a Microsoft Azure részletes használati](billing-understand-your-usage.md). 

További részletek a felügyeleti költségek jelentésekre: [Azure-portálon költségkezelésére](https://docs.microsoft.com/azure/billing/billing-getting-started).

## <a name="charges"></a>Hogyan tehetem meg arról, hogy helyesen-e a költségeket a számla?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Ha a kívánt további részleteket a számlán járnak, többféle beállítások.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>1. lehetőség: Tekintse át a számla és a használati és költségek összehasonlításra a részletes használati CSV-fájl

A részletes használati CSV-fájlt a díjak számlázási időszakban és napi használat jeleníti meg. Ahhoz, hogy a részletes használati CSV-fájlban, lásd: [beolvasni a számlázási számla és a napi használati adatok Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

A használati díjak jelennek meg a mérési szinten. A következő feltételek ugyanazt a számla és a részletes használati fájl is jelenti. Például a számlázási ciklusban, a számlán értéke megegyezik a részletes használati fájl számlázási időszakban.

 | Számla (PDF) | A részletes használati (CSV)|
 | --- | --- |
|Billing Cycle (Számlázási ciklus) | Billing Period (Számlázási időszak) |
 |Name (Név) |Meter Category (Mérési kategória) |
 |Típus |A mérési alkategória |
 |Erőforrás |Meter Name (Mérés neve) |
 |Régió |Meter Region (Mérési régió) |
 |Consumed (Felhasznált mennyiség) |Consumed Quantity (Felhasznált mennyiség) |
 |Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
 |Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |

A **használati díjak** a számla szakasza minden mérő a a számlázási időszak alatt felhasznált teljes értékkel rendelkezik. Például az alábbi képernyőfelvételen látható, az Azure Scheduler szolgáltatás használati járnak.

![Számla használati díjak](./media/billing-understand-your-bill/1.png)

A **utasítás** a részletes használati CSV része mutatja meg az azonos kell fizetni. Mindkét a *felhasznált* összeg és *érték* felel meg a számla kibocsátása.

![Fürt megosztott kötetei szolgáltatás használati díjak](./media/billing-understand-your-bill/2.png)

Ez a díj részletes információkat naponta, válassza a **napi használat** a CSV részét. "Feladatütemező" szerint szűrhet *mérő kategória* láthatja, hogy melyik napon és a mérési lett megadva, és mekkora felhasznált. A *erőforrás* és *erőforráscsoport* információk is láthatók az összehasonlításhoz. A *felhasznált* értékeket kell, hogy egyezzen a számlán is látható.

![A fürt megosztott kötetei szolgáltatás napi használati szakasz](./media/billing-understand-your-bill/3.png)

Ahhoz, hogy a napi költségét, szorozza meg a *felhasznált* rendelkező összegek a *arány* értéket a **utasítás** szakasz.

A számla kapcsolatos további információkért lásd: [megismerése az Azure számla](billing-understand-your-invoice.md).

A fürt megosztott kötetei szolgáltatás a oszlopainak mindegyike kapcsolatos további tudnivalókért lásd: [az Azure részletes használatának megértéséhez](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>2. lehetőség: A számla tekintse át, és hasonlítsa össze a használati és költségek az Azure-portálon

Az Azure portál segítségével ellenőrizze a költségeket. Az Azure-portálon költség felügyeleti diagramok a használati és a költségek a számla gyors áttekintést biztosít.

A fenti példát folytatva, látogasson el a [előfizetések oldalán](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), jelölje ki az előfizetését, és válassza a **elemzés**. Ott adja meg az időtartama, és tekintse meg az Azure Scheduler szolgáltatás használati költségek.

![Azure-portálon költség elemző nézet](./media/billing-understand-your-bill/4.png)

Napi költségek bontását megjelenítéséhez **előzmények költség**, kattintson a sor.

![Azure-portálon költség előzményeinek megtekintése](./media/billing-understand-your-bill/5.png)

További tudnivalókért lásd: [Azure számlázás és költség felügyeleti váratlan költségek megakadályozása](billing-getting-started.md#costs).

## <a name="external"></a>Mi a helyzet a külső szolgáltatási díjak?
Külső szolgáltatások független szolgáltatás szerezhetők (más néven Azure piactér rendelések), és külön vannak számlázva. A díjak nem jelennek meg az Azure számlán. További tudnivalókért lásd: [az Azure külső szolgáltatási díjak megismeréséhez](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Hogyan tehetem egy fizetési?

Ha beállított hitelkártyával vagy bankkártyával egy a fizetési módot, a fizetési a számlázott időszak végén 10 napon belül automatikusan fel van töltve. A hitelkártya fényében a sorelemet volna tegyük fel például **MSFT Azure**.

Ha Ön [kell fizetnie, amennyit számlázás által](billing-how-to-pay-by-invoice.md), a helyre a befizetést a számla alján felsorolt küldése. További segítségért [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Hogyan által hitelkártya állapotának ellenőrzése?

[Támogatási jegy létrehozásával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) kérje meg a befizetést állapotának. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Vannak-e az Azure különböző ügyfél-típusok? Honnan tudhatom, hogy milyen felhasználói típusú vagyok?
Nincsenek Azure-ügyfél különböző típusú. Jobb megértése érdekében a tarifa- és számlázási, tekintse meg az alábbi ügyfél típusú leírásokat.

- **Vállalati**: a vállalati felhasználók regisztráltak-e az egyeztetett pénzügyi kötelezettségvállalások és az Azure-erőforrások egyéni díjszabása eléréséhez Azure nagyvállalati szerződéssel.
- **Webalkalmazás-közvetlen**: webes közvetlen ügyfelek még nem igényelt bármilyen egyéni megállapodás az Azure-ral. Ezen ügyfelek regisztráltak-e az Azure-keresztül Azure.com webhelyre, és az összes Azure-erőforrások nyilvános irányuló árak fogadhat.
- **A felhő szolgáltató**: Felhőszolgáltatók jellemzően vállalatok számára a megoldások Azure fölött egy záró-ügyfél által felvett.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Miért nem látom, hogy az erőforrás, a számlázási létrehoztam költsége?
Az Azure nem nem számlázási közvetlenül erőforrás költsége alapján. Számlázási ki egy vagy több mérőszámok, amelyek segítségével nyomon követhető a teljes élettartamuk egy erőforrás-használat alapján történik. Ezek a mérőszámok használata a számlázási kiszámításához. Lásd: további információk az Azure-mérés alatt.

## <a name="how-does-azure-charge-metering-work"></a>Hogyan díjat számítanak a Azure mérési munkahelyi?
Ha Ön léptetési egyetlen Azure erőforrást, például egy virtuális gép egy vagy több mérő példánylétrehozás is lesz. Ezek a mérőszámok segítségével nyomon követhető az erőforrás-felhasználásának adott idő alatt. Minden bocsát ki, majd által használt Azure a használatmérő rendszer költségek kiszámításához a számlázási rekordok. 

Előfordulhat például, hogy egyetlen virtuális gép létrehozása az Azure-ban a következő mérőszámok a használatát nyomon követésére:

- Compute Hours – számítási óraszám
- IP-címszolgáltatási óra
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard szintű felügyelt lemezes
- Standard szintű felügyelt lemezes műveletek
- Standard IO – lemez
- Standard I/O-blokk Blob olvasása
- Standard I/O-blokk Blob írási
- Standard I/O-blokk Blob törlése

A virtuális gép létrehozása után a fenti mérőszámok megkezdődik a rekordok kibocsátó. A használati fog majd használható Azure mérési rendszerről és a mérési ár annak meghatározásához, hogy mekkora az ügyfél fel van töltve.

> [!Note]
> A fenti példa mérőszámok csak lehet létrehozni egy virtuális Gépet, amely jön létre a mérőszámok egy részét.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Mi az Azure közötti különbség az 1. fél és Azure piactér díjakat?
Azure 1. fél díjak vonatkoznak közvetlenül fejlesztett, Azure által kínált erőforrások. Az Azure piactér díjak hozott létre, amelyek használhatók az Azure piactéren keresztül szoftvergyártók harmadik féltől származó erőforrások vannak. Például egy Barracuda tűzfal egy harmadik fél által kínált Azure piactér erőforrás. A tűzfal és a megfelelő mérőszámok díjak megfizetése piactér díjak néven jelennek meg. 

## <a name="tips-for-cost-management"></a>Költség felügyeleti tippek
- Becsült költségeit használatával a [árképzési Számológép](https://azure.microsoft.com/pricing/calculator/) és [összköltsége tulajdonjoga Számológép](https://aka.ms/azure-tco-calculator), és a [részletes díjszabási információk minden egyes szolgáltatás](https://azure.microsoft.com/pricing/).
- [Állítson be riasztásokat számlázási](billing-set-up-alerts.md).
- [Tekintse át a használati és költségek az Azure-portálon rendszeresen](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

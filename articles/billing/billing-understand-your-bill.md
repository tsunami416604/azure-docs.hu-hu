---
title: Az Azure-hoz kapcsolódó számlák magyarázata |} A Microsoft Docs
description: Ismerje meg a használati és az Azure-előfizetés számlájának értelmezése
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
ms.author: cwatson
ms.openlocfilehash: be2e774bc4220d5d93f555263fa9f342f449f8b2
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423442"
---
# <a name="understand-your-bill-for-microsoft-azure"></a>A Microsoft Azure-hoz kapcsolódó számlák magyarázata
Az Azure-elszámolások ismertetése, hasonlítsa össze a számla a részletes napi használatot részletező fájl és a költségkezelési jelentések az Azure Portalon.

>[!NOTE]
>Ez a cikk nem vonatkozik az ügyfelek a nagyvállalati szerződés (EA) számára. Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek [a vállalati portál számla dokumentációjában talál.](https://ea.azure.com/helpdocs/understandingYourInvoice)  

A PDF-dokumentum, a számla és a egy példányát a részletes napi használat fájl CSV letöltése, lásd: [az Azure számlázási és napi használati adatainak lekérése](billing-download-azure-invoice-daily-usage-date.md). 

Részletes használati és a számlázási és napi használati fájlok részletesebb leírását lásd: [a Microsoft Azure-számla feltételeinek értelmezése](billing-understand-your-invoice.md) és [Understand feltételek a Microsoft Azure-on részletes használati](billing-understand-your-usage.md). 

A költségkezelési jelentések részletes ismertetéséért lásd: [az Azure portal Költségkezelés](https://docs.microsoft.com/azure/billing/billing-getting-started).

> [!div class="nextstepaction"]
> [Segítsen az Azure számlázási dokumentumok fejlesztésében](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="charges"></a>Hogyan ellenőrizhetem, hogy, hogy helyesek-e a költségek, a számlán?

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Nincs díj a számlán, hogy a további adatokra van szüksége, van-e több lehetőség közül választhat.

### <a name="option-1-review-your-invoice-and-compare-the-usage-and-costs-with-the-detailed-usage-csv-file"></a>1. lehetőség: Tekintse át a számlán, és a használat és költségek összehasonlítani a részletes használati adatok CSV-fájl

A CSV formátumú részletes használati díjak a számlázási időszak és a napi használati jeleníti meg. A CSV formátumú részletes használati lekéréséhez lásd: [az Azure számlázási és napi használati adatainak lekérése](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date).

A használati díjak jelennek meg a fogyasztásmérő szintjén. Az alábbi feltételek a számlán és a részletes használati fájlt is ugyanazt jelenti. A számlán az elszámolási időszakban például megegyezik a számlázási időszak alatt jelenik meg a részletes használati fájlban.

 | Invoice (PDF) | A részletes használati (CSV)|
 | --- | --- |
|Billing Cycle (Számlázási ciklus) | Billing Period (Számlázási időszak) |
 |Name (Név) |Meter Category (Mérési kategória) |
 |Típus |Fogyasztásmérő alkategóriája |
 |Erőforrás |Meter Name (Mérés neve) |
 |Régió |Meter Region (Mérési régió) |
 |Consumed (Felhasznált mennyiség) |Consumed Quantity (Felhasznált mennyiség) |
 |Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
 |Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |

A **használati díjak** a számla szakaszában az egyes mérőszám a a számlázási időszak alatt felhasznált teljes értékkel rendelkezik. Például az alábbi képernyőképen látható, az Azure Scheduler szolgáltatás használati díját.

![Számla használati díjak](./media/billing-understand-your-bill/1.png)

A **utasítás** a részletes használati adatok CSV részén láthatók a azonos díjat. Mindkét a *Consumed* összeget és *érték* felel meg a számlán.

![Fürt megosztott kötetei szolgáltatás a használati díjak](./media/billing-understand-your-bill/2.png)

Kibontásának megtekintéséhez az ezt a díjat naponta, lépjen a **napi használat** a fürt megosztott kötetei szolgáltatás szakaszában. "A Scheduler" szűrő *mérőszám kategóriája* és láthatja, hogy melyik nappal mérőszám lett megadva, és mennyi volt felhasznált. A *erőforrás* és *erőforráscsoport* információkat is szerepel az összehasonlítást. A *Consumed* értékek hozzá kell adnia mi jelenik meg a számlán.

![A fürt megosztott kötetei szolgáltatás napi használat szakasz](./media/billing-understand-your-bill/3.png)

A költség / nap lekéréséhez szorozza meg a *Consumed* az összegek a *arány* értéket a **utasítás** szakaszban.

A számla kapcsolatos további információkért lásd: [megismerheti az Azure-számla](billing-understand-your-invoice.md).

A Megosztott fürtköteten oszlopokhoz kapcsolatos további információkért lásd: [az Azure részletes használatának megértéséhez](billing-understand-your-invoice.md).

### <a name="option-2-review-your-invoice-and-compare-with-the-usage-and-costs-in-the-azure-portal"></a>2. lehetőség: Tekintse át a számlán, és hasonlítsa össze a használat és költségek az Azure Portalon

Az Azure Portalon is segít a költségek ellenőrzése. Az Azure Portalon a használat és a díjait a számlán gyors áttekintést biztosít a cost management diagramok.

A fenti példában folytatásához, látogasson el a [előfizetések lapon](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), válassza ki az előfizetését, és válassza **költségelemzés**. Itt adja meg az az időtartam, és tekintse meg az Azure Scheduler szolgáltatás használati díja.

![Az Azure portal nézetében költségelemzés](./media/billing-understand-your-bill/4.png)

A napi költségek részletezése a megtekintéséhez **költségeket**, kattintson az adott sorra.

![Az Azure portal nézetében költségelőzmények](./media/billing-understand-your-bill/5.png)

További tudnivalókért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md#costs).

## <a name="external"></a>Mi a helyzet a külső szolgáltatási díjak?
Külső szolgáltatásokhoz (más néven az Azure Marketplace-en orders) szolgáltatás független gyártók által biztosított, és külön számlázzuk. A díjak nem jelennek meg az Azure-számlán. További tudnivalókért lásd: [az Azure külső szolgáltatási díjak értelmezése](billing-understand-your-azure-marketplace-charges.md).

## <a name="payment"></a>Hogyan ellenőrizhetem, a fizetés?

Ha beállította hitelkártyával vagy bankkártyával kártya a fizetési módként alkalmazott, a fizetési automatikusan a számlázási időszak végén 10 napon belül kell fizetnie. A hitelkártya utasítás a sortételt építünk **MSFT Azure**.

Ha Ön [azért kell fizetnie, a számlázás](billing-how-to-pay-by-invoice.md), a fizetési a helyet, a számla alján felsorolt küldése. További segítségért [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="how-do-i-check-the-status-of-a-payment-made-by-credit-card"></a>Hogyan tudom ellenőrizni, hitelkártyával kifizetés állapotát?

[Hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) meg a fizetési állapotát. 

## <a name="are-there-different-azure-customer-types-how-do-i-know-what-customer-type-i-am"></a>Különböző Azure-ügyfél típusok vannak? Honnan tudhatom, hogy milyen ügyfél-típusának, vagyok?
Az Azure-ügyfelek különböző típusai vannak. A díjszabása és számlázási jobb megismeréséhez tekintse meg az alábbi ügyfél-type-leírásokat.

- **Vállalati**: vállalati ügyfelek regisztrált nagyvállalati szerződés az Azure-ral az egyeztetett pénzügyi kötelezettségvállalások és egyéni díjszabás az Azure-erőforrásokhoz való hozzáférést.
- **A Web Direct**: Web Direct ügyfelei számára még nem igényelt bármely egyéni szerződés az Azure-ral. Ezen ügyfelek regisztrált-e Azure Azure.com-on keresztül, és az összes Azure-erőforrások nyilvános irányuló díjai fogadhat.
- **Felhőszolgáltatója**: Felhőszolgáltatók általában olyan cégek, amelyek megoldásokat építhetnek az Azure a végfelhasználói által felvett.

## <a name="why-dont-i-see-the-cost-the-resource-i-have-created-in-my-bill"></a>Miért nem látom, hogy a költség az erőforrás létrehoztam a számlán?
Közvetlenül nem számla alapján erőforrás költségének Azure nem. A számlázás ki egy vagy több, élettartama során az erőforrás-használat nyomon követésére használt mérőszámok alapján történik. Ezek a mérőszámok szolgálnak majd a számla kiszámításának alapjául. Talál további információt az alábbi Azure-mérés.

## <a name="how-does-azure-charge-metering-work"></a>Hogyan számítja az Azure mérési munkahelyi?
Ha Ön egy egyetlen Azure-erőforrás, például egy virtuális gép üzembe helyezése egy vagy több mérőszám példányok is fog rendelkezni. Ezek a mérőszámok segítségével nyomon követhető az erőforrás-felhasználásának idővel. Felbontáson bocsát ki, majd az Azure használja a mérési rendszer díja a számla kiszámításának alapjául használati rekordok. 

Az Azure-ban létrehozott egyetlen virtuális gép Előfordulhat például, a következő mérőszámok esetében a használat nyomon követésére:

- Compute Hours – számítási óraszám
- IP-cím-Szolgáltatásóra
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard szintű felügyelt lemez
- Standard szintű felügyelt lemez műveletei
- Standard IO – lemez
- Standard IO – Block Blob olvasása
- Standard IO – Block Blob írása
- Standard IO – Block Blob törlése

A virtuális gép létrehozása után a mérőszámok a fenti mindegyike megkezdi a rekordok kibocsátó. Felhasználási lesz majd használható az Azure mérési rendszerről és a fogyasztásmérők ár meghatározni, hogy mekkora a számítunk fel.

> [!Note]
> A fenti példa mérőszámok csak lehet a mérőszámok, létrehozott egy virtuális Gépet, amely létrejön egy részét.

## <a name="what-is-the-difference-between-azure-1st-party-charges-and-azure-marketplace-charges"></a>Mi a különbség az Azure közötti 1-től felek díjait és az Azure piactér-díjak?
Azure 1. fél díjakat az erőforrások, amelyek közvetlenül fejlesztett és Azure-ban elérhető. Az Azure piactér-díjak vonatkoznak, amelyek az Azure marketplace-en elérhető külső szoftvergyártók által létrehozott erőforrásokat. Például Barracuda tűzfal egy harmadik fél által nyújtott az Azure marketplace-erőforrás. Piactér-díjak a tűzfal és a megfelelő mérőszámok összes díja fog megjelenni. 

## <a name="tips-for-cost-management"></a>Tippek a cost management
- Költségbecslést használatával a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) és [összköltsége tulajdonjog Számológép](https://aka.ms/azure-tco-calculator), és a [részletes díjszabási információkat az egyes szolgáltatások](https://azure.microsoft.com/pricing/).
- [Tekintse át a használat és költségek az Azure Portalon rendszeresen](billing-getting-started.md#costs).

## <a name="need-help-contact-support"></a>Segítség Forduljon az ügyfélszolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

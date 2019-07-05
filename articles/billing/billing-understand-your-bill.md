---
title: Az Azure számlázásának ismertetése
description: 'Útmutató: a használati és az Azure-előfizetés számlájának értelmezése.'
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: c8b0d6febbffb80cb4e8bcbf62febb51d059282c
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490328"
---
# <a name="understand-your-microsoft-azure-bill"></a>A Microsoft Azure-elszámolások ismertetése
Az Azure-elszámolások ismertetése, hasonlítsa össze a számla a részletes napi használat fájlt, és a költségkezelési jelentések az Azure Portalon.

Ez a cikk az Azure-ügyfelek a nagyvállalati szerződés (EA-ügyfelek) nem vonatkozik. Ha Ön nagyvállalati szerződéssel rendelkező ügyfelek, [számlázással kapcsolatos tudnivalók az Azure-nagyvállalati szerződéssel rendelkező ügyfelek](billing-understand-your-bill-ea.md).

Ez a cikk az Azure-ügyfelek nem vonatkozik a [Microsoft Ügyfélszerződéséhez](#check-access-to-a-microsoft-customer-agreement). Ha a Microsoft vevői szerződéssel rendelkezik, tekintse meg [megismerheti az Azure díjat a Microsoft Ügyfélszerződéséhez számla](billing-mca-understand-your-bill.md).

Hogyan működik a számlázás az Azure Cloud Solution Provider (az Azure CSP) program ismertetése, beleértve a számlázási ciklus, a díjszabást és a használatot, lásd: [Azure számlázási Konfigurációszolgáltatásokról](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/).

## <a name="charges"></a>A költségek áttekintése

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Ha további információt szeretne kapni, a számla díj, összehasonlíthatja használat és költségek a használatot részletező fájl vagy az Azure Portalon.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>Option 1: Hasonlítsa össze a használat és költségek a használati fájlban

A CSV formátumú részletes használati díjak a számlázási időszak és a napi használati jeleníti meg. Töltse le, vagy a fájl megtekintéséhez, lásd: [az Azure számlázási és napi használati adatainak lekérése](billing-download-azure-invoice-daily-usage-date.md).

A használati díjak jelennek meg a fogyasztásmérő szintjén. Az alábbi feltételek a számlán és a részletes használati fájlt is ugyanazt jelenti. Például a számlázási ciklus a számlán megegyezik a számlázási időszak alatt jelenik meg a részletes használati fájlban.

 | Invoice (PDF) | A részletes használati (CSV)|
 | --- | --- |
|Billing Cycle (Számlázási ciklus) | Billing Period (Számlázási időszak) |
 |Name (Név) |Meter Category (Mérési kategória) |
 |Típus |Fogyasztásmérő alkategóriája |
 |Resource |Meter Name (Mérés neve) |
 |Régió |Meter Region (Mérési régió) |
 |Consumed (Felhasznált mennyiség) |Consumed Quantity (Felhasznált mennyiség) |
 |Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
 |Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |

A **használati díjak** a számla részén láthatók a a számlázási időszak alatt felhasznált felbontáson teljes értékét. Ha például az alábbi képen látható az Azure Scheduler szolgáltatás használati díját.

![Számla használati díjak](./media/billing-understand-your-bill/1.png)

A **utasítás** a részletes használati adatok CSV részén láthatók a azonos díjat. Mindkét a *Consumed* összeget és *érték* felel meg a számlán.

![Fürt megosztott kötetei szolgáltatás a használati díjak](./media/billing-understand-your-bill/2.png)

A díj napi bontásban megtekintéséhez nyissa meg a **napi használat** szakaszában a CSV-fájlt. Állítson be szűrőt *Scheduler* alatt *mérőszám kategóriája*. Láthatja, hogy melyik nappal mérőszám lett megadva, és mennyi volt felhasznált. A *erőforrás* és *erőforráscsoport* információk is megjelennek az összehasonlítást. A *Consumed* értékeket kell felhasználni, és a felel meg a számlán jelenik meg.

![A fürt megosztott kötetei szolgáltatás napi használat szakasz](./media/billing-understand-your-bill/3.png)

A költség / nap lekéréséhez szorozza meg a *Consumed* az összegek a *arány* értéket a **utasítás** szakaszban.

További tudnivalókért lásd:

- [Az Azure-számlák ismertetése](billing-understand-your-invoice.md)
- [Az Azure részletes használatának megértéséhez](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>Option 2: Hasonlítsa össze a használat és költségek az Azure Portalon

Az Azure Portalon is segít a költségek ellenőrzése. A számlázott használat és a díj gyors áttekintést kaphat, hogy a cost management diagramok megtekintése.

1. Az Azure Portalon lépjen a [előfizetések](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Válassza ki az előfizetést > **költségelemzés**.
1. Szűrés **Timespan**.
1. Az előző példában a folytatáshoz tekintse meg az Azure Scheduler szolgáltatás használati díját.

   ![Az Azure portal nézetében költségelemzés](./media/billing-understand-your-bill/4.png)

1. Válassza ki a sort, amely megjeleníti a díjat a napi költségek részletezése megtekintéséhez.

   ![Az Azure portal nézetében költségelőzmények](./media/billing-understand-your-bill/5.png)

További tudnivalókért lásd: [az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md#costs).

## <a name="external"></a>Külső szolgáltatások számlázása külön

Külső szolgáltatások vagy a piactér-díjak vonatkoznak külső szoftvergyártók által létrehozott erőforrásokat. Ezeket az erőforrásokat az Azure marketplace-ről való használatra érhetők el. Például Barracuda tűzfal egy harmadik fél által nyújtott egy az Azure marketplace-erőforrást. Minden költséget a tűzfal és az annak megfelelő mérőszámok külső szolgáltatási díjakkal jelennek meg.

A külső szolgáltatási díjak külön számlázzuk. A díjak nem jelennek meg az Azure-számlán. További tudnivalókért lásd: [az Azure külső szolgáltatási díjak értelmezése](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>A számlázás alapját a használati mérőszámok erőforrások

Az Azure nem közvetlenül a számla alapján az erőforrás költségének. Egy erőforrás kamatot egy vagy több mérőszámok használatával. Mérőszámok segítségével nyomon követhető az erőforrás-használat élettartama során. Ezek a mérőszámok szolgálnak majd a számla kiszámításának alapjául.

Ha például egy egyetlen Azure-erőforrás, például egy virtuális gép létrehozásakor azt egy vagy több mérőszám példánnyal rendelkezik létrehozott. Mérőszámok segítségével nyomon követhető az erőforrás-felhasználásának idővel. Minden használati rekord, a számla kiszámításának alapjául az Azure által használt bocsát ki.

Az Azure-ban létrehozott egyetlen virtuális gép (VM) előfordulhat például, a következő mérőszámok esetében a használat nyomon követésére:

- Compute Hours – számítási óraszám
- IP-cím-Szolgáltatásóra
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard Managed Disk
- Standard szintű felügyelt lemez műveletei
- Standard IO – lemez
- Standard IO – Block Blob olvasása
- Standard IO – Block Blob írása
- Standard IO – Block Blob törlése

A virtuális gép létrehozásakor a felbontáson megkezdi a rekordok kibocsátó. A használat és a fogyasztásmérők ára van nyomon a mérési rendszer Azure-ban.

## <a name="payment"></a>A számla kell fizetnie

Ha beállította hitelkártyával vagy bankkártyával kártya a fizetési módként alkalmazott, a fizetési automatikusan a számlázási időszak végén 10 napon belül kell fizetnie. A hitelkártya utasítás a sortételt építünk **MSFT Azure**.

Ha módosítani szeretné a bankkártya vagy egy hitelkártya, amely fel van töltve, lásd: [hozzáadása, frissítése vagy eltávolítása egy bankkártya vagy egy hitelkártya Azure](billing-how-to-change-credit-card.md).

Ha Ön [azért kell fizetnie, a számla ellenében](billing-how-to-pay-by-invoice.md), a fizetési a helyet, a számla alján felsorolt küldése.

A fizetési módot, a állapotának [hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tippek a cost management

- Becsült költség használatával a:
  - [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
  - [Tulajdonjog Számológép teljes költsége](https://aka.ms/azure-tco-calculator)
  - [Részletes díjszabási információkat az egyes szolgáltatások](https://azure.microsoft.com/pricing/)
- [Tekintse át a használat és költségek az Azure Portalon rendszeresen](billing-getting-started.md#costs).

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Részletek

- [Az Azure számlázási számlázási és napi használati adatok lekérése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft Azure-számla feltételeinek értelmezése](billing-understand-your-invoice.md)
- [A feltételeinek értelmezése a Microsoft Azure részletes használat](billing-understand-your-usage.md)
- [Az Azure portal Költségkezelés](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Az Azure-elszámolással és költségkezeléssel váratlan költségek megelőzése](billing-getting-started.md#costs)

---
title: Az Azure számlázásának ismertetése
description: Ismerje meg, hogyan olvashatja el és értelmezheti az Azure-előfizetésének használatát és számlázását.
author: bandersmsft
manager: dougeby
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/28/2019
ms.author: banders
ms.openlocfilehash: 51143644a62a77a61c4540d9f2ad3dce401c496b
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68610135"
---
# <a name="understand-your-microsoft-azure-bill"></a>A Microsoft Azure-számla ismertetése
Az Azure-számla megismeréséhez össze kell hasonlítani a számláját a részletes napi használati fájllal és a Azure Portal a Cost Management jelentéseivel.

Ez a cikk nem vonatkozik a következő ügyfelekre:
- Azure-ügyfelek Nagyvállalati Szerződés (EA-ügyfelekkel). Ha Ön nagyvállalati ügyfél, tekintse [meg az Azure-ügyfelek számlájának megismerése nagyvállalati Szerződéskal](billing-understand-your-bill-ea.md)című témakört.
- [Microsoft ügyfél](#check-access-to-a-microsoft-customer-agreement)-szerződéssel rendelkező Azure-ügyfelek. Ha rendelkezik Microsoft-ügyféli szerződéssel, tekintse [meg az Azure-díjakat a Microsoft Customer Agreement](billing-mca-understand-your-bill.md)-számláján.

A Azure Cloud Solution Provider-(Azure CSP-) program számlázási működésének leírását, beleértve a számlázási ciklust, a díjszabást és a használatot, az [Azure CSP számlázási áttekintése](/azure/cloud-solution-provider/billing/azure-csp-billing-overview/)című témakörben olvashat.

## <a name="charges"></a>A díjak áttekintése

>[!VIDEO https://www.youtube.com/embed/3YegFD769Pk]

Ha a számlán további információra van szüksége, a használat és a költségek összevetése a használati fájllal vagy a Azure Portal is lehetséges.

### <a name="option-1-compare-usage-and-costs-with-usage-file"></a>1\. lehetőség: Használat és költségek összehasonlítása a használati fájllal

A részletes használati CSV-fájl számlázási időszak és napi használat szerint jeleníti meg a díjakat. A fájl letöltéséhez vagy megtekintéséhez tekintse [meg az Azure számlázási számla és a napi használati adatok](billing-download-azure-invoice-daily-usage-date.md)beszerzése című témakört.

A használati díjak a mérési szinten jelennek meg. A következő kifejezések ugyanazt a dolgot jelentik, mint a számla és a részletes használati fájl. Például a számla számlázási ciklusa megegyezik a részletes használati fájlban látható számlázási időszaktal.

 | Számla (PDF) | Részletes használat (CSV)|
 | --- | --- |
|Billing Cycle (Számlázási ciklus) | Billing Period (Számlázási időszak) |
 |Name (Név) |Fogyasztásmérő kategóriája |
 |Type |Fogyasztásmérő alkategóriája |
 |Resource |Fogyasztásmérő neve |
 |Régió |Fogyasztásmérő régiója |
 |Felhasznált |Felhasznált mennyiség |
 |Tartalmazza |Included Quantity (Bennefoglalt mennyiség) |
 |Billable (Számlázandó) |Overage Quantity (Kereten túli mennyiség) |

A számla **használati díjak** szakasza a számlázási időszakban felhasznált egyes mérőszámok teljes értékét jeleníti meg. Az alábbi képen például az Azure Scheduler szolgáltatás használati díja látható.

![Számla használati díjai](./media/billing-understand-your-bill/1.png)

A részletes használati CSV-fájl **utasítás** szakasza ugyanazt a díjat mutatja. Mind a *felhasznált* mennyiség, mind az *érték* megegyezik a számlával.

![CSV-használati díjak](./media/billing-understand-your-bill/2.png)

A díj napi bontásának megtekintéséhez nyissa meg a CSV-fájl **napi használat** szakaszát. A *Scheduler* szűrése az *Meter kategóriában*. Láthatja, hogy a rendszer mely napokat használta, és mennyit felhasznált. Az összehasonlításhoz az *erőforrás* -és *erőforráscsoport* -információ is látható. A *felhasznált* értékeknek fel kell venniük a számlát, és meg kell egyezniük a számlán láthatókkal.

![Napi használat szakasz a CSV-fájlban](./media/billing-understand-your-bill/3.png)

A napi díj megszerzéséhez szorozza meg a *felhasznált* mennyiségeket a *ráta* értékével az **utasítás** szakasz alapján.

További tudnivalókért lásd:

- [Az Azure-számla megismerése](billing-understand-your-invoice.md)
- [Az Azure részletes használatának megismerése](billing-understand-your-invoice.md)

### <a name="option-2-compare-the-usage-and-costs-in-the-azure-portal"></a>2\. lehetőség: A Azure Portal használatának és költségeinek összehasonlítása

A Azure Portal a díjak ellenőrzéséhez is segítséget nyújt. Ha gyors áttekintést szeretne kapni a számlázott használatáról és díjairól, tekintse meg a Cost Management diagramokat.

1. A Azure Portal lépjen az előfizetések elemre. [](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Válassza ki az előfizetését > a **Cost Analysis**elemet.
1. Szűrés **TimeSpan**szerint.
1. Az előző példa folytatásához az Azure Scheduler szolgáltatás használati díját fogja látni.

   ![Cost Analysis nézet a Azure Portal](./media/billing-understand-your-bill/4.png)

1. Válassza ki azt a sort, amely megjeleníti a díjat a napi költségek részletezésének megtekintéséhez.

   ![Cost History-nézet a Azure Portal](./media/billing-understand-your-bill/5.png)

További információ: [a váratlan költségek megelőzése az Azure-számlázással és a költségek kezelésével](billing-getting-started.md#costs).

## <a name="external"></a>Külön számlázott külső szolgáltatások

A külső szolgáltatások vagy a piactér díjai a külső gyártóktól származó szoftvergyártók által létrehozott erőforrásokra vonatkoznak. Ezek az erőforrások az Azure piactéren használhatók. A Barracuda-tűzfal például egy harmadik fél által kínált Azure Marketplace-erőforrás. A tűzfal és a hozzá tartozó mérőszámok összes díja külső szolgáltatási díjakként jelenik meg.

A külső szolgáltatási díjak számlázása külön történik. A díjak nem jelennek meg az Azure-számlán. További információ: [Az Azure-beli külső szolgáltatási díjak megismerése](billing-understand-your-azure-marketplace-charges.md).

## <a name="resources-billed-by-usage-meters"></a>Használati mérőszámok által számlázott erőforrások

Az Azure nem számláz közvetlenül az erőforrás-költségeket. Egy erőforrás díjait egy vagy több mérőszám segítségével számítjuk ki. A mérőszámok segítségével nyomon követhető az erőforrás használatának teljes élettartama. A rendszer ezt a mérőszámot használja a számla kiszámításához.

Ha például egyetlen Azure-erőforrást hoz létre, mint például egy virtuális gép, egy vagy több mérőszám-példány lett létrehozva. A mérőszámok az erőforrás használatának időbeli nyomon követésére szolgálnak. Az egyes mérőműszerek az Azure által a számla kiszámításához használt használati rekordokat bocsátanak ki.

Az Azure-ban létrehozott egyetlen virtuális gép (VM) például a következő mérőszámokkal hozható létre a használat nyomon követéséhez:

- Üzemóra
- IP-cím-szolgáltatásóra
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard szintű felügyelt lemez
- Standard felügyelt lemez műveletei
- Standard i/o – lemez
- Standard i/o – blob letiltása – olvasás
- Standard i/o – blob-írás tiltása
- Standard i/o – blob törlésének tiltása

A virtuális gép létrehozásakor minden mérőszám elkezdi a használati rekordok kibocsátását. Ezt a használatot és a fogyasztásmérő árát az Azure-mérési rendszeren követjük nyomon.

## <a name="payment"></a>Számla kifizetése

Ha bankkártyás kártyát állít be fizetési módszerként, a rendszer a számlázási időszak lejárta után 10 napon belül automatikusan kiszámlázza a befizetést. A bankkártya-utasításban a tétel azt jelentené, hogy az **MSFT Azure**.

A felszámított hitelkártya módosításához tekintse meg az [Azure-beli hitelkártyák hozzáadása, frissítése és eltávolítása](billing-how-to-change-credit-card.md)című témakört.

Ha [számlán fizet](billing-how-to-pay-by-invoice.md), küldje el a fizetését a számla alján felsorolt helyre.

A fizetés állapotának megtekintéséhez [hozzon létre egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).


## <a name="tips-for-cost-management"></a>Tippek a Cost Managementhez

- A költségek becslése a használatával:
  - [Az Azure díjszabásának kalkulátora](https://azure.microsoft.com/pricing/calculator/)
  - [A tulajdonosi kalkulátor teljes költsége](https://aka.ms/azure-tco-calculator)
  - [Az egyes szolgáltatások részletes díjszabási információi](https://azure.microsoft.com/pricing/)
- [Tekintse át a használatot és a költségeket rendszeresen a Azure Portal](billing-getting-started.md#costs)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft ügyfél-szerződéshez való hozzáférés engedélyezése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

- [Azure számlázási számla és napi használati adatok beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft Azure-számla feltételeinek megismerése](billing-understand-your-invoice.md)
- [A Microsoft Azure részletes használati feltételeinek megismerése](billing-understand-your-usage.md)
- [Azure Portal Cost Management](https://docs.microsoft.com/azure/billing/billing-getting-started)
- [Az Azure-számlázással és a költségek kezelésével kapcsolatos váratlan költségek megelőzése](billing-getting-started.md#costs)

---
title: Megismerheti a díjat a Microsoft Ügyfélszerződéséhez számlán – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatók és érthetők a díjait a számlán
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing
ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: f93152ae3db926fb989c219d1e515abaf0281bf4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57837881"
---
# <a name="understand-the-charges-on-your-microsoft-customer-agreements-invoice"></a>A Microsoft Ügyfélszerződéséhez számlán a költségek ismertetése

A számla a költségek elemzésével, az egyes tranzakciók képes megérteni.

A Microsoft vevői szerződés számlázási fiók, a számlán minden hónapban, minden elszámolási profil jön létre. A számlán minden költséget az előző hónap tartalmazza. Az Azure Portalon is megtekintheti a számlákat. További információkért lásd: [számlákat letölteni a Microsoft vevői szerződés](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Az Azure Portalon számla tartozó tranzakciók megtekintése

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. A Keresés **Cost Management és számlázás**.

    ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki **összes tranzakció** a képernyő bal oldalán. Attól függően, a hozzáférés, előfordulhat, hogy egy számlázási fiók és egy számlázási profil kiválasztásához, majd válassza ki **összes tranzakció**.

4. Az összes tranzakció lapon az alábbi információkat jeleníti meg:

    ![A számlázott tranzakciók lista bemutató képernyőkép](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

    |Oszlop  |Meghatározás  |
    |---------|---------|
    |Dátum     | A tranzakció dátuma  |
    |Számla azonosítója     | A számlán, amelyen a tranzakció van számlázva azonosítója. Küldjön egy támogatási kérést, ha az azonosító megoszthatja a támogatási kérelem gyorsíthatja fel az Azure-támogatás |
    |Tranzakció típusa     |  Typ transakce transaction, például beszerzési, a Mégse gombra és a használati díjak  |
    |Termékcsalád     | Például a virtuális gépek számítási és az Azure SQL database-adatbázis termékkategória|
    |Termék sku     | A termék példányát azonosító egyedi kódot |
    |Mennyiség     |  A tranzakciók mennyiségét      |
    |Számlázási szakasz     | Az ebben a szakaszban az billing invoice profil megjelenik a tranzakció |
    |Számlázási profil     | A tranzakció megjelenik-e a számlán a számlázási profilja |

5. Keresés a tranzakciók, a számla szűréséhez azonosítója.

### <a name="view-transactions-by-invoice-sections"></a>A számla szakaszok tranzakciók megtekintése

Számla szakaszok segítségével rendszerezheti egy számlázási profilja számlán a költségeket. További információkért lásd: [számla szakaszban megismerheti](billing-mca-overview.md#understand-invoice-sections). Ha számla alapján jön létre, a számlán jelenik meg a számlázási profilja szakaszai kell fizetni.

Az alábbi képen látható a könyvelési részleg számla szakasz díjai minta számlán.

![Példa a kép a részletek a számla szakasz információkat](./media/billing-understand-your-bill-mca/invoicesection-details.png)

Miután azonosította a költségek, számlák szakasz, megtekintheti a tranzakciók az Azure Portalon, a költségek ismertetése.

1. Nyissa meg az összes tranzakció lapon megtekintheti az adott számlához tranzakciókat az Azure Portalon. További információkért lásd: [tranzakciók számla megtekintése az Azure Portalon](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Szűrés számlanév szakaszban megtekintheti a számla szakaszának tranzakciókat.

## <a name="understand-pending-charges-to-estimate-your-next-invoice"></a>A következő számla becslése érdekében függőben lévő díjak ismertetése

A Microsoft vevői szerződés számlázási fiók, a számlázása a díjakat, amíg becslés, és függőben lévő tekinthető. Függőben lévő díjak a következő számla becslése érdekében az Azure Portalon tekintheti meg. A függőben lévő díjak becsült, és nem tartalmazzák a forgalmi adót, így a tényleges díjak a következő számla változhatnak a függőben lévő kell fizetni.

### <a name="view-summary-of-pending-charges"></a>Függőben lévő díjak összegzésének megtekintése

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **összefoglalás** fülre a képernyő tetején.

5. A díjak a szakasz a hónap elejétől számított és a díjak a múlt hónapban jelennek meg.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

A hónap elejétől számított díjakat a függőben lévő díjak az aktuális hónaphoz és számlázása a számla jön létre a hónap során. A számla a múlt hónapban még nem jön létre, akkor a díjak a múlt hónapban is vannak függőben lévő és a következő számla fogja tartalmazni.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

Ha azonosította a függőben lévő díjak, az egyes tranzakciók hozzájáruló a költségek elemzésével is ismernie kell fizetni. Ezen a ponton függőben lévő használati díjak nem jelennek meg az összes tranzakció oldalon. A függőben lévő használati díjak az Azure-előfizetések oldalon tekintheti meg. További információkért lásd: [függőben lévő használati díjak megtekintése](#view-pending-usage-charges)

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **összes tranzakció** a képernyő bal oldalán.

5. Keresse meg **függőben lévő**. Használja a **Timespan** szűrőt a függőben lévő az aktuális vagy a múlt hónapban díjak megtekintése.

   ![A függőben lévő tranzakciók lista bemutató képernyőkép](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. A Keresés **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **minden előfizetés** a képernyő bal oldalán.

5. Az Azure-előfizetések lapon megjeleníti a számlázási profilja aktuális és a díjak a múlt hónapban az egyes előfizetésekhez. A hónap elejétől számított díjakat a függőben lévő díjak az aktuális hónaphoz és számlázása a számla jön létre a hónap során. A számla a múlt hónapban még nem jön létre, akkor a díjak a múlt hónapban is vannak függőben.

    ![Képernyőkép az Azure-előfizetések számlázási profilja listája](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Az Azure-használati díjakhoz elemzése

Az Azure használat és költségek csv-fájl segítségével elemezheti a használat alapú költségek. Letöltheti a fájlt, egy számla vagy vonatkozó díjakat függőben van. További információkért lásd: [az Azure számlázási és napi használati adatainak lekérése](billing-download-azure-invoice-daily-usage-date.md).

### <a name="view-detailed-usage-by-invoice-section"></a>Számla szakaszban részletes használati adatok megtekintéséhez

Szűrhet az Azure-beli használat és költségek fájlmegosztás egyeztetni a használati díjak a számla szakaszokat.

A következő lépések végigvezetik a könyvelési részleg számla szakaszának fizetendő számítási díjakat összeegyezteti:

![Példa a kép a részletek a számla szakasz információkat](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Számla PDF | Azure-használat és költségek CSV |
 | --- | --- |
 |Könyvelési részleg |invoiceSectionName |
 |A használati díjak – Microsoft Azure-csomag |productOrderName |
 |Compute |serviceFamily |

1. Szűrés a **invoiceSectionName** a CSV-fájlban, az oszlop **számviteli részleg**.
2. Szűrés a **productOrderName** a CSV-fájlban, az oszlop **a Microsoft Azure csomag**.
3. Szűrés a **serviceFamily** a CSV-fájlban, az oszlop **Microsoft.Compute**.

![Képernyőkép, amely megjeleníti a használati és charges szakasz számla alapján szűrt fájl](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-invoice-section.png)

<!--Todo Add screenshot of usage file -->

### <a name="view-detailed-usage-by-subscription"></a>Részletes használati adatok megtekintéséhez az előfizetés szerint

Szűrhet az Azure használat és költségek csv-fájl a szolgáltatások használati díjai egyeztetéséhez. Az összes előfizetés megtekintéséhez a számlázási profilja: [függőben lévő használati díjak megtekintése](#view-pending-usage-charges).

Ha azonosította a előfizetést kell fizetni, a költségek elemzése az Azure használat és költségek csv-fájl használatával.

Az alábbi képernyőképen az előfizetések listáját jeleníti meg az Azure Portalon.

![Képernyőkép az Azure-előfizetések számlázási profilja listája](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Szűrő a **subscriptionName** az Azure használat és költségek CSV-fájl a oszlopára **WA_Subscription** WA_Subscription a részletes használati költségek megtekintéséhez.

![Képernyőkép, amely megjeleníti a használati és előfizetés alapján szűrt fájl díjak](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>A számla kell fizetnie

A számla kifizetésére vonatkozó utasításokat a számla alján jelennek meg. [Ismerje meg, hogyan kell fizetnem](billing-mca-understand-your-invoice.md#how-to-pay).

Ha már korábban fizetett a számlán, az állapot, a fizetési módot, az Azure Portalon a számlákat oldalon ellenőrizheti.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlák és a részletes használati kapcsolatos további információkért lásd:

- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft Ügyfélszerződéséhez számla feltételeinek értelmezése](billing-mca-understand-your-invoice.md)
- [A Microsoft Ügyfélszerződéséhez használat CSV feltételeinek értelmezése](billing-mca-understand-your-usage.md)

---
title: A számlán a Microsoft Ügyfélszerződéséhez – Azure költségek ismertetése
description: Megtudhatja, hogyan olvashatók és érthetők a díjait a számlán.
author: jureid
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: ee250589133abb1944ff17e39dc650cbae4279c6
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490679"
---
# <a name="understand-charges-on-your-microsoft-customer-agreement-invoice"></a>A Microsoft Ügyfélszerződéséhez számla költségek ismertetése

A számla a költségek elemzésével, az egyes tranzakciók képes megérteni. A Microsoft vevői szerződés számlázási fiók, a számla alapján jön létre minden egyes számlázási profilok hónap. A számlán minden költséget az előző hónap tartalmazza. Az Azure Portalon is megtekintheti a számlákat. További információkért lásd: [számlákat letölteni a Microsoft vevői szerződés](billing-download-azure-invoice-daily-usage-date.md#download-invoices-for-a-microsoft-customer-agreement).

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

## <a name="view-transactions-for-an-invoice-in-the-azure-portal"></a>Az Azure Portalon számla tartozó tranzakciók megtekintése

1. Jelentkezzen be az [Azure Portalra](https://www.azure.com).

2. Keresse meg **Cost Management és számlázás**.

    ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki **összes tranzakció** az oldal bal oldalán. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók, számlázási profilja vagy az invoice szakasz válassza, majd válassza a **összes tranzakció**.

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
    |Számla szakasz     | Az ebben a szakaszban az billing invoice profil megjelenik a tranzakció |
    |Számlázási profilja     | A tranzakció megjelenik-e a számlán a számlázási profilja |

5. Keresse meg a számlán azonosító a számla a tranzakciók szűréséhez.

### <a name="view-transactions-by-invoice-sections"></a>A számla szakaszok tranzakciók megtekintése

Számla szakaszokból rendszerezheti egy számlázási profilja számla költségeit. További információkért lásd: [számla szakaszban megismerheti](billing-mca-overview.md#invoice-sections). Számla alapján jön létre, ha a számlázási profilban szereplő összes szakasz díjai a számlán jelennek meg.

Az alábbi képen látható a könyvelési részleg számla szakaszának díjak minta számlán.

![Példa a kép a részletek a számla szakasz információkat](./media/billing-understand-your-bill-mca/invoicesection-details.png)

A költségek, számlák szakasz azonosítása, megtekintheti a tranzakciók az Azure Portalon, a költségek ismertetése.

1. Nyissa meg az összes tranzakció lapon megtekintheti az adott számlához tranzakciókat az Azure Portalon. További információkért lásd: [tranzakciók számla megtekintése az Azure Portalon](#view-transactions-for-an-invoice-in-the-azure-portal).

2. Szűrés a számla szakasznév tranzakciók megtekintéséhez.

## <a name="review-pending-charges-to-estimate-your-next-invoice"></a>Függőben van a következő számla becslése érdekében a költségek áttekintése

A Microsoft vevői szerződés számlázási fiókban díjak becsült és függőben lévő minősülnek mindaddig, amíg a számlázás. Függőben lévő díjak a következő számla becslése érdekében az Azure Portalon tekintheti meg. Függőben lévő díjak összegek becslések, és nem tartalmazzák a forgalmi adót. A tényleges díjak a következő számla változnak, a függőben lévő kell fizetni.

### <a name="view-summary-of-pending-charges"></a>Függőben lévő díjak összegzésének megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **összefoglalás** fülre a képernyő tetején.

5. A díjak a szakasz a hónap elejétől számított és a díjak a múlt hónapban jelennek meg.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

A hónap elejétől számított díjakat a függőben lévő díjak az aktuális hónaphoz és számlázása a számla jön létre a hónap során. A számla a múlt hónapban még nem jön létre, akkor a díjak a múlt hónapban is vannak függőben lévő és a következő számlán.

### <a name="view-pending-transactions"></a>Függőben lévő tranzakciók megtekintése

Azonosíthatja a függőben lévő díjakat, ha az egyes tranzakciók hozzájáruló a költségek elemzésével is ismernie kell fizetni. Ezen a ponton függőben lévő használati díjak nem jelennek meg az összes tranzakció oldalon. A függőben lévő használati díjak az Azure-előfizetések oldalon tekintheti meg. További információkért lásd: [függőben lévő használati díjak megtekintése](#view-pending-usage-charges)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg **Cost Management és számlázás**.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **összes tranzakció** az oldal bal oldalán.

5. Keresse meg *függőben lévő*. Használja a **Timespan** szűrőt a függőben lévő az aktuális vagy a múlt hónapban díjak megtekintése.

   ![A függőben lévő tranzakciók lista bemutató képernyőkép](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### <a name="view-pending-usage-charges"></a>Függőben lévő használati díjak megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg *Cost Management és számlázás*.

   ![Költségkezelés + számlázás az Azure portál keresési bemutató képernyőkép](./media/billing-understand-your-bill-mca/billing-search-cost-management-billing.png)

3. Válassza ki a számlázási profilt. A hozzáférést, attól függően előfordulhat, hogy rendelkezik számlázási fiók kiválasztása. Válassza ki a számlázási fiók **profilok számlázási** válassza ki a számlázási profilja.

4. Válassza ki **minden előfizetés** egyet az oldal bal oldalán.

5. Az Azure-előfizetések lapon megjeleníti a számlázási profilja az aktuális és a díjak a múlt hónapban az egyes előfizetésekhez. A hónap elejétől számított díjakat a függőben lévő díjak az aktuális hónaphoz és számlázása a számla jön létre a hónap során. A számla a múlt hónapban még nem jön létre, akkor a díjak a múlt hónapban is vannak függőben.

    ![Képernyőkép az Azure-előfizetések számlázási profilja listája](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

## <a name="analyze-your-azure-usage-charges"></a>Az Azure-használati díjakhoz elemzése

Az Azure használat és költségek CSV-fájl segítségével elemezheti a használat alapú költségek. Letöltheti a fájlt az adott számlához vagy a függőben lévő díjak. További információkért lásd: [az Azure számlázási és napi használati adatainak lekérése](billing-download-azure-invoice-daily-usage-date.md).

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


### <a name="view-detailed-usage-by-subscription"></a>Részletes használati adatok megtekintéséhez az előfizetés szerint

Szűrhet az Azure használat és költségek CSV-fájl a szolgáltatások használati díjai egyeztetéséhez. Az összes előfizetés megtekintéséhez a számlázási profilja: [függőben lévő használati díjak megtekintése](#view-pending-usage-charges).

Amikor azonosította előfizetést kell fizetni, a költségek elemzése az Azure használat és költségek CSV-fájl használatával.

Az alábbi képen látható az előfizetések listáját az Azure Portalon.

![Képernyőkép az Azure-előfizetések számlázási profilja listája](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list-highlighted.png)

Szűrő a **subscriptionName** az Azure használat és költségek CSV-fájl a oszlopára **WA_Subscription** WA_Subscription a részletes használati költségek megtekintéséhez.

![Képernyőkép, amely megjeleníti a használati és előfizetés alapján szűrt fájl díjak](./media/billing-understand-your-bill-mca/billing-usage-file-filtered-by-subscription.png)

## <a name="pay-your-bill"></a>A számla kell fizetnie

A számla kifizetésére vonatkozó utasításokat a számla alján jelennek meg. [Ismerje meg, hogyan kell fizetnem](billing-mca-understand-your-invoice.md#how-to-pay).

Ha már korábban fizetett a számlán, az állapot, a fizetési módot, az Azure Portalon a számlákat oldalon ellenőrizheti.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

A számlák és a részletes használati kapcsolatos további információkért lásd:

- [Az Azure számlázási és napi használati adatainak beszerzése](billing-download-azure-invoice-daily-usage-date.md)
- [A Microsoft Ügyfélszerződéséhez számla feltételeinek értelmezése](billing-mca-understand-your-invoice.md)
- [A Microsoft Ügyfélszerződéséhez használat CSV feltételeinek értelmezése](billing-mca-understand-your-usage.md)

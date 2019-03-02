---
title: A Microsoft vevői szerződés az Azure-használat és költségek CSV feltételeinek értelmezése |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatók és érthetők a szakaszok az az Azure használatáról és a fürt megosztott kötetei szolgáltatás díja a számlázási profilja
services: ''
documentationcenter: ''
author: bandersmsft
manager: alherz
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: banders
ms.openlocfilehash: 774f6bd4beb2c2b8b36588ee595996860a9c7549
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57249029"
---
# <a name="understand-terms-on-your-azure-usage-and-charges-csv-for-a-microsoft-customer-agreement"></a>A Microsoft vevői szerződés az Azure-használat és költségek CSV feltételeinek értelmezése

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

Az Azure használat és költségek CSV-fájlt a jelenlegi számlázási időszak napi és a mérőszám szintű használati díjakat tartalmazza.

A használat és költségek az Azure file lekéréséhez lásd: [és a Microsoft Ügyfélszerződéséhez díjai megtekintése és letöltése az Azure használati](billing-download-azure-daily-usage.md).
Egy vesszővel tagolt (.csv) formátumú, amelyeket megnyithat egy számolótábla alkalmazásban érhető el.

A használati díjak az összes **havi** díjak egy adott előfizetés. A használati díjak ne vegye figyelembe bármely jóváírások és engedmények.

## <a name="changes-in-the-enterprise-agreement-azure-usage-and-charges-csv"></a>A nagyvállalati szerződés program Azure-használat és költségek fürt megosztott kötetei szolgáltatás változásai

Ha nagyvállalati szerződéssel rendelkező ügyfelek, láthatja, hogy a számlázási profilja CSV-fájlból az Azure használati feltételeit eltérnek a feltételeket a nagyvállalati szerződéssel rendelkező Azure használati CSV-fájl. A nagyvállalati szerződésre vonatkozó használati feltételek profil használati feltételek számlázással leképezés a következő:

| Fürt megosztott kötetei szolgáltatás nagyvállalati szerződéssel rendelkező Azure-használat | A Microsoft vevői szerződés program Azure-használat és a fürt megosztott kötetei szolgáltatás díj |
| --- | --- |
| Dátum | dátum |
| Hónap| dátum |
| Nap | dátum |
| Év | dátum |
| Product | termék |
| Fogyasztásmérő azonosítója | meterID |
| Fogyasztásmérő kategóriája | meterCategory |
| Fogyasztásmérő alkategóriája | meterSubCategory |
| Fogyasztásmérő régiója | meterRegion |
| Fogyasztásmérő neve | meterName |
| Felhasznált mennyiség | Mennyiség |
| Erőforrásdíj | effectivePrice | <!-- this was highlighted -->
| Kiterjesztett költség | költség |
| Erőforrás helye | resourceLocation |
| Használt szolgáltatás | consumedService |
| Példány azonosítója | instanceId |
| 1. szolgáltatási információ | serviceInfo1 |
| 2. szolgáltatási információ | serviceInfo2 |
| További információk | additionalInfo |
| Címkék | tags |
| Társzolgáltatás azonosítója | – |
| Részleg neve | invoiceSection | <!-- this was highlighted -->
| Költséghely | costCenter |
| Mértékegység | unitofMeasure |
| ResourceGroup | resourceGroup |
| Külön számlázott díjak | isAzureCreditEligible | <!-- this was highlighted -->

<!-- TO DO: Marketplace CSV? -->

## <a name="detailed-terms-and-descriptions-in-your-azure-usage-and-charges-file"></a>Részletes használati és -leírások az Azure használat és költségek fájlban

Az alábbi szakasz ismerteti az Azure használat és költségek jelenik meg a fontos fogalmakat.

Időtartam | Leírás
--- | ---
invoiceId | Az egyedi dokumentumazonosító szerepel a számlán PDF
previousInvoiceId | Egy eredeti számlát, ha a jelen visszatérítés referencia
billingAccountName | Számlázási fiók nevét
billingAccountId | A legfelső szintű számlázási fiók egyedi azonosítója
billingProfileId | A számlázási profilja, amely a számlázott díjak keletkezhetnek neve
billingProfileName | A számlázási profilt, amely a számlázott díjak keletkezhetnek egyedi azonosítója
invoiceSectionId | A számla szakaszának egyedi azonosítója
invoiceSectionName | A számla szakasz nevét
costCenter | A költségközpont definiálva az előfizetésre vonatkozó költségeit (csak nyissa meg a számlázási időszak érhető el)
billingPeriodStartDate | A számlázási időszak, amelynek a számla jön létre, a kezdő dátum
billingPeriodEndDate | A számlázási időszak, amelynek a számla jön létre, a záró dátum
servicePeriodStartDate | A minősítés időszak kezdő dátuma amely definiált és zárolva van, a felhasznált vagy beszerzett szolgáltatás díjszabása
servicePeriodEndDate | A minősítés időszak vége amely definiált és zárolva van, a felhasznált vagy beszerzett szolgáltatás díjszabása
dátum | Az Azure-ban és a piactér használat alapú költségek Ez az értékelés dátuma. Egyszeri vásárlások (foglalásokat, Marketplace-en) vagy rögzített ismétlődő költségek (támogatási ajánlatok) Ez a vásárlás időpontjától.
serviceFamily | A szolgáltatáshoz tartozó szolgáltatás termékcsalád
productOrderId | A termék sorrendben egyedi azonosítója
productOrderName | Egyedi nevet a termék sorrend
consumedService | Az igénybe vett szolgáltatás neve
meterId | A mérőszám az egyedi azonosítója
meterName | Mérőszám neve
meterCategory | A mérőszám a besorolási kategória neve. Ha például *Cloud services*, *hálózatkezelés*stb.
meterSubCategory | A mérőszám alárendelt besorolási kategória neve
meterRegion | Neve az a régió, ahol a szolgáltatás a fogyasztásmérő érhető el. A releváns adatközpont bizonyos szolgáltatásokhoz, amelyek díjszabása adatokat szolgáltató adatközpont elhelyezkedése alapján.
az ajánlat | A megvásárolt előfizetési csomag neve
productId | A díjakat halmoz fel a termék egyedi azonosítója
termék | A díjakat halmoz fel a termék neve
előfizetés-azonosító | Az előfizetés, a díjakat halmoz fel egyedi azonosítója
subscriptionName | A díjakat halmoz fel az előfizetés neve
reservationId | A megvásárolt foglalás-példány egyedi azonosítója
reservationName | A megvásárolt foglalás példány neve
publisherType | Írja be a publisher (értékek: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | Marketplace-szolgáltatások közzétevője
resourceGroupId | Az erőforráscsoport az erőforráshoz tartozó egyedi azonosítója
resourceGroupName | Az erőforrással az erőforráscsoport neve
resourceId | Az erőforrás-példány egyedi azonosítója
resourceType | Erőforrás-példány típusa
resourceLocation | Az Adatközpont, ahol az erőforrás fut. helyét azonosítja.
location | Az erőforrás, ha különböző helyeken vannak konfigurálva az azonos régióban normalizált helye
Mennyiség | Vásárolt, vagy a felhasznált egységek száma
unitOfMeasure | A szolgáltatás számlázási mértékegysége. Például a számítási szolgáltatások díjszabása óradíjalapú.
chargeType | A díj típusa. Értékek: <ul><li>AsCharged-használat: Vannak a feladatokkal kapcsolatos díjakat alapján az Azure-szolgáltatások használatát. Ez magában foglalja a használati adatainak összevetésére a virtuális gépeket, amelyek miatt a fenntartott példányok nem terheli.</li><li>AsCharged-PurchaseMarketplace: A piactér-beli vásárlásokra egyszeri vagy rögzített ismétlődő díjak</li><li>AsCharged-UsageMarketplace: Marketplace-szolgáltatások, amelyek díja fogyasztás egységek alapján kell fizetni</li></ul>
isAzureCreditEligible | Azt a jelzőt, amely azt jelzi, hogy a díj a szolgáltatás az Azure-kreditek használatával fizetendő jogosult (értékeket: IGAZ, hamis)
serviceInfo1 | Szolgáltatásspecifikus metaadatok láthatók
serviceInfo2 | Régi mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok láthatók
additionalInfo | További szolgáltatásspecifikus metaadatok láthatók.
tags | Az erőforrás rendel címkék

### <a name="how-do-i-make-sure-that-the-charges-in-my-azure-usage-and-charges-file-are-correct"></a>Hogyan ellenőrizhetem, hogy, hogy helyesek-e a saját Azure-használat és költségek fájlban kell fizetni?

Ha a részletes használati fájl, amelyet további részleteket a díj, lásd: [a számlázási profilja számlán a költségek ismertetése](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a Microsoft Azure-használat és a díj](billing-download-azure-daily-usage.md)

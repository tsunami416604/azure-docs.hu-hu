---
title: Az Azure használat és a díj a Microsoft vevői szerződés feltételei
description: Megtudhatja, hogyan olvashatók és érthetők, az Azure-használat és a fürt megosztott kötetei szolgáltatás díja a számlázási profilja szakaszait.
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
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490624"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Az Azure használat és a díj a Microsoft vevői szerződés feltételei

Ez a cikk a Microsoft vevői szerződés számlázási fiók vonatkozik. [Ellenőrizze, hogy egy Microsoft Ügyfélszerződéséhez hozzáférést](#check-access-to-a-microsoft-customer-agreement).

Az Azure használat és költségek CSV-fájlt a jelenlegi számlázási időszak napi és a mérőszám-szintű használati díjak tartalmazza.

A használat és költségek az Azure file lekéréséhez lásd: [és a Microsoft Ügyfélszerződéséhez díjai megtekintése és letöltése az Azure használati](billing-download-azure-daily-usage.md). Egy vesszővel tagolt (.csv) formátumú, amelyeket megnyithat egy számolótábla alkalmazásban érhető el.

A használati díjak az összes **havi** díjak egy adott előfizetés. A használati díjak ne vegye figyelembe bármely jóváírások és engedmények.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Változások az Azure nagyvállalati szerződéssel rendelkező használat és költségek

Ha nagyvállalati szerződéssel rendelkező ügyfelek, láthatja, hogy az Azure számlázási profil használati CSV-fájl a feltételek eltérnek a feltételeket az Azure nagyvállalati szerződéssel rendelkező használati CSV-fájl. A nagyvállalati szerződésre vonatkozó használati feltételek profil használati feltételek számlázással leképezés a következő:

| Az Azure nagyvállalati szerződéssel rendelkező használati CSV | A Microsoft vevői szerződés program Azure-használat és a fürt megosztott kötetei szolgáltatás díj |
| --- | --- |
| Dátum | date |
| Hónap| date |
| Nap | date |
| Év | date |
| Product | product |
| MeterId | meterID |
| MeterCategory | MeterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | MeterRegion |
| MeterName | MeterName |
| ConsumedQuantity | Mennyiség |
| ResourceRate | effectivePrice |
| ExtendedCost | költség |
| resourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Tags | címkék |
| StoreServiceIdentifier | – |
| Részleg neve | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Részletes használati és -leírások

Az alábbi feltételek jelennek meg az Azure használat és költségek.

Kifejezés | Leírás
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
date | Az Azure-ban és a piactér használat alapú költségek Ez az értékelés dátuma. Egyszeri vásárlások (foglalásokat, Marketplace-en) vagy rögzített ismétlődő költségek (támogatási ajánlatok) Ez a vásárlás időpontjától.
serviceFamily | A szolgáltatáshoz tartozó szolgáltatás termékcsalád
productOrderId | A termék sorrendben egyedi azonosítója
productOrderName | Egyedi nevet a termék sorrend
consumedService | Az igénybe vett szolgáltatás neve
meterId | A mérőszám az egyedi azonosítója
MeterName | Mérőszám neve
MeterCategory | A mérőszám a besorolási kategória neve. Ha például *Cloud services*, *hálózatkezelés*stb.
meterSubCategory | A mérőszám alárendelt besorolási kategória neve
MeterRegion | Neve az a régió, ahol a szolgáltatás a fogyasztásmérő érhető el. A releváns adatközpont bizonyos szolgáltatásokhoz, amelyek díjszabása adatokat szolgáltató adatközpont elhelyezkedése alapján.
az ajánlat | A megvásárolt előfizetési csomag neve
productId | A díjakat halmoz fel a termék egyedi azonosítója
product | A díjakat halmoz fel a termék neve
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
UnitOfMeasure | A szolgáltatás számlázási mértékegysége. Például a számítási szolgáltatások díjszabása óradíjalapú.
chargeType | A díj típusa. Értékek: <ul><li>AsCharged-használat: Vannak a feladatokkal kapcsolatos díjakat alapján az Azure-szolgáltatások használatát. Ez magában foglalja a használati adatainak összevetésére a virtuális gépeket, amelyek miatt a fenntartott példányok nem terheli.</li><li>AsCharged-PurchaseMarketplace: A piactér-beli vásárlásokra egyszeri vagy rögzített ismétlődő díjak</li><li>AsCharged-UsageMarketplace: Marketplace-szolgáltatások, amelyek díja fogyasztás egységek alapján kell fizetni</li></ul>
isAzureCreditEligible | Azt a jelzőt, amely azt jelzi, hogy a díj a szolgáltatás az Azure-kreditek használatával fizetendő jogosult (értékeket: IGAZ, hamis)
serviceInfo1 | Szolgáltatásspecifikus metaadatok láthatók
serviceInfo2 | Régi mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok láthatók
additionalInfo | További szolgáltatásspecifikus metaadatok láthatók.
címkék | Az erőforrás rendel címkék

### <a name="make-sure-that-charges-are-correct"></a>Győződjön meg arról, hogy helyesek-e a költségek

Ha azt szeretné, győződjön meg arról, hogy helyesek-e a részletes használati fájlban kell fizetni, ellenőrizheti őket. Lásd: [a számlázási profilja számlán a költségek ismertetése](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>A Microsoft Ügyfélszerződéséhez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a Microsoft Azure-használat és a díj](billing-download-azure-daily-usage.md)

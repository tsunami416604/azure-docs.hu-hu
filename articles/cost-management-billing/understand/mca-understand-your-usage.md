---
title: Microsoft Customer szerződés – Azure használati feltételek
description: A számlázási profiljához tartozó, az Azure használati adatait és díjait tartalmazó CSV-fájl szakaszainak ismertetése.
author: bandersmsft
manager: jureid
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: b48cc5d5a36a382909adb250dd76b4f5783a4340
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75990904"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>A Microsoft-ügyfélszerződéshez tartozó, az Azure használati adatait és díjait tartalmazó fájlban foglalt kifejezések

Ez a cikk a Microsoft-ügyfélszerződéshez tartozó számlázási fiókokra vonatkozik. [Ellenőrizze, hogy rendelkezik-e hozzáféréssel egy Microsoft-ügyfélszerződéshez](#check-access-to-a-microsoft-customer-agreement).

Az Azure használati adatok és -díjak CSV-fájlja az aktuális számlázási időszakra vonatkozó napi és fogyasztásszintű használati díjakat tartalmazza.

Az Azure használati adatait és díjait tartalmazó fájl letöltéséhez lásd [a Microsoft-ügyfélszerződéshez tartozó Azure-használati adatok és -díjak megtekintését és letöltését](download-azure-daily-usage.md) ismertető részt. Egy vesszővel tagolt (.csv) fájlként érhető el, amelyet táblázatkezelő alkalmazásokkal nyithat meg.

A használati díjak az előfizetés keretében felszámított teljes **havi** díjak. A használati díjak nem veszik figyelembe a jóváírásokat és a kedvezményeket.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Változások a nagyvállalati ügyfelek Azure-használati adataihoz és -díjaihoz képest

Ha Ön nagyvállalati ügyfél volt, látni fogja, hogy az Azure számlázási profil használati adatokat tartalmazó CSV-fájljának kifejezései eltérnek az Azure Nagyvállalati Szerződéshez tartozó, használati adatokat tartalmazó CSV-fájl kifejezéseitől. Az alábbiakban látható a Nagyvállalati Szerződés használati kifejezéseinek leképezése a számlázási profil használati kifejezéseire:

| Az Azure Nagyvállalati Szerződés használati adatait tartalmazó CSV-fájl | A Microsoft-ügyfélszerződéshez tartozó, Azure-használati adatokat és -díjakat tartalmazó CSV-fájl |
| --- | --- |
| Dátum | dátum |
| Hónap| dátum |
| Nap | dátum |
| Év | dátum |
| Termék | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| MeterName | meterName |
| ConsumedQuantity | quantity |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| ResourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | serviceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | additionalInfo |
| Címkék | címkét |
| StoreServiceIdentifier | – |
| DepartmentName | invoiceSection |
| CostCenter | costCenter |
| UnitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Az egyes kifejezések és leírásaik

A következő kifejezések jelennek meg az Azure használati adatait és díjait tartalmazó fájlban.

Időtartam | Leírás
--- | ---
invoiceId | A számla PDF-fájlján látható egyedi dokumentumazonosító
previousInvoiceId | Hivatkozás az eredeti számlára, ha ez a számla visszatérítésről lett kiállítva
billingAccountName | A számlázási fiók neve
billingAccountId | Az alapszintű számlázási fiók egyedi azonosítója
billingProfileId | Annak a számlázási profilnak a neve, amelyben a kiszámlázott díjak keletkeznek
billingProfileName | Annak a számlázási profilnak az egyedi azonosítója, amelyben a kiszámlázott díjak keletkeznek
invoiceSectionId | A számlaszakasz egyedi azonosítója
invoiceSectionName | A számlaszakasz neve
costCenter | Az előfizetésben a költségek nyomon követésére meghatározott költséghely (csak nyitott számlázási időszakokban érhető el)
billingPeriodStartDate | Annak a számlázási időszaknak a kezdődátuma, amelyre a számla ki lett állítva
billingPeriodEndDate | Annak a számlázási időszaknak a záródátuma, amelyre a számla ki lett állítva
servicePeriodStartDate | Annak a díjszabási időszaknak a kezdődátuma, amely meghatározta és rögzítette az igénybe vett vagy megvásárolt szolgáltatások árképzését
servicePeriodEndDate | Annak a díjszabási időszaknak a záródátuma, amely meghatározta és rögzítette az igénybe vett vagy megvásárolt szolgáltatások árképzését
dátum | Az Azure és a Marketplace használatán alapuló díjak díjszabási dátuma. Egyszeri vásárlások (Reservations, Marketplace) vagy állandó, időszakos díjak (támogatási ajánlatok) esetén ez a vásárlás dátuma.
serviceFamily | Az a szolgáltatáscsalád, amelybe a szolgáltatás tartozik
productOrderId | A termékrendelés egyedi azonosítója
productOrderName | A termékrendelés egyedi neve
consumedService | Az igénybe vett szolgáltatás neve
meterId | A fogyasztásmérő egyedi azonosítója
meterName | A fogyasztásmérő neve
meterCategory | A fogyasztásmérő osztályozási kategóriájának neve. Például *Felhőszolgáltatások*, *Hálózat* stb.
meterSubCategory | A fogyasztásmérő osztályozási alkategóriájának neve
meterRegion | Azon régió neve, ahol a szolgáltatáshoz tartozó fogyasztásmérő elérhető. Az adatközpont helye, ha a szolgáltatás díjszabása az adatközpont helyétől is függ.
offer | A megvásárolt ajánlat neve
productId | Annak a terméknek az egyedi azonosítója, amelyre vonatkozóan a díjak keletkeznek
product | Annak a terméknek a neve, amelyre vonatkozóan a díjak keletkeznek
subscription ID | Annak az előfizetésnek az egyedi azonosítója, amelyre vonatkozóan a díjak keletkeznek
subscriptionName | Annak az előfizetésnek a neve, amelyre vonatkozóan a díjak keletkeznek
reservationId | A megvásárolt foglaláspéldány egyedi azonosítója
reservationName | A megvásárolt foglaláspéldány neve
publisherType | Közzétevő típusa (értékek: firstParty, thirdPartyReseller, thirdPartyAgency)
publisherName | A Marketplace-szolgáltatások közzétevője
resourceGroupId | Az erőforráshoz társított erőforráscsoportok egyedi azonosítója
resourceGroupName | Az erőforráshoz társított erőforráscsoportok neve
resourceId | Az erőforráspéldány egyedi azonosítója
resourceType | Az erőforráspéldány típusa
resourceLocation | Megadja az adatközpont helyét, ahol az erőforrás fut.
location | Az erőforrás normalizált helye, ha különböző erőforráshelyek vannak konfigurálva ugyanazokhoz a régiókhoz
quantity | A megvásárolt vagy igénybe vett egységek száma
unitOfMeasure | A szolgáltatás számlázásának mértékegysége. A számítási szolgáltatások számlázása például óraalapú.
chargeType | A díj típusa. Értékek: <ul><li>AsCharged – használat: az Azure-szolgáltatások használata alapján felhalmozódott díjak. Ez magában foglalja a fenntartott példányok miatt nem felszámított virtuális gépeken való használatot.</li><li>AsCharged-PurchaseMarketplace: a Piactéri vásárlások egyszeri vagy rögzített ismétlődő díjai</li><li>AsCharged-UsageMarketplace: a Marketplace-szolgáltatások díjszabása a használati egységek alapján</li></ul>
isAzureCreditEligible | Jelző, amely azt jelzi, hogy az Azure-kreditek használatára jogosult-e a szolgáltatásért fizetendő díj (Values: true, false)
serviceInfo1 | Szolgáltatásspecifikus metaadatok
serviceInfo2 | Örökölt mező, amelyben a nem kötelező szolgáltatásspecifikus metaadatok rögzíthetők.
additionalInfo | További szolgáltatásspecifikus metaadatok.
címkét | Az erőforráshoz hozzárendelt címkék

### <a name="make-sure-that-charges-are-correct"></a>A díjak helyességének ellenőrzése

Ha meg szeretne győződni arról, hogy a részletes használati adatokat tartalmazó fájlban szereplő díjak helyesek, ellenőrizheti őket. Lásd [a számlázási profil számláján szereplő díjakat ismertető részt.](review-customer-agreement-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Microsoft-ügyfélszerződéshez való hozzáférés ellenőrzése
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Következő lépések

- [A Microsoft Azure-számla megtekintése és letöltése](download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](download-azure-daily-usage.md)

---
title: A részletes használat és költségek |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatók és érthetők, a részletes használat és költségek
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 9ff9b6b5313026d2102b98659183fa97c6a5ef84
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683987"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>A használati megismerheti az Azure használat és költségek fájlban

A részletes használat és költségek fájl tartalmazza a megadott időtartam alapján egyeztetett értékek, a vásárlások (például a foglalásokat, a Marketplace-díjakat) és a visszatérítéseket napi névleges használat.
Díjak nem tartalmazzák a kreditek, adók, költségek, illetve más kedvezményeket jelentenek.
Írja be a következő táblázat tartalmazza, mely költségek tartoznak az egyes fiókok számára.

Fióktípus | Azure-használat | Marketplace-használat | Vásárlás | A befizetett
--- | --- | --- | --- | ---
Nagyvállalati Szerződés (EA) | Igen | Igen | Igen | Nem
Microsoft Ügyfélszerződés (Microsoft Customer Agreement, MCA) | Igen | Igen | Igen | Igen
Használatalapú fizetés (PAYG) | Igen | Nem | Nem | Nem

Marketplace-en orders (más néven a külső szolgáltatások) kapcsolatos további információkért lásd: [az Azure külső szolgáltatási díjak értelmezése](billing-understand-your-azure-marketplace-charges.md).

Lásd: [beszerzése az Azure számlázási és napi használati adatok](billing-download-azure-invoice-daily-usage-date.md) a letöltési utasításokat.
A használat és költségek fájlt egy vesszővel tagolt (.csv) formátumú, megnyithat egy számolótábla alkalmazásban érhető el.

## <a name="list-of-terms-and-descriptions"></a>Feltételek és leírásukat

A következő táblázat ismerteti a fontos kifejezések, a használat és költségek Azure-beli fájlmegosztás legújabb verzióját.
A lista ismerteti a használatalapú fizetés (PAYG), a nagyvállalati szerződés (EA) és a Microsoft ügyfél-szerződés (MCA) fiókok.

Időtartam | Fióktípus | Leírás
--- | --- | ---
Fióknév | Nagyvállalati szerződés | A regisztrációs fiók megjelenített neve.
Fióktulajdonos azonosítója | Nagyvállalati szerződés | Az eszközregisztráció-fiók egyedi azonosítója.
További információk | Összes | Szolgáltatásspecifikus metaadatok láthatók. Ha például egy lemezkép típusa egy virtuális gép.
BillingAccountId | EA, MCA | A legfelső szintű számlázási fiók egyedi azonosítója.
BillingAccountName | EA, MCA | Számlázási fiók nevét.
BillingCurrency | EA, MCA | Számlázási fiókhoz tartozó pénznem.
BillingPeriod | Nagyvállalati szerződés | A számlázási időszak az ingyenesen elérhető.
BillingPeriodEndDate | EA, MCA | A számlázási időszak vége dátuma.
BillingPeriodStartDate | EA, MCA | A számlázási időszak kezdete dátuma.
BillingProfileId | EA, MCA | Az EA-regisztrációhoz vagy MCA számlázási profil egyedi azonosítója.
BillingProfileName | EA, MCA | Az EA-regisztrációhoz vagy MCA számlázási profil neve.
ChargeType | EA, MCA | Azt jelzi, hogy a díj használat (**használati**), egy vásárlási (**beszerzési**), vagy a visszatérítés (**visszatérítés**).
Felhasznált mennyiség | HASZNÁLATALAPÚ | Tekintse meg a mennyiséget.
Használt szolgáltatás | Összes | A díj szolgáltatás neve társítva van.
Költségek | Nagyvállalati szerződés | Tekintse meg a CostInBillingCurrency.
Költséghely | EA, MCA | A költségközpont definiálva az előfizetés (csak a nyílt elszámolási időszakok MCA fiókokhoz érhető el) költségeit.
CostInBillingCurrency | MCA | A díj a kreditek vagy adók előtt a Számlázás pénzneme költsége.
CostInPricingCurrency | MCA | A díj a díjszabási pénznemben krediteket vagy adók előtt költsége.
Currency (Pénznem) | HASZNÁLATALAPÚ | Tekintse meg a BillingCurrency.
Dátum | EA, MCA | A díj használat vagy a vásárlás dátuma.
ExchangeRateDate | MCA | A dátum az átváltási árfolyam lett létrehozva.
ExchangeRatePricingToBilling | MCA | A költség, a díjszabási pénznemben átalakítása a Számlázás pénzneme használt árfolyam.
Gyakoriság | EA, MCA | Azt jelzi, hogy díj várhatóan ismételje meg. Díjak, vagy akkor fordulhat elő többször (**OneTime**) ismételje meg a havi vagy éves történik (**ismétlődő**), vagy a használat alapján kell (**UsageBased**).
includedQuantity | HASZNÁLATALAPÚ | A mérőszám, amely tartalmazza az aktuális elszámolási időszakban érvényes díjmentesen mennyisége.
Példány azonosítója | PAGY | See ResourceId.
InvoiceId | EA, MCA | A PDF-fájlt a számlán szereplő egyedi dokumentumazonosító.
invoiceSection | MCA | Tekintse meg a InvoiceSectionName.
InvoiceSectionId | EA, MCA | A nagyvállalati szerződéssel rendelkező részleg vagy MCA számla szakasz egyedi azonosítója.
InvoiceSectionName | EA, MCA | A nagyvállalati szerződéssel rendelkező részleg vagy MCA számla szakasz nevét.
IsAzureCreditEligible | EA, MCA | Azt jelzi, hogy a díj fizetendő Azure-kreditek használatával támogatható (értékeket: IGAZ, hamis).
Location egység | EA, MCA | -Adatközpontot, ahol az erőforrás fut.
Fogyasztásmérő kategóriája | Összes | A mérőszám a besorolási kategória neve. Ha például *Cloud services* és *hálózatkezelés*.
Fogyasztásmérő azonosítója | Összes | A mérő egyedi azonosítója.
Fogyasztásmérő neve | Összes | A mérő neve.
Fogyasztásmérő régiója | Összes | Az Adatközpont helyét szolgáltatások díjszabása neve helye alapján. Tekintse meg a helyet.
Fogyasztásmérő alkategóriája | Összes | A mérőszám subclassification kategória nevét.
Ajánlat azonosítója | EA, MCA | Az ajánlat keretében vásárolt neve.
Cikkszám | Nagyvállalati szerződés | Adott mérőszámok díjszabás beszerzéséhez használt azonosító.
PlanName | Nagyvállalati szerződés | Marketplace-csomag neve.
PreviousInvoiceId | MCA | Egy eredeti számlát, ha a jelen visszatérítés hivatkozás.
pricingCurrency | MCA | Értékelés alapján egyeztetett árak használt pénznem.
Product | MCA | Tekintse meg a ProductName.
Termékazonosító | EA, MCA | A termék egyedi azonosítója.
Terméknév | Nagyvállalati szerződés | A termék nevét.
productOrderId | EA, MCA | A termék sorrendben egyedi azonosítója.
productOrderName | EA, MCA | A termék rendelés egyedi neve.
Közzétevő neve | EA, MCA | A Publisher a Marketplace-szolgáltatásait.
PublisherType | EA, MCA | Írja be a publisher (értékek: firstParty, thirdPartyReseller, thirdPartyAgency).
Mennyiség | EA, MCA | Az egységek számát vásárolt, vagy a felhasznált.
Rate (Egységár) | HASZNÁLATALAPÚ | Tekintse meg a UnitPrice.
ReservationId | EA, MCA | A megvásárolt foglalás-példány egyedi azonosítója.
ReservationName | EA, MCA | A megvásárolt foglalás-példány nevét.
ResourceGroupId | EA, MCA | Egyedi azonosítója a [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) van az erőforrás.
ResourceGroupName | EA, MCA | Neve a [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) van az erőforrás.
ResourceId | EA, MCA | Egyedi azonosítója a [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) erőforrás.
Erőforrás helye | EA, MCA | -Adatközpontot, ahol az erőforrás fut. Tekintse meg a helyet.
ResourceName nevű erőforrásáról | Nagyvállalati szerződés | Az erőforrás neve.
ResourceType | MCA | Erőforrás-példány típusát.
serviceFamily | EA, MCA | Termékcsalád szolgáltatás, amely a szolgáltatás tartozik.
1. szolgáltatási információ | Összes | Szolgáltatásspecifikus metaadatok láthatók.
2. szolgáltatási információ | Összes | Régi mező a nem kötelező szolgáltatásspecifikus metaadatok láthatók.
ServicePeriodEndDate | MCA | A minősítés időszak vége, hogy meghatározott, és zárolva van, a felhasznált vagy beszerzett szolgáltatás díjszabása.
ServicePeriodStartDate | MCA | A minősítés időszak kezdő dátuma, hogy meghatározott, és zárolva van, a felhasznált vagy beszerzett szolgáltatás díjszabása.
SubscriptionId | Összes | Az előfizetéshez tartozó egyedi azonosítója.
Előfizetés neve | Összes | Az előfizetés nevét.
Címkék | Összes | Az erőforráshoz hozzárendelt címkék. Erőforráscímkék csoport nem tartalmazza. Belső költséghelyi elszámolás költségeinek terjesztése vagy a csoport használható. További információkért lásd: [címkékkel rendelkező Azure-erőforrások rendszerezése](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Unit (Egység) | HASZNÁLATALAPÚ | Tekintse meg a UnitOfMeasure.
Mértékegység | Összes | A szolgáltatás számlázási mértékegysége. Például a számítási szolgáltatások díjszabása óradíjalapú.
Egységár | Nagyvállalati szerződés | Az Egységár esetében a díjat.
UsageDate | HASZNÁLATALAPÚ | Tekintse meg a dátumot.

Vegye figyelembe, egyes mezőit eltérhet a kis-és nagybetűhasználatának és fiókok típusai közötti térköz.
Használatalapú fizetéses használati fájlok régebbi verzióit külön szakaszokban a nyilatkozat és a napi használati rendelkezik.

## <a name="ensure-that-your-charges-are-correct"></a>Győződjön meg arról, hogy helyesek-e a költségeket

Részletes használat és költségek kapcsolatos további tudnivalókért olvassa el tudni, hogyan lehet a [utólagos elszámolású](./billing-understand-your-bill.md) vagy [Microsoft Ügyfélszerződéséhez](billing-mca-understand-your-bill.md) számla.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Megtekintheti, és a Microsoft Azure-számla letöltése](billing-download-azure-invoice.md)
- [Megtekintheti és letöltheti a Microsoft Azure-használat és a díj](billing-download-azure-daily-usage.md)

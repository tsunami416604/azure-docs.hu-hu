---
title: A részletes használati adatok és díjak ismertetése | Microsoft Docs
description: Ismerje meg, hogyan olvashatók le és értelmezhetők a részletes használati adatok és díjak
author: bandersmsft
manager: micflan
tags: billing
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: a68393b2852f8ddc758e2a47b9e1b5d94befb7b4
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76290138"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Az Azure használati adatait és díjait tartalmazó fájl kifejezéseinek ismertetése

A részletes használati adatokat és díjakat tartalmazó fájlban megtalálhatók az egyeztetett díjszabáson alapuló napi szintű használati adatok, a vásárlások (például a foglalások és a Marketplace díjai) és a megadott időszakra vonatkozó visszatérítések.
A díjak nem tartalmazzák a krediteket, adókat és egyéb díjakat vagy kedvezményeket.
A következő táblázat ismerteti, hogy a fájl melyik fióktípusnál melyik díjakat tartalmazza.

Fiók típusa | Azure-használat | Marketplace-használat | Vásárlások | Visszatérítések
--- | --- | --- | --- | ---
Nagyvállalati Szerződés (EA) | Igen | Igen | Igen | Nem
Microsoft-ügyfélszerződés (Microsoft Customer Agreement, MCA) | Igen | Igen | Igen | Igen
Használatalapú fizetés (PAYG) | Igen | Igen | Nem | Nem

A Marketplace-rendelésekkel (más néven külső szolgáltatásokkal) kapcsolatos további tudnivalók: [Az Azure külső szolgáltatásaival kapcsolatos díjak ismertetése](understand-azure-marketplace-charges.md).

Letöltési útmutató: [Az Azure számlázási és napi használati adatainak beszerzése](../manage/download-azure-invoice-daily-usage-date.md).
A használati adatokat és díjakat tartalmazó CSV-fájl megnyitható a Microsoft Excelben vagy egy másik táblázatkezelő alkalmazásban.

## <a name="list-of-terms-and-descriptions"></a>Kifejezések és leírások listája

A következő tábla leírja az Azure használati adatait és díjait tartalmazó fájljának legújabb verziójában használt fontos kifejezéseket.
A lista a használatalapú fizetéses (PAYG), a Nagyvállalati Szerződéssel (EA) és Microsoft-ügyfélszerződéssel (Microsoft Customer Agreement, MCA) rendelkező fiókok kifejezéseit is tartalmazza.

Időszak | Fiók típusa | Leírás
--- | --- | ---
AccountName | EA, PAYG | Az EA regisztrációs fiók vagy PAYG számlázási fiók megjelenítendő neve.
AccountOwnerId<sup>1</sup> | EA, PAYG | Az EA regisztrációs fiók vagy PAYG számlázási fiók egyedi azonosítója.
AdditionalInfo | Összes | Szolgáltatásspecifikus metaadatok. Például egy virtuális gép rendszerképének típusa.
BillingAccountId<sup>1</sup> | Összes | Az alapszintű számlázási fiók egyedi azonosítója.
BillingAccountName | Összes | A számlázási fiók neve.
BillingCurrency | Összes | A számlázási fiókhoz tartozó pénznem.
BillingPeriod | EA, PAYG | A díj számlázási időszaka.
BillingPeriodEndDate | Összes | A számlázási időszak záródátuma.
BillingPeriodStartDate | Összes | A számlázási időszak kezdődátuma.
BillingProfileId<sup>1</sup> | Összes | Az EA-regisztráció, PAYG-előfizetés, MCA-számlázásiprofil vagy összesített AWS-fiók egyedi azonosítója.
BillingProfileName | Összes | Az EA-regisztráció, PAYG-előfizetés, MCA-számlázásiprofil vagy összesített AWS-fiók neve.
ChargeType | Összes | Jelzi, hogy a díj használatra (**Usage**), vásárlásra (**Purchase**) vagy visszatérítésre (**Refund**) vonatkozik-e.
ConsumedService | Összes | A szolgáltatás neve, amelyre a díj vonatkozik.
CostCenter<sup>1</sup> | EA, MCA | Az előfizetésben a költségek nyomon követésére meghatározott költséghely (csak az MCA-fiókok nyitott számlázási időszakaiban érhető el).
Költségek | EA, PAYG | Lásd: CostInBillingCurrency.
CostInBillingCurrency | MCA | A díj költsége a számlázási pénznemben számítva, kreditek és adók nélkül.
CostInPricingCurrency | MCA | A díj költsége a díjszabási pénznemben számítva, kreditek és adók nélkül.
Currency (Pénznem) | EA, PAYG | Lásd: BillingCurrency.
Date<sup>1</sup> | Összes | A díjhoz tartozó használati vagy vásárlási dátum.
EffectivePrice | Összes | Az időszakra vonatkozó kevert egységár. A kevert árak átlagolással csökkentik az egységár bármely ingadozását, például a fokozatos szintbeállítás esetében, amely csökkenti az árat, ahogy a mennyiség az idő múlásával növekszik.
ExchangeRateDate | MCA | Az átváltási árfolyam hatályba lépésének dátuma.
ExchangeRatePricingToBilling | MCA | A költség árképzési pénznemről a számlázási pénznemre való átváltásához használt árfolyam.
Frequency | Összes | Jelzi, ha a díj várhatóan ismétlődni fog. A díjak lehetnek egyszeriek (**OneTime**) ismétlődhetnek havonta vagy évenként (**Recurring**), vagy lehetnek használatalapúak (**UsageBased**).
InvoiceId | PAYG, MCA | A számla PDF-fájlján látható egyedi dokumentumazonosító.
InvoiceSection | MCA | Lásd: InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Az EA-részleg vagy MCA-számlaszakasz egyedi azonosítója.
InvoiceSectionName | EA, MCA | Az EA-részleg vagy MCA-számlaszakasz neve.
IsAzureCreditEligible | Összes | Jelzi, hogy a díj fizethető-e Azure-kreditekkel (értékek: True (Igaz), False (Hamis)).
Hely | MCA | Az erőforrást futtató adatközpont helye.
MeterCategory | Összes | A fogyasztásmérő osztályozási kategóriájának neve. Például: *Felhőszolgáltatások* vagy *Hálózat*.
MeterId<sup>1</sup> | Összes | A fogyasztásmérő egyedi azonosítója.
MeterName | Összes | A fogyasztásmérő neve.
MeterRegion | Összes | Az adatközpont neve a hely alapján árazott szolgáltatások esetén. Lásd: Hely.
MeterSubCategory | Összes | A fogyasztásmérő osztályozási alkategóriájának neve.
OfferId<sup>1</sup> | Összes | A megvásárolt ajánlat neve.
PartNumber<sup>1</sup> | EA, PAYG | Az adott fogyasztásmérő árképzéséhez használt azonosító.
PlanName | EA, PAYG | A Marketplace-csomag neve.
PreviousInvoiceId | MCA | Hivatkozás az eredeti számlára, ha ez a számla visszatérítésről lett kiállítva.
PricingCurrency | MCA | Az egyeztetett árakon alapuló árképzéshez használt pénznem.
Product | Összes | A termék neve.
ProductId<sup>1</sup> | MCA | A termék egyedi azonosítója.
ProductOrderId | Összes | A termékrendelés egyedi azonosítója.
ProductOrderName | Összes | A termékrendelés egyedi neve.
Közzétevő neve | Összes | A Marketplace-szolgáltatások közzétevője.
PublisherType | Összes | A közzétevő típusa (értékek: **Azure**, **AWS**, **Marketplace**).
Mennyiség | Összes | A megvásárolt vagy igénybe vett egységek száma.
ReservationId | EA, MCA | A megvásárolt foglaláspéldány egyedi azonosítója.
ReservationName | EA, MCA | A megvásárolt foglaláspéldány neve.
ResourceGroup | Összes | Az erőforrást tartalmazó [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) neve. Nem minden díjtétel az erőforráscsoportokon üzembe helyezett erőforrásokból származik. Az erőforráscsoporthoz nem kapcsolódó díjtételek a következőként jelennek meg: null/üres, **Egyéb** vagy **Nem alkalmazható**.
ResourceId<sup>1</sup> | Összes | Az [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources)-erőforrás egyedi azonosítója.
ResourceLocation | Összes | Az erőforrást futtató adatközpont helye. Lásd: Hely.
ResourceName nevű erőforrásáról | EA, PAYG | Az erőforrás neve. Nem minden díjtétel az üzembe helyezett erőforrásokból származik. Az erőforrástípussal nem rendelkező díjtételek a következőként jelennek meg: null/üres, **Egyéb** vagy **Nem alkalmazható**.
ResourceType | MCA | Az erőforráspéldány típusa. Nem minden díjtétel az üzembe helyezett erőforrásokból származik. Az erőforrástípussal nem rendelkező díjtételek a következőként jelennek meg: null/üres, **Egyéb** vagy **Nem alkalmazható**.
ServiceFamily | MCA | Az a szolgáltatáscsalád, amelybe a szolgáltatás tartozik.
ServiceInfo1 | Összes | Szolgáltatásspecifikus metaadatok.
ServiceInfo2 | Összes | Örökölt mező a nem kötelező szolgáltatásspecifikus metaadatoknak.
ServicePeriodEndDate | MCA | Annak a díjszabási időszaknak a záródátuma, amely meghatározta és rögzítette az igénybe vett vagy megvásárolt szolgáltatások árképzését.
ServicePeriodStartDate | MCA | Annak a díjszabási időszaknak a kezdődátuma, amely meghatározta és rögzítette az igénybe vett vagy megvásárolt szolgáltatások árképzését.
SubscriptionId<sup>1</sup> | Összes | Az Azure-előfizetés egyedi azonosítója.
SubscriptionName | Összes | Az Azure-előfizetés neve.
Tags<sup>1</sup> | Összes | Az erőforráshoz hozzárendelt címkék. Nem tartalmazza az erőforráscsoport címkéit. A költségek csoportosítására vagy elosztására szolgál a belső költséghelyi elszámolás során. További információkért lásd: [Az Azure-erőforrások rendszerezése címkék használatával](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Időszak | Összes | Megjeleníti az ajánlat érvényességi időszakát. Például: Fenntartott példányok esetén a megjelenített időszak 12 hónap. Egyszeri vagy ismétlődő vásárlások esetén az időszak 1 hónap (SaaS, Marketplace-ügyfélszolgálat). Ez nem vonatkozik az Azure-felhasználásra.
UnitOfMeasure | Összes | A szolgáltatás számlázásának mértékegysége. A számítási szolgáltatások számlázása például óraalapú.
UnitPrice | EA, PAYG | A díj egységenkénti ára.

_<sup>**1**</sup> Az egy adott költségrekord egyedi azonosítójának létrehozásához használatos mezők._

Vegye figyelembe, hogy egyes mezők esetén a fiók típusától függően az írásmód és a szóközök használata eltérhet.
A használatalapú fizetés használati fájljainak régebbi verziói külön szakaszokat tartalmaztak a kimutatáshoz és a napi használathoz.

### <a name="list-of-terms-from-older-apis"></a>Régebbi API-k kifejezéseinek listája
A következő táblázat egymáshoz rendeli a régebbi API-kban használt kifejezéseket és az új kifejezéseket. Az új kifejezések leírásait a fenti tábla tartalmazza.

Régi kifejezés | Új kifejezés
--- | ---
ConsumedQuantity | Mennyiség
IncludedQuantity | N/A
InstanceId | ResourceId
Rate (Egységár) | EffectivePrice
Unit (Egység) | UnitOfMeasure
UsageDate | Dátum
UsageEnd | Dátum
UsageStart | Dátum


## <a name="ensure-charges-are-correct"></a>Ellenőrizze, hogy a díjak helyesek-e

A részletes használati adatokkal és díjakkal kapcsolatos további tudnivalókért olvassa el a [használatalapú fizetéses](review-individual-bill.md) vagy a [Microsoft-ügyfélszerződéses](review-customer-agreement-bill.md) számlák ismertetését.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [A Microsoft Azure-számla megtekintése és letöltése](download-azure-invoice.md)
- [A Microsoft Azure használati adatainak és díjainak megtekintése és letöltése](download-azure-daily-usage.md)

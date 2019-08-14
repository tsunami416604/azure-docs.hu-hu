---
title: A részletes használat és díjak megismerése | Microsoft Docs
description: Ismerje meg, hogyan olvashatja és értelmezheti részletes használati adatait és díját
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
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954340"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Ismerje meg az Azure-használat és a díjak fájljának feltételeit

A részletes használati és díjszabási fájl napi rendszerességű használatot tartalmaz az egyeztetett díjszabás, a vásárlások (például a foglalások, a Piactéri díjak) és a megadott időszakra vonatkozó visszatérítések alapján.
A díjak nem tartalmazzák a krediteket, adókat és egyéb díjakat vagy kedvezményeket.
Az alábbi táblázat tartalmazza, hogy az egyes fióktípus milyen díjakat tartalmaz.

Fióktípus | Azure-használat | Marketplace-használat | Vásárlások | Befizetett összegeket
--- | --- | --- | --- | ---
Nagyvállalati Szerződés (EA) | Igen | Igen | Igen | Nem
Microsoft Ügyfélszerződés (Microsoft Customer Agreement, MCA) | Igen | Igen | Igen | Igen
Utólagos elszámolású (TB) | Igen | Igen | Nem | Nem

Ha többet szeretne megtudni a Piactéri rendelésekről (más néven külső szolgáltatásokról), tekintse meg [Az Azure-beli külső szolgáltatási díjak megismerése](billing-understand-your-azure-marketplace-charges.md)című témakört.

A letöltési utasításokért lásd: az [Azure számlázási számla és a napi használati adatok](billing-download-azure-invoice-daily-usage-date.md)beszerzése.
A Microsoft Excelben vagy egy másik táblázatkezelő alkalmazásban megnyithatja a használatot, és kiszámlázhatja a CSV-fájlt.

## <a name="list-of-terms-and-descriptions"></a>A kifejezések és a leírások listája

Az alábbi táblázat az Azure-használati és-díjak fájl legújabb verziójában használt fontos kifejezéseket ismerteti.
A lista az utólagos elszámolású (TB), a Nagyvállalati Szerződés (EA) és a Microsoft Customer Agreement (MCA) fiókokat tartalmazza.

Kifejezés | Fióktípus | Leírás
--- | --- | ---
Fióknév | EA, TB | Az EA beléptetési fiók vagy TB számlázási fiók megjelenítendő neve.
<sup>1</sup> . AccountOwnerId | EA, TB | Az EA-beléptetési fiók vagy a TB számlázási fiók egyedi azonosítója.
További információk | Összes | Szolgáltatás-specifikus metaadatok. Például egy virtuális gép rendszerképének típusa.
<sup>1</sup> . BillingAccountId | Összes | A legfelső szintű számlázási fiók egyedi azonosítója.
BillingAccountName | Összes | A Számlázási fiók neve.
BillingCurrency | Összes | A számlázási fiókhoz társított pénznem.
BillingPeriod | EA, TB | A díj számlázási időszaka.
BillingPeriodEndDate | Összes | A számlázási időszak záró dátuma.
BillingPeriodStartDate | Összes | A számlázási időszak kezdő dátuma.
<sup>1</sup> . BillingProfileId | Összes | Az EA-regisztráció, a TB-előfizetés, az MCA számlázási profil vagy az AWS konszolidált fiók egyedi azonosítója.
BillingProfileName | Összes | Az EA-regisztráció, a TB-előfizetés, az MCA számlázási profil vagy az AWS konszolidált fiók neve.
ChargeType | Összes | Azt jelzi, hogy a díj a használat (**használat**), a vásárlás (**vásárlás**) vagy a visszatérítés (**visszatérítés**) értékének felel-e meg.
Használt szolgáltatás | Összes | Annak a szolgáltatásnak a neve, amelyhez a díj társítva van.
<sup>1</sup> . CostCenter | EA, MCA | Az előfizetéshez a költségek követéséhez meghatározott költséghely (csak az MCA-fiókok nyitott számlázási időszakai esetében érhető el).
Költség | EA, TB | Lásd: CostInBillingCurrency.
CostInBillingCurrency | MCA | A számlázási pénznemben a kreditek vagy adók előtt felszámított díj díja.
CostInPricingCurrency | MCA | A díjak díja az árképzési pénznemben kreditek vagy adók előtt.
Currency | EA, TB | Lásd: BillingCurrency.
<sup>1</sup> . dátum | Összes | A díj használati vagy beszerzési dátuma.
EffectivePrice | Összes | Az időszakhoz tartozó kevert egység ára. A kevert árak az egység árának bármely ingadozását, például a beosztott rétegek árát csökkentik, ami csökkenti az árat, ahogy a mennyiség növekszik az idő múlásával.
ExchangeRateDate | MCA | Az Exchange-arány létrejöttének dátuma.
ExchangeRatePricingToBilling | MCA | Az árképzési pénznemben a számlázási pénznemre való áttéréshez használt árfolyam.
Gyakoriság | Összes | Azt jelzi, hogy a díjat várhatóan meg kell-e ismételni. A díjak akár egyszer is megtörténnek, vagy megismétlődnek havonta vagy évenként (**ismétlődő**), vagy a használaton (**UsageBased**) alapulnak.
InvoiceId | TB, MCA | A számla PDF-fájljában felsorolt egyedi dokumentum-azonosító.
InvoiceSection | MCA | Lásd: InvoiceSectionName.
<sup>1</sup> . InvoiceSectionId | EA, MCA | A nagyvállalati szerződés vagy az MCA-számla szakasz egyedi azonosítója.
InvoiceSectionName | EA, MCA | Az EA részleg vagy az MCA-számla szakasz neve.
IsAzureCreditEligible | Összes | Azt jelzi, hogy a díj jogosult-e az Azure-kreditek (Values) használatával történő fizetésre. Igaz, hamis).
Location | MCA | Az adatközpont helye, ahol az erőforrás fut.
Fogyasztásmérő kategóriája | Összes | A fogyasztásmérő besorolási kategóriájának neve. Például a *Cloud Services* és a *hálózatkezelés*.
<sup>1</sup> . MeterId | Összes | A fogyasztásmérő egyedi azonosítója.
Fogyasztásmérő neve | Összes | A fogyasztásmérő neve.
Fogyasztásmérő régiója | Összes | Az adatközpont helyének neve a szolgáltatások díjszabása alapján. Lásd: hely.
MeterSubCategory | Összes | A fogyasztásmérő albesorolási kategóriájának neve.
<sup>1</sup> . OfferID | Összes | A megvásárolt ajánlat neve.
<sup>1</sup> . PartNumber | EA, TB | Az adott fogyasztásmérő díjszabásához használt azonosító.
PlanName | EA, TB | Marketplace-csomag neve.
PreviousInvoiceId | MCA | Hivatkozás egy eredeti számlára, ha ez a tétel visszatérítés.
PricingCurrency | MCA | Az egyeztetett árakon alapuló minősítéskor használt pénznem.
Product | Összes | A termék neve.
<sup>1</sup> . Termékkód | MCA | A termék egyedi azonosítója.
ProductOrderId | Összes | A termék rendelésének egyedi azonosítója.
ProductOrderName | Összes | A termék rendelésének egyedi neve.
Közzétevő neve | Összes | A Marketplace-szolgáltatások közzétevője.
PublisherType | Összes | Közzétevő típusa (értékek: **Azure**, **AWS**, **piactér**).
Mennyiség | Összes | A megvásárolt vagy felhasznált egységek száma.
ReservationId | EA, MCA | A megvásárolt foglalási példány egyedi azonosítója.
ReservationName | EA, MCA | A megvásárolt foglalási példány neve.
Erőforráscsoport | Összes | Azon [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) neve, amelyben az erőforrás található.
<sup>1</sup> . ResourceId | Összes | A [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) erőforrás egyedi azonosítója.
Erőforrás helye | Összes | Az adatközpont helye, ahol az erőforrás fut. Lásd: hely.
ResourceName nevű erőforrásáról | EA, TB | Az erőforrás neve.
Erőforrástípus | MCA | Az erőforrás-példány típusa.
ServiceFamily | MCA | A szolgáltatáshoz tartozó szolgáltatási család.
1\. szolgáltatási információ | Összes | Szolgáltatás-specifikus metaadatok.
ServiceInfo2 | Összes | Örökölt mező, amely nem kötelező, a szolgáltatásra jellemző metaadatokat tartalmaz.
ServicePeriodEndDate | MCA | A felhasznált vagy megvásárolt szolgáltatás díjszabása által meghatározott és zárolt minősítési időszak záró dátuma.
ServicePeriodStartDate | MCA | A felhasznált vagy megvásárolt szolgáltatás díjszabása által meghatározott és zárolt minősítési időszak kezdő dátuma.
<sup>1</sup> . SubscriptionId | Összes | Az Azure-előfizetés egyedi azonosítója.
Előfizetés neve | Összes | Az Azure-előfizetés neve.
<sup>1</sup> . Címkék | Összes | Az erőforráshoz rendelt címkék. Nem tartalmazza az erőforráscsoportok címkéit. A belső jóváírások költségeinek csoportosítására és terjesztésére használható. További információ: Azure- [erőforrások rendszerezése címkékkel](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/).
Kifejezés | Összes | Az ajánlat érvényességi idejét jeleníti meg. Példa: Fenntartott példányok esetén 12 hónapig jelenik meg a kifejezés. Egyszeri vásárlás vagy ismétlődő vásárlás esetén a kifejezés 1 hónap (SaaS, Piactéri támogatás). Ez az Azure-felhasználás esetében nem alkalmazható.
Mértékegység | Összes | A szolgáltatás számlázási mértékegysége. Például a számítási szolgáltatások számlázása óránként történik.
Egységár | EA, TB | A díj egységenkénti díja.

_<sup>**1**</sup> olyan mező, amely egyetlen KÖLTSÉGADATOK egyedi azonosítójának összeállítására szolgál._

Megjegyzés: egyes mezők eltérhetnek a fiókok típusai között a burkolatban és a térközben.
Az utólagos elszámolású használati fájlok régebbi verziói külön szakaszt biztosítanak az utasításhoz és a napi használathoz.

### <a name="list-of-terms-from-older-apis"></a>A régebbi API-k feltételeinek listája
Az alábbi táblázat a régebbi API-kra vonatkozó kifejezéseket képezi le az új kifejezésekre. Ezekről a leírásokról a fenti táblázatban talál további információt.

Régi időszak | Új kifejezés
--- | ---
Felhasznált mennyiség | Mennyiség
IncludedQuantity | –
InstanceId | ResourceId
Díj | EffectivePrice
Unit (Egység) | Mértékegység
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Ellenőrizze, hogy helyesek-e a díjak

Ha többet szeretne megtudni a részletes használatról és a költségekről, olvassa el az utólagos elszámolású vagy a [Microsoft ügyfél](billing-mca-understand-your-bill.md) [-](./billing-understand-your-bill.md) szerződési számla megismerését ismertető témakört.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Microsoft Azure-számla megtekintése és letöltése](billing-download-azure-invoice.md)
- [Microsoft Azure használat és díjak megtekintése és letöltése](billing-download-azure-daily-usage.md)

---
title: Az Azure Cost Management-adatkezelés ismertetése | Microsoft Docs
description: Ennek a cikknek a segítségével jobban megismerheti a Azure Cost Managementban található adatokat, valamint azt, hogy milyen gyakran dolgozza fel, gyűjti, jeleníti meg és zárja le azokat.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721373"
---
# <a name="understand-cost-management-data"></a>A Cost Management adatainak értelmezése

Ennek a cikknek a segítségével jobban megismerheti az Azure-beli és a Azure Cost Managementban található használati adatokat. Elmagyarázza, hogy az adatok feldolgozása, gyűjtése, megjelenítése és bezárása milyen gyakran történik. Az Azure-használatért havonta kell fizetnie. Bár a számlázási ciklusok havi időszakok, a ciklus kezdési és befejezési dátumai az előfizetés típusa szerint változnak. Milyen gyakran Cost Management a használati adatok fogadása különböző tényezőktől függően. Ilyen tényezők közé tartozik, hogy mennyi ideig tart az adatok feldolgozása, és hogy az Azure-szolgáltatások milyen gyakran bocsátják ki a használatot a számlázási rendszeren.

Cost Management tartalmazza az összes használatot és vásárlást, beleértve a foglalásokat és a harmadik féltől származó ajánlatokat Nagyvállalati Szerződés (EA) fiókokhoz. A Microsoft ügyfél-szerződési fiókjai és az utólagos elszámolású díjszabással rendelkező egyéni előfizetések csak az Azure és a piactér szolgáltatásainak használatát tartalmazzák. A támogatási és egyéb költségek nem tartoznak ide. A költségek becslése csak a számla generálása után történik, és a kreditek nem számítanak fel tényezőket.

## <a name="supported-microsoft-azure-offers"></a>Támogatott Microsoft Azure ajánlatok

Az alábbi információk a Azure Cost Management jelenleg támogatott [Microsoft Azure ajánlatait](https://azure.microsoft.com/support/legal/offer-details/) mutatják be. Az Azure-ajánlat az Ön által használt Azure-előfizetés típusa. Az adatok Cost Management a dátumtól kezdve **elérhető adatoktól** kezdve érhetők el. Ha az előfizetés módosul, az ajánlat változási dátuma előtt felmerülő költségek nem lesznek elérhetők.

| **Kategória**  | **Ajánlat neve** | **Kvóta azonosítója** | **Ajánlat száma** | **Elérhető adatok innen:** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USA KORM.-0017P | 2014 május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR – 0148P | 2014 május<sup>1</sup> |
| **Nagyvállalati Szerződés (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR – 0017P | 2014 május<sup>1</sup> |
| **Microsoft-ügyfélszerződés** | [Microsoft Azure terv](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | Március 2019<sup>3</sup> |
| **Microsoft-ügyfélszerződés** | [Fejlesztési és tesztelési terv Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | Március 2019<sup>3</sup> |
| **A partnerek által támogatott Microsoft-ügyfél szerződés** | Microsoft Azure-csomag | CSP_2015-05-01, CSP_MG_2017-12-01 és CSPDEVTEST_2018-05-01<br><br>A rendszer újra felhasználja a kvóta AZONOSÍTÓját a Microsoft ügyfél-szerződés és a régi CSP-előfizetések esetében. Jelenleg csak a Microsoft Customer Agreement-előfizetések támogatottak. | N/A | 2019. október |
| **Microsoft Developer Network (MSDN)** | [MSDN platformok](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR – 0062P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | [Használatalapú fizetés](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR – 0003P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR – 0023P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR – 0025P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | <sup>4</sup> . [ingyenes próbaverzió](https://azure.microsoft.com/offers/ms-azr-0044p)         | FreeTrial_2014-09-01 | MS-AZR – 0044P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | [Azure in Open Licencprogram](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR – 0111P | Október 2., 2018<sup>2</sup> |
| **Használatalapú fizetés** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | Október 2., 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR – 0029P | Október 2., 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR – 0059P | Október 2., 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR – 0060P | Október 2., 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR – 0063P | Október 2., 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR – 0064P | Október 2., 2018<sup>2</sup> |

_<sup>**1**</sup> az [Azure Enterprise portálon](https://ea.azure.com)az 2014-as előtti adatgyűjtéshez látogasson el._

_<sup>**2**</sup> az 2018. október 2. előtt az [adatAzure Fiókközpont](https://account.azure.com/subscriptions)a következő címen tájékozódhat:._

_<sup>**3**</sup> a Microsoft ügyfél-szerződések 2019 márciusában kezdődtek, és ehhez a ponthoz nem tartozik korábbi adatvesztés._

_<sup>**4**</sup> a kredit-alapú és előre fizetett előfizetések korábbi adatai nem felelnek meg a számlájának. A [korábbi adatértékek nem egyeznek az alábbi számlával](#historical-data-might-not-match-invoice) ._

A következő ajánlatok még nem támogatottak:

| Kategória  | **Ajánlat neve** | **Kvóta azonosítója** | **Ajánlat száma** |
| --- | --- | --- | --- |
| **Azure Germany** | [Azure Germany – utólagos fizetés](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Felhőalapú megoldás-szolgáltató (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR – 0145P |
| **Felhőalapú megoldás-szolgáltató (CSP)** | Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USA KORM.-0145P |
| **Felhőalapú megoldás-szolgáltató (CSP)** | Azure Germany a Microsoft Cloud Germany felhőszolgáltató programjában   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Használatalapú fizetés**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR – 0144P |
| **Használatalapú fizetés** | [Azure diákoknak](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR – 0170P |
| **Használatalapú fizetés**                 | [Microsoft Azure szponzorálás](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR – 0036P |
| **Támogatási csomagok** | Standard szintű támogatás                    | Default_2014-09-01 | MS-AZR – 0041P |
| **Támogatási csomagok** | Közvetlen professzionális támogatás         | Default_2014-09-01 | MS-AZR – 0042P |
| **Támogatási csomagok** | Fejlesztői támogatás                   | Default_2014-09-01 | MS-AZR – 0043P |
| **Támogatási csomagok** | Németország támogatási csomag                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Támogatási csomagok** | Azure Government Standard szintű támogatás   | Default_2014-09-01 | MS-AZR-USA KORM.-0041P |
| **Támogatási csomagok** | Azure Government Pro-Direct támogatás | Default_2014-09-01 | MS-AZR-USA KORM.-0042P |
| **Támogatási csomagok** | Azure Government Fejlesztői támogatás  | Default_2014-09-01 | MS-AZR-USA KORM.-0043P |

## <a name="determine-your-offer-type"></a>Ajánlat típusának meghatározása
Ha nem látja az előfizetés adatait, és meg szeretné állapítani, hogy az előfizetése a támogatott ajánlatok alá esik-e, ellenőrizheti, hogy az előfizetés támogatott-e. Ha ellenőrizni szeretné, hogy az Azure-előfizetés támogatott-e, jelentkezzen be a [Azure Portalba](https://portal.azure.com). Ezután válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben. A szolgáltatások listájában válassza az **előfizetések**lehetőséget. Az előfizetés listája menüben kattintson az ellenőrizni kívánt előfizetésre. Az előfizetés megjelenik az Áttekintés lapon, és megtekintheti az **ajánlat** és az **ajánlat azonosítóját**. Az alábbi képen egy példa látható.

![Példa az előfizetés áttekintése lapra az ajánlat és az ajánlat AZONOSÍTÓjának megjelenítéséhez](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>A Cost Managementban foglalt költségek

A következő táblázatok a benne foglalt vagy nem Cost Managementban lévő adatértékeket mutatják. A rendszer minden költséget a számla generálása után becsül. A feltüntetett költségek nem tartalmazzák az ingyenes és előre fizetett krediteket.

**A költségeket és a használati adatokat**

| **Tartalmazza** | **Nem tartalmazza** |
| --- | --- |
| Azure-szolgáltatás használata<sup>5</sup>        | Támogatási díjak – további információ: a [Számlázási feltételek magyarázata](../billing/billing-understand-your-invoice.md). |
| Marketplace-ajánlat használata<sup>6</sup> | Adók – további információ: a [Számlázási feltételek magyarázata](../billing/billing-understand-your-invoice.md). |
| Piactéri vásárlások<sup>6</sup>      | Kreditek – további információért lásd a [Számlázási feltételek magyarázatát](../billing/billing-understand-your-invoice.md). |
| Foglalási vásárlások<sup>7</sup>      |  |
| A foglalások beszerzésének amortizációja<sup>7</sup>      |  |

_<sup>**5**</sup> az Azure-szolgáltatások használata a foglalás és az egyeztetett árakon alapul._

_a <sup>**piactéren**</sup> vásárolt vásárlások jelenleg nem érhetők el az utólagos elszámolású, az MSDN és a Visual Studio-ajánlatok esetében._

_<sup>**7**</sup> a foglalások beszerzése jelenleg csak NAGYVÁLLALATI szerződés (EA) fiókok esetében érhető el._

**Metaadatok**

| **Tartalmazza** | **Nem tartalmazza** |
| --- | --- |
| <sup>8</sup> . erőforrás-Címkék | Erőforráscsoport-Címkék |

_<sup>**8**</sup> erőforrás-címkét alkalmaz a rendszer, mivel a használatot az egyes szolgáltatásokból kibocsátja, és nem érhető el visszamenőlegesen a korábbi használathoz._

## <a name="rated-usage-data-refresh-schedule"></a>Névleges használati adatok frissítési ütemterve

A Cost és a használati adatok Cost Management + számlázásban érhetők el a Azure Portal és a [támogató API](index.yml)-kon. A költségek áttekintése során tartsa szem előtt az alábbi szempontokat:

- Az aktuális számlázási időszak becsült díja naponta hat alkalommal frissül.
- Az aktuális számlázási időszakra vonatkozó becsült díjak a további használat során változhatnak.
- Minden frissítés kumulatív, és az előző frissítésből származó összes sort és információt tartalmazza.
- Az Azure a számlázási időszak lejárta után legfeljebb 72 óráig véglegesíti vagy _zárja_ be az aktuális számlázási időszakot (három naptári nap).

Az alábbi példák bemutatják, hogyan végződhet a számlázási időszakok.

Nagyvállalati Szerződés (EA) előfizetések – ha a számlázási hónap március 31-én lejár, a becsült díjak akár 72 órával később frissülnek. Ebben a példában az éjfél (UTC) április 4.

Utólagos elszámolású előfizetések – ha a számlázási hónap május 15-én ér véget, előfordulhat, hogy a becsült díjak akár 72 órával később is frissülnek. Ebben a példában éjfélkor (UTC) május 19.

### <a name="rerated-data"></a>Újraértékelt adatforgalom

Függetlenül attól, hogy a [Cost Management API-kat](index.yml), Power BIokat vagy az Azure Portal az adatok lekérésére használja-e, várhatóan az aktuális számlázási időszak díjait számítjuk fel, és így változik, amíg a számla le nem zárul.

## <a name="cost-management-data-fields"></a>Adatmezők Cost Management

A következő adatmezők találhatók a használati adatok fájljaiban és a Cost Management API-kon. A következő félkövér mezők esetében a partnerek a Cost Analysis funkciói alapján a költségeket több mező alapján elemezve használhatják. A félkövér mezők csak a partnerek által támogatott Microsoft-ügyfél-szerződésekre érvényesek.

| **Mező neve** | **Leírás** |
| --- | --- |
| invoiceId | Az adott tranzakcióhoz tartozó számlán látható FIÓKAZONOSÍTÓ. |
| previousInvoiceID | Az eredeti számlára való hivatkozás a visszatérítés (negatív díj). Csak akkor tölthető fel, ha visszatérítés van. |
| billingAccountName | A partnert képviselő számlázási fiók neve. A szolgáltatás minden olyan ügyfelet felszámít, akik Microsoft-ügyféli szerződéssel rendelkeznek, és azon CSP-ügyfelek, akik az SaaS-t, az Azure Marketplace-t és a foglalásokat tették elérhetővé. |
| billingAccountID | A partnert képviselő számlázási fiók azonosítója. |
| billingProfileID | A számlázási profil azonosítója, amely egyetlen számlázási pénznemben csoportosítja a számlákat a Microsoft ügyfél-szerződésben és azon CSP-ügyfeleken, akik az SaaS, az Azure Marketplace és a foglalások. |
| billingProfileName | Annak a számlázási profilnak a neve, amely egy számlázási pénznemben csoportosítja a számlákat egy olyan ügyfélen, akik Microsoft-ügyfél szerződéssel rendelkeznek, és azon CSP-ügyfelek számára, akik jogosultságokat vásároltak, például SaaS, Azure Marketplace és foglalások. |
| invoiceSectionName | A számlán felszámított projekt neve. Nem alkalmazható a partnerek által bekészített Microsoft-szerződésekre. |
| invoiceSectionID | A számlán felszámított projekt azonosítója. Nem alkalmazható a partnerek által bekészített Microsoft-szerződésekre. |
| **CustomerTenantID** | Az ügyfél&#39;-előfizetéshez tartozó Azure Active Directory bérlő azonosítója. |
| **Customername (** | Az ügyfél&#39;előfizetéséhez tartozó Azure Active Directory bérlő neve. |
| **CustomerTenantDomainName** | Az ügyfél&#39;-előfizetéshez tartozó Azure Active Directory bérlő tartományneve. |
| **PartnerTenantID** | A partner&#39;s Azure Active Directory bérlő azonosítója. |
| **PartnerName** | A partner Azure Active Directory bérlő neve. |
| **ResellerMPNID** | Az előfizetéshez társított viszonteladó MPNID. |
| costCenter | Az előfizetéshez társított költséghely. |
| billingPeriodStartDate | A számlázási időszak kezdő dátuma, ahogy az a számlán látható. |
| billingPeriodEndDate | A számlázási időszak befejezési dátuma, ahogy az a számlán látható. |
| servicePeriodStartDate | A minősítési időszak kezdő dátuma, ha a szolgáltatás kihasználtsága díjköteles. Az Azure-szolgáltatások díjszabását a minősítési időszakra vonatkozóan határozzák meg. |
| servicePeriodEndDate | Az időszak záró dátuma, amikor a szolgáltatás kihasználtsága díjköteles volt. Az Azure-szolgáltatások árai a minősítési időszak alapján vannak meghatározva. |
| dátum | Az Azure-beli felhasználással kapcsolatos adatok esetében a minősítési dátum a használati idő szerint jelenik meg. Fenntartott példány esetén a megvásárolt dátumot jeleníti meg. Az ismétlődő költségek és az egyszeri költségek, például a piactér és a támogatás esetében a vásárlás dátuma látható. |
| productID | Annak a terméknek az azonosítója, amely felszámított díjakat használ a felhasználás vagy a beszerzés alapján. Ez a termékkód és a SKuID összefűzött kulcsa, ahogy az a partner Centerben is látható. |
| product | Annak a terméknek a neve, amely a számlán látható, felhasználás vagy beszerzés alapján felhalmozott díjat tartalmaz. |
| serviceFamily | Megjeleníti a megvásárolt vagy felszámított termékhez tartozó szolgáltatási családot. Például tárolás vagy számítás. |
| productOrderID | Annak az eszköznek vagy az Azure-csomagnak az azonosítója, amelyhez az előfizetés tartozik. Például: Azure-csomag. |
| productOrderName | Annak az Azure-csomagnak a neve, amelyhez az előfizetés tartozik. Például: Azure-csomag. |
| consumedService | A régi EA-használati részletekben használt szolgáltatás (örökölt besorolás). |
| meterID | A mért használat mért azonosítója. |
| meterName | A mért felhasználáshoz tartozó fogyasztásmérő nevét azonosítja. |
| meterCategory | A legfelső szintű szolgáltatást azonosítja a használathoz. |
| meterSubCategory | Meghatározza az Azure-szolgáltatás típusát vagy alkategóriáját, amely hatással lehet a díjszabásra. |
| meterRegion | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ. |
| subscription ID | Egyedi Microsoft által generált azonosító az Azure-előfizetéshez. |
| subscriptionName | Az Azure-előfizetés neve. |
| Időtartam | Megjeleníti az ajánlat érvényességi időszakát. A fenntartott példányok például a fenntartott példány éves időszakában 12 hónapot jelenítenek meg. Egyszeri vásárlás vagy ismétlődő vásárlás esetén a kifejezés egy hónapot jelenít meg a SaaS, az Azure Marketplace és a support számára. Nem alkalmazható az Azure-beli felhasználásra. |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | A közzétevőt az első fél, harmadik féltől származó viszonteladó vagy harmadik féltől származó ügynökségként azonosító közzétevő típusa. |
| PartNumber | A nem használt fenntartott példány és az Azure Marketplace szolgáltatásainak cikkszám. |
| publisherName | A szolgáltatás közzétevője, beleértve a Microsoftot vagy a harmadik féltől származó közzétevőket. |
| reservationId | A fenntartott példányok vásárlásának azonosítója. |
| reservationName | A fenntartott példány neve. |
| reservationOrderId | A fenntartott példányhoz tartozó Rendeléskód. |
| frequency | Fenntartott példány fizetési gyakorisága. |
| resourceGroup | Az életciklus-erőforrások kezeléséhez használt Azure-erőforráscsoport neve. |
| instanceID (vagy) ResourceID | Az erőforrás-példány azonosítója. |
| resourceLocation | Az erőforrás helyének neve. |
| Hely | Az erőforrás normalizált helye. |
| effectivePrice | A szolgáltatás érvényes egységára, az árképzési pénznemben. A termék, a szolgáltatás családja, a mérő és az ajánlat egyedi. A számlázási fiókhoz tartozó árlista díjszabásával használható. Ha többértékű díjszabás vagy belefoglalt mennyiség szerepel, akkor a rendszer megjeleníti a kevert árat a felhasználáshoz. |
| Mennyiség | A megvásárolt vagy felhasznált mennyiség mért mennyisége. A számlázási időszak során felhasznált fogyasztásmérő mennyisége. |
| unitOfMeasure | Meghatározza azt az egységet, amelyben a szolgáltatás díjköteles. Például GB és óra. |
| pricingCurrency | Az egység árát meghatározó pénznem. |
| billingCurrency | A számlázott költségeket meghatározó pénznem |
| chargeType | Meghatározza, hogy a költségek milyen típusú díjat jelentenek Azure Cost Management például a vásárláshoz és a visszatérítéshez. |
| costinBillingCurrency | ExtendedCost vagy kevert díj a számlázott pénznemben megadott adózás előtt. |
| costinPricingCurrency | ExtendedCost vagy kevert költség az árképzési pénznemben, az árakkal való összekapcsoláshoz. |
| **costinUSD** | Becsült ExtendedCost vagy kevert költségek az adó USD-ben történő megkezdése előtt. |
| **paygCostInBillingCurrency** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. A számlázási pénznemben az utólagos elszámolású díjakat jeleníti meg. Csak RBAC hatóköröknél érhető el. |
| **paygCostInUSD** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. Az utólagos elszámolású árakat az USD értékben jeleníti meg. Csak RBAC hatóköröknél érhető el. |
| exchangeRate | Az árképzési pénznemről a számlázási pénznemre való áttéréshez használt árfolyam. |
| exchangeRateDate | Az a dátum, ameddig&#39;a díjszabás az árképzési pénznemről a számlázási pénznemre való áttéréshez használatos. |
| isAzureCreditEligible | Azt jelzi, hogy a költség jogosult-e az Azure-kreditek fizetésére. |
| serviceInfo1 | A nem kötelező szolgáltatás-specifikus metaadatokat rögzítő örökölt mező. |
| serviceInfo2 | A nem kötelező szolgáltatás-specifikus metaadatokat rögzítő örökölt mező. |
| additionalInfo | Szolgáltatásspecifikus metaadatok. Például egy virtuális gép rendszerképének típusa. |
| címkét | A mérőműszerhez hozzárendelt címke. Használjon címkéket a számlázási rekordok csoportosításához. A címkék használatával például a mérőszámot használó részleg a költségeket terjesztheti. |
| **partnerEarnedCreditRate** | A partneri rendszergazdai kapcsolat elérésén alapuló partneri kreditek (PEC) alapján alkalmazott kedvezmény mértéke. |
| **partnerEarnedCreditApplied** | Azt jelzi, hogy alkalmazva lett-e a partner által létrehozott kredit. |


## <a name="usage-data-update-frequency-varies"></a>A használati adatok frissítési gyakorisága eltérő

A Cost Managementban felmerülő használati adatok rendelkezésre állása néhány tényezőtől függ, többek között:

- Az Azure-szolgáltatások (például a Storage, a számítás, a CDN és az SQL) használatának gyakorisága.
- A használati adatoknak a minősítési motoron és a Cost Management-folyamatokon keresztüli feldolgozásához szükséges idő.

Egyes szolgáltatások gyakrabban bocsátanak ki használatot, mint mások. Így előfordulhat, hogy az egyes szolgáltatásokban az adatmennyiséget ritkábban kibocsátó más szolgáltatásokhoz tartozó Cost Management tartalmaz. A szolgáltatások használata általában 8-24 órát vesz igénybe Cost Management. Ne feledje, hogy a megnyitott hónapok adatait a rendszer akkor frissíti, amikor több használatba kerül, mert a frissítések kumulatívak.

## <a name="historical-data-might-not-match-invoice"></a>Előfordulhat, hogy az előzmények nem egyeznek a számlával

Előfordulhat, hogy a kredit-alapú és a díjköteles ajánlatok korábbi adatai nem felelnek meg a számlájának. Az Azure utólagos elszámolású, MSDN-és Visual Studio-ajánlatai az Azure-kreditekkel és a számlán való speciális fizetéssel is rendelkezhetnek. A Cost Managementban megjelenített korábbi adatok azonban csak a becsült fogyasztási díjakon alapulnak. Cost Management korábbi adatforgalom nem tartalmazza a befizetéseket és a krediteket. Ennek eredményeképpen előfordulhat, hogy a következő ajánlatokban megjelenített korábbi adatértékek nem egyeznek pontosan a számlával.

- Azure diákoknak (MS-AZR-0170P)
- Azure in Open licencprogram (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Ingyenes próbaverzió (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Lásd még:

- Ha még nem végezte el a Cost Management első gyors útmutatóját, olvassa el a [költségeket](quick-acm-cost-analysis.md).

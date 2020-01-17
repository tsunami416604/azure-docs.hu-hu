---
title: Ismerkedjen meg Azure Cost Management partnerekkel
description: Ez a cikk azt ismerteti, hogyan használják a partnerek a Azure Cost Management funkciókat, és hogyan teszik lehetővé Cost Management hozzáférését az ügyfelek számára.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: b337c1d57d253f55f3171e1de78a81b6de13ba31
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157155"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Ismerkedjen meg Azure Cost Management partnerekkel

A Azure Cost Management natív módon elérhető azon partnereink számára, akik Microsoft-ügyféli szerződés keretében vettek fel ügyfeleit, és [megvásároltak egy Azure-csomagot](/partner-center/purchase-azure-plan). Ez a cikk azt ismerteti, hogyan használják a partnerek a [Azure Cost Management](../index.yml) funkciókat az Azure-csomagbeli előfizetések költségeinek megtekintéséhez. Azt is leírja, hogy a partnerek hogyan engedélyezhetik Cost Management hozzáférését ügyfeleiknek. Az ügyfelek a CSP-partnerük által engedélyezett Cost Management funkciókat is használhatják.

A CSP-partnerek a következő Cost Management használják:

- Ismerje meg a számlázott költségeket, és rendelje hozzá a költségeket az ügyfélhez, az előfizetésekhez, az erőforrás-csoportokhoz és a szolgáltatásokhoz.
- Az Azure-költségek [elemzésével](quick-acm-cost-analysis.md) és az ügyfelek, az előfizetés, az erőforráscsoport, az erőforrás, a mérő, a szolgáltatás és számos más dimenzió között a költségek elemzése révén intuitív áttekintést kaphat az Azure költségeiről.
- Tekintse meg a Cost Analysis szolgáltatásban alkalmazott, a partner által szerzett Kredittel (PEC) rendelkező erőforrás-költségeket.
- Az értesítések és az automatizálás beállítása programozott [költségvetésekkel](tutorial-acm-create-budgets.md) és riasztásokkal, ha a költségek meghaladják a költségvetést.
- Engedélyezze a Azure Resource Manageri házirendet, amely hozzáférést biztosít az ügyfelek számára Cost Management-adathozzáféréshez. Az ügyfelek ezt követően megtekinthetik az előfizetésük használati költségeit az utólagos elszámolású [díjszabás](https://azure.microsoft.com/pricing/calculator/)alapján.
- Az utólagos elszámolású előfizetéssel exportálhatja a költségeket és a használati adatokat egy Storage-blobba.

Az alábbi példa az összes ügyfél költségeit mutatja be.
![példa az összes ügyfélre vonatkozó költségek megjelenítésére](./media/get-started-partners/customer-costs1.png)

Az alábbi példa egy adott ügyfél költségeit mutatja be.
![például egyetlen ügyfél költségeinek megjelenítése](./media/get-started-partners/customer-costs2.png)

A Azure Cost Managementban elérhető összes funkció REST API-kkal is elérhető. Az API-k segítségével automatizálhatja a Cost Management feladatait.

## <a name="prerequisites"></a>Előfeltételek

Partnerként a Azure Cost Management natív módon csak az Azure-csomaghoz tartozó előfizetések esetében érhető el.

Ha engedélyezni szeretné a Azure Cost Managementt a Azure Portalban, a Microsoft ügyfél-szerződésének (az ügyfél nevében) jóvá kell lennie az ügyfél elfogadását és az Azure-csomagra való áttérést. Csak az Azure-csomagba átadott előfizetések költségei érhetők el Azure Cost Managementban.

Azure Cost Management olvasási jogosultsággal kell rendelkeznie a számlázási fiókjához vagy előfizetéséhez.

A számlázási fiókok Azure Cost Managementhoz való hozzáférés engedélyezésével és hozzárendelésével kapcsolatos további információkért lásd: [felhasználói szerepkörök és engedélyek](/partner-center/permissions-overview)kiosztása. A **globális rendszergazda** és a **rendszergazdai ügynök** szerepkörei kezelhetik a számlázási fiókok költségeit.

Az előfizetési hatókörben lévő Azure Cost Management eléréséhez bármely RBAC-hozzáféréssel rendelkező felhasználó megtekintheti a kiskereskedelmi (utólagos elszámolású) díjszabás díját. Az ügyfél bérlője esetében azonban engedélyezni kell a Cost láthatósági házirendjét. A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>A Cost Management hatókörök használata

A hatókörök a számlázási információk kezelése, a kifizetésekhez tartozó szerepkörök, a számlák megtekintése és az általános fiókok kezelése. A számlázási és a fiók szerepkörei a RBAC-t használó erőforrás-kezeléshez használt hatóköröktől függetlenül kezelhetők. Annak érdekében, hogy egyértelműen megkülönböztesse a különálló hatókörök célját, beleértve a hozzáférés-vezérlési különbségeket is, ezeket számlázási hatóköröknek és RBAC-hatóköröknek nevezzük.

A számlázási hatókörök és a RBAC hatókörök, valamint a Cost Management és a hatókörök együttműködésének megismeréséhez tekintse meg a [hatókörök megismerése és használata](understand-work-scopes.md)című témakört.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Költségek kezelése a partner bérlői számlázási hatókörökkel

Az ügyfelek Microsoft-szerződésbe való bevezetését követően az alábbi _Számlázási hatókörök_ állnak rendelkezésre a bérlőben. A hatókörök használatával kezelheti a Cost Management költségeit.

### <a name="billing-account-scope"></a>Számlázási fiók hatóköre

A Számlázási fiók hatókörével megtekintheti az ügyfelek és a számlázási profilok adózás előtti költségeit. A számlázási költségek csak a Microsoft ügyfél-szerződésben szereplő, fogyasztáson alapuló termékek esetében jelennek meg. A számlázási költségek azonban a Microsoft ügyfél-szerződésben és a CSP-ajánlatban szereplő vásárlók számára is megjelennek a vásárolt termékeken. Jelenleg a hatókörben a költségek megtekintéséhez az alapértelmezett pénznem az USA dollár. A hatókörhöz beállított költségkeretek USD-ben is elérhetők.

A különböző ügyfelek által számlázott pénznemtől függetlenül a partnerek a Számlázási fiók hatókörét használják a költségvetések megadásához és a költségek kezeléséhez USD-ben az ügyfelek, előfizetések, erőforrások és erőforráscsoportok tekintetében.

A partnerek a Cost Analysis nézetben egy adott számlázási pénznemben is szűrhetik a költségeket. Válassza ki a **tényleges költségek** listáját a támogatott ügyfél számlázási pénznemek költségeinek megtekintéséhez.

![A pénznemek tényleges kiválasztását bemutató példa](./media/get-started-partners/actual-cost-selector.png)

A számlázási hatókörökben az elszámolt [költségek nézet](quick-acm-cost-analysis.md#customize-cost-views) használatával tekintheti meg a fenntartott példányok elszámolási díját a foglalási időszakon belül.

### <a name="billing-profile-scope"></a>Számlázási profil hatóköre

A számlázási profil hatókörével megtekintheti a számlázási pénznemben az összes ügyfélnél a számlán szereplő összes termékre és előfizetésre vonatkozó adózás előtti költségeket. A **InvoiceID** szűrő használatával szűrheti a költségeket egy adott számla számlázási profiljában. A szűrő egy adott számla fogyasztási és termék-vásárlási költségeit jeleníti meg. A számlán egy adott ügyfél költségeit is szűrheti az adózás előtti költségek megtekintéséhez.

Miután bevezette az ügyfeleket egy Microsoft-ügyfél szerződésbe, egy olyan számlát kap, amely tartalmazza az ügyfelek számára a Microsoft ügyfél-szerződésben szereplő összes termék (felhasználás, vásárlás és jogosultság) díját. Ha a számlázás ugyanabban a pénznemben történik, akkor ezek a számlák tartalmazzák a jogosultságok és a megvásárolt termékek, például az SaaS, az Azure Marketplace és az olyan ügyfelek foglalásait is, akik még szerepelnek a CSP-ajánlatban.

A számlázási profil hatóköre lehetővé teszi a díjak összeegyeztetését az ügyfél számláján, így megtekintheti az ügyfelek számláján felmerülő összes költséget. A számlához hasonlóan a hatókör az új Microsoft ügyfél-szerződésben szereplő összes ügyfél költségeit is megjeleníti. A hatókör a jelenlegi CSP-ajánlatban még mindig az ügyfél-jogosultsági termékek díját is megjeleníti.

A számlázási profil és a Számlázási fiók hatóköre az egyetlen érvényes hatókör, amely a jogosultsági és vásárlási alapú termékek, például az Azure Marketplace és a foglalások díjait jeleníti meg.

A számlázási profilok határozzák meg a számlán szereplő előfizetéseket. A számlázási profilok a nagyvállalati szerződés beléptetésének funkcionális megfelelői. A számlázási profil az a hatókör, amelyben a számlák jönnek létre.

Az ügyfél számlázási pénzneme jelenleg az alapértelmezett pénznem, amikor a számlázási profil hatókörében megtekinti a költségeket. A számlázási profil hatókörében beállított költségvetések a számlázási pénznemben vannak megadva.

A partnerek a hatókör használatával is összehangolják a számlákat. Továbbá a hatókör használatával határozzák meg a költségkereteket a számlázási pénznemben a következő elemek esetében:

- Adott szűrt számla
- Ügyfél
- Előfizetést
- Erőforráscsoport
- Erőforrás
- Azure-szolgáltatás
- Forgalmi díj
- ResellerMPNID

### <a name="customer-scope"></a>Ügyfél hatóköre

A partnerek a hatókör használatával kezelik a Microsoft ügyfél-szerződésbe bekészített ügyfelekhez kapcsolódó költségeket. A hatókör lehetővé teszi, hogy a partnerek megtekintsék az adózás előtti költségeket egy adott ügyfél esetében. Egy adott előfizetés, erőforráscsoport vagy erőforrás adózás előtti költségeit is szűrheti.

Az ügyfél hatóköre nem tartalmazza azokat az ügyfeleket, akik a jelenlegi CSP-ajánlatban szerepelnek. A hatókör csak a Microsoft ügyfél-szerződéssel rendelkező ügyfeleket tartalmazza. A jogosultsági költségek, nem az Azure-használat, az aktuális CSP-ajánlathoz tartozó ügyfelek a Számlázási fiók és a számlázási profil hatókörében érhetők el az ügyfél szűrő alkalmazása során.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partneri hozzáférés a számlázási hatókörökhöz Cost Management

Csak a **globális rendszergazdai** és **rendszergazdai ügynök** szerepkörökkel rendelkező felhasználók kezelhetik és tekinthetik meg a számlázási fiókok, a számlázási profilok és az ügyfelek költségeit közvetlenül a partner Azure-bérlője számára. A partner Center szerepköreivel kapcsolatos további információkért lásd: [felhasználói szerepkörök és engedélyek kiosztása](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>A Cost Management engedélyezése az ügyfél bérlője számára

A partnerek a Microsoft felhasználói szerződésbe való bevezetésük után engedélyezhetik Cost Management hozzáférését. A partnerek ezután engedélyezhetik a szabályzatot, amely lehetővé teszi, hogy az ügyfelek megtekintsék az utólagos elszámolású kiskereskedelmi díjszabás szerint kiszámított költségeiket. A költségek az ügyfél számlázási pénznemében jelennek meg a RBAC-előfizetés és az erőforráscsoportok hatókörén.

Ha a partner által engedélyezett a költségek láthatóságára vonatkozó házirend, az előfizetéshez Azure Resource Manager hozzáféréssel rendelkező felhasználók az utólagos elszámolású díjszabás keretében kezelhetik és elemezhetik a költségeket. Az Azure-előfizetésekhez megfelelő RBAC hozzáféréssel rendelkező viszonteladók és ügyfelek hatékonyan megtekinthetik a költségeket.

A szabályzattól függetlenül a partnerek is megtekinthetik a költségeket, ha hozzáférnek az előfizetéshez és az erőforráscsoporthoz.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Az Azure-használati díjak megtekintésének engedélyezése a szabályzat számára

A partnerek az alábbi információk segítségével teszik lehetővé a szabályzat számára az Azure-használati díjak megtekintését az ügyfelek számára.

A Azure Portal jelentkezzen be a partner bérlőbe, és válassza a **Cost Management + számlázás**lehetőséget. Válassza ki a számlázási fiókot, majd válassza az **ügyfelek**lehetőséget. Az ügyfelek listája a számlázási fiókhoz van társítva.

Az ügyfelek listájában válassza ki azt az ügyfelet, aki számára engedélyezni szeretné a költségek megtekintését.

![Ügyfelek kiválasztása Cost Management](./media/get-started-partners/customer-list.png)

A **Beállítások**területen válassza a **szabályzatok**lehetőséget.

A kiválasztott ügyfél előfizetéséhez tartozó **Azure-használati** díjak esetében az aktuális költség-láthatósági szabályzat jelenik meg.
![szabályzat, amely lehetővé teszi az ügyfelek számára az utólagos elszámolású díjak megtekintését](./media/get-started-partners/cost-management-billing-policies.png)

Ha a házirend **nem**értékre van állítva, Azure Cost Management nem érhető el az ügyfélhez társított előfizetési felhasználók számára. Hacsak a partner nem engedélyezte, a Cost láthatósági szabályzata alapértelmezés szerint le van tiltva az összes előfizetés felhasználója számára.

Ha a költségadatok értéke **Igen**, az ügyfél bérlője számára társított előfizetés-felhasználók a használati díjakat az utólagos elszámolású díjszabás szerint tekinthetik meg.

Ha a költségek láthatóságára vonatkozó házirend engedélyezve van, az előfizetés-használattal rendelkező összes szolgáltatás az utólagos elszámolású díjszabás szerint jár el. A foglalások használata a tényleges és az elszámolt költségekkel együtt nulla költséggel jelenik meg. A vásárlások és jogosultságok nem egy adott előfizetéshez vannak társítva. Így a vásárlások nem jelennek meg az előfizetés hatókörében.

Az ügyfél bérlője költségeinek megtekintéséhez nyissa meg Cost Management + számlázást, majd válassza a számlázási fiókok elemet. A számlázási fiókok listájában válassza ki a számlázási fiókot.

![Számlázási fiók kiválasztása](./media/get-started-partners/select-billing-account.png)

A **számlázás**területen válassza az **Azure-előfizetések**lehetőséget, majd válassza ki az ügyfelet.

![Válasszon ki egy Azure-előfizetési ügyfelet](./media/get-started-partners/subscriptions-select-customer.png)

Válassza a **Cost Analysis** lehetőséget, és tekintse meg a költségek áttekintését.
A Cost Analysis, a költségkeretek és a riasztások az előfizetés és az erőforráscsoport RBAC-hatókörök esetében az utólagos elszámolású díjszabás alapján érhetők el.

![A Cost Analysis megtekinthető ügyfélként ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Az elszámolt nézetek és a fenntartott példányok tényleges költségei a RBAC-hatókörökben nulla díjat jelenítenek meg. A fenntartott példányok költségei csak azokra a számlázási hatókörökre mutatnak, ahol a vásárlások történtek.

## <a name="analyze-costs-in-cost-analysis"></a>Költségek elemzése a Cost Analysis szolgáltatásban

A partner bérlő számlázási hatóköréhez hozzáféréssel rendelkező partnerek megvizsgálják és elemezhetik a számlázott költségeket egy adott ügyfélre vagy számlára vonatkozó költségek elemzése során. A [Cost Analysis](quick-acm-cost-analysis.md) nézetben a nézetek és az adatexportálás [CSV-vagy PNG-fájlokba](quick-acm-cost-analysis.md#automation-and-offline-analysis)is [menthetők](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) .

Az ügyfél bérlője előfizetéséhez hozzáféréssel rendelkező felhasználók RBAC a vásárlói bérlői előfizetések kiskereskedelmi költségeit, a nézetek mentését és az adatexportálást CSV-és PNG-fájlokban is.

A Cost Analysis szolgáltatásban a szűrés és csoportosítás funkció segítségével több mező alapján elemezheti a költségeket. A következő szakaszban a Partner-specifikus mezők láthatók.

## <a name="data-fields"></a>Adatmezők

A következő adatmezők találhatók a használati adatok fájljaiban és a Cost Management API-kon. Ahol elérhető, a partner centerrel egyenértékű információk jelennek meg. A következő félkövér mezők esetében a partnerek a Cost Analysis funkciói alapján a költségeket több mező alapján elemezve használhatják. A félkövér mezők csak a partnerek által támogatott Microsoft-ügyfél-szerződésekre érvényesek.

| **Mező neve** | **Leírás** | **A partner Center egyenértékű** |
| --- | --- | --- |
| invoiceId | Az adott tranzakcióhoz tartozó számlán látható FIÓKAZONOSÍTÓ. | A tranzakciót tartalmazó számla száma. |
| previousInvoiceID | Az eredeti számlára való hivatkozás a visszatérítés (negatív díj). Csak akkor tölthető fel, ha visszatérítés van. | – |
| billingAccountName | A partnert képviselő számlázási fiók neve. A szolgáltatás minden olyan ügyfelet felszámít, akik Microsoft-ügyféli szerződéssel rendelkeznek, és azon CSP-ügyfelek, akik az SaaS-t, az Azure Marketplace-t és a foglalásokat tették elérhetővé. | – |
| billingAccountID | A partnert képviselő számlázási fiók azonosítója. | A MCAPI partner kereskedelmi főtanúsítványának azonosítója. Egy kérelemben használatos, de nem szerepel a válaszban.|
| billingProfileID | A számlázási profil azonosítója, amely egyetlen számlázási pénznemben csoportosítja a számlákat a Microsoft ügyfél-szerződésben és azon CSP-ügyfeleken, akik az SaaS, az Azure Marketplace és a foglalások. | A MCAPI partner számlázási csoportjának azonosítója. Egy kérelemben használatos, de nem szerepel a válaszban. |
| billingProfileName | Annak a számlázási profilnak a neve, amely egy számlázási pénznemben csoportosítja a számlákat egy olyan ügyfélen, akik Microsoft-ügyfél szerződéssel rendelkeznek, és azon CSP-ügyfelek számára, akik jogosultságokat vásároltak, például SaaS, Azure Marketplace és foglalások. | – |
| invoiceSectionName | A számlán felszámított projekt neve. Nem alkalmazható a partnerek által bekészített Microsoft-szerződésekre. | – |
| invoiceSectionID | A számlán felszámított projekt azonosítója. Nem alkalmazható a partnerek által bekészített Microsoft-szerződésekre. | – |
| **CustomerTenantID** | Az ügyfél előfizetése Azure Active Directory bérlő azonosítója. | Ügyfél szervezeti azonosítója – az ügyfél Azure Active Directory TenantID. |
| **Customername (** | Az ügyfél előfizetéséhez tartozó Azure Active Directory bérlő neve. | Az ügyfél szervezetének neve, ahogy az a partner Centerben látható. Fontos: a számla egyeztetése a rendszeradatokkal. |
| **CustomerTenantDomainName** | Az ügyfél előfizetéséhez tartozó Azure Active Directory bérlő tartományneve. | Ügyfél Azure Active Directory bérlői tartomány. |
| **PartnerTenantID** | A partner Azure Active Directory bérlő azonosítója. | A partner-AZONOSÍTÓként hívott Azure Active Directory bérlő azonosítója GUID formátumban. |
| **PartnerName** | A partner Azure Active Directory bérlő neve. | Partner neve. |
| **ResellerMPNID** | Az előfizetéshez társított viszonteladó MPNID. | Az előfizetéshez tartozó, az előfizetéshez tartozó-viszonteladó MPN-azonosítója. Nem érhető el az aktuális tevékenységhez. |
| costCenter | Az előfizetéshez társított költséghely. | – |
| billingPeriodStartDate | A számlázási időszak kezdő dátuma, ahogy az a számlán látható. | – |
| billingPeriodEndDate | A számlázási időszak befejezési dátuma, ahogy az a számlán látható. | – |
| servicePeriodStartDate | A minősítési időszak kezdő dátuma, ha a szolgáltatás kihasználtsága díjköteles. Az Azure-szolgáltatások díjszabását a minősítési időszakra vonatkozóan határozzák meg. | ChargeStartDate a partner Centerben. A számlázási ciklus kezdő dátuma, kivéve, ha az előző számlázási ciklusból korábban nem feltöltött látens használati adatokat jelenít meg. Az idő mindig a nap kezdete, 0:00. |
| servicePeriodEndDate | Az időszak záró dátuma, amikor a szolgáltatás kihasználtsága díjköteles volt. Az Azure-szolgáltatások árai a minősítési időszak alapján vannak meghatározva. | – |
| dátum | Az Azure-beli felhasználással kapcsolatos adatok esetében a minősítési dátum a használati idő szerint jelenik meg. Fenntartott példány esetén a megvásárolt dátumot jeleníti meg. Az ismétlődő költségek és az egyszeri költségek, például a piactér és a támogatás esetében a vásárlás dátuma látható. | – |
| productID | Annak a terméknek az azonosítója, amely felszámított díjakat használ a felhasználás vagy a beszerzés alapján. Ez a termékkód és a SKuID összefűzött kulcsa, ahogy az a partner Centerben is látható. | A termék azonosítója. |
| product | Annak a terméknek a neve, amely a számlán látható, felhasználás vagy beszerzés alapján felhalmozott díjat tartalmaz. | A termék neve a katalógusban. |
| serviceFamily | Megjeleníti a megvásárolt vagy felszámított termékhez tartozó szolgáltatási családot. Például tárolás vagy számítás. | – |
| productOrderID | Annak az eszköznek vagy az Azure-csomagnak az azonosítója, amelyhez az előfizetés tartozik. Például: Azure-csomag. | – |
| productOrderName | Annak az Azure-csomagnak a neve, amelyhez az előfizetés tartozik. Például: Azure-csomag. | –|
| consumedService | A régi EA-használati részletekben használt szolgáltatás (örökölt besorolás). | A szolgáltatás a partner Centerben látható. Például: Microsoft. Storage, Microsoft. számítás és Microsoft. operationalinsights. |
| meterID | A mért használat mért azonosítója. | A felhasznált fogyasztásmérő azonosítója. |
| meterName | A mért felhasználáshoz tartozó fogyasztásmérő nevét azonosítja. | A felhasznált fogyasztásmérő neve. |
| meterCategory | A legfelső szintű szolgáltatást azonosítja a használathoz. | A legfelső szintű szolgáltatás a használathoz. |
| meterSubCategory | Meghatározza az Azure-szolgáltatás típusát vagy alkategóriáját, amely hatással lehet a díjszabásra. | Az Azure-szolgáltatás típusa, amely hatással lehet a díjszabásra.|
| meterRegion | Az igénybe vett vagy üzemeltető adatközpont elhelyezkedése, ha a szolgáltatás díjszabása az adatközpontok elhelyezkedésétől is függ. | A szolgáltatások adatközpontjának regionális helye, ahol alkalmazható és feltölthető. |
| subscription ID | Egyedi Microsoft által generált azonosító az Azure-előfizetéshez. | – |
| subscriptionName | Az Azure-előfizetés neve. | – |
| Időtartam | Megjeleníti az ajánlat érvényességi időszakát. A fenntartott példányok például a fenntartott példány éves időszakában 12 hónapot jelenítenek meg. Egyszeri vásárlás vagy ismétlődő vásárlás esetén a kifejezés egy hónapot jelenít meg a SaaS, az Azure Marketplace és a support számára. Nem alkalmazható az Azure-beli felhasználásra. | – |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | A közzétevőt az első fél, harmadik féltől származó viszonteladó vagy harmadik féltől származó ügynökségként azonosító közzétevő típusa. | – |
| partNumber | A nem használt fenntartott példány és az Azure Marketplace szolgáltatásainak cikkszám. | – |
| publisherName | A szolgáltatás közzétevője, beleértve a Microsoftot vagy a harmadik féltől származó közzétevőket. | A termék közzétevője neve.|
| reservationId | A fenntartott példányok vásárlásának azonosítója. | – |
| reservationName | A fenntartott példány neve. | – |
| reservationOrderId | A fenntartott példányhoz tartozó Rendeléskód. | – |
| frequency | Fenntartott példány fizetési gyakorisága. | – |
| resourceGroup | Az életciklus-erőforrások kezeléséhez használt Azure-erőforráscsoport neve. | Az erőforráscsoport neve. |
| instanceID (vagy) ResourceID | Az erőforrás-példány azonosítója. | A teljes erőforrás-tulajdonságokat tartalmazó ResourceURI jelenik meg. |
| resourceLocation | Az erőforrás helyének neve. | Az erőforrás helye. |
| Földrajzi egység | Az erőforrás normalizált helye. | – |
| effectivePrice | A szolgáltatás érvényes egységára, az árképzési pénznemben. A termék, a szolgáltatás családja, a mérő és az ajánlat egyedi. A számlázási fiókhoz tartozó árlista díjszabásával használható. Ha többértékű díjszabás vagy belefoglalt mennyiség szerepel, akkor a rendszer megjeleníti a kevert árat a felhasználáshoz. | Az egység ára a korrekciók után. |
| Mennyiség | A megvásárolt vagy felhasznált mennyiség mért mennyisége. A számlázási időszak során felhasznált fogyasztásmérő mennyisége. | Egységek száma Egyeztetés közben ellenőrizze, hogy az megfelel-e a számlázási rendszeren található információknak. |
| unitOfMeasure | Meghatározza azt az egységet, amelyben a szolgáltatás díjköteles. Például GB és óra. | Meghatározza azt az egységet, amelyben a szolgáltatás díjköteles. Például: GB, óra és 10 000 s. |
| pricingCurrency | Az egység árát meghatározó pénznem. | A pénznem a árlista listában.|
| billingCurrency | A számlázott költségeket meghatározó pénznem. | Az ügyfél földrajzi régiójának pénzneme. |
| chargeType | Meghatározza, hogy a költségek milyen típusú díjat jelentenek Azure Cost Management például a vásárláshoz és a visszatérítéshez. | A díj vagy a beállítás típusa. Nem érhető el az aktuális tevékenységhez. |
| costinBillingCurrency | ExtendedCost vagy kevert díj a számlázott pénznemben megadott adózás előtt. | – |
| costinPricingCurrency | ExtendedCost vagy kevert költség az árképzési pénznemben, az árakkal való összekapcsoláshoz. | – |
| **costinUSD** | Becsült ExtendedCost vagy kevert költségek az adó USD-ben történő megkezdése előtt. | – |
| **paygCostInBillingCurrency** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. A számlázási pénznemben az utólagos elszámolású díjakat jeleníti meg. Csak RBAC hatóköröknél érhető el. | – |
| **paygCostInUSD** | Megjeleníti a költségeket, ha a díjszabás kiskereskedelmi áron történik. Az utólagos elszámolású árakat az USD értékben jeleníti meg. Csak RBAC hatóköröknél érhető el. | – |
| exchangeRate | Az árképzési pénznemről a számlázási pénznemre való áttéréshez használt árfolyam. | A partner Centerben a PCToBCExchangeRate néven is ismert. A díjszabási pénznem a számlázási valuta árfolyama alapján.|
| exchangeRateDate | Az árképzési pénznemről a számlázási pénznemre való áttéréshez használt Exchange-díj dátuma. | A partner Centerben a PCToBCExchangeRateDat néven is ismert. Az árképzési valuta árfolyama|
| isAzureCreditEligible | Azt jelzi, hogy a költség jogosult-e az Azure-kreditek fizetésére. | – |
| serviceInfo1 | A nem kötelező szolgáltatás-specifikus metaadatokat rögzítő örökölt mező. | Belső Azure-szolgáltatás metaadatainak. |
| serviceInfo2 | A nem kötelező szolgáltatás-specifikus metaadatokat rögzítő örökölt mező. | Szolgáltatás adatai. Például egy virtuális gép és az INTERNETSZOLGÁLTATÓ neve ExpressRoute.|
| additionalInfo | Szolgáltatásspecifikus metaadatok. Például egy virtuális gép rendszerképének típusa. | A többi oszlopban nem szereplő további információk. A szolgáltatásra vonatkozó metaadatok. Például egy virtuális gép rendszerképének típusa.|
| tags | A mérőműszerhez hozzárendelt címke. Használjon címkéket a számlázási rekordok csoportosításához. A címkék használatával például a mérőszámot használó részleg a költségeket terjesztheti. | Az ügyfél által hozzáadott címkék.|
| **partnerEarnedCreditRate** | A partneri rendszergazdai kapcsolat elérésén alapuló partneri kreditek (PEC) alapján alkalmazott kedvezmény mértéke. | A partner által létrehozott kreditek (PEC) aránya. Például: 0% vagy 15%. |
| **partnerEarnedCreditApplied** | Azt jelzi, hogy alkalmazva lett-e a partner által létrehozott kredit. | – |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Partner által létrehozott kredit (PEC) erőforrás-költségek megtekintése

Azure Cost Management a partnerek a Cost Analysis szolgáltatással tekinthetik meg a PEC-előnyöket kapott költségeket.

A Azure Portal jelentkezzen be a partner bérlőbe, és válassza a **Cost Management + számlázás**lehetőséget. A **Cost Management**területen válassza a **Cost Analysis**elemet.

A Cost Analysis nézet a partner számlázási fiókjának költségeit jeleníti meg. A számlák egyeztetéséhez válassza ki a partner, egy adott ügyfél vagy egy számlázási profil szükséges **hatókörét** .

A fánkos diagramon válassza ki a legördülő listát, és válassza a **PartnerEarnedCreditApplied** lehetőséget a PEC-költségek részletezéséhez.

![Példa a partner által létrehozott kreditek megtekintésére](./media/get-started-partners/cost-analysis-pec1.png)

Ha a **PartnerEarnedCreditApplied** tulajdonság értéke _true (igaz_), a kapcsolódó díj a partner által létrehozott rendszergazdai hozzáférés előnyeit élvezi.

Ha a **PartnerEarnedCreditApplied** tulajdonság értéke _false (hamis_), a kapcsolódó díj nem felelt meg a jóváíráshoz szükséges jogosultságoknak. Vagy a megvásárolt szolgáltatás nem jogosult partner által létrehozott kreditre.

A szolgáltatás használati adatait általában 8-24 óra alatt kell megjeleníteni Cost Managementban. További információ: a [használati adatok frissítési gyakorisága változó](understand-cost-mgt-data.md#usage-data-update-frequency-varies). A PEC-kreditek 48 órán belül megjelennek Azure Cost Managementban való hozzáférés időpontjában.


A **PartnerEarnedCreditApplied** tulajdonság a **csoportosítási** lehetőségek alapján is csoportosíthatja és szűrheti is. A beállítások segítségével megvizsgálhatja a PEC-t és a-t nem tartalmazó költségeket.

![Csoport vagy szűrés partner által létrehozott kredit alapján](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Költségadatok exportálása az Azure Storage szolgáltatásba

A partner bérlői számlázási hatókörökhöz hozzáféréssel rendelkező partnerek a költségeket és a használati adatokat egy Azure Storage-blobba is exportálhatunk. A blobnak olyan előfizetésben kell lennie a partner bérlőn, amely nem megosztott szolgáltatás előfizetése vagy az ügyfél előfizetése. A költségadatok exportálásának engedélyezéséhez ajánlott egy független utólagos elszámolású előfizetést beállítani a partner bérlőben az exportált költségadatok üzemeltetéséhez. Az export Storage-fiók az utólagos elszámolású előfizetésben üzemeltetett Azure Storage-blobon jön létre. Azon hatókör alapján, ahol a partner létrehozta az exportálást, a rendszer ismétlődő módon automatikusan exportálja a kapcsolódó adatmennyiséget a Storage-fiókba.

Az előfizetéshez RBAC hozzáféréssel rendelkező felhasználók a költségadatok exportálását egy Azure Storage-blobba is exportálhatja, amelyet az ügyfél bérlője bármely előfizetésében üzemeltet.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Exportálás létrehozása partner bérlő vagy ügyfél bérlője számára

A Azure Portal jelentkezzen be a partner bérlő vagy az ügyfél bérlőbe, és válassza a **Cost Management + számlázás**lehetőséget. Válasszon ki egy megfelelő hatókört, például egy számlázási fiókot, majd válassza a **Cost Analysis**lehetőséget. Amikor az oldal betöltődik, válassza az **Exportálás**lehetőséget. válassza **az összes exportálás megtekintése** az ütemterv exportálása alatt lehetőséget.

![Válassza az Exportálás lehetőséget, és tekintse meg az összes exportálást](./media/get-started-partners/export01.png)

Ezután válassza a **Hozzáadás** elemet, és írja be a nevet, és válassza ki az Exportálás típusát. Válassza a **Storage (tárolás** ) fület, és adja meg a szükséges adatokat.

![Új Exportálás hozzáadása és a tár kiválasztása lap](./media/get-started-partners/export02.png)

Amikor létrehoz egy exportálást a partner bérlőben, válassza az utólagos elszámolású előfizetést a partner bérlőben. Hozzon létre egy Azure Storage-fiókot az előfizetés használatával.

Az ügyfél bérlője RBAC felhasználói számára válasszon ki egy előfizetést az ügyfél bérlője számára. Hozzon létre egy Azure Storage-fiókot az előfizetés használatával.

Tekintse át a tartalmat, majd válassza a **Létrehozás** lehetőséget az exportáláshoz.

Az exportálási listán szereplő adatellenőrzéshez válassza ki a Storage-fiók nevét. A Storage-fiók lapon válassza a **tárolók** lehetőséget, majd válassza ki a tárolót. Navigáljon a megfelelő mappához, és válassza ki a CSV-fájlt. Válassza a **Letöltés** lehetőséget a CSV-fájl beszerzéséhez és a megnyitásához. Az exportált adatok a Azure Portal a használati adatokhoz hasonló költségeket hasonlítanak.

![Exportált adatmennyiség – példa](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API-k

A partnerek és az ügyfelek az alábbi szakaszokban ismertetett Cost Management API-kat használhatják a gyakori feladatokhoz.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API-k – közvetlen és közvetett szolgáltatók

A partner bérlői számlázási hatókörökhöz hozzáféréssel rendelkező partnerek a következő API-kkal tekinthetik meg a számlázott költségeket.

Az előfizetések hatókörében lévő API-k meghívhatók partnertől függetlenül, ha hozzáféréssel rendelkeznek az előfizetéshez. Az előfizetéshez hozzáférő más felhasználók (például az ügyfél vagy a viszonteladó) csak akkor hívhatják meg az API-kat, ha a partner engedélyezi az ügyfél bérlője számára a díjszabási szabályzatot.


#### <a name="to-get-a-list-of-billing-accounts"></a>Számlázási fiókok listájának beolvasása

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Ügyfelek listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Az előfizetések listájának lekérése

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Számlák listájának lekérése egy adott időszakra

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

Az API-hívás olyan számlák tömbjét adja vissza, amelyek a következő JSON-kódhoz hasonló elemekből állnak.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Használja az előző visszaadott azonosító mező értékét, és cserélje le a következő példában a használati adatok lekérdezési hatókörére.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

A példa az adott számlához társított használati rekordokat adja vissza.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>A szabályzat lekérése az ügyfelek számára a költségek megtekintéséhez

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>A szabályzat beállítása az ügyfelek számára a költségek megtekintéséhez

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Az Azure-szolgáltatás használati számlázási fiókhoz való beszerzése

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Ügyfél Azure-szolgáltatásbeli használatának letöltése

A következő Get hívás egy aszinkron művelet.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Hívja meg a válaszban visszaadott `Location` URI-t a művelet állapotának vizsgálatához. Az állapot *befejezése után*a `downloadUrl` tulajdonság egy hivatkozást tartalmaz, amely a generált jelentés letöltésére használható.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>A felhasznált Azure-szolgáltatások árlista beszerzése vagy letöltése

Először használja a következő bejegyzést.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Ezután hívja meg az aszinkron művelet tulajdonság értékét. Példa:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
Az előző Get hívás az árlistát tartalmazó letöltési hivatkozást adja vissza.


#### <a name="to-get-aggregated-costs"></a>Összesített költségek beszerzése

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Költségvetés létrehozása partner számára

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Költségvetés létrehozása az ügyfél számára

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Költségvetés törlése

```
PUT
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Következő lépések
- A [költségek elemzésének Megkezdése](quick-acm-cost-analysis.md) Cost Management
- [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) Cost Management

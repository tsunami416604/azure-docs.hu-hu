---
title: Ismerkedjen meg Azure Cost Management partnerekkel
description: Ez a cikk azt ismerteti, hogyan használják a partnerek a Azure Cost Management funkciókat, és hogyan teszik lehetővé Cost Management hozzáférését az ügyfelek számára.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 9d95e23cf92c7ee98291831d60088d610c3e5c52
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377693"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Ismerkedjen meg Azure Cost Management partnerekkel

A Azure Cost Management natív módon elérhető azon partnereink számára, akik Microsoft-ügyfél szerződésbe vettek be ügyfeleiket. Ez a cikk azt ismerteti, hogyan használják a partnerek a [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) funkciókat. Azt is leírja, hogy a partnerek hogyan engedélyezhetik Cost Management hozzáférését ügyfeleiknek. A CSP-ügyfelek a CSP-partnerük által engedélyezett Cost Management funkciókat is használhatják.

A CSP-partnerek a következő Cost Management használják:

- Ismerje meg a számlázott költségeket, és rendelje hozzá a költségeket az ügyfélhez, az előfizetésekhez, az erőforrás-csoportokhoz és a szolgáltatásokhoz.
- Az Azure-költségek [elemzésével](quick-acm-cost-analysis.md) és az ügyfelek, az előfizetés, az erőforráscsoport, az erőforrás, a mérő, a szolgáltatás és számos más dimenzió között a költségek elemzése révén intuitív áttekintést kaphat az Azure költségeiről.
- Tekintse meg a Cost Analysis szolgáltatásban alkalmazott, a partner által szerzett Kredittel (PEC) rendelkező erőforrás-költségeket.
- Az értesítések és az automatizálás beállítása programozott [költségvetésekkel](tutorial-acm-create-budgets.md) és riasztásokkal, ha a költségek meghaladják a költségvetést.
- Engedélyezze a Azure Resource Manageri házirendet, amely hozzáférést biztosít az ügyfelek számára Cost Management-adathozzáféréshez. Az ügyfelek ezt követően megtekinthetik az előfizetésük használati költségeit az utólagos elszámolású [díjszabás](https://azure.microsoft.com/pricing/calculator/)alapján.

A Azure Cost Managementban elérhető összes funkció REST API-kkal is elérhető. Az API-k segítségével automatizálhatja a Cost Management feladatait.

## <a name="prerequisites"></a>Előfeltételek

Azure Cost Management olvasási jogosultsággal kell rendelkeznie a számlázási fiókjához vagy előfizetéséhez. A hozzáférés bármely szinten megadható az erőforrások felett, a számlázási fióktól vagy egy felügyeleti csoporttól egészen az egyes erőforráscsoportokig, ahol kezelheti az alkalmazásokat. Ahhoz, hogy az előfizetéshez tartozó felhasználók megtekintsék a díjszabást és a költségeket, engedélyezni kell a hozzáférési díjakat a számlázási fiókjához. További információ a Azure Cost Management való hozzáférés engedélyezéséről és hozzárendeléséről: az [adatokhoz való hozzáférés kiosztása](assign-access-acm-data.md). A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>A Cost Management hatókörök használata

A hatókörök a számlázási információk kezelése, a kifizetésekhez tartozó szerepkörök, a számlák megtekintése és az általános fiókok kezelése. A számlázási és a fiók szerepkörei a RBAC-t használó erőforrás-kezeléshez használt hatóköröktől függetlenül kezelhetők. Annak érdekében, hogy egyértelműen megkülönböztesse a különálló hatókörök célját, beleértve a hozzáférés-vezérlési különbségeket is, ezeket számlázási hatóköröknek és RBAC-hatóköröknek nevezzük.

A számlázási hatókörök és a RBAC hatókörök, valamint a Cost Management és a hatókörök együttműködésének megismeréséhez tekintse meg a [hatókörök megismerése és használata](understand-work-scopes.md)című témakört.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Költségek kezelése a partner bérlői számlázási hatókörökkel

Az ügyfelek Microsoft-szerződésbe való bevezetését követően az alábbi _Számlázási hatókörök_ állnak rendelkezésre a bérlőben. A hatókörök használatával kezelheti a Cost Management költségeit.

### <a name="billing-account-scope"></a>Számlázási fiók hatóköre

A Számlázási fiók hatókörével megtekintheti az ügyfelek és a számlázási profilok adózás előtti költségeit. A számlázási költségeket a Microsoft ügyfél-szerződésben szereplő ügyfelek számára is megtekintheti a fogyasztáson alapuló termékeknél. A számlázási költségeket a Microsoft ügyfél-szerződésben és a CSP-ajánlatban található ügyfelek számára vásárolt termékek is mutatják. Jelenleg a hatókörben a költségek megtekintéséhez az alapértelmezett pénznem az USA dollár. A hatókörhöz beállított költségkeretek USD-ben is elérhetők.

Az ügyfél által számlázott pénznemtől függetlenül a partnerek az ügyfelek, előfizetések, erőforrások és erőforráscsoportok esetében a hatókör segítségével állíthatják be a költségvetéseket és kezelhetik a költségeket USD-ben.

A partnerek a Cost Analysis nézetben egy adott számlázási pénznemben is szűrhetik a költségeket. Válassza ki a **tényleges költségek** listáját a támogatott ügyfél számlázási pénznemek költségeinek megtekintéséhez.

![A pénznemek tényleges kiválasztását bemutató példa](./media/get-started-partners/actual-cost-selector.png)

A számlázási hatókörökben az elszámolt [költségek nézet](quick-acm-cost-analysis.md#customize-cost-views) használatával tekintheti meg a fenntartott példányok elszámolási díját a foglalási időszakon belül.

### <a name="billing-profile-scope"></a>Számlázási profil hatóköre

A számlázási profil hatókörével megtekintheti a számlázási pénznemben az összes ügyfélnél a számlán szereplő összes termékre és előfizetésre vonatkozó adózás előtti költségeket. A **InvoiceID** szűrő használatával szűrheti a költségeket egy adott számla számlázási profiljában. A szűrő egy adott számla fogyasztási és termék-vásárlási költségeit jeleníti meg. A számlán egy adott ügyfél költségeit is szűrheti az adózás előtti költségek megtekintéséhez.

Miután bevezette az ügyfeleket egy Microsoft-ügyfél szerződésbe, egy ügyfél-számla jelenik meg, amely a jogosultságok és a megvásárolt termékek, például az SaaS, az Azure Marketplace és a foglalások díjait jeleníti meg. Ha a számlázás ugyanabban a számlázási pénznemben történik, akkor a számla az új Microsoft ügyfél-szerződésben nem szereplő ügyfelek díját is megjeleníti.

A számlázási profil hatóköre lehetővé teszi a díjak összeegyeztetését az ügyfél számláján, így megtekintheti az ügyfelek számláján felmerülő összes költséget. A számlához hasonlóan a hatókör az új Microsoft ügyfél-szerződésben szereplő összes ügyfél költségeit is megjeleníti. A hatókör a jelenlegi CSP-ajánlatban még mindig az ügyfél-jogosultsági termékek díját is megjeleníti.

A számlázási profil és a Számlázási fiók hatóköre az egyetlen, amely a jogosultsági és a vásárlási alapú termékek díját mutatja.

A számlázási profilok határozzák meg a számlán szereplő előfizetéseket. A számlázási profilok a nagyvállalati szerződés beléptetésének funkcionális megfelelői. A regisztráció az a hatókör, amelyet a számlák generálnak. Hasonlóképpen, a nem használaton alapuló, például az Azure Marketplace és a Reservations vásárlások csak a számlázási profil hatókörében érhetők el.

Az ügyfél számlázási pénzneme jelenleg az alapértelmezett pénznem, amikor a számlázási profil hatókörében megtekinti a költségeket. A számlázási profil hatókörében beállított költségvetések számlázási pénznemben vannak megadva.

A partnerek a hatókör használatával is összehangolják a számlákat. Továbbá a hatókör használatával határozzák meg a költségvetést a számlázási pénznemben a következőhöz:

- Adott szűrt számla
- Ügyfél
- Előfizetés
- Erőforráscsoport
- Erőforrás
- Azure-szolgáltatás
- Forgalmi díj
- ResellerMPNID

### <a name="customer-scope"></a>Ügyfél hatóköre

A partnerek a hatókör használatával kezelik a Microsoft ügyfél-szerződésbe bekészített ügyfelekhez kapcsolódó költségeket. A hatókör lehetővé teszi, hogy a partnerek megtekintsék az adózás előtti költségeket egy adott ügyfél esetében. Egy adott előfizetés, erőforráscsoport vagy erőforrás adózás előtti költségeit is szűrheti.

Az ügyfél hatóköre nem tartalmazza azokat az ügyfeleket, akik a jelenlegi CSP-ajánlatban szerepelnek. A jogosultsági költségek, nem az Azure-használat, az aktuális CSP-ajánlathoz tartozó ügyfelek a Számlázási fiók és a számlázási profil hatókörében érhetők el az ügyfél szűrő alkalmazása során.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partneri hozzáférés a számlázási hatókörökhöz Cost Management

Csak a **globális rendszergazdai** és **rendszergazdai ügynök** szerepkörökkel rendelkező felhasználók kezelhetik és tekinthetik meg a számlázási fiókok, a számlázási profilok és az ügyfelek költségeit közvetlenül a partner Azure-bérlője számára. A partner Center szerepköreivel kapcsolatos további információkért lásd: [felhasználói szerepkörök és engedélyek kiosztása](/partner-center/permissions-overview).

### <a name="enable-cost-management-in-the-customer-tenant"></a>A Cost Management engedélyezése az ügyfél bérlője számára

A partnerek a Microsoft felhasználói szerződésbe való bevezetésük után engedélyezhetik Cost Management hozzáférését. A partnerek ezután engedélyezhetik a szabályzatot, amely lehetővé teszi, hogy az ügyfelek megtekintsék az utólagos elszámolású kiskereskedelmi díjszabás szerint kiszámított költségeiket. A költségek az ügyfél számlázási pénznemében jelennek meg a RBAC-előfizetés és az erőforráscsoportok hatókörén.

Ha a partner által engedélyezett a költségek láthatóságára vonatkozó házirend, az előfizetéshez Azure Resource Manager hozzáféréssel rendelkező felhasználók az utólagos elszámolású díjszabás keretében kezelhetik és elemezhetik a költségeket. Az Azure-előfizetésekhez megfelelő RBAC hozzáféréssel rendelkező viszonteladók és ügyfelek hatékonyan megtekinthetik a költségeket.

A szabályzattól függetlenül a partnerek is megtekinthetik a költségeket, ha hozzáférnek az előfizetéshez és az erőforráscsoporthoz.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Az Azure-használati díjak megtekintésének engedélyezése a szabályzat számára

A partnerek az alábbi információk segítségével teszik lehetővé a szabályzat számára az Azure-használati díjak megtekintését az ügyfelek számára.

A Azure Portal jelentkezzen be a partner bérlőbe, és kattintson a **Cost Management + számlázás**elemre. Válasszon számlázási fiókot, majd kattintson az **ügyfelek**elemre. Az ügyfelek listája a számlázási fiókhoz van társítva.

Az ügyfelek listájában válassza ki azt az ügyfelet, aki számára engedélyezni szeretné a költségek megtekintését.

![Ügyfelek kiválasztása Cost Management](./media/get-started-partners/customer-list.png)

A **Beállítások**területen kattintson a **házirendek**elemre.

A kiválasztott ügyfél előfizetéséhez tartozó **Azure-használati** díjak esetében az aktuális költség-láthatósági szabályzat jelenik meg.
![Policy, amely lehetővé teszi, hogy az ügyfelek megtekintsék az utólagos elszámolású díjakat @ no__t-1

Ha a házirend **nem**értékre van állítva, Azure Cost Management nem érhető el az ügyfélhez társított előfizetési felhasználók számára. Hacsak a partner nem engedélyezte, a Cost láthatósági szabályzata alapértelmezés szerint le van tiltva az összes előfizetés felhasználója számára.

Ha a költségadatok értéke **Igen**, az ügyfél bérlője számára társított előfizetés-felhasználók a használati díjakat az utólagos elszámolású díjszabás szerint tekinthetik meg.

Ha a költségek láthatóságára vonatkozó házirend engedélyezve van, az előfizetés-használattal rendelkező összes szolgáltatás az utólagos elszámolású díjszabás szerint jár el. A foglalások használata a tényleges és az elszámolt költségekkel együtt nulla költséggel jelenik meg. A vásárlások és jogosultságok nem egy adott előfizetéshez vannak társítva. Így a vásárlások nem jelennek meg az előfizetés hatókörében.

Az ügyfél bérlője költségeinek megtekintéséhez nyissa meg Cost Management + számlázást, majd kattintson a számlázási fiókok elemre. A számlázási fiókok listájában kattintson egy számlázási fiókra.

![Számlázási fiók kiválasztása](./media/get-started-partners/select-billing-account.png)

A **számlázás**alatt kattintson az **Azure-előfizetések**elemre, majd kattintson a megfelelő ügyfélre.

![Válasszon ki egy Azure-előfizetési ügyfelet](./media/get-started-partners/subscriptions-select-customer.png)

Kattintson a **költségek elemzése** lehetőségre, és tekintse meg a költségek áttekintését.
A Cost Analysis, a költségkeretek és a riasztások mostantól elérhetők az előfizetés és az erőforráscsoport RBAC-hatókörökhöz az utólagos elszámolású díjszabás alapján.

![A Cost Analysis megtekinthető ügyfélként ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

Az elszámolt nézetek és a fenntartott példányok tényleges költségei a RBAC-hatókörökben nulla díjat jelenítenek meg. A fenntartott példányok költségei csak azokra a számlázási hatókörökre mutatnak, ahol a vásárlások történtek.

## <a name="analyze-costs-in-cost-analysis"></a>Költségek elemzése a Cost Analysis szolgáltatásban

A partnerek megvizsgálják és elemezhetik a költségeket a költségek elemzéséhez az ügyfeleken egy adott ügyfélhez vagy egy számlához. A szűrési és csoportosítási funkciók lehetővé teszik a költségek több mező alapján történő elemzését, beleértve a következőket:

| **Mező** | **Leírás** | **Egyenértékű oszlop a partner Centerben** |
| --- | --- | --- |
| PartnerTenantID | A partner Azure Active Directory bérlő azonosítója | A partner Azure Active Directory TenantID hívják. GUID formátumban. |
| PartnerName | A partner Azure Active Directory bérlő neve | Partner neve |
| CustomerTenantID | Az ügyfél előfizetése Azure Active Directory bérlő azonosítója | Az ügyfél szervezeti azonosítója. Például az ügyfél Azure Active Directory TenantID. |
| Customername ( | Az ügyfél előfizetését tartalmazó Azure Active Directory bérlő neve | Az ügyfél szervezetének neve, ahogy az a partner Centerben szerepel. Fontos: a számla egyeztetése a rendszeradatokkal. |
| ResellerMPNID | Az előfizetéshez társított viszonteladó MPNID | Az előfizetéshez tartozó rekord viszonteladójának MPN-azonosítója. Nem érhető el az aktuális tevékenységhez. |
| subscription ID | Az Azure-előfizetés egyedi, Microsoft által generált azonosítója | – |
| subscriptionName | Az Azure-előfizetés neve | – |
| billingProfileID | A számlázási profil azonosítója. Egyetlen számlázási pénznemben csoportosítja a számlákat a különböző ügyfeleken. | A MCAPI partner számlázási csoportjának azonosítója. API-kérelmekben használatos, de a válaszokban nem szerepel. |
| invoiceID | A számla azonosítója azon a számlán, ahol az adott tranzakció megjelenik | A megadott tranzakciót tartalmazó számla száma. |
| resourceGroup | Az Azure-erőforráscsoport neve. Erőforrás-életciklus-felügyelethez használatos. | Az erőforráscsoport neve. |
| partnerEarnedCreditRate | Kedvezményes díjszabás akkor alkalmazható, ha a partner rendszergazdai kapcsolati hozzáférése alapján egy partner által létrehozott kredit (PEC) van. | A partner által létrehozott kreditek (PEC) aránya. Például: 0% vagy 15%. |
| partnerEarnedCreditApplied | Azt jelzi, hogy alkalmazva lett-e a partner által létrehozott kredit. | – |

A [Cost Analysis](quick-acm-cost-analysis.md) nézetben a nézetek és az adatexportálás [CSV-vagy PNG-](quick-acm-cost-analysis.md#automation-and-offline-analysis) fájlokba is [menthetők](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) .

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Partner által létrehozott kredit (PEC) erőforrás-költségek megtekintése

Azure Cost Management a partnerek a Cost Analysis szolgáltatással tekinthetik meg a PEC-előnyöket kapott költségeket.

A Azure Portal jelentkezzen be a partner bérlőbe, és válassza a **Cost Management + számlázás**lehetőséget. A **Cost Management**alatt kattintson a **Cost Analysis**elemre.

A Cost Analysis nézet a partner számlázási fiókjának költségeit jeleníti meg. A számlák egyeztetéséhez válassza ki a partner, egy adott ügyfél vagy egy számlázási profil szükséges **hatókörét** .

Egy fánk diagramban kattintson a legördülő listára, és válassza a **PartnerEarnedCreditApplied** lehetőséget a PEC-költségek részletezéséhez.

![Példa a partner által létrehozott kreditek megtekintésére](./media/get-started-partners/cost-analysis-pec1.png)

Ha a **PartnerEarnedCreditApplied** tulajdonság értéke _true (igaz_), a kapcsolódó díj a partner által létrehozott rendszergazdai hozzáférés előnyeit élvezi.

Ha a **PartnerEarnedCreditApplied** tulajdonság értéke _false (hamis_), a kapcsolódó díj nem felelt meg a jóváíráshoz szükséges jogosultságoknak. Vagy a megvásárolt szolgáltatás nem jogosult partner által létrehozott kreditre.

A szolgáltatás használati adatait általában 8-24 óra alatt kell megjeleníteni Cost Managementban. További információ: a [használati adatok frissítési gyakorisága változó](understand-cost-mgt-data.md#usage-data-update-frequency-varies). A PEC-kreditek 48 órán belül megjelennek Azure Cost Managementban való hozzáférés időpontjában.


A **PartnerEarnedCreditApplied** tulajdonság a **csoportosítási** lehetőségek alapján is csoportosíthatja és szűrheti is. A beállítások segítségével megvizsgálhatja a PEC-t és a-t nem tartalmazó költségeket.

![Csoport vagy szűrés partner által létrehozott kredit alapján](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="cost-management-rest-apis"></a>Cost Management REST API-k

A partnerek, a közvetett szolgáltatók és az ügyfelek a gyakori feladatokhoz a következő szakaszokban ismertetett Cost Management API-kat használhatják.

### <a name="azure-cost-management-apis-for-partners"></a>Azure Cost Management API-k a partnereknek

A partner bérlői számlázási hatókörökhöz hozzáféréssel rendelkező partnerek és felhasználók a következő API-kat használhatják.

#### <a name="to-get-a-list-of-billing-accounts"></a>Számlázási fiókok listájának beolvasása

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Ügyfelek listájának lekérése

```
armclient get "providers/Microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Az előfizetések listájának lekérése

```
armclient get "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-create-new-subscription"></a>Új előfizetés létrehozása

```
armclient post "/providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub.json -verbose
```

#### <a name="to-get-or-download-usage-for-azure-services"></a>Az Azure-szolgáltatások használatának beolvasása vagy letöltése

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-get-a-list-of-billing-profiles"></a>Számlázási profilok listájának beolvasása

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/billingProfiles?api-version=2019-10-01-preview
```

#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>A felhasznált Azure-szolgáltatások árlista beszerzése vagy letöltése

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/BillingProfiles/JUT6-EU3Q-BG7-TGB/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Az ügyfelek költségeinek lekérése az elmúlt két hónapban, hónap szerint rendezve

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Az Azure-előfizetések költségeinek beszerzése az elmúlt két hónapban, hónap szerint rendezve

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Napi költségek beszerzése az aktuális hónapra

```
armclient post providers/microsoft.billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>A szabályzat lekérése az ügyfelek számára a költségek megtekintéséhez

```
armclient get "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>A szabályzat beállítása az ügyfelek számára a költségek megtekintéséhez

```
armclient put "providers/Microsoft.Billing/billingAccounts/99a13315-2f87-5b46-9dbd-606071106352:1d100e69-2833-4677-a5d4-8ad35035d9a3_2019-05-31/customers/9553eda2-2bd7-4ae6-a1f8-6a19eb40be22/policies/default?api-version=2019-10-01-preview" @policy.json
```

### <a name="azure-cost-management-apis-for-indirect-providers"></a>Azure Cost Management API-k közvetett szolgáltatók számára

Az ügyfél bérlői RBAC-hatókörökhöz hozzáféréssel rendelkező közvetett szolgáltatók a következő API-kat használhatják. Első lépésként jelentkezzen be felhasználóként vagy egy egyszerű szolgáltatással.

#### <a name="to-get-the-billing-account-information"></a>A Számlázási fiók adatainak beolvasása

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Ügyfelek listájának lekérése

```
armclient get "providers/Microsoft.billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-resellers-associated-with-the-customer"></a>Az ügyfélhez társított viszonteladók listájának lekérése

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db?api-version=2019-10-01-preview&$expand=resellers
```

#### <a name="to-get-a-list-of-subscriptions-with-reseller-information"></a>A viszonteladói információkkal rendelkező előfizetések listájának lekérése

```
armclient get "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-create-a-subscription"></a>Előfizetés létrehozása

```
armclient post "/providers/Microsoft.Billing/billingAccounts/ec1b88ba-5681-517e-f657-4cc6a4a407cb:52f143a9-6524-4e5e-9d4a-120c7a79ca65_2019-05-31/customers/b51df1fa-62fa-4c92-9a74-fe860016d4db/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview" @createsub_reseller.json
```

### <a name="azure-cost-management-apis-for-customers"></a>Azure Cost Management API-k ügyfelek számára

Az ügyfelek az alábbi információkat használják az API-k eléréséhez. Első lépésként jelentkezzen be felhasználóként.

#### <a name="to-get-or-download-azure-consumption-usage-information-with-retail-rates"></a>Az Azure-használati adatok kiskereskedelmi díjszabással való lekérése vagy letöltése

```
armclient post /subscriptions/66bada28-271e-4b7a-aaf5-c0ead63923d7/providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

## <a name="next-steps"></a>Következő lépések
- A [költségek elemzésének Megkezdése](quick-acm-cost-analysis.md) Cost Management
- [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) Cost Management

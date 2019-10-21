---
title: Ismerkedjen meg Azure Cost Management partnerekkel
description: Ez a cikk azt ismerteti, hogyan használják a partnerek a Azure Cost Management funkciókat, és hogyan teszik lehetővé Cost Management hozzáférését az ügyfelek számára.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: cost-management
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 661e1b6e3811ff2b5ae25e4fd59764a69e9ebe48
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596641"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Ismerkedjen meg Azure Cost Management partnerekkel

A Azure Cost Management natív módon elérhető azon partnereink számára, akik Microsoft-ügyfél szerződésbe vettek be ügyfeleiket. Ez a cikk azt ismerteti, hogyan használják a partnerek a [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/) funkciókat. Azt is leírja, hogy a partnerek hogyan engedélyezhetik Cost Management hozzáférését ügyfeleiknek. Az ügyfelek a CSP-partnerük által engedélyezett Cost Management funkciókat is használhatják.

A CSP-partnerek a következő Cost Management használják:

- Ismerje meg a számlázott költségeket, és rendelje hozzá a költségeket az ügyfélhez, az előfizetésekhez, az erőforrás-csoportokhoz és a szolgáltatásokhoz.
- Az Azure-költségek [elemzésével](quick-acm-cost-analysis.md) és az ügyfelek, az előfizetés, az erőforráscsoport, az erőforrás, a mérő, a szolgáltatás és számos más dimenzió között a költségek elemzése révén intuitív áttekintést kaphat az Azure költségeiről.
- Tekintse meg a Cost Analysis szolgáltatásban alkalmazott, a partner által szerzett Kredittel (PEC) rendelkező erőforrás-költségeket.
- Az értesítések és az automatizálás beállítása programozott [költségvetésekkel](tutorial-acm-create-budgets.md) és riasztásokkal, ha a költségek meghaladják a költségvetést.
- Engedélyezze a Azure Resource Manageri házirendet, amely hozzáférést biztosít az ügyfelek számára Cost Management-adathozzáféréshez. Az ügyfelek ezt követően megtekinthetik az előfizetésük használati költségeit az utólagos elszámolású [díjszabás](https://azure.microsoft.com/pricing/calculator/)alapján.

Az alábbi példa az összes ügyfél költségeit mutatja be.
![Example az összes ügyfél költségeinek megjelenítése ](./media/get-started-partners/customer-costs1.png)

Az alábbi példa egy adott ügyfél költségeit mutatja be.
egyetlen ügyfél költségeinek megjelenítése ![Example ](./media/get-started-partners/customer-costs2.png)

A Azure Cost Managementban elérhető összes funkció REST API-kkal is elérhető. Az API-k segítségével automatizálhatja a Cost Management feladatait.

## <a name="prerequisites"></a>Előfeltételek

Azure Cost Management olvasási jogosultsággal kell rendelkeznie a számlázási fiókjához vagy előfizetéséhez. A hozzáférés bármely szinten megadható az erőforrások felett, a számlázási fióktól vagy egy felügyeleti csoporttól egészen az egyes erőforráscsoportokig, ahol kezelheti az alkalmazásokat. A számlázási fiókok Azure Cost Managementhoz való hozzáférés engedélyezésével és hozzárendelésével kapcsolatos további információkért lásd: [felhasználói szerepkörök és engedélyek](/partner-center/permissions-overview)kiosztása. A **globális rendszergazda** és a **rendszergazdai ügynök** szerepkörei kezelhetik a számlázási fiókok költségeit.

A támogatott fióktípus teljes listájának megtekintéséhez lásd: Cost Management- [adat megismerése](understand-cost-mgt-data.md).


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

Miután bevezette az ügyfeleket egy Microsoft-ügyfél szerződésbe, egy olyan számlát kap, amely tartalmazza az ügyfelek számára a Microsoft ügyfél-szerződésben szereplő összes termékre vonatkozó díjat (felhasználás, beszerzések és jogosultságok). Ha a számlázás ugyanabban a pénznemben történik, akkor ezek a számlák tartalmazzák a jogosultságok és a megvásárolt termékek, például az SaaS, az Azure Marketplace és az olyan ügyfelek foglalásait is, akik még szerepelnek a CSP-ajánlatban.

A számlázási profil hatóköre lehetővé teszi a díjak összeegyeztetését az ügyfél számláján, így megtekintheti az ügyfelek számláján felmerülő összes költséget. A számlához hasonlóan a hatókör az új Microsoft ügyfél-szerződésben szereplő összes ügyfél költségeit is megjeleníti. A hatókör a jelenlegi CSP-ajánlatban még mindig az ügyfél-jogosultsági termékek díját is megjeleníti.

A számlázási profil és a Számlázási fiók hatóköre az egyetlen érvényes hatókör, amely a jogosultsági és vásárlási alapú termékek, például az Azure Marketplace és a foglalások díjait jeleníti meg.

A számlázási profilok határozzák meg a számlán szereplő előfizetéseket. A számlázási profilok a nagyvállalati szerződés beléptetésének funkcionális megfelelői. A számlázási profil az a hatókör, amelyben a számlák jönnek létre.

Az ügyfél számlázási pénzneme jelenleg az alapértelmezett pénznem, amikor a számlázási profil hatókörében megtekinti a költségeket. A számlázási profil hatókörében beállított költségvetések a számlázási pénznemben vannak megadva.

A partnerek a hatókör használatával is összehangolják a számlákat. Továbbá a hatókör használatával határozzák meg a költségkereteket a számlázási pénznemben a következő elemek esetében:

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

Az ügyfél hatóköre nem tartalmazza azokat az ügyfeleket, akik a jelenlegi CSP-ajánlatban szerepelnek. A hatókör csak a Microsoft ügyfél-szerződéssel rendelkező ügyfeleket tartalmazza. A jogosultsági költségek, nem az Azure-használat, az aktuális CSP-ajánlathoz tartozó ügyfelek a Számlázási fiók és a számlázási profil hatókörében érhetők el az ügyfél szűrő alkalmazása során.

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
![Policy lehetővé teszi, hogy az ügyfelek megtekintsék az utólagos elszámolású díjakat ](./media/get-started-partners/cost-management-billing-policies.png)

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

| **Mező** | **Leírás** |
| --- | --- |
| PartnerTenantID | A partner Azure Active Directory bérlő azonosítója |
| PartnerName | A partner Azure Active Directory bérlő neve |
| CustomerTenantID | Az ügyfél előfizetése Azure Active Directory bérlő azonosítója |
| Customername ( | Az ügyfél előfizetését tartalmazó Azure Active Directory bérlő neve |
| ResellerMPNID | Az előfizetéshez társított viszonteladó MPNID |
| subscription ID | Az Azure-előfizetés egyedi, Microsoft által generált azonosítója |
| subscriptionName | Az Azure-előfizetés neve |
| billingProfileID | A számlázási profil azonosítója. Egyetlen számlázási pénznemben csoportosítja a számlákat a különböző ügyfeleken.
| invoiceID | A számla azonosítója azon a számlán, ahol az adott tranzakció megjelenik |
| resourceGroup | Az Azure-erőforráscsoport neve. Erőforrás-életciklus-felügyelethez használatos. |
| partnerEarnedCreditRate | Kedvezményes díjszabás akkor alkalmazható, ha a partner rendszergazdai kapcsolati hozzáférése alapján egy partner által létrehozott kredit (PEC) van. |
| partnerEarnedCreditApplied | Azt jelzi, hogy alkalmazva lett-e a partner által létrehozott kredit. |

A [Cost Analysis](quick-acm-cost-analysis.md) nézetben a nézetek és az adatexportálás [CSV-vagy PNG-fájlokba](quick-acm-cost-analysis.md#automation-and-offline-analysis)is [menthetők](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) .

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

A partnerek és az ügyfelek az alábbi szakaszokban ismertetett Cost Management API-kat használhatják a gyakori feladatokhoz.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management API-k – közvetlen és közvetett szolgáltatók

A partner bérlői számlázási hatókörökhöz hozzáféréssel rendelkező partnerek a következő API-kkal tekinthetik meg a számlázott költségeket.

Az előfizetések hatókörében lévő API-k meghívhatók partnertől függetlenül, ha hozzáféréssel rendelkeznek az előfizetéshez. Az előfizetéshez hozzáférő más felhasználók (például az ügyfél vagy a viszonteladó) csak akkor hívhatják meg az API-kat, ha a partner engedélyezi az ügyfél bérlője számára a díjszabási szabályzatot.


#### <a name="to-get-a-list-of-billing-accounts"></a>Számlázási fiókok listájának beolvasása

```
armclient get "providers/Microsoft.billing/billingAccounts?api-version=2019-10-01-preview"
```

#### <a name="to-get-a-list-of-customers"></a>Ügyfelek listájának lekérése

```
armclient get "providers/Microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers?api-version=2019-10-01-preview"
```
#### <a name="to-get-a-list-of-subscriptions"></a>Az előfizetések listájának lekérése

```
armclient get "/providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/billingSubscriptions?api-version=2019-10-01-preview"
```

#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>A szabályzat lekérése az ügyfelek számára a költségek megtekintéséhez

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview"
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>A szabályzat beállítása az ügyfelek számára a költségek megtekintéséhez

```
armclient put "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/policies/default?api-version=2019-10-01-preview" @policy.json
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Az Azure-szolgáltatás használati számlázási fiókhoz való beszerzése

```
armclient GET /providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Ügyfél Azure-szolgáltatásbeli használatának letöltése

A következő Get hívás egy aszinkron művelet.

```
armclient get providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/YYYYYYYY-YYYY-YYYY-YYYY-YYYYYYYYYYYY/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Hívja meg a válaszban visszaadott `Location` URI-t a művelet állapotának vizsgálatához. Az állapot *befejezése után*a `downloadUrl` tulajdonság egy hivatkozást tartalmaz, amely a generált jelentés letöltésére használható.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>A felhasznált Azure-szolgáltatások árlista beszerzése vagy letöltése

Először használja a következő bejegyzést.

```
armclient post "/providers/Microsoft.Billing/BillingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Ezután hívja meg az aszinkron művelet tulajdonság értékét. Példa:

```
armclient get "providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/billingProfiles/YYYY-YYYY-YYY-YYYY-YYY/pricesheetDownloadOperations/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX?sessiontoken=0:11186&api-version=2019-10-01-preview"
```
Az előző Get hívás az árlistát tartalmazó letöltési hivatkozást adja vissza.

#### <a name="to-get-customer-costs-for-the-last-two-months-sorted-by-month"></a>Az ügyfelek költségeinek lekérése az elmúlt két hónapban, hónap szerint rendezve

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryCustomer.json
```

#### <a name="to-get-azure-subscription-costs-for-the-last-two-months-sorted-by-month"></a>Az Azure-előfizetések költségeinek beszerzése az elmúlt két hónapban, hónap szerint rendezve

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQuerySubscription.json
```

#### <a name="to-get-daily-costs-for-the-current-month"></a>Napi költségek beszerzése az aktuális hónapra

```
armclient post providers/microsoft.billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31//providers/microsoft.costmanagement/query?api-version=2019-10-01 @CCMQueryDaily.json
```

#### <a name="create-a-budget-for-a-partner"></a>Költségvetés létrehozása partner számára

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01 @budgetCreate.json
```


#### <a name="create-a-budget-for-a-customer"></a>Költségvetés létrehozása az ügyfél számára

```
armclient put providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/customers/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/providers/Microsoft.Consumption/budgets/test-partner-demo?api-version=2019-10-01 @budgetCreate.json
```
#### <a name="delete-a-budget"></a>Költségvetés törlése

```
armclient delete providers/Microsoft.Billing/billingAccounts/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX:XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXXXXXXX_2019-05-31/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```


## <a name="next-steps"></a>Következő lépések
- A [költségek elemzésének Megkezdése](quick-acm-cost-analysis.md) Cost Management
- [Költségvetések létrehozása és kezelése](tutorial-acm-create-budgets.md) Cost Management

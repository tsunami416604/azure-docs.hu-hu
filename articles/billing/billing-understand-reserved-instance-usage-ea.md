---
title: Az Azure foglalások használatának ismertetése nagyvállalati szerződésekben
description: Megtudhatja, hogyan olvashatja el a használatát, hogy megtudja, hogyan alkalmazza a rendszer a vállalati regisztrációhoz szükséges Azure-foglalást.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 507ad62a917120689bee3f1e293e23c9ab8b0f66
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68598100"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Nagyvállalati Szerződés foglalási költségek és használat beszerzése

A foglalási költségek és a használati adatok a Azure Portal és a REST API-k Nagyvállalati Szerződés ügyfelei számára érhetők el. Ez a cikk a következőket segíti elő:

- Foglalási vásárlási adatlekérdezés
- A foglalást használó előfizetés, erőforráscsoport vagy erőforrás ismerete
- Foglalás kihasználtsága
- Foglalások megtakarításának kiszámítása
- Foglalási szolgáltatás lekérése a kihasználtsági adatai alapján
- Foglalási költségek törlesztése

A Piactéri díjak a használati adatokban vannak összesítve. Megtekintheti az első féltől származó használat, a piactér és a vásárlások díját egyetlen adatforrásból.

## <a name="reservation-charges-in-azure-usage-data"></a>Foglalási díjak az Azure használati adatokban

Az adat két külön adatkészletre oszlik: A _tényleges költségeket_ és az _amortizációs költségeket_. A két adathalmaz különbsége:

**Tényleges költség** – adatokat biztosít a havi számlával való összeegyeztetéshez. Ezen adatok foglalási vásárlási költségei és a foglalási alkalmazás részletei. Ezekből az adatokból megtudhatja, hogy melyik előfizetés vagy erőforráscsoport vagy erőforrás fogadta a foglalási kedvezményt egy adott napon belül. A foglalási kedvezményt fogadó használat EffectivePrice nulla.

**Amortizációs díj** – ez az adatkészlet hasonló a tényleges Cost-adatkészlethez, kivéve azt, hogy a foglalási kedvezményt tartalmazó használat EffectivePrice a foglalás arányosan fizetendő díja (nulla helyett). Ez segít megismerni a foglalások felhasználásának pénzügyi értékét előfizetés, erőforráscsoport vagy erőforrás alapján, és segít a foglalás kihasználtságának belső feltöltésében. Az adatkészlet nem használt foglalási órát is tartalmaz. Az adatkészlet nem rendelkezik foglalási vásárlási rekordokkal. 

Két adathalmaz összehasonlítása:

| Data | Tényleges költségadatok készlete | Amortizációs költségadatok készlete |
| --- | --- | --- |
| Foglalások vásárlása | Ebben a nézetben érhető el.<br><br>  Az adatszűrő beszerzése a ChargeType &quot;=&quot;vásárlás elemnél. <br><br> Tekintse meg a ReservationID vagy a ReservationName, hogy megtudja, melyik foglalásra vonatkozik a díj.  | Erre a nézetre nem alkalmazható. <br><br> Az elszámolt értékekben nem szerepelnek a vásárlási költségek. |
| EffectivePrice | Az érték nulla a foglalási kedvezményt bekerülő használathoz. | A foglalási kedvezménysel rendelkező használat esetén az érték óránkénti arányban történik. |
| Nem használt foglalás (azon órák számát adja meg, amelyet a foglalás nem használ egy nap alatt és a hulladék pénzügyi értékét) | Ebben a nézetben nem alkalmazható. | Ebben a nézetben érhető el.<br><br> Az adatlekérdezéshez szűrje a ChargeType = &quot;UnusedReservation.&quot;<br><br>  Tekintse meg a ReservationID vagy a ReservationName, hogy megtudja, melyik foglalást használta ki. A foglalások mennyisége a nap folyamán elvész.  |
| Egységár (az erőforrás díja a díjszabási lapon) | Elérhető | Elérhető |

Az Azure-használati adatokban elérhető egyéb információk megváltoztak:

- Termék-és mérőszám-információk – az Azure nem cseréli le az eredetileg felhasznált mérőszámot a ReservationId és a ReservationName, ahogy korábban volt.
- ReservationId és ReservationName – ezek a saját mezői az adatban. Korábban csak a AdditionalInfo alatt volt elérhető.
- ProductOrderId – a foglalási rendelés azonosítója, amely a saját mezőként lett hozzáadva.
- ProductOrderName – a megvásárolt foglalás terméknév.
- Időtartam – 12 hónap vagy 36 hónap.
- RINormalizationRatio – elérhető a AdditionalInfo alatt. Ez az arány, ahol a foglalás a használati rekordra vonatkozik. Ha a példány méretének rugalmassága engedélyezve van a foglalásnál, akkor más méretekre is alkalmazható. Az érték azt az arányt jeleníti meg, amelyet a foglalás a használati rekordra vonatkozóan alkalmazott.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Azure-beli felhasználás és foglalás használati adatok beszerzése API használatával

Az adatok az API használatával szerezhetők be, vagy letölthetők a Azure Portalról.

Az új adatok beszerzéséhez hívja meg a &quot; [használati adatok API](/rest/api/consumption/usagedetails/list) -t az 2019-04-01-es API-&quot; verzióval. A terminológia részleteit a [használati feltételek](billing-understand-your-usage.md)című részben tekintheti meg. A hívónak a nagyvállalati szerződéshez tartozó vállalati rendszergazdának kell lennie az [EA Portal](https://ea.azure.com)használatával. A csak olvasási jogosultsággal rendelkező vállalati rendszergazdák is lekérhetik az adatgyűjtést.

Az adatok nem érhetők el a [Reporting API-k vállalati ügyfelek számára – használati](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)adatok.

Íme egy példa az API-ra:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

További információ a {enrollmentId} és a {billingPeriodId} szolgáltatásról: [használati adatok – lista API-](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) cikk.

A metrika és a szűrő a következő táblázatban található információk segíthetnek a gyakori foglalási problémák megoldásában.

| **API-adattípusok** | API-Hívási művelet |
| --- | --- |
| **Minden díj (használat és vásárlás)** | {Metrikus} cseréje a ActualCost |
| **Foglalási kedvezménnyel kapott használat** | {Metrikus} cseréje a ActualCost<br><br>{Filter} cseréje a (z): Properties/reservationId% 20ne% 20 |
| **A foglalási kedvezményt nem tartalmazó használat** | {Metrikus} cseréje a ActualCost<br><br>{Filter} cseréje a (z): Properties/reservationId% 20eq% 20 |
| **Amortizációs díjak (használat és vásárlás)** | {Metrikus} cseréje a AmortizedCost |
| **Nem használt foglalási jelentés** | {Metrikus} cseréje a AmortizedCost<br><br>A (z) {Filter} cseréje a következővel: Properties/ChargeType% 20eq% 20 ' UnusedReservation ' |
| **Foglalások vásárlása** | {Metrikus} cseréje a ActualCost<br><br>{Filter} cseréje a következővel: tulajdonságok/ChargeType% 20eq% 20 ' vásárlás '  |
| **Befizetett összegeket** | {Metrikus} cseréje a ActualCost<br><br>A (z) {Filter} cseréje a következővel: Properties/ChargeType% 20eq% 20 ' visszatérítés ' |

## <a name="download-the-usage-csv-file-with-new-data"></a>A használati CSV-fájl letöltése új adatokkal

Ha Ön egy nagyvállalati rendszergazda, akkor letöltheti az új használati adatokat tartalmazó CSV-fájlt Azure Portalról. Ezek az adatok nem érhetők el az [EA portálról](https://ea.azure.com).

A Azure Portal navigáljon a [Cost Management + számlázás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts)elemre.

1. Válassza ki a számlázási fiókot.
2. Kattintson a **használat + díjak**elemre.
3. Kattintson a **Letöltés** gombra.  
![Példa a CSV-használati adatok fájljának letöltésére a Azure Portal](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. A **használati adatok letöltése és díjai** területen a **használati részletek 2** . verziójában válassza a **minden díj (használat és vásárlás)** elemet, majd kattintson a letöltés gombra. Ismétlődő **díjak (használat és vásárlások)**

A letöltött CSV-fájlok tényleges költségeket és amortizációs költségeket tartalmaznak.

## <a name="common-cost-and-usage-tasks"></a>Általános Cost-és használati feladatok

A következő fejezetek olyan gyakori feladatok, amelyeket a legtöbb felhasználó a foglalási és használati adatok megtekintésére használ.

### <a name="get-reservation-purchase-costs"></a>Foglalási költségek beszerzése

A foglalások vásárlásának költségei a tényleges költségadatok szerint érhetők el. Szűrő a _ChargeType = vásárláshoz_. Tekintse meg a ProductOrderID, és határozza meg, hogy a vásárlás melyik foglalási sorrendben történik.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Kihasználatlan foglalási mennyiség és költség

A _ChargeType_ _= UnusedReservation_esetében az elszámolt költségadatok és szűrők beolvasása. A napi fel nem használt foglalási mennyiséget és a költségeket kapja meg. A foglalási és foglalási sorrendek adatai a _ReservationId_ és a _ProductOrderId_ mezők használatával szűrhetők. Ha a foglalás 100%-ban lett kihasználva, a rekord értéke 0.

### <a name="amortize-reservation-costs"></a>Foglalási költségek törlesztése

Az elszámolt költségadatok beszerzése és szűrése a _ProductOrderID_ használatával a foglalás napi törlesztési költségeinek beszerzése érdekében.

### <a name="chargeback-for-a-reservation"></a>Foglalás jóváírása

Az előfizetés, az erőforráscsoportok vagy a címkék alapján lefoglalhatja a foglalást más szervezeteknek. Az elszámolt költségadatok a foglalás kihasználtságának pénzügyi értékét biztosítják a következő adattípusok esetén:

- Erőforrások (például virtuális gépek)
- Resource group
- Tags
- Subscription

### <a name="get-the-blended-rate-for-chargeback"></a>A jóváíráshoz tartozó kevert díj beolvasása

A kevert arány meghatározásához szerezze be az elszámolási költségeket, és összesítse a teljes költséget. Virtuális gépek esetében a MeterName vagy a ServiceType adatokat is használhatja a AdditionalInfo JSON-adatokból. A teljes díj felosztása a kevert arány beszerzéséhez használt mennyiség alapján.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Az optimális foglalások használatának naplózása a példányok méretének rugalmassága érdekében

Több a mennyiség a _RINormalizationRatio_, a AdditionalInfo. Az eredmények azt jelzik, hogy a használati rekordban hány órányi foglalást alkalmaztak.

### <a name="determine-reservation-savings"></a>Foglalások megtakarításának meghatározása

Lekérheti az elszámolt költségekkel kapcsolatos adatgyűjtést, és szűrheti egy fenntartott példányra vonatkozó adatszűrési Majd

1. A becsült utólagos elszámolású költségek beszerzése. Szorozza meg az _Egységár_ értéket a _mennyiségi_ értékekkel a becsült utólagos elszámolású költségek beszerzéséhez, ha a foglalási kedvezmény nem vonatkozik a használatra.
2. A foglalási költségek beszerzése. Adja meg a _költségadatok_ összegét, hogy megkapja a fenntartott példányhoz fizetett összeg értékét. Magában foglalja a foglalás felhasznált és fel nem használt költségeit.
3. Foglalási költségek kivonása a becsült utólagos elszámolású költségekből a becsült megtakarítások beszerzéséhez.

## <a name="reservation-purchases-and-amortization-in-cost-analysis"></a>Foglalások beszerzése és amortizációja a Cost Analysis szolgáltatásban

A foglalási költségek a [Cost Analysis](https://aka.ms/costanalysis)szolgáltatásban érhetők el. Alapértelmezés szerint a Cost Analysis megjeleníti a **tényleges költségeket**, ami azt mutatja, hogy a költségek hogyan jelennek meg a számlán. Ha szeretné megtekinteni a foglalások lebontását és a juttatást használó erőforrásokhoz társított erőforrásokat, váltson a következőre: **amortizációs Cost**:

![Példa arra, hogy hol válassza ki az elszámolt költségeket a Cost Analysis szolgáltatásban](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

Csoportosítási díj típusa szerint a használat, a beszerzések és a visszatérítések lebontása; vagy fenntartással a foglalás lebontására és igény szerinti költségekre. Ne feledje, hogy a vásárlások tényleges költségeinek megvizsgálása esetén csak a foglalás költségeit kell megtekinteni, a költségek azonban a benfit használt egyes erőforrásokhoz lesznek lefoglalva, az elszámolt költségek megvizsgálása esetén. Az elszámolt költségek kiszámításakor egy új **UnusedReservation** -díjat is látni fog.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:

- [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [A foglalási kedvezmény alkalmazási módjának megismerése](billing-understand-vm-reservation-charges.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
- [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)

---
title: Az Azure-foglalások használatának megértéséhez a nagyvállalati szerződések
description: Ismerje meg, hogyan olvashatja be a használat megértéséhez, hogyan kell alkalmazni az Azure a nagyvállalati beléptetés-foglalás.
author: bandersmsft
manager: yashar
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/07/2019
ms.author: banders
ms.openlocfilehash: b2452580eaecc0ab922f8e7db48676f70831a8ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66126843"
---
# <a name="get-enterprise-agreement-reservation-costs-and-usage"></a>Nagyvállalati Szerződés fenntartási költségeket és használat beszerzése

Foglalási költség- és használati adatok érhető el nagyvállalati szerződéssel rendelkező ügyfeleknek az Azure portal és a REST API-k. Ez a cikk segítségével:

- Foglalás megvásárlása adatok lekérése
- Foglalás korrigáljuk kihasználtsági adatok lekérése
- Foglalási költség amortize
- Jóváírási foglalás kihasználtság
- Foglalás megtakarítás kiszámítása

Piactér-díjak a használati adatok van összevonva. Megtekintheti az entitás első használat, a Marketplace-en használatának és a vásárlások egyetlen adatforrásból származó kell fizetni.

## <a name="reservation-charges-in-azure-usage-data"></a>Foglalás a költségeket az Azure-használati adatok

Adatok két külön adatkészletek oszlik: _Tényleges költség_ és _amortizált költségek_. Két Miben:

**Tényleges költség** -nyújt az adatok a havi számla egyeztetni. Ezeket az adatokat a beszerzési költségeket foglalás rendelkezik. A használatra, a foglalási kedvezményt kapott nulla EffectivePrice rendelkezik.

**Amortizált költség** – az erőforrás EffectiveCost, amely lekérdezi a foglalási kedvezményt a fenntartott példány időarányos mennyibe kerül. Az adatkészlet is rendelkezik nem használt foglalás költségeket. A fenntartási költségeket és a fel nem használt foglalás összege a Foglalás napi amortizált költségének biztosít.

Két adatkészletet összehasonlítása:

| Adatok | Tényleges költség-adatkészlet | Amortizált költség-adatkészlet |
| --- | --- | --- |
| Foglalás beszerzések | Ebben a nézetben érhető el.<br><br>  Úgy szerezheti be az adatok szűrése ChargeType = &quot;beszerzési&quot;. <br><br> Tekintse meg reservationid értékhez vagy ReservationName tudni, hogy melyik foglalás a díjat.  | Ez a nézet nem alkalmazható. <br><br> Beszerzési költségek amortizált adatok nincsenek megadva. |
| effectivePrice | A tulajdonság értéke nulla, amely lekérdezi a foglalási kedvezményt használatra. | Óraalapú elszámolással költsége a foglalást, amely rendelkezik a foglalási kedvezményt használat értéke. |
| A fel nem használt foglalás (biztosít a foglalás nem használható egy nap alatt órák számát és a veszteség részének pénzügyi értékét) | Ebben a nézetben nem alkalmazható. | Ebben a nézetben érhető el.<br><br> Ezen adatok beolvasásához, szűrjön az ChargeType = &quot;UnusedReservation&quot;.<br><br>  Tekintse meg reservationid értékhez vagy ReservationName tudni, hogy melyik foglalás volt eredményeztek. Ez az, hogy mekkora a Foglalás volt adattisztítást a napon.  |
| Egységár (az árlista erőforrás díj) | Elérhető | Elérhető |

Más Azure-használati adatok elérhető információk megváltozott:

- Termék- és mérési adatok, az Azure nem cserélje le az eredetileg felhasznált mérőszám a reservationid értékhez és ReservationName, mint korábban.
- Reservationid értékhez és ReservationName – azok a saját mezőket az adatok. Korábban a ezt használja csak a további információ területén érhetők el.
- ProductOrderId - foglalás rendelési azonosítója, a saját mezőként hozzáadva.
- ProductOrderName – a termék nevét, a megvásárolt foglalás.
- Kifejezés - 12 hónapon belül vagy 36 hónapos.
- RINormalizationRatio - további információ területen érhető el. Ez az arány ahol a Foglalás a használati rekord van alkalmazva. Ha a példány mérete rugalmasan engedélyezve van a foglalás, majd alkalmazhatja további méretek. Az érték látható, hogy a Foglalás lett alkalmazva a használati rekord aránya.

## <a name="get-azure-consumption-and-reservation-usage-data-using-api"></a>Az Azure használat és a fenntartás használati adatok lekérése API használatával

Az adatok lekérése az API használatával, vagy töltse le az Azure Portalon.

Hívja a [használati részletek API](/rest/api/consumption/usagedetails/list) API-verzióval &quot;2019-04-01-preview&quot; az új adatok beolvasásához. Terminológiai kapcsolatos részletekért lásd: [használati feltételek](billing-understand-your-usage.md). A hívónak a nagyvállalati szerződés a vállalati rendszergazdának kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com). Írásvédett vállalati rendszergazdák is kérheti le az adatokat.

Az adatok nem érhető el a [Reporting API-kkal a vállalati ügyfelek - használatról](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail).

Íme egy példa az API meghívásához:

```
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{enrollmentId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodId}/providers/Microsoft.Consumption/usagedetails?metric={metric}&amp;api-version=2019-04-01-preview&amp;$filter={filter}
```

További információ a {enrollmentId} és {billingPeriodId}: a [használat részletei – lista](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API cikk.

Információk az alábbi táblázatban kapcsolatos metrika- és a szűrő segítségével a Foglalás kapcsolatos gyakori problémák megoldásában.

| **Az API-adatok típusa** | API-hívás művelet |
| --- | --- |
| **Minden költséget (használati és vásárlások)** | Cserélje le ActualCost {metrika} |
| **Itt van a foglalási kedvezményt használat** | Cserélje le ActualCost {metrika}<br><br>Helyettesítse be {szűrő} a: properties/reservationId%20ne%20 |
| **Kapta a foglalási kedvezményt használat** | Cserélje le ActualCost {metrika}<br><br>Helyettesítse be {szűrő} a: properties/reservationId%20eq%20 |
| **Amortizált költségek (használati és vásárlások)** | Cserélje le AmortizedCost {metrika} |
| **A fel nem használt foglalás jelentés** | Cserélje le AmortizedCost {metrika}<br><br>Helyettesítse be {szűrő} a: properties/ChargeType%20eq%20'UnusedReservation " |
| **Foglalás beszerzések** | Cserélje le ActualCost {metrika}<br><br>Helyettesítse be {szűrő} a: properties/ChargeType%20eq%20'Purchase "  |
| **A befizetett** | Cserélje le ActualCost {metrika}<br><br>Helyettesítse be {szűrő} a: properties/ChargeType%20eq%20'Refund " |

## <a name="download-the-usage-csv-file-with-new-data"></a>Az új adatokat a használati CSV-fájl letöltése

Ha Ön EA rendszergazdája, letöltheti az új használati adatokat az Azure portal, a CSV-fájl. Ezek az adatok nem elérhető a [a nagyvállalati szerződések portáljának](https://ea.azure.com).

Az Azure Portalon lépjen [Költségkezelés + számlázás](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade/BillingAccounts).

1. Válassza ki a számlázási fiókot.
2. Kattintson a **használat és költségek**.
3. Kattintson a **Letöltés** gombra.  
![Példa: hol kell töltse le a fürt megosztott kötetei szolgáltatás használati adatokat az Azure Portalon](./media/billing-understand-reserved-instance-usage-ea/portal-download-csv.png)
4. A **használati adatok letöltése + költségek** alatt **használati részletek 2-es verzió** , jelölje be **minden költséget (használati és vásárlások)** és majd kattintson a Letöltés gombra. Ismételje meg a műveletet **amortizált költségek (használati és vásárlások)** .

A CSV-fájlok letöltésével beszerzett tartalmaznak tényleges költségek és az amortizált költségek.

## <a name="common-cost-and-usage-tasks"></a>Gyakori felhasználási és feladatok

Az alábbi szakaszok olyan gyakori feladatokat, amelyek a legtöbb ember foglalási költség- és használati adatok megtekintéséhez.

### <a name="get-reservation-purchase-costs"></a>Első foglalás beszerzési költségek

Foglalás beszerzési költségek tényleges költségek adatok érhetők el. Állítson be szűrőt _ChargeType beszerzési =_ . Tekintse meg ProductOrderID meghatározni, melyik foglalásrendelés, a vásárlás szól.

### <a name="get-underutilized-reservation-quantity-and-costs"></a>Gyenge kihasználtságú foglalási mennyiség és költségek

Amortizált költség adatok lekérése és szűrheti a _ChargeType_ _= UnusedReservation_. A napi fel nem használt foglalás mennyiségét és a költségek kap. Szűrheti az adatokat a fenntartás vagy foglalási sorrend használatával _reservationid értékhez_ és _ProductOrderId_ mezők jelölik. Ha a Foglalás volt 100 %-os használt fel, a rekord tartalmazza-e adott mennyiségű 0.

### <a name="amortize-reservation-costs"></a>Foglalási költség amortize

Amortizált költség adatok és a egy foglalás rendelési használatára vonatkozó szűrő _ProductOrderID_ napi amortizált költségek beolvasni egy foglalást.

### <a name="chargeback-for-a-reservation"></a>A Foglalás jóváírási

Jóváírási foglalás használatával más szervezetek így előfizetés, erőforráscsoport vagy címkéket. Amortizált költség adatokat egy foglalást kihasználtságát, a következő adattípusokat részének pénzügyi értékét tartalmazza:

- Erőforrások (például a virtuális Géphez)
- Erőforráscsoport
- Tags
- Előfizetés

### <a name="get-the-blended-rate-for-chargeback"></a>A kevert arány beolvasása a jóváírások használatára

Annak megállapításához, a kombinált sebesség, lekérheti az amortizált költségek adatokat, és a teljes költség összevonása. A virtuális gépek esetében használhatja a további információ a JSON-adatok MeterName vagy szolgáltatástípus adatait. Ossza meg a teljes költség kéri le a kevert sebesség mennyiséggel.

### <a name="audit-optimum-reservation-use-for-instance-size-flexibility"></a>Naplózási optimális foglalás használata például mérete rugalmasság

A több mennyiség a _RINormalizationRatio_, a további információ. Az eredmények jelzik, hogy hány órás foglalás használat a használati rekord lett alkalmazva.

### <a name="determine-reservation-savings"></a>Határozza meg a Foglalás megtakarítások

A Amortized költségek adatokat, és szűrje az adatokat a fenntartott példányhoz. Ezután:

1. Első becsült használatalapú fizetéses költségekhez képest. Szorozza meg a _UnitPrice_ értéket _mennyiség_ értékét becsült használatalapú fizetéses költségekhez képest, ha a foglalási kedvezményt a kihasználtsága nem lett alkalmazva.
2. A fenntartási költségeket beolvasása. Sum a _költség_ értékét a fenntartott példány fizetett pénzügyi értékét. Ez magában foglalja a Foglalás felhasznált és költségeit.
3. Kivonás becsült használatalapú fizetéses költségekhez képest beolvasni a becsült megtakarítások fenntartási költségeket.

## <a name="reservation-purchases-and-amortization-in-azure-cost-analysis"></a>Foglalás beszerzések és az Azure cost analysis amortizációs

A fenntartott példányok költségei érhető [előnézeti módot az Azure cost analysis](https://preview.portal.azure.com/?feature.canmodifystamps=true&amp;microsoft_azure_costmanagement=stage2&amp;Microsoft_Azure_CostManagement_arm_canary=true&amp;Microsoft_Azure_CostManagement_apiversion=2019-04-01-preview&amp;Microsoft_Azure_CostManagement_amortizedCost=true#blade/Microsoft_Azure_CostManagement/Menu/costanalysis). Alapértelmezés szerint a költségek adatnézet a tényleges költségek mellett. Az amortizált költségek válthat. Íme egy példa.

![Példa: hol kell válassza ki a amortizált költség költségelemzés](./media/billing-understand-reserved-instance-usage-ea/portal-cost-analysis-amortized-view.png)

A alkalmazni annak érdekében, hogy a díjakkal egy foglalás vagy költséghelyi típus szerint. Csoportosítás foglalás nevére kattintva megtekintheti a foglalások bontásban költségeket.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

---
title: Foglalások megtekintése az Azure-erőforrásoknál | Microsoft Docs
description: Ismerje meg, hogyan tekintheti meg az Azure Reservationst az Azure Portalon.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: banders
ms.openlocfilehash: 5c9d9074e4b8d0d9e36417daee4d58c1d9b28b64
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199245"
---
# <a name="view-azure-reservations-in-the-azure-portal"></a>Az Azure Reservations megtekintése az Azure Portalon

Az előfizetés típusától és a meglévő engedélyektől függően több módon is megtekinthetők az Azure számára történt foglalások.

## <a name="view-purchased-reservations"></a>Vásárolt foglalások megtekintése

Alapértelmezés szerint a vásárolt foglalásokat a vásárló és a fiókadminisztrátor tudja megtekinteni. A vásárló és a fiókadminisztrátor automatikusan megkapja a Tulajdonos szerepkört a foglalási rendelés és a foglalás tekintetében. Amennyiben mások számára is engedélyezni kívánja a megtekintést, a **Tulajdonos** vagy az **Olvasó** szerepkör tagjaként kell hozzáadnia őket a foglalási rendeléshez vagy a foglaláshoz.

További információk [a foglalást kezelő felhasználók hozzáadásáról vagy módosításáról](manage-reserved-vm-instance.md#add-or-change-users-who-can-manage-a-reservation).

Foglalás megtekintése Tulajdonosként vagy Olvasóként:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keressen a **Foglalások** kifejezésre.
    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/view-reservations/portal-reservation-search.png)  
3. A listán szerepel az összes olyan foglalás, amelyhez Tulajdonos vagy Olvasó szerepkörben hozzáfér. Mindegyik foglalás mellett a kihasználtság legutóbb ismert százalékos értéke látható.  
    ![Példa egy foglaláslistára](./media/view-reservations/view-reservations.png)
4. A foglalás kijelölésekor megjelenik az elmúlt öt nap kihasználtsági trendje.  
    ![Példa a foglalás kihasználtsági trendjének megjelenítésére](./media/view-reservations/reservation-utilization.png)
5. A [foglalás kihasználtsága](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ezenkívül a Reserved Instance használati API, illetve a [Microsoft Azure Consumption Insights Power BI tartalomcsomag](/power-bi/service-connect-to-azure-consumption-insights) használatával is megállapítható.

Ha módosítania kell a foglalás hatókörét, fel kell osztania a foglalást, vagy módosítania kell, hogy ki kezelheti a foglalást, tekintse meg [az Azure Reservations kezelését](manage-reserved-vm-instance.md) ismertető témakört.

## <a name="view-reservation-transactions-for-enterprise-enrollments"></a>A nagyvállalati regisztrációk foglalási tranzakcióinak megtekintése

 Ha partneralapú nagyvállalati regisztrációval rendelkezik, a foglalásokat az EA Portal **Jelentések** lapján tekintheti meg. Az egyéb nagyvállalati regisztrációk esetében a foglalásokat az EA Portalon és az Azure Portalon tekintheti meg. A foglalási tranzakciók megtekintéséhez EA-rendszergazdának kell lennie.

Foglalási tranzakciók megtekintése az Azure Portalon:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **Költségkezelés + számlázás** kifejezésre.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/view-reservations/portal-cm-billing-search.png)

1. Válassza a **Foglalási tranzakciók** lehetőséget.
1. Az eredmények szűréséhez válassza az **Időtartomány**, a **Típus** vagy a **Leírás** lehetőséget.
1. Kattintson az **Alkalmaz** gombra.

    ![A foglalási tranzakciók eredményeit megjelenítő képernyőkép](./media/view-reservations/portal-billing-reservation-transaction-results.png)

Ha az adatokat egy API használatával kérné le, tekintse meg [a fenntartott példány tranzakciós díjainak vállalati ügyfelek esetén történő lekérését](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) ismertető cikket.

## <a name="next-steps"></a>További lépések

Az Azure Reservationsszel kapcsolatos további információért tekintse meg a következő cikkeket:

- [Mik azok a foglalások az Azure-ban?](save-compute-costs-reservations.md)
- [Foglalások kezelése az Azure-ban](manage-reserved-vm-instance.md)

Szolgáltatáscsomag vásárlása:

- [Előre fizetés fenntartott Cosmos DB-kapacitásért](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../../sql-database/sql-database-reserved-capacity.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../../virtual-machines/windows/prepay-reserved-vm-instances.md)

Szoftvercsomag vásárlása:

- [Előre fizetés Azure Reservations-beli Red Hat-szoftvercsomagokért](../../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Előre fizetés Azure Reservations-beli SUSE-szoftvercsomagokért](../../virtual-machines/linux/prepay-suse-software-charges.md)

A kihasználtság ismertetése:

- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

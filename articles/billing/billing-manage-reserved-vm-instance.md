---
title: Azure Reservations kezelése
description: Ismerje meg, hogyan kezelheti Azure Reservationseit.
ms.service: billing
author: bandersmsft
manager: yashesvi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/06/2019
ms.author: banders
ms.openlocfilehash: b161fc7cd4faa75dd87613c297c12f1edd862510
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840043"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure-erőforrások foglalásának kezelése

Az Azure-foglalás megvásárlása után előfordulhat, hogy a foglalást egy másik előfizetésre kell alkalmaznia, módosítania kell, hogy ki kezelheti a foglalást, vagy módosíthatja a foglalás hatókörét. A foglalásokat két foglalásra is feloszthatja, hogy egy másik előfizetésbe felvásárolt példányokat alkalmazzon.

Ha Azure Reserved Virtual Machine Instances vásárolta, akkor módosíthatja a foglalás optimalizálási beállítását. A foglalási kedvezmény ugyanarra a adatsorozatú virtuális gépekre vonatkozhat, vagy egy adott virtuálisgép-mérethez fenntarthatja az adatközpont kapacitását is. Emellett érdemes a fenntartásokat optimalizálni, hogy azok teljes mértékben használatban legyenek.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Foglalási sorrend és foglalás

Foglalás megvásárlásakor két objektum jön létre: **Foglalási sorrend** és **foglalás**.

A vásárlás időpontjában egy foglalási rendeléshez tartozik egy foglalás. Az olyan műveletek, mint például a felosztás, az egyesítés, a részleges visszatérítés vagy az Exchange új foglalásokat hoznak létre a **foglalási sorrendben**.

A foglalási sorrend megtekintéséhez lépjen a foglalások > Válassza ki a foglalást, majd kattintson a **foglalási rendelés azonosítóra**.

![A foglalási rendelés részleteinek példája a foglalási sorrend AZONOSÍTÓjának megjelenítése ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

A foglalás az engedélyeket a foglalási sorrendjétől örökli.

## <a name="change-the-reservation-scope"></a>A foglalás hatókörének módosítása

 A foglalási kedvezmény a virtuális gépekre, az SQL-adatbázisokra, a Azure Cosmos DBekre és a foglalási hatókörben futtatott egyéb erőforrásokra vonatkozik. A számlázási környezet a foglalás megvásárlásához használt előfizetéstől függ.

Foglalás hatókörének frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza **a minden szolgáltatás** > **foglalás**lehetőséget.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Módosítsa a hatókört.

Ha a megosztottról az önálló hatókörre vált, csak azokat az előfizetéseket választhatja ki, amelyeken Ön a tulajdonos. Csak a foglalással megegyező számlázási környezetben található előfizetések választhatók ki.

A hatókör csak az utólagos elszámolású díjszabású egyéni előfizetésekre vonatkozik (ajánlatok MS-AZR-0003P vagy MS-AZR-0023P), Enterprise ajánlat MS-AZR-0017P vagy MS-AZR-0148P vagy CSP előfizetési típusok.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>A foglalást kezelő felhasználók hozzáadása vagy módosítása

A foglalások felügyeletét delegálhatja úgy, hogy a foglalási sorrendben vagy a foglalásban személyeket ad hozzá a szerepkörökhöz. Alapértelmezés szerint az a személy, aki elhelyezi a foglalási sorrendet, és a fiók rendszergazdája tulajdonosi szerepkörrel rendelkezik a foglalási rendelésben és a foglalásban.

A foglalási kedvezményekhez tartozó előfizetések függetlenül kezelheti a fenntartási rendelésekhez és a foglalásokhoz való hozzáférést. Ha valaki engedélyt ad a foglalási rendelés vagy a foglalás kezelésére, nem ad nekik engedélyt az előfizetés kezeléséhez. Hasonlóképpen, ha engedélyt ad valakinek a foglalás hatókörébe tartozó előfizetés kezelésére, nem biztosít számukra jogosultságot a foglalási sorrend vagy a foglalás kezeléséhez.

Exchange vagy visszatérítés végrehajtásához a felhasználónak hozzáféréssel kell rendelkeznie a foglalási rendeléshez. Ha valaki engedélyeket ad meg, a legjobb megoldás a foglalási rendeléshez való hozzáférés, nem pedig a foglalás.


A hozzáférés-kezelés delegálása egy foglaláshoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** > **foglalás** lehetőséget a foglalások listázásához, amelyekhez hozzáfér.
3. Válassza ki azt a foglalást, amelynek a hozzáférését delegálni szeretné más felhasználók számára.
4. Válassza ki **hozzáférés-vezérlés (IAM)** .
5. Válassza a **szerepkör-hozzárendelési** > **szerepkör** > **tulajdonosának**hozzáadása lehetőséget. Ha korlátozott hozzáférést szeretne biztosítani, válasszon egy másik szerepkört.
6. Adja meg a tulajdonosként hozzáadni kívánt felhasználó e-mail-címét.
7. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

## <a name="split-a-single-reservation-into-two-reservations"></a>Egyetlen foglalás felosztása két foglalásra

 Miután egy foglalásban egynél több erőforrás-példányt vásárolt, érdemes lehet a foglalásban lévő példányokat hozzárendelni a különböző előfizetésekhez. Alapértelmezés szerint minden példánynak van egy hatóköre – vagy egyetlen előfizetés vagy megosztott. Megvásárolt például 10 foglalási példányt, és megadta a hatókört az A előfizetéshez. Most már módosítani szeretné a hét fenntartások hatókörét az A-előfizetésre, a fennmaradó háromat pedig a B előfizetésre. a foglalás felosztása lehetővé teszi a példányok terjesztését a részletes hatókör-kezeléshez. A megosztott hatókör lehetőség kiválasztásával leegyszerűsítheti az előfizetések kiosztását. A költségek kezeléséhez és a költségvetéshez azonban adott előfizetésekhez is rendelhet mennyiségeket.

 A foglalásokat a PowerShell, a CLI vagy az API használatával feloszthatja két fenntartásra.

### <a name="split-a-reservation-by-using-powershell"></a>Foglalás felosztása a PowerShell használatával

1. A foglalási rendelés AZONOSÍTÓjának lekéréséhez futtassa a következő parancsot:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. A foglalás részleteinek beolvasása:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. A foglalás felosztása kettőre, és a példányok terjesztése:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. A hatókört a következő parancs futtatásával frissítheti:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancel-exchange-or-refund-reservations"></a>Megszakítási, Exchange-vagy visszatérítési foglalások

Bizonyos korlátozásokkal megszakíthatja, átválthatja vagy visszaállíthatja a foglalásokat. További információ: önkiszolgáló [cserék és visszatérítések Azure Reservations számára](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Fenntartott VM-példányok optimalizálási beállításának módosítása

 A fenntartott VM-példányok vásárlásakor a példány méretének rugalmassága vagy a kapacitás prioritása közül választhat. A példányok méretének rugalmassága a foglalási kedvezményt a virtuálisgép-méretezési csoportba tartozó többi virtuális [gépre](https://aka.ms/RIVMGroups)alkalmazza. A kapacitás prioritása az üzemelő példányok adatközpont-kapacitását rangsorolja. Ez a lehetőség további megbízhatóságot biztosít a virtuálisgép-példányok elindításához, amikor szüksége van rájuk.

Alapértelmezés szerint a foglalás hatókörének megosztásakor a példány méretének rugalmassága be van kapcsolva. Az adatközpont kapacitása nem rangsorolja a virtuális gépek központi telepítését.

Azon foglalások esetében, amelyeknél a hatókör egyetlen, a virtuálisgép-példányok méretének rugalmassága helyett optimalizálhatja a kapacitás prioritásának foglalását.

A foglalás optimalizálási beállításának frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza **a minden szolgáltatás** > **foglalás**lehetőséget.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Módosítsa a beállítás optimalizálását.

## <a name="optimize-reservation-use"></a>Foglalási használat optimalizálása

Az Azure foglalási megtakarítások csak a tartós erőforrás-használatból származnak. A foglalások megvásárlásakor a kezdeti díjat a 100%-os lehetséges erőforrás-használatért kell fizetnie egy-vagy hároméves időszakra. Próbálja ki, hogy maximalizálja a foglalást, hogy minél nagyobb mennyiségű használatot és megtakarítást érhet el. A következő szakaszokban megtudhatja, hogyan figyelheti a foglalásokat, és hogyan optimalizálhatja annak használatát.

### <a name="view-reservation-use-in-the-azure-portal"></a>Foglalási használat megtekintése a Azure Portalban

A foglalások használatának egyik módja a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **minden szolgáltatás** > [**foglalás**](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) lehetőséget, és jegyezze fel a foglalás **kihasználtságát (%)** .  
  ![A foglalások listáját bemutató kép](./media/billing-manage-reserved-vm-instance/reservation-list.png)
3. Válasszon egy foglalást.
4. Tekintse át a foglalás használati trendjét az idő múlásával.  
  ![A foglalási használatot ábrázoló kép ](./media/billing-manage-reserved-vm-instance/reservation-utilization-trend.png)

### <a name="view-reservation-use-with-api"></a>Fenntartási használat megtekintése API-val

Ha Ön Nagyvállalati Szerződés (EA) ügyfél, programozott módon megtekintheti, hogyan használják a szervezeten belüli foglalásokat. A nem használt foglalásokat a használati adatokon keresztül érheti el. Ha áttekinti a foglalási díjakat, vegye figyelembe, hogy az adatok a tényleges költségek és az amortizációs költségek között oszlanak meg. A tényleges költség biztosítja az adatok összeegyeztetését a havi számlán. Emellett a foglalási vételár és a foglalási alkalmazás részletei is szerepelnek. Az elszámolási költség olyan, mint a tényleges költség, azzal a különbséggel, hogy a foglalások használatának érvényes díjait arányosan számítjuk fel. A nem használt foglalási órák az elszámolt költségadatok szerint jelennek meg. A nagyvállalati szerződéssel rendelkező ügyfelek használati adataival kapcsolatos további információkért lásd: [nagyvállalati szerződés foglalási költségek és használat](billing-understand-reserved-instance-usage-ea.md)beszerzése.

Más előfizetések esetén használja az API [-foglalások összegzése-listát foglalási sorrend és foglalás alapján](/rest/api/consumption/reservationssummaries/listbyreservationorderandreservation).

### <a name="optimize-your-reservation"></a>A foglalás optimalizálása

Ha azt tapasztalja, hogy a szervezete foglalásait használják fel:

- Győződjön meg arról, hogy a szervezet által létrehozott virtuális gépek egyeznek a foglaláshoz használt virtuálisgép-mérettel.
- Győződjön meg arról, hogy a példány méretének rugalmassága be van kapcsolva. További információ: fenntartások [kezelése – a fenntartott VM-példányok optimalizálási beállításainak módosítása](#change-optimize-setting-for-reserved-vm-instances).
- Módosítsa a foglalás hatókörét úgy, hogy az szélesebb körben is érvénybe lépjen. További információ: [a foglalás hatókörének módosítása](#change-the-reservation-scope).
- Vegye fontolóra a fel nem használt mennyiség cseréjét. További információ: megszakítások [és cserék](#cancel-exchange-or-refund-reservations).


## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van, vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Reservationsről, tekintse meg a következő cikkeket:

- [Mik az Azure-foglalások?](billing-save-compute-costs-reservations.md)

Szolgáltatási csomag vásárlása:
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md)
- [Előre Azure Cosmos DB erőforrásokhoz Azure Cosmos DB fenntartott kapacitással](../cosmos-db/cosmos-db-reserved-capacity.md)

Szoftvercsomag vásárlása:
- [A Red Hat-szoftvercsomagok előre fizetése Azure Reservations](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Előre fizetés Azure Reservations-beli SUSE-szoftvercsomagokért](../virtual-machines/linux/prepay-suse-software-charges.md)

A kedvezmény és a használat megismerése:
- [A virtuális gépek foglalási kedvezményének ismertetése](billing-understand-vm-reservation-charges.md)
- [A Red Hat Enterprise Linux szoftvercsomag-kedvezmény alkalmazásának ismertetése](../billing/billing-understand-rhel-reservation-charges.md)
- [A SUSE Linux Enterprise szoftvercsomag-kedvezmény alkalmazásának ismertetése](../billing/billing-understand-suse-reservation-charges.md)
- [Az egyéb foglalási kedvezmények alkalmazásának ismertetése](billing-understand-reservation-charges.md)
- [Az utólagos elszámolású előfizetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalási használatának ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows-szoftverek nem tartalmazzák a foglalásokat](billing-reserved-instance-windows-software-costs.md)

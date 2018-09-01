---
title: Az Azure-fenntartások kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan módosíthatja egy előfizetésre és -hozzáférés kezelése Azure-lefoglalási.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2018
ms.author: yashesvi
ms.openlocfilehash: c530fdca9d5fe499df680211a741bfd9950bb1fe
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382150"
---
# <a name="manage-reservations-for-azure-resources"></a>Az Azure-erőforrások foglalások kezelése

Miután vásárol egy Azure-foglalást, szükség lehet a alkalmazni a Foglalás egy másik előfizetésben, aki kezelheti a foglalás, vagy módosítsa a foglalás hatókörének módosítása. Akkor is feloszthatja az egy foglalást, két foglalást a alkalmazni egyes beszerezte a példányok egy másik előfizetéshez is.

Ha az Azure Reserved Virtual Machine Instances vásárolt, módosíthatja az optimalizálási beállítás a foglalás. A foglalási kedvezményt ugyanazon a virtuális gépekre is vonatkozik, vagy egy adott Virtuálisgép-méret adatközpont-kapacitás tartható fenn.

## <a name="change-the-scope-for-a-reservation"></a>A foglalás hatókörének módosítása

 A foglalási kedvezményt virtuális gépeket, SQL-adatbázisok vagy más erőforrások, amely egyezik a foglalás, és futtassa a Foglalás hatókörön belül érvényes. A Foglalás hatóköre egy előfizetés vagy az összes előfizetést a számlázási környezetben lehet. Egyetlen előfizetéshez állítsa be a hatókört, ha a Foglalás megfeleltetett futó erőforrások a kijelölt előfizetésben. A megosztott hatókör beállítása, ha az Azure megfelel a Foglalás számlázási környezetében előfizetések futtató erőforrások. A számlázási környezetben szolgáltatás megvásárolni a foglalást használt előfizetés függ.

A Foglalás hatóköre frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. A hatókör módosítása. 

Ha módosítja a hatókör számára történő megosztása, ahol Ön a tulajdonosa az előfizetések csak kiválaszthatja. Csak a foglalást végrehajtó számlázási ugyanabban a környezetben található előfizetések jelölhető ki.

A hatókör csak a használatalapú fizetéses ajánlatra MS-AZR - 0003P, MS-AZR - 0017P Enterprise ajánlat vagy CSP előfizetéstípusok vonatkozik. A nagyvállalati szerződések fejlesztési és tesztelési előfizetések nem jogosultak a foglalási kedvezményt beolvasásához.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>A Foglalás kezelésére jogosult felhasználók hozzáadása és módosítása

A Foglalás felügyeleti szerepkörökhöz a Foglalás a személyek hozzáadásával delegálhat. Alapértelmezés szerint a személy, amely a Foglalás vásárolt, és a fiók rendszergazdája a tulajdonosi szerepkör rendelkezik a foglalást.

Akkor is hozzáférésének kezelése foglalások egymástól függetlenül az előfizetést, a foglalási kedvezményt beolvasása. Amikor engedélyezi a valaki engedélyeit egy foglalást, amely nem jogokat őket az előfizetés kezelése céljából. És ha valaki engedélyek belül a Foglalás hatóköre egy előfizetés kezelése céljából, amely nem jogokat őket a Foglalás kezelése.

A Foglalás kezelési delegálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalás** lista foglalásokat, amely hozzáfér a.
3. Válassza ki a foglalást, amelyet szeretne más felhasználókkal való hozzáférés delegálására.
4. Válassza ki **hozzáférés-vezérlés (IAM)**.
5. Válassza ki **hozzáadása** > **szerepkör** > **tulajdonosa**. Vagy, ha korlátozott hozzáférést szeretne, válassza ki egy másik szerepkör.
6. Írja be a tulajdonosként hozzáadni kívánt felhasználó e-mail-címét.
7. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

## <a name="split-a-single-reservation-into-two-reservations"></a>Két foglalást, egy egyetlen foglalás felosztása

 Miután vásárol egy foglalást belül egynél több erőforráspéldány, érdemes a Foglalás példányok hozzárendelése eltérő előfizetésekben. Alapértelmezés szerint minden példány egy hatókör - vagy egyetlen előfizetéssel rendelkezik, vagy megosztott. Például 10 foglalás példányt vásárolt és a hatókör-előfizetéssé. a megadott Előfordulhat, hogy most szeretné 7 lefoglalási hatókörének módosítása egy előfizetéshez, és a fennmaradó 3, b foglalás felosztása előfizetéshez lehetővé teszi, hogy a példányok részletes hatókör Management terjesztése. Válassza ki a megosztott hatókörrel egyszerűsítheti a hozzárendelést az előfizetések. Azonban a cost management vagy a költségvetés célokra, foglalhat le mennyiségeket az adott előfizetésekhez.

 Akkor is feloszthatja az be két foglalást egy foglalást, ha PowerShell, CLI-t, vagy az API-n keresztül.

### <a name="split-a-reservation-by-using-powershell"></a>A Foglalás felosztása a PowerShell-lel

1. Kérje le a Foglalás rendelési azonosítója a következő parancs futtatásával:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```

2. Kérje le a egy foglalás adatai:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Két a Foglalás felosztása és terjesztése a példányok:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. A hatókör a következő parancs futtatásával frissítheti:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Módosítása optimalizálni a fenntartott VM-példányok beállítása

 A fenntartott VM-példány vásárol, amikor kiválasztja példány mérete rugalmasan, vagy kapacitás prioritása. Példány mérete rugalmasan alkalmazza a foglalási kedvezményt más virtuális gépek ugyanazon [VM-méret csoport](https://aka.ms/RIVMGroups). Kapacitás prioritása fenntartja az Adatközpont-kapacitás az üzemelő példányok. Ezt a lehetőséget kínál további megbízhatósági arra, hogy indítsa el a Virtuálisgép-példányok, amikor szüksége van rájuk.

Alapértelmezés szerint a Foglalás hatóköre van osztva, ha a példány méretének rugalmasságot van. Az Adatközpont-kapacitás nem priorizálva virtuális gépek üzembe helyezése.

Lefoglalási, ahol a hatóköre egyetlen optimalizálhatja a kapacitás prioritása Virtuálisgép-példányok rugalmassága mérete helyett a foglalást.

A Foglalás az optimalizálási beállítás frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Módosítsa a **optimalizálás** beállítás.

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)
- [Fizessen elő az Azure foglalásai közül SUSE szoftvercsomagok – alap](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Megismerheti, hogyan kell alkalmazni a Virtuálisgép-foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [Megismerheti, hogyan kell alkalmazni a SUSE Linux Enterprise szoftverfrissítési csomag kedvezmény](../billing/billing-understand-suse-reservation-charges.md)
- [Megismerheti, hogyan más foglalás kedvezmények](billing-understand-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

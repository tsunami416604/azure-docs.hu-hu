---
title: Az Azure fenntartott VM-példányok kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan lehet módosítani az előfizetési hatókört és hozzáférés kezelése Azure Reserved VM Instances.
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
ms.date: 07/31/2018
ms.author: yashesvi
ms.openlocfilehash: 589afc736faaa210fdcd5cf6c79d10af4af3c0e9
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2018
ms.locfileid: "39502525"
---
# <a name="manage-reserved-instances-in-azure"></a>Az Azure-beli fenntartott példányok kezelése

Miután vásárol egy Azure fenntartott VM-példány, érdemes a fenntartott példány alkalmazása fut, a vásárlás során megadott egy másik előfizetésbe. Azt is megteheti Ha több előfizetéssel a megfelelő virtuális gépek futnak, előfordulhat, hogy módosítani szeretné a megosztott a fenntartott példány hatóköre. Maximalizálhatja a fenntartott példány kedvezményeket, ellenőrizze, hogy, hogy beszerezte példányok megegyezik-e az attribútumokat és van virtuális gépek száma futtatása. Az Azure fenntartott példányok kapcsolatos további információkért lásd: [pénzt takaríthat meg, az Azure-beli virtuális gépek előre és](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>A hatókör egy fenntartott példány módosítása
 A fenntartott példány vonatkozó kedvezmény felel meg a fenntartott példányok és a fenntartott példány hatóköre belül futtatott virtuális gépekre vonatkozik. A fenntartott példány hatóköre lehet egyetlen előfizetés vagy az összes előfizetést a számlázási környezetben. Ha egy előfizetés állítsa be a hatókört, a fenntartott példány megfeleltetett futó virtuális gépeket a kijelölt előfizetésben. Ha beállította a hatókör közös, az Azure megfelel a virtuális gépeken is futtatható, a számlázási környezetben belüli összes előfizetésre a fenntartott példány. A számlázási környezetben az adott nyelvtől függ a fenntartott példány megvásárlása használt előfizetés. További tudnivalókért lásd: [fenntartott példányokkal-beli virtuális gépek Előre fizetve](https://go.microsoft.com/fwlink/?linkid=861721).

A fenntartott példány hatóköre frissítése: 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a fenntartott példány.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. A hatókör módosítása. Ha módosítja a hatókör számára történő megosztása, ahol Ön a tulajdonosa az előfizetések csak kiválaszthatja. Csak a fenntartott példány számlázási ugyanabban a környezetben lévő előfizetések jelölhető ki. A számlázási környezetet választotta, amikor a fenntartott példány vásárolt az előfizetés határozza meg. A hatókör csak az utólagos számlázású MS-AZR - 0003P előfizetések és a vállalati ajánlat MS-AZR - 0017P előfizetések vonatkozik. A nagyvállalati szerződések fejlesztési és tesztelési előfizetések nem jogosultak beolvasni a fenntartott példány kedvezményt.

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>A fenntartott példány kezelésére jogosult felhasználók hozzáadása és módosítása
A fenntartott példány felügyeleti szerepkörökhöz a fenntartott példány személyek hozzáadásával delegálhat. Alapértelmezés szerint a személy, amely a fenntartott példány vásárolt, és a fiók rendszergazdája a fenntartott példányok a tulajdonosi szerepkörrel rendelkezik. 

Akkor is hozzáférésének kezelése a fenntartott példányok egymástól függetlenül az előfizetést, a fenntartott példány kedvezményt beolvasása. Amikor valaki engedélyezi a fenntartott példány kezeléséhez engedélyekkel, amely nem jogokat őket az előfizetés kezelése céljából. És ha valaki engedélyek belül a fenntartott példány hatóköre egy előfizetés kezelése céljából, amely nem tegye lehetővé számukra a fenntartott példány kezeléséhez szükséges jogokkal.
 
A fenntartott példány kezelési delegálása: 
1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2.  Válassza ki **minden szolgáltatás** > **foglalás** , amelyekhez hozzáfér a fenntartott példányok listáját.
3.  Válassza ki a fenntartott példány, amelyet szeretne más felhasználókkal való hozzáférés delegálására.
4.  Válassza ki **hozzáférés-vezérlés (IAM)** menüjében.
5.  Válassza ki **Hozzáadás** > **szerepkör** > **tulajdonosa** (vagy egy másik szerepkört, ha korlátozott hozzáférést szeretne). 
6. Írja be a tulajdonosként hozzáadni kívánt felhasználó e-mail-címét. 
7. Válassza ki a felhasználót, és válassza **mentése**.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Fenntartott VM-példány optimalizálása a virtuális gép mérete rugalmasságot, vagy kapacitás prioritása
 Virtuálisgép-példányok rugalmassága alkalmazza a foglalási kedvezményt más virtuális gépek ugyanazon [VM-méret csoport](https://aka.ms/RIVMGroups). Alapértelmezés szerint a Foglalás hatóköre van osztva, ha a példány méretének rugalmasságot és adatközpont-kapacitással priorizálva nem virtuális gépek üzembe helyezése. Lefoglalási, ahol a hatóköre egyetlen optimalizálhatja a kapacitás prioritása Virtuálisgép-példányok rugalmassága mérete helyett a foglalást. Kapacitás prioritása fenntartja az Adatközpont-kapacitás az üzemelő példányok, amikor szükséges, indítsa el a Virtuálisgép-példányok képességét való bizalmát.

A fenntartott példány hatóköre frissítése: 
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a fenntartott példány.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Az optimalizálási beállítás módosítható.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>Felosztása két fenntartott példányok be egyetlen fenntartott példány
 Több példány vásárol, miután érdemes példányok által biztosított fenntartott példányok hozzárendelése eltérő előfizetésekben. Alapértelmezés szerint az összes példányát (a vásárlás során megadott mennyiség) egy olyan hatókör - vagy egyetlen előfizetéssel rendelkezik, vagy megosztott. Például 10 Standard D2 virtuális vásárolt és a hatókör-előfizetéssé. a megadott Előfordulhat, hogy most szeretné hét a fenntartott példányok hatókörének módosítása egy előfizetéshez, és a fennmaradó 3, b felosztása egy fenntartott példány előfizetéshez lehetővé teszi, hogy a példányok részletes hatókör Management terjesztése. Válassza ki a megosztott hatókörrel egyszerűsítheti a hozzárendelést az előfizetések. Azonban a cost management vagy a költségvetés célokra, foglalhat le mennyiségeket az adott előfizetésekhez.

 Akkor is feloszthatja a fenntartott példány be két fenntartott példányok azonban PowerShell, CLI-t, vagy az API-n keresztül.

### <a name="split-a-reserved-instance-by-using-powershell"></a>A fenntartott példány felosztása a PowerShell használatával
1. Kérje le a fenntartott példány rendelés azonosítója a következő parancs futtatásával:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```

2. A fenntartott példány adatainak lekérése:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. A fenntartott példány két felosztása és terjesztése a példányok:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```

1. A hatókör a következő parancs futtatásával frissítheti:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="next-steps"></a>További lépések
Az Azure fenntartott példányok kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure Reserved VM Instances?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [A fenntartott példányokra vonatkozó kedvezmény alkalmazásának ismertetése](billing-understand-vm-reservation-charges.md)
- [Fenntartott példányok használata használatalapú fizetéses előfizetésnél](billing-understand-reserved-instance-usage.md)
- [Fenntartott példányok használata vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)
- [A fenntartott példányok díjában nem szereplő Windows-szoftverköltségek](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

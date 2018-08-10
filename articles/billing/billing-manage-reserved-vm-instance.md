---
title: Az Azure-fenntartások kezelése |} A Microsoft Docs
description: Megtudhatja, hogyan lehet módosítani az előfizetési hatókört és Azure lefoglalási-hozzáférés kezelése.
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
ms.openlocfilehash: d47c85d4197f45db50f1974b6faea270e6761237
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39628572"
---
# <a name="manage-reservations-for-resources-in-azure"></a>Az Azure-erőforrások lefoglalását kezelése

Miután vásárol egy Azure-foglalást, érdemes alkalmazni a Foglalás fut, a vásárlás során megadott egy másik előfizetésbe. Azt is megteheti Ha a megfelelő virtuális gépeket, SQL Database-adatbázisok vagy más erőforrások, a több előfizetést is fut, érdemes a megosztott foglalás hatókörének módosítása. A foglalási kedvezményt maximalizálása győződjön meg arról, hogy a példányok vásárolt megegyezik az attribútumokat és az erőforrások száma, hogy futtatása. További tudnivalókért lásd: [Azure foglalások](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>A foglalás hatókörének módosítása

 A foglalási kedvezményt virtuális gépeket, SQL-adatbázisok vagy más erőforrások, amely egyezik a foglalás, és futtassa a Foglalás hatókörön belül érvényes. A Foglalás hatóköre egy előfizetés vagy az összes előfizetést a számlázási környezetben lehet. Egyetlen előfizetéshez állítsa be a hatókört, ha a Foglalás megfeleltetett futó erőforrások a kijelölt előfizetésben. A megosztott hatókör beállítása, ha az Azure megfelel a Foglalás számlázási környezetében előfizetések futtató erőforrások. A számlázási környezetben szolgáltatás megvásárolni a foglalást használt előfizetés függ.

A Foglalás hatóköre frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. A hatókör módosítása. Ha módosítja a hatókör számára történő megosztása, ahol Ön a tulajdonosa az előfizetések csak kiválaszthatja. Csak a foglalást végrehajtó számlázási ugyanabban a környezetben található előfizetések jelölhető ki. A számlázási környezetben határozza meg az előfizetést, ha a Foglalás vásárolt választotta. A hatókör csak az utólagos számlázású MS-AZR - 0003P előfizetések és a vállalati ajánlat MS-AZR - 0017P előfizetések vonatkozik. A nagyvállalati szerződések fejlesztési és tesztelési előfizetések nem jogosultak a foglalási kedvezményt beolvasásához.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>A Foglalás kezelésére jogosult felhasználók hozzáadása és módosítása

A Foglalás felügyeleti szerepkörökhöz a Foglalás a személyek hozzáadásával delegálhat. Alapértelmezés szerint a személy, amely a Foglalás vásárolt, és a fiók rendszergazdája a tulajdonosi szerepkör rendelkezik a foglalást.

Akkor is hozzáférésének kezelése foglalások egymástól függetlenül az előfizetést, a foglalási kedvezményt beolvasása. Amikor engedélyezi a valaki engedélyeit egy foglalást, amely nem jogokat őket az előfizetés kezelése céljából. És ha valaki engedélyek belül a Foglalás hatóköre egy előfizetés kezelése céljából, amely nem jogokat őket a Foglalás kezelése.

A Foglalás kezelési delegálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalás** lista foglalásokat, amely hozzáfér a.
3. Válassza ki a foglalást, amelyet szeretne más felhasználókkal való hozzáférés delegálására.
4. Válassza ki **hozzáférés-vezérlés (IAM)** menüjében.
5. Válassza ki **Hozzáadás** > **szerepkör** > **tulajdonosa** (vagy egy másik szerepkört, ha korlátozott hozzáférést szeretne).
6. Írja be a tulajdonosként hozzáadni kívánt felhasználó e-mail-címét. 
7. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

## <a name="optimize-reserved-vm-instance-for-vm-size-flexibility-or-capacity-priority"></a>Fenntartott VM-példány optimalizálása a virtuális gép mérete rugalmasságot, vagy kapacitás prioritása

 Virtuálisgép-példányok rugalmassága alkalmazza a foglalási kedvezményt más virtuális gépek ugyanazon [VM-méret csoport](https://aka.ms/RIVMGroups). Alapértelmezés szerint a Foglalás hatóköre van osztva, ha a példány méretének rugalmasságot és adatközpont-kapacitással priorizálva nem virtuális gépek üzembe helyezése. Lefoglalási, ahol a hatóköre egyetlen optimalizálhatja a kapacitás prioritása Virtuálisgép-példányok rugalmassága mérete helyett a foglalást. Kapacitás prioritása fenntartja az Adatközpont-kapacitás az üzemelő példányok, amikor szükséges, indítsa el a Virtuálisgép-példányok képességét való bizalmát.

A Foglalás hatóköre frissítése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a foglalást.
4. Válassza a **Beállítások** > **Konfiguráció** lehetőséget.
5. Az optimalizálási beállítás módosítható.

## <a name="split-a-single-reservation-into-two-reservations"></a>Két foglalást, egy egyetlen foglalás felosztása

 Után több példányt vásárol, érdemes egy foglalást példányok hozzárendelése eltérő előfizetésekben. Alapértelmezés szerint az összes példányát (a vásárlás során megadott mennyiség) egy olyan hatókör - vagy egyetlen előfizetéssel rendelkezik, vagy megosztott. Például 10 Standard D2 virtuális vásárolt és a hatókör-előfizetéssé. a megadott Előfordulhat, hogy most szeretné hét lefoglalási hatókörének módosítása egy előfizetéshez, és a fennmaradó 3, b foglalás felosztása előfizetéshez lehetővé teszi, hogy a példányok részletes hatókör Management terjesztése. Válassza ki a megosztott hatókörrel egyszerűsítheti a hozzárendelést az előfizetések. Azonban a cost management vagy a költségvetés célokra, foglalhat le mennyiségeket az adott előfizetésekhez.

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

## <a name="next-steps"></a>További lépések

Azure foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

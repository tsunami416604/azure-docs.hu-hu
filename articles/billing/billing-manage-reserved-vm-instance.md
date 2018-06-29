---
title: Azure fenntartott Virtuálisgép-példányok kezelése |} Microsoft Docs
description: Ismerje meg, hogyan módosíthatja a előfizetési hatókört és Azure fenntartott Virtuálisgép-példányok való hozzáférés kezelése.
services: billing
documentationcenter: ''
author: vikramdesai01
manager: vikramdesai01
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: vikdesai
ms.openlocfilehash: ddb9d46dc2689b0dbcd8734e276916f7cd9d2728
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064009"
---
# <a name="manage-reserved-instances-in-azure"></a>Az Azure-ban fenntartott példányok kezelése

Miután vásárol egy Azure fenntartott Virtuálisgép-példány, érdemes lehet alkalmazni a fenntartott példány egy másik előfizetésben található a megadottól vásárlás során. Azt is megteheti Ha több előfizetése a hozzá tartozó virtuális gépek futnak, érdemes a megosztott fenntartott példány hatókörének módosításához. A fenntartott példány kedvezményeket maximalizálása érdekében győződjön meg arról, hogy a vásárolt példányok száma megegyezik-e az attribútumok és a virtuális gépet, hogy rendelkezik futó. Az Azure fenntartott példányaira vonatkozó további tudnivalókért lásd: [pénzt takaríthat meg, az Azure virtuális gépek előre fizető](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reserved-instance"></a>A fenntartott példányához hatókörének módosítása
 A fenntartott példány kedvezményes felel meg a fenntartott példányát, és futtassa a fenntartott példány hatókörön belüli virtuális gépek vonatkozik. A hatókör egy fenntartott példány lehet egyetlen előfizetés vagy az összes előfizetést a számlázási környezetben. A hatókör megadásával egyetlen előfizetés, a fenntartott példány megfeleltetett futó virtuális gépeket a kijelölt előfizetésben. Ha beállította a hatókör megosztott, Azure megfelel az összes olyan előfizetést, a számlázási környezeten belül futó virtuális gépek fenntartott példányt. A számlázási környezet vásárolható meg a fenntartott példány használt előfizetés függ. További tudnivalókért lásd: [fenntartott példányok rendelkező virtuális gépek előtti fizetési](https://go.microsoft.com/fwlink/?linkid=861721).

A hatókör egy fenntartott példány frissítése: 
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki a fenntartott példányt.
4. Válassza ki **beállítások** > **konfigurációs**.
5. A hatókör módosítása. Ha módosítja a hatókörre osztja, jelölhet ki a tulajdonos előfizetések. Csak belül a fenntartott példány azonos számlázási összefüggésben előfizetések választható ki. A számlázási környezetben az előfizetés, amikor a fenntartott példány vásárolt kiválasztott határozza meg. A hatókör csak a használatalapú ajánlatok MS-AZR - 0003P előfizetések és a vállalati ajánlat MS-AZR - 0017P előfizetések vonatkozik. A nagyvállalati szerződés fejlesztési és tesztelési célú előfizetések nem jogosultak a fenntartott példány kedvezményeket eléréséhez.

## <a name="split-a-single-reserved-instance-into-two-reserved-instances"></a>A két fenntartott példányok fenntartott egypéldányos felosztása
 Után több példány vásárol, érdemes lehet belül a fenntartott példánya példányok hozzárendelése másik előfizetést. Alapértelmezés szerint az összes példányát (a vásárlás során megadott mennyiséget) egy olyan hatókör - vagy egyetlen előfizetéssel rendelkezik, vagy megosztott. Például 10 Standard D2 VM vásárolt, és az előfizetés A. hatókörben megadott Előfordulhat, hogy szeretné-előfizetéshez A hét fenntartott példányok hatókörének módosítása, és a fennmaradó 3, b felosztásával egy fenntartott példány előfizetéshez lehetővé teszi részletes hatókörök kezelése példányainak terjesztése. Válassza ki a megosztott hatókör egyszerűbbé teheti az előfizetések a hozzárendelést. De költség felügyeleti vagy a tervezés célokra, foglalhatja le mennyiségeket adott előfizetésekhez.

 Fel egy fenntartott példányának két fenntartott példányok azonban PowerShell parancssori felület, vagy az API-n keresztül.

### <a name="split-a-reserved-instance-by-using-powershell"></a>A fenntartott példánya felosztása a PowerShell használatával
1. Töltse le a fenntartott Példányazonosítója sorrendje a következő parancs futtatásával:

    ```powershell
    # Get the reserved instance orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Ezzel az adatokat egy fenntartott példány:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Ossza fel kettő a fenntartott példányának, és terjeszteni a példányok:

    ```powershell
    # Split the reserved instance. The sum of the reserved instances, the quantity, must equal the total number of instances in the reserved instance that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. A hatókör frissítheti a következő parancs futtatásával:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reserved-instance"></a>A fenntartott példánya kezelésére jogosultak felhasználók felvétele és módosítása
Egy fenntartott példány felügyeleti delegálhatja személyek felvételével szerepkörök fenntartott-példányon. Alapértelmezés szerint a személy, amely a fenntartott példány vásárolt, és a fiók rendszergazdája rendelkezzen a tulajdonosi szerepkört fenntartott-példányon. 

Kezelheti hozzáférés fenntartott példányokhoz egymástól függetlenül olyan előfizetést, amely az beszerzése a fenntartott példány kedvezményeket. Ha valaki adjon egy fenntartott példány kezeléséhez szükséges jogokat kapjon, amely nem számukra jogosultsága ahhoz, hogy az előfizetés kezelése. Így ha valaki a fenntartott példány hatókörén belül előfizetés kezeléséhez szükséges jogokat kapjon, amely nem azok jogosultsága ahhoz, hogy a fenntartott példány kezelése.
 
A fenntartott példányához kezelési delegálása: 
1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2.  Válassza ki **minden szolgáltatás** > **foglalás** fenntartott példányok hozzáféréssel rendelkező elemet.
3.  Válassza ki a fenntartott példányt, amelyet szeretne hozzáférést biztosíthat más felhasználók.
4.  Válassza ki **hozzáférés-vezérlés (IAM)** a menüben.
5.  Válassza ki **Hozzáadás** > **szerepkör** > **tulajdonos** (vagy egy másik szerepkört, ha azt szeretné, hogy korlátozott hozzáférést). 
6. Írja be a felhasználó hozzá szeretne adni a tulajdonos e-mail címe. 
7. A felhasználó, majd válassza ki és **mentése**.

## <a name="next-steps"></a>További lépések
Azure fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Mik azok a Azure fenntartott Virtuálisgép-példányok?](billing-save-compute-costs-reservations.md)
- [A virtuális gépek Azure fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

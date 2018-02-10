---
title: "Azure fenntartott virtuálisgép-példányok kezelése |} Microsoft Docs"
description: "Ismerje meg, hogyan módosíthatja a előfizetési hatókört és Azure fenntartott Virtuálisgép-példányok való hozzáférés kezelése."
services: billing
documentationcenter: 
author: vikramdesai01
manager: vikramdesai01
editor: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2017
ms.author: vikdesai
ms.openlocfilehash: 8f75bab7fc795a59dabfedca5a4889622f66b73a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="manage-reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok kezelése

Miután vásárol egy Azure fenntartott Virtuálisgép-példány, érdemes lehet alkalmazni a Foglalás a megadottól beszerzési során egy másik előfizetést. Azt is megteheti Ha több előfizetése a hozzá tartozó virtuális gépek futnak, érdemes lehet osztani a fenntartási hatókör módosítása. A foglalási kedvezményeket maximalizálása érdekében győződjön meg arról, hogy a vásárolt példányok száma megegyezik-e az attribútumok és a virtuális gépet, hogy rendelkezik futó. Fenntartott virtuálisgép-példányok kapcsolatos további információkért lásd: [pénzt takaríthat meg, az Azure virtuális gépek előre fizető](https://go.microsoft.com/fwlink/?linkid=862121).

## <a name="change-the-scope-for-a-reservation"></a>Módosítsa a Foglalás hatóköre
 A foglalási kedvezményes felel meg a Foglalás és a fenntartás hatókörén belül futtatott virtuális gépére érvényes lesz. A Foglalás hatóköre lehet egyetlen előfizetés vagy az összes előfizetést a számlázási környezetben. A hatókör megadásával egyetlen előfizetés, a Foglalás megfeleltetett futó virtuális gépeket a kijelölt előfizetésben. Ha beállította a hatókör megosztott, Azure megegyezik a Foglalás az előfizetéseket a számlázási környezeten belül futó virtuális gépek számára. A számlázási környezet vásárolható meg a Foglalás használt előfizetés függ. További tudnivalókért lásd: [fenntartott Virtuálisgép-példányok rendelkező virtuális gépek előtti fizetési](https://go.microsoft.com/fwlink/?linkid=861721).

A Foglalás körét frissítése: 
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2. Válassza ki **további szolgáltatások** > **foglalások**.
3. Válassza ki a foglalás.
4. Válassza ki **beállítások** > **konfigurációs**.
5. A hatókör módosítása. Ha módosítja a hatókörre osztja, jelölhet ki a tulajdonos előfizetések. Csak a Foglalás azonos számlázási összefüggésben belül előfizetések választható ki. A számlázási környezetben az előfizetést, ha a Foglalás vásárolt kiválasztott határozza meg. A hatókör csak a használatalapú ajánlatok MS-AZR - 0003P előfizetések és a vállalati ajánlat MS-AZR - 0017P előfizetések vonatkozik. A nagyvállalati szerződés fejlesztési és tesztelési célú előfizetések nem jogosultak a Foglalás kedvezményeket eléréséhez.

## <a name="split-a-single-reservation-into-two-reservations"></a>A két foglalások egyetlen foglalás felosztása
 Után több példány vásárol, érdemes lehet foglalás példányokat hozzárendelése másik előfizetést. Alapértelmezés szerint az összes példányát (a vásárlás során megadott mennyiséget) egy olyan hatókör - vagy egyetlen előfizetéssel rendelkezik, vagy megosztott. Például 10 Standard D2 VM vásárolt, és az előfizetés A. hatókörben megadott Előfordulhat, hogy most szeretné 7 fenntartott Virtuálisgép-példányok hatókörének módosítása A előfizetéshez, és a fennmaradó 3, b felosztásával foglalás előfizetéshez lehetővé teszi részletes hatókörök kezelése példányainak terjesztése. Válassza ki a megosztott hatókör egyszerűbbé teheti az előfizetések a hozzárendelést. De költség felügyeleti vagy a tervezés célokra, foglalhatja le mennyiségeket adott előfizetésekhez.

 Fel a két foglalások foglalás azonban PowerShell parancssori felület, vagy az API-n keresztül.

### <a name="split-a-reservation-by-using-powershell"></a>Ossza fel a Foglalás PowerShell használatával
1. Töltse le a Foglalásazonosító sorrendje a következő parancs futtatásával:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzureRmReservationOrder
    ```
2. Töltse le a Foglalás részletei:

    ```powershell
    Get-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```
3. Ossza fel a Foglalás két és terjeszteni a példányok:

    ```powershell
    # Split the reservation. The sum of the Reserved VM instances, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
1. A hatókör frissítheti a következő parancs futtatásával:

    ```powershell
    Update-AzureRmReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Adja hozzá, vagy módosítsa a Foglalás is kezelő felhasználóknak
A foglalási felügyeleti személyek ad hozzá a Foglalás a szerepkörök delegálhat. Alapértelmezés szerint a személy, hogy a Foglalás vásárolt és a fiókja rendszergazda rendelkezik a Foglalás a tulajdonosi szerepkört. 

Akkor is hozzáférésének kezelése foglalások egymástól függetlenül olyan előfizetést, amely az beszerzése a Foglalás kedvezményeket. Ha valaki adjon foglalás kezeléséhez szükséges jogokat kapjon, amely nem számukra jogosultsága ahhoz, hogy az előfizetés kezelése. Így ha valaki a Foglalás hatókörén belül előfizetés kezeléséhez szükséges jogokat kapjon, amely nem azok jogosultsága ahhoz, hogy a Foglalás kezelése.
 
A Foglalás kezelési delegálása: 
1.  Jelentkezzen be az [Azure portálra](https://portal.azure.com).
2.  Válassza ki **több szolgáltatások** > **foglalás** a lista foglalásokat, amely rendelkezik hozzáféréssel.
3.  Válassza ki a kívánt hozzáférést biztosíthat más felhasználók foglalás.
4.  Válassza ki **hozzáférés-vezérlés (IAM)** a menüben.
5.  Válassza ki **Hozzáadás** > **szerepkör** > **tulajdonos** (vagy egy másik szerepkört, ha azt szeretné, hogy korlátozott hozzáférést). 
6. Írja be a felhasználó hozzá szeretne adni a tulajdonos e-mail címe. 
7. A felhasználó, majd válassza ki és **mentése**.

## <a name="next-steps"></a>További lépések
Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [A virtuális gépek fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok](billing-save-compute-costs-reservations.md)
- [A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

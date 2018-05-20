---
title: A vállalati - Azure számlázási Azure fenntartott példány használatának megértéséhez |} Microsoft Docs
description: Olvassa el a megértése, hogyan kell alkalmazni az Azure fenntartott Virtuálisgép-példány a nagyvállalati beléptetés a használati útmutató.
services: billing
documentationcenter: ''
author: manish-shukla01
manager: manshuk
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: manshuk
ms.openlocfilehash: a92fce33b194c5cb7b763930e7fd11135f9fbd4f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>A nagyvállalati beléptetés használata Azure fenntartott példány ismertetése
Fenntartott példány kihasználtsági értelmezése segítségével a **ReservationId** a [foglalások lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és használata a fájl a [EA portal](https://ea.azure.com). Azt is láthatja a használati összefoglaló szakaszában foglalt példány használatát [EA portal](https://ea.azure.com).

>[!NOTE]
>Ha beszerezte a fenntartott példány, használatalapú számlázási környezetben, tekintse meg [fenntartott példány megértéséhez használati a használatalapú fizetéses előfizetésre.](billing-understand-reserved-instance-usage.md)

A következő szakasz azt feltételezik, hogy egy standard D1 v2 Windows virtuális Gépet a US régió keleti és a következőképpen néz információkat a következő táblázat a fenntartott példányát futtatja:

| Mező | Érték |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Mennyiség |1|
|SKU | Standard_D1|
|Régió | eastus |

## <a name="reserved-instance-application"></a>Fenntartott példány alkalmazás

A virtuális Gépet, a hardver része van tartalmazza, mivel a központilag telepített virtuális gép megfelel a fenntartott példány attribútumokat. Milyen Windows szoftverek nem fedi le a fenntartott példány megtekintéséhez nyissa meg az Azure fenntartott Virtuálisgép-példányok származó szoftverek költségeit, írja be a [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md)


### <a name="reserved-instance-usage-in-csv"></a>Fürt megosztott kötetei szolgáltatás fenntartott példány használata
Letöltheti a EA Használati csv EA portálról. További információ a szűrje a letöltött csv-fájlban, és írja be a **ReservationID**. Az alábbi képernyőfelvételen látható a fenntartott példányhoz kapcsolódó mezők:

![Nagyvállalati Szerződés (EA) csv Azure fenntartott példány](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **ReservationId** további információ a mező juttatás alkalmazása a virtuális gép használt fenntartott példányt jelenti.
2. ConsumptionMeter a MeterId a virtuális gép.
3. A foglalási mérő $0 költség Ez azért, mert a fenntartott példány által már fizetett a virtuális gép futtatásának. 
4. Standard szintű, D1 egy vCPU virtuális gép és a virtuális gép központi telepítése Azure hibrid juttatás nélkül. Ezért a mérési hozzá van rendelve a felesleges ingyenesen elérhető Windows szoftver. Lásd: [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md) a mérő megfelelő D sorozat 1 core virtuális gép található. Azure hibrid juttatás használata esetén ez külön díj nem alkalmazható.

### <a name="reserved-instance-usage-in-usage-summary-page-in-ea-portal"></a>Használat összegzése lapjára EA fenntartott példány használata

Használati is megjelennek használati összefoglaló adatai EA portál példány fenntartva: ![nagyvállalati szerződés (EA) használatának összegzése](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Meg nem felszámított hardverösszetevő a virtuális gép fenntartott példány módosított. 
2. Van szó, a szoftver a Windows Azure hibrid juttatás nem használt. 

## <a name="next-steps"></a>További lépések
Azure fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Kevesebbet költeni a virtuális gépek Azure fenntartott osztályt](billing-save-compute-costs-reservations.md)
- [A virtuális gépek fenntartott osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
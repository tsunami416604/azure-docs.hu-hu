---
title: A vállalati Azure fenntartott példány használatának megértéséhez |} Microsoft Docs
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
ms.openlocfilehash: d6e8b2544f919abeb7fde0e37fc12bd29f0171ef
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064012"
---
# <a name="understand-azure-reserved-instance-usage-for-your-enterprise-enrollment"></a>A nagyvállalati beléptetés használata az Azure fenntartott példány ismertetése
Egy fenntartott példány kihasználtsági értelmezése segítségével a **ReservationId** a [foglalások lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és használata a fájl a [EA portal](https://ea.azure.com). Azt is láthatja a használati összefoglaló szakaszában foglalt példány használatát [EA portal](https://ea.azure.com).

>[!NOTE]
>Ha beszerezte a fenntartott példány, használatalapú számlázási környezetben, tekintse meg [megértése fenntartott példány használati a használatalapú fizetéses előfizetésre.](billing-understand-reserved-instance-usage.md)

A következő szakasz azt feltételezik, hogy a Windows Standard D1 v2 virtuális gép fut a US régió keleti és a következő táblázatban foglalt példány információk tűnik:

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

Fenntartott példány használati is megjelennek EA portál használati összefoglaló adatai: ![nagyvállalati szerződés (EA) használatának összegzése](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Meg nem felszámított hardverösszetevő a virtuális gép fenntartott példány módosított. 
2. Van szó, a szoftver a Windows Azure hibrid juttatás nem használt. 

## <a name="next-steps"></a>További lépések
Azure fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Mik azok a Azure fenntartott Virtuálisgép-példányok?](billing-save-compute-costs-reservations.md)
- [A virtuális gépek Azure fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Az Azure-ban fenntartott példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
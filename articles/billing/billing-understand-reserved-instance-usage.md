---
title: A használatalapú fizetéses előfizetésre - Azure számlázási Azure fenntartott példány használatának megértéséhez |} Microsoft Docs
description: Megtudhatja, hogyan olvassa el a használati megértése, hogyan kell alkalmazni az Azure fenntartott Virtuálisgép-példány a használatalapú fizetéses előfizetésre.
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
ms.openlocfilehash: 7bf4aea86d4d430c15d60a8d73365705ace18b5a
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez

A fenntartott virtuális gép Azure példány használata az értelmezése a ReservationId segítségével [foglalási lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és használata a fájl a [fiókok Azure portal](https://account.azure.com).


>[!NOTE]
>Ez a cikk nem vonatkozik a Nagyvállalati ügyfelek. Ha az ügyfél egy EA, lásd: [fenntartott példány megérteni a nagyvállalati beléptetés használata.](billing-understand-reserved-instance-usage-ea.md) Ez a cikk feltételezi, hogy egy-egy előfizetéshez fenntartott példány van hozzárendelve. A fenntartott példány egynél több előfizetésnek alkalmazza, ha a fenntartott példány juttatás előfordulhat, hogy span használati több csv-fájl. 

A következő szakasz azt feltételezik, hogy a US régió-keleti Standard_DS1_v2 Windows virtuális gép és a következőképpen néz információkat a következő táblázat a fenntartott példányát futtatja:

| Mező | Érték |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Mennyiség |1|
|SKU | Standard_DS1_v2|
|Régió | eastus |

## <a name="reserved-instance-application"></a>Fenntartott példány alkalmazás

A virtuális Gépet, a hardver része van tartalmazza, mivel a központilag telepített virtuális gép megfelel a fenntartott példány attribútumokat. Milyen Windows szoftverek nem fedi le a fenntartott példány megtekintéséhez keresse fel [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Fürt megosztott kötetei szolgáltatás utasítás szakasza
Ez a szakasz a fürt megosztott kötetei szolgáltatás a a teljes használatát mutatja a fenntartott példányhoz. A szűrőt, amely tartalmazza a "Foglalási-" és az alábbi képernyőfelvételen a adatok tűnik mérő alkategóriát mező: ![szűrt fenntartott példány használati adatai és díjak képernyőképe](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Foglalási-alap VG sor tartalmaz a fenntartott példány szereplő órák száma. A sor nem $0,00, mert a fenntartott példányát tartalmazza azt. Windows-foglalás Svr (1 mag) sor tartalmazza a Windows szoftverek költségét.

### <a name="daily-usage-section-of-csv"></a>A fürt megosztott kötetei szolgáltatás napi használati adatai
További információ a szűrésére, és írja be a **Foglalásazonosító**. Az alábbi képernyőfelvételen látható a fenntartott példányhoz kapcsolódó mezőket. 

![Képernyőkép a napi használat részleteiről és díjak](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **ReservationId** további információ a mező a juttatás alkalmazása a virtuális gép használt fenntartott példány.
2. A rendszer a mérési azonosítóját, a virtuális gép ConsumptionMeter.
3. Base-foglalás VM mérő alkategóriát vonal jelzi a $0 sorában utasítás szakaszban található. A virtuális gép futtatásának már ki van fizetve a fenntartott példánya.
4. Ez a fenntartott példány mérő azonosítója. A mérő költsége $0 lesz. Minden virtuális gép által fenntartott példány a CSV-kódot, hogy figyelembe vegye a költségeket a MeterId van. 
5. Standard_DS1_v2 egy vCPU virtuális gép és a virtuális gép központi telepítése Azure hibrid juttatás nélkül. Ezért a mérési hozzá van rendelve a felesleges ingyenesen elérhető Windows szoftver. Lásd: [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md) a mérő megfelelő D sorozat 1 core virtuális gép található. Azure hibrid juttatás használata esetén a felesleges ingyenesen elérhető nem lesz alkalmazva. 

## <a name="next-steps"></a>További lépések
Fenntartott példányok kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- [Kevesebbet költeni a virtuális gépek Azure fenntartott osztályt](billing-save-compute-costs-reservations.md)
- [A virtuális gépek fenntartott osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott példányok kezelése](billing-manage-reserved-vm-instance.md)
- [A fenntartott példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdései további, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.

---
title: "A használatalapú fizetéses előfizetésre Azure fenntartott példány használatának megértéséhez |} Microsoft Docs"
description: "Útmutató: az használatának megértése alkalmazását fenntartott példány használatalapú fizetés előfizetés olvasása"
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: 29f153803d5eb74e2d287d97cf9436e81b2a3e20
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="understand-reserved-instance-usage-for-your-pay-as-you-go-subscription"></a>A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez

Fenntartott példány kihasználtsági értelmezése a ReservationId segítségével [foglalási lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade ) és a használati fájlt [fiókok Azure portálon.](https://account.azure.com)


>[!NOTE]
>Ez a cikk nem vonatkozik a Nagyvállalati ügyfelek. Ha az ügyfél egy EA, lásd: [fenntartott példány megérteni a nagyvállalati beléptetés használata.](billing-understand-reserved-instance-usage-ea.md) Ez a cikk feltételezi, hogy a foglalás vonatkozik-e az egy-egy előfizetéshez. A Foglalás egynél több előfizetésnek alkalmazza, ha a Foglalás juttatás előfordulhat, hogy span használati több csv-fájl. 

A következő szakasz azt feltételezik, hogy a Standard_DS1_v2 Windows virtuális gépek futnak a US régió keleti és a következő táblázat a foglalási információkat tűnik:

| Mező | Érték |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Mennyiség |1|
|SKU | Standard_DS1_v2|
|Régió | eastus |

## <a name="reservation-application"></a>Foglalási alkalmazás

A virtuális Gépet, a hardver része van tartalmazza, mivel a központilag telepített virtuális gép megfelel a Foglalás attribútumok. Milyen Windows szoftverek nem fedi le a fenntartott példány megtekintéséhez keresse fel [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv"></a>Fürt megosztott kötetei szolgáltatás utasítás szakasza
Ez a szakasz a fürt megosztott kötetei szolgáltatás a a teljes használata a Foglalás jeleníti meg. A szűrőt, amely tartalmazza a "Foglalási-" és az alábbi képernyőfelvételen a adatok tűnik mérő alkategória mező: ![közvetlen utasítás foglalás](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

Foglalási-alap VG sor tartalmaz a Foglalás szereplő órák száma. A sor nem $0,00, mert a fenntartott példányát tartalmazza azt. Windows-foglalás Svr (1 mag) sor tartalmazza a Windows szoftverek költségét.

### <a name="daily-usage-section-of-csv"></a>A fürt megosztott kötetei szolgáltatás napi használati adatai
További információ a szűrésére, és írja be a foglalási azonosítóját. Az alábbi képernyőfelvételen látható a Foglalás kapcsolódó mezőket. 

![Napi használati díjak](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. További információ mezőben ReservationId a Foglalás használt juttatás alkalmazása a virtuális gép.
2. A rendszer a mérési azonosítóját, a virtuális gép ConsumptionMeter.
3. Base-foglalás VM mérő alkategória vonal jelzi a $0 sorában utasítás szakaszban található. Ez a virtuális gép futtatásának már fizeti a foglalás.
4. Ez a Foglalás mérő azonosítója. A mérő költsége $0 lesz. Minden virtuális gép által fenntartott példány a CSV-kódot, hogy figyelembe vegye a költségeket a MeterId van. 
5. Standard_DS1_v2 egy vCPU virtuális gép és a virtuális gép központi telepítése Azure hibrid juttatás nélkül. Ezért a mérési hozzá van rendelve a felesleges ingyenesen elérhető Windows szoftver. Lásd: [Azure tartalék virtuális gép példányok Windows szoftverek költségeit.](billing-reserved-instance-windows-software-costs.md) a mérő megfelelő D sorozat 1 core virtuális gép található. Azure hibrid juttatás használata esetén a felesleges ingyenesen elérhető nem lesz alkalmazva. 

## <a name="next-steps"></a>További lépések
Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [A virtuális gépek fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott virtuálisgép-példányok kezelése](billing-manage-reserved-vm-instance.md)
- [Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok](billing-save-compute-costs-reservations.md)
- [A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
- [A Windows szoftverek költségeit fenntartott példányok nem találhatók](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha további segítségre van, [forduljon a támogatási szolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma elhárítva gyors eléréséhez.
---
title: Az Azure-foglalások használati adatai használatalapú fizetéses előfizetésre |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatja be a használat megértéséhez, hogyan kell alkalmazni a foglalást az Azure használatalapú fizetéses előfizetésében.
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
ms.date: 08/08/2018
ms.author: cwatson
ms.openlocfilehash: c81db66637a4c56a36b6995ad8df0fe1967d08ef
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47391670"
---
# <a name="understand-azure-reservation-usage-for-your-pay-as-you-go-subscription"></a>A használatalapú fizetéses előfizetést Azure foglalás használati adatai

Használja a reservationid értékhez a [foglalási lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és a használatot részletező fájl a a [fiókok az Azure portal](https://account.azure.com) a Foglalás használat kiértékeléséhez.

Ha nagyvállalati szerződéssel rendelkező ügyfél, tekintse meg [a nagyvállalati beléptetés foglalás használati adatai.](billing-understand-reserved-instance-usage-ea.md).

Ez a cikk azt feltételezi, hogy a Foglalás egyetlen előfizetéshez van alkalmazva. Ha a Foglalás egynél több előfizetéssel alkalmazza, a foglalási kedvezmény használati több CSV-fájlok magában foglalhat.

## <a name="usage-for-reserved-virtual-machine-instances"></a>Fenntartott virtuálisgép-példányok használatát

A következő szakaszokban feltételezzük, hogy futtatja a keleti régiójában és a fenntartott virtuális gép példány adatokat úgy tűnik, az alábbi táblázat a Standard_DS1_v2 Windows virtuális gépek:

| Mező | Érték |
|---| :---: |
|ReservationId |8117adfb-1d94-4675-be2b-f3c1bca808b6|
|Mennyiség |1|
|SKU | Standard_DS1_v2|
|Régió | eastus |

A hardver része a virtuális gép hatálya alá tartozó, mert az üzembe helyezett virtuális gép megfelel a Foglalás attribútumok. A fenntartott VM-példány nem rendelkezik Windows szoftverek megtekintéséhez lásd: [Azure számára fenntartott VM-példányok Windows szoftverek díjait](billing-reserved-instance-windows-software-costs.md)

### <a name="statement-section-of-csv-file-for-vms"></a>A virtuális gépek CSV-fájl utasítás szakasz

Ez a szakasz a CSV-fájl látható a teljes felhasználás a foglalás. A szűrőt a alkalmazni a **fogyasztásmérő alkategóriája** tartalmazó mezőt **"Foglalás-"**. Hiba a következő képernyőképhez hasonlóan jelenik meg:

![Képernyőkép a szűrt foglalás használatra vonatkozó részletek és a költségek](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-statements.png)

A **foglalás alap VM** sort tartalmaz a Foglalás alá esnek órák száma. Ez a sor $0,00 azért a Foglalás mindegyikhez megoldást jelent. A **foglalás – Windows fenntartott példányok (1 mag)** sor fedezi a Windows szoftverek költségeit.

### <a name="daily-usage-section-of-csv-file"></a>Napi használat szakasz CSV-fájl

Szűrés **további adatok** , és írja be a **Foglalásazonosító**. Az alábbi képernyőfelvételen a Foglalás kapcsolódó mezőket.

![Képernyőkép a napi használati adatokat és díjak](./media/billing-understand-reserved-instance-usage/billing-payg-reserved-instance-csv-details.png)

1. **Reservationid értékhez** a a **további adatok** mező a alkalmazni a virtuális gép foglalást.
2. **ConsumptionMeter** a mérőszám azonosítója van a virtuális gép számára.
3. A **foglalás alap VM** **fogyasztásmérő alkategóriája** vonal jelzi a 0 USD költséget utasítás szakaszban. Ez a virtuális gép futtatásával járó költségeket a Foglalás már fizeti.
4. **Mérőszám azonosítója** a Foglalás mérő azonosítója. Ez az érték költsége 0 USD. Ez a mérőszám azonosítója megjelenik az összes virtuális gép, amely jogosult a foglalási kedvezményt.
5. Standard_DS1_v2 egy vCPU virtuális gép és a virtuális gép központi telepítése az Azure Hybrid Benefit nélkül. Tehát ez az érték magában foglalja a Windows-szoftverek külön díjfizetés nélkül. A mérőszám a D sorozatú virtuális gép 1 mag, lásd: [Azure számára fenntartott VM-példányok Windows szoftverek díjait](billing-reserved-instance-windows-software-costs.md). Ha az Azure Hybrid Benefit, a rendszer nem alkalmazza ezt külön díjfizetés nélkül.

## <a name="usage-for-sql-database-reserved-capacity-reservations"></a>Használat az SQL Database szolgáltatás számára fenntartott kapacitás foglalások

A következő szakaszokban feltételezzük, hogy futtatja a keleti régiójában és a foglalási adatokat úgy tűnik, az alábbi táblázat, egy SQL Database Gen 4:

| Mező | Érték |
|---| --- |
|ReservationId |446ec809-423D-467c-8c5c-bbd5d22906b1|
|Mennyiség |2|
|Product| Az SQL Database Gen 4 (2 mag)|
|Régió | eastus |

### <a name="statement-section-of-csv-file"></a>CSV-fájl utasítás szakasz

Szűrés **fenntartott példány használata** fogyasztásmérő neve. Hiba a következő képernyőképhez hasonlóan jelenik meg:

![Az SQL Database szolgáltatás számára fenntartott kapacitás CSV-példafájlja](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-statements.png)

A **fenntartott példány használata** sor tartalmaz a Foglalás hatálya üzemóra teljes száma. Ez a sor 0 USD a fenntartás költsége hatálya alá.

### <a name="detail-section-of-csv-file"></a>CSV-fájl szakasza

Szűrés **további adatok** , és írja be a **Foglalásazonosító**. Az alábbi képernyőfelvételen az SQL Database szolgáltatás számára fenntartott kapacitás foglalás kapcsolódó mezőket.

![Az SQL Database szolgáltatás számára fenntartott kapacitás CSV-példafájlja](./media/billing-understand-reserved-instance-usage/billing-payg-sql-db-reserved-capacity-csv-details.png)

1. **Reservationid értékhez** a a **további adatok** mező a alkalmazni az SQL-adatbázis-erőforrás SQL Database szolgáltatás számára fenntartott kapacitás foglalás.
2. **ConsumptionMeter** az SQL Database erőforrás mérő azonosítója.
3. A **mérőszám azonosítója** foglalás mérőszám. Ez az érték költsége 0 USD. Bármely SQL Database-erőforrásokat, amelyek esetében a foglalási kedvezményt a mérőszám azonosítója jeleníti meg a CSV-fájlt.

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Fizessen elő az Azure fenntartott VM-példányok a virtuális gépekkel](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás](../sql-database/sql-database-reserved-capacity.md)
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha további kérdése van, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.

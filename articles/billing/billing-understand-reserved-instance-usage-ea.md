---
title: Az Azure-foglalások vállalati használati adatai |} A Microsoft Docs
description: Ismerje meg, hogyan olvashatja be a használat megértéséhez, hogyan kell alkalmazni az Azure a nagyvállalati beléptetés-foglalás.
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
ms.date: 09/28/2018
ms.author: cwatson
ms.openlocfilehash: 87f6437f836d46fc24ae016823a1b091bcd81943
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582049"
---
# <a name="understand-azure-reservation-usage-for-your-enterprise-enrollment"></a>A nagyvállalati beléptetés Azure foglalás használati adatai

Használja a **reservationid értékhez** a [foglalások lap](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=Reservations&Microsoft_Azure_Reservations=true#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) és a használatot részletező fájl a a [a nagyvállalati szerződések portáljának](https://ea.azure.com) kiértékelni a Foglalás használat. Megtekintheti a Foglalás használati használat összefoglaló szakaszában is [a nagyvállalati szerződések portáljának](https://ea.azure.com).

Ha beszerezte a foglalást, használatalapú számlázási környezetben, lásd: [a használatalapú fizetéses előfizetést foglalás használatának megértéséhez.](billing-understand-reserved-instance-usage.md)

## <a name="usage-for-reserved-virtual-machines-instances"></a>Fenntartott virtuálisgép-példányok használatát

A következő szakaszokban feltételezzük, hogy Windows Standard D1 v2 virtuális gép fut a keleti régiójában és az alábbi táblázat a Foglalás információk tűnik:

| Mező | Érték |
|---| --- |
|ReservationId |8f82d880-d33e-4e0d-bcb5-6bcb5de0c719|
|Mennyiség |1|
|SKU | Standard_D1|
|Régió | eastus |

A hardver része a virtuális gép hatálya alá tartozó, mert az üzembe helyezett virtuális gép megfelel a Foglalás attribútumok. A foglalás nem rendelkezik Windows szoftverek megtekintéséhez lásd: [Azure számára fenntartott VM-példányok Windows szoftverek díjait](billing-reserved-instance-windows-software-costs.md).

### <a name="usage-in-csv-file-for-reserved-vm-instances"></a>Használat a Reserved VM Instances CSV-példafájlja

Letöltheti a vállalati használati CSV-fájl a vállalati portálról. Szűrés a CSV-fájl **további adatok** , és írja be a **reservationid értékhez**. Az alábbi képernyőfelvételen a Foglalás kapcsolódó mezőket:

![Nagyvállalati Szerződés (EA) fürt megosztott kötetei szolgáltatás az Azure-foglalás](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-csv.png)

1. **Reservationid értékhez** a **további adatok** mező képviseli a alkalmazni a virtuális gép foglalást.
2. **ConsumptionMeter** a mérőszám azonosítója van a virtuális gép számára.
3. **Mérőszám azonosítója** 0 USD költséget a Foglalás mérőszám. A fenntartott VM-példány fizeti a futó virtuális gép költsége.
4. Standard_D1 egy vCPU virtuális gép és a virtuális gép központi telepítése az Azure Hybrid Benefit nélkül. Tehát ez az érték magában foglalja a Windows-szoftverek külön díjfizetés nélkül. A mérőszám, a D sorozatú virtuális gép 1 mag, lásd: [Azure számára fenntartott VM-példányok Windows szoftverek díjait](billing-reserved-instance-windows-software-costs.md).  Ha az Azure Hybrid Benefit, ez külön díjfizetés nélkül nem alkalmazza.

## <a name="usage-for-sql-database--cosmos-db-reserved-capacity-reservations"></a>Az SQL Database és a Cosmos DB használatát fenntartott kapacitás foglalások

A következő szakaszok példaként az Azure SQL Database használatával a használati jelentés ismertetik. Ugyanezen lépések segítségével használatának megtekintése az Azure Cosmos DB is. 

Tegyük fel, hogy futtatja a keleti régiójában és a foglalási adatokat úgy tűnik, az alábbi táblázat, egy SQL Database Gen 4:

| Mező | Érték |
|---| --- |
|ReservationId |8244e673-83e9-45ad-b54b-3f5295d37cae|
|Mennyiség |2|
|Product| Az SQL Database Gen 4 (2 mag)|
|Régió | eastus |

### <a name="usage-in-csv-file"></a>CSV-fájl használata 

Szűrjön az **további információ** , és írja be a **Foglalásazonosító**, és válassza ki a szükséges **mérőszám kategóriája** – Azure SQL database vagy az Azure Cosmos DB. Az alábbi képernyőfelvételen a Foglalás kapcsolódó mezőket.

![Nagyvállalati Szerződés (EA) fürt megosztott kötetei szolgáltatás az SQL Database szolgáltatás számára fenntartott kapacitás](./media/billing-understand-reserved-instance-usage-ea/billing-ea-sql-db-reserved-capacity-csv.png)

1. **Reservationid értékhez** a a **további adatok** mező a alkalmazni az SQL Database erőforrás foglalást.
2. **ConsumptionMeter** az SQL Database erőforrás mérő azonosítója.
3. **Mérőszám azonosítója** 0 USD költséget a Foglalás mérőszám. Bármely SQL-adatbázis-erőforrás, amely jogosult a Foglalás jeleníti meg a CSV-fájlt a mérőszám azonosítója.

## <a name="usage-summary-page-in-enterprise-portal"></a>Használati Összegzés lapon a vállalati portál

A foglalást az Azure-használat is megjelenik-e, a vállalati portál használati összesítő szakaszában: ![nagyvállalati szerződés (EA) használatának összegzése](./media/billing-understand-reserved-instance-usage-ea/billing-ea-reserved-instance-usagesummary.png)

1. Ön nem számítunk fel díjat a virtuális gép a hardverösszetevő, Foglalás körébe. Egy SQL Database foglalás lát egy sort a **szolgáltatásnév** mint az Azure SQL Database szolgáltatás számára fenntartott kapacitás kihasználtsága.
2. Ebben a példában nem rendelkezik a az Azure Hybrid Benefit, így a Windows szoftverek, a virtuális gép együttes díjkötelesek.

## <a name="next-steps"></a>További lépések

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
- [Előre fizetés Azure-beli fenntartott virtuálisgép-példányokkal rendelkező virtuális gépekért](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Előre fizetés fenntartott Azure SQL Database-kapacitással rendelkező SQL Database számítási erőforrásokért](../sql-database/sql-database-reserved-capacity.md) 
- [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
- [Megismerheti, hogyan kell alkalmazni a foglalási kedvezményt](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
- [Windows szoftverek díjait nem tartalmazza a foglalások](billing-reserved-instance-windows-software-costs.md)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).


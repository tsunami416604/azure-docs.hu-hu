---
title: Fizessen elő az SQL Data Warehouse-díjak, hogy az Azure fenntartott kapacitás |} A Microsoft Docs
description: Ismerje meg, hogyan akkor is fizessen elő az SQL Data Warehouse díjai fenntartott kapacitás pénzt takaríthat meg.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 3d56ee99f8056139a234477b6eed93d9fcbe2bbb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013813"
---
# <a name="prepay-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Fizessen elő az SQL Data Warehouse díjai, hogy a lefoglalt kapacitás

Az Azure SQL Data Warehouse pénzt takaríthat előre a cDWU használati időtartama egy vagy három év. Az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás vásárlása, meg kell adnia az Azure régióban és az időszakot. Ezután adja hozzá az SQL Data Warehouse Termékváltozat a kosárhoz, és válassza ki a vásárolni kívánt cDWU egységek mennyisége.

Ha vásárol egy foglalást, az SQL Data Warehouse, amely megfelel a Foglalás attribútumok használata már nem díja a használatalapú – mint-akkor lépjen a díjszabás szerint.

A foglalás nem vonatkozik a storage vagy az SQL Data Warehouse használatának társított hálózati díjak.

Amikor lejár a lefoglalt kapacitás, az SQL Data Warehouse-példány továbbra is futnak, de számlázzuk a használatalapú – mint-akkor lépjen. Foglalások nem újítja meg automatikusan.

Díjszabási információkért tekintse meg a [SQL Data Warehouse szolgáltatás számára fenntartott kapacitás ajánlat](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/).

Azure SQL Data Warehouse szolgáltatás számára fenntartott kapacitás megvásárolhatja a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Lefoglalt kapacitás vásárlása:

- Legalább egy vállalati vagy használatalapú fizetéses előfizetésre a tulajdonosi szerepkörrel kell rendelkeznie.
- Vállalati előfizetés esetén a **fenntartott példányok hozzáadása** beállítást engedélyezni kell a [a nagyvállalati szerződések portáljának](https://ea.azure.com/). Ha a beállítás le van tiltva, kell lennie az EA-rendszergazdája.
- A Cloud Solution Provider (CSP) program csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás.

Hogyan vállalati ügyfelek és a használatalapú fizetéses ügyfelek számára számlázzuk foglalás vásárlásokra kapcsolatos további információkért lásd: [Azure foglalás használati adatai a nagyvállalati beléptetés](billing-understand-reserved-instance-usage-ea.md) és [tudnivalók az Azure Foglalás használat a használatalapú fizetéses előfizetést](billing-understand-reserved-instance-usage.md).

## <a name="choose-the-right-size-before-purchase"></a>Válassza ki a megfelelő méretű vásárlás előtt

Az SQL Data Warehouse foglalási méret alapján az összes számítási adattárházegységig (cDWU) felhasznált. A beszerzést 100 cDWU lépésekben.

Tegyük fel például, a teljes használat az SQL Data Warehouse: DW3000c. Szeretne vásárolni fenntartott kapacitás minden adatot. Tehát fenntartott cDWU 30 kapacitásegységek kell vásárolnia.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válasszon egy előfizetést. Az előfizetés-lista segítségével válassza ki az előfizetést, amellyel a lefoglalt kapacitás kell fizetnie. A fizetési módot, az előfizetés az előzetes költségek, a szolgáltatás számára fenntartott kapacitás után kell fizetni. Az előfizetés típusúnak kell lennie a nagyvállalati szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P) vagy a használatalapú fizetés (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P).
  - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
  - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
4. Válassza ki a hatókört. A hatókör-lista használatával egy előfizetési hatókört válassza.
  - Az a **egyetlen** beállítást, a foglalási kedvezményt az SQL Data Warehouse a kijelölt előfizetésben üzembe helyezett alkalmazzák.
  - Az a **megosztott** beállítást, a foglalási kedvezményt a számlázási környezetben előfizetésekkel példányai alkalmazzák.
    - Nagyvállalati ügyfeleink esetén a számlázási környezetben az EA-regisztrációhoz.
    - Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.
5. Válassza ki egy régiót, és válassza ki a kívánt Azure-régiót, amelyet a lefoglalt kapacitás jelez.
6. Válassza ki a mennyiséget. Adja meg a 100 Data Warehouse egységek mennyiségét (cDWU) szeretne vásárolni.    
  Például 30 adott mennyiségű adnák, fenntartott kapacitás 3000 cDWU óránként.
7. Tekintse át az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás fenntartási költségei a **költségek** szakaszban.
8. Válassza a **Beszerzés** lehetőséget.
9. Válassza ki **megtekintése a Foglalás** a beszerzési állapotának megtekintéséhez.

## <a name="cancellations-and-exchanges"></a>Lemondás és cseréje

Ha megszakítja az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás, előfordulhat, hogy a 12 %-os korai lemondási díj levonása után. A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számolja. A befizetett 50,000.00 $ / év korlátozódnak. A visszatérítés kap a fennmaradó időarányos egyenleget a 12 %-os korai lemondási díj. Visszavonást, nyissa meg az Azure Portalon, és válassza a Foglalás **visszatérítés** támogatási kérelmet szeretne létrehozni.

Az SQL Data Warehouse szolgáltatás számára fenntartott kapacitást módosítani egy másik régiót vagy kifejezés kell, ha egy másik, amely egyenlő vagy nagyobb értékű foglalás tudjon cserélni. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves időszakra, akkor kezdődik, amikor az új foglalás létrehozása. Egy exchange kérést, nyissa meg a foglalást az Azure Portalon, és válassza **Exchange** támogatási kérelmet szeretne létrehozni.

Exchange-hez vagy a visszatérítés foglalások kapcsolatos további információkért lásd: [foglalás cseréje és a visszatérítéseket](billing-azure-reservations-self-service-exchange-and-refund.md).

A foglalási kedvezményt az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás hatókör és a régió megfelelő SQL Data Warehouse-példányok száma automatikusan érvényes. Az SQL Data Warehouse szolgáltatás számára fenntartott kapacitás hatókörének frissítheti a [az Azure portal](https://portal.azure.com/), PowerShell, CLI vagy az API-n keresztül.

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/).

## <a name="next-steps"></a>További lépések

- Hogyan a Foglalás kedvezmények az Azure SQL Data Warehouse érvényesek kapcsolatos további információkért lásd: [hogyan a Foglalás kedvezmények az Azure SQL Data Warehouse érvényesek](billing-prepay-sql-data-warehouse-charges-with-reserved-capacity.md).

- Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
  - [Az Azure Reservations kezelése](billing-manage-reserved-vm-instance.md)
  - [Megismerheti a kedvezményes Azure-foglalások](billing-understand-reservation-charges.md)
  - [A használatalapú fizetéses előfizetést foglalás használati adatai](billing-understand-reserved-instance-usage.md)
  - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

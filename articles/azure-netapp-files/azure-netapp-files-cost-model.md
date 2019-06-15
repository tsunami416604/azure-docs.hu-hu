---
title: Azure NetApp fájlok költségű modell |} A Microsoft Docs
description: A témakör ismerteti a költségmodell Azure NetApp fájlok költségek kezeléséhez a szolgáltatásból.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65524220"
---
# <a name="cost-model-for-azure-netapp-files"></a>Az Azure NetApp Files költségmodellje 

A költségmodell ismertetése a NetApp Azure-fájlok segítségével kezelheti a költségeket a szolgáltatás.

## <a name="calculation-of-capacity-consumption"></a>A számítási kapacitás felhasználását

NetApp Azure Files üzembe helyezett tárolási kapacitás díjszabását.  Kiosztott kapacitást lefoglalt kapacitás-címkészletek létrehozásával.  A kapacitás készletek díjszabása $/ kiépített-GB/hó óránkénti növekményekben alapul. Egyetlen kapacitás készlet minimális mérete 4 Tib-ra, és a kapacitás készletek 1-TiB lépésekben ezt követően bővíthetők. Kötetek kapacitása készletek belül jönnek létre.  Minden egyes kötethez hozzárendelt kvóta, hogy a készletek kiosztott kapacitás csökkentésére. A kvóta rendelhető kötetek címtartományok 92 Tib-ra legfeljebb 100 GiB közötti.  

Egy aktív kötet kapacitásfogyasztásának kvótába logikai (tényleges) kapacitás alapul.

Ha a kötet tényleges kapacitásfogyasztásának tárolási kvótát túllépi, a kötet továbbra is növekszik. Írási műveletek továbbra is engedélyezhető, mindaddig, amíg a kötet tényleges mérete kisebb, mint a rendszerben meghatározott korlátot (100 TiB).  

A teljes felhasznált kapacitás ellen a kiépített összeg kapacitás készletben összege nagyobb, mint a hozzárendelt kvótát vagy tényleges fogyasztásnál a készletben lévő összes kötet: 

   ![Teljes felhasznált kapacitás kiszámítása](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Az alábbi ábra azt szemlélteti, hogy ezek a fogalmak.  
* Összeállítottunk egy kapacitás-készletet, a kiosztott kapacitás 4 Tib-ra.  A készlet három-köteteket tartalmazza.  
    * 1 kötet hozzá van rendelve egy kvótáját 2 Tib-ra és a felhasználás 800 GB.  
    * Kötet 2 hozzá van rendelve egy kvótáját 1 Tib-ra és a felhasználás 100 GB.  
    * Kötet (3) van hozzárendelve, 500 GB-kvótát, de 800 GB-felhasználás (kerettúllépés) rendelkezik.  
* A kapacitás a készlet kapacitásának (a kiépített összeg) 4 Tib-ra a forgalmi díjas.  
    3.8 felhasznált kapacitás Tib-ra (2 Tib-ra, és 1 Tib-ra kvóta kötetek 1. és 2 és 3. kötet tényleges fogyasztás 800 GB). És a kapacitás 200 GB szabad van.

   ![A kapacitás címkészlet, amely három kötetek](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Kapacitás felhasználását – kerettúllépés  

Egy készlet kapacitásának használatakor az összes meghaladja a kiosztott kapacitást, az adatírásért továbbra is engedélyezi.  A türelmi időszak (1 óra) után, ha a használt kapacitás a készlet továbbra is meghaladja a kiosztott kapacitást, majd a készlet méretét automatikusan nő 1 Tib-ra egységnyi növekményekben mindaddig, amíg a kiosztott kapacitás értéke nagyobb, mint a teljes felhasznált kapacitás.  Például a fenti ábrán kötet 3 fejlesztéseiről és a tényleges használat eléri a 1.2-es Tib-ra, majd a türelmi időszak után a készlet program automatikusan átméretezze-e 5 Tib-ra.  Az eredménye, hogy az üzembe helyezett tárolókészlet-kapacitást (5 TiB) meghaladja a felhasznált kapacitás (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>A készlet méretét a manuális módosítások  

Manuálisan növelheti vagy csökkentheti a készlet méretét. Azonban a következő korlátozások vonatkoznak:
* Szolgáltatás minimális és maximális értékeket.  
    Tekintse meg a cikket [erőforráskorlátok](azure-netapp-files-resource-limits.md).
* A kezdeti 4-TiB minimális vásárlás után egy 1 – Tib-ra növelése
* Egy egyórás minimális számlázási növekmény
* A kiépített készlet mérete nem csökkenthető kisebb, mint az összes használt kapacitás a készletben.

## <a name="behavior-of-maximum-size-pool-overage"></a>Viselkedés a maximális méretű készlet – kerettúllépés   

A kapacitás, amely lehet létrehozni és méretezze át a készlet maximális mérete 500 Tib-ra.  Használatakor a teljes kapacitás a készlet kapacitásának meghaladja az 500 Tib-ra, a következő helyzetek:
* Az adatírásért továbbra is lesz engedélyezett a (Ha a kötet nem éri el a rendszer legfeljebb 100 TiB).
* Az egyórás türelmi időszak után a készlet program automatikusan átméretezze 1-TiB lépésekben, mindaddig, amíg az üzembe helyezett tárolókészlet-kapacitást meghaladja a teljes felhasznált kapacitás.
* A további kiépített, a díjat a tárolókészlet-kapacitást meghaladó 500 TiB köteten kvóta hozzárendelése nem használható. Azt is csak akkor használható, bontsa ki a QoS teljesítménykorlátok.  
    Lásd: [szolgáltatásszintek](azure-netapp-files-service-levels.md) teljesítménykorlátok és a QoS osztályozás.

Az alábbi ábra mutatja be, hogy ezek a fogalmak:
* Összeállítottunk egy kapacitás-készlet egy prémium szintű storage és a egy 500-TiB kapacitással. A készlet kilenc-köteteket tartalmazza.
    * 1 – 8 kötetek vannak rendelve 60 TiB-kvótát.  Teljes felhasznált kapacitása 480 Tib-ra.  
        Minden kötet esetében a QoS 3,75 GB/s átviteli sebesség (60 TiB * 64 MiB/s).  
    * Kötet 9 hozzá van rendelve egy 20 TiB kvótáját.  
        Kötetnek 1,25 GB/s átviteli QoS legfeljebb 9 (60 TiB * 64 MiB/s).
* 9 lemez egy kerettúllépési forgatókönyvet. A tényleges fogyasztás 25 TiB rendelkezik.  
    * Az egyórás türelmi időszak után a kapacitás készlet 505 Tib-ra lesz méretezhetők.  
        Azt jelenti, az összes használt kapacitás 8 * 60 =-kötetek 1 – 8 és a tényleges fogyasztás kötet 9 25 TiB TiB kvótát.
    * Számlázott kapacitása 505 Tib-ra.
    * Kötet 9 kötet kvótája nem növelhető, (mivel a készlet az összes hozzárendelt kvóta nem haladhatja meg az 500 TiB).
    * További QoS átviteli sebesség (mivel a készlet az összes QoS továbbra is alapján 500 TiB) nem lehet hozzárendelni.

   ![A kapacitás készlet nine kötetén](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>A pillanatképek kapacitás felhasználása 

Az Azure NetApp fájlokban pillanatképek kapacitás felhasználását a kvótába, a szülő kötet kell fizetnie.  Ennek eredményeképpen közös számlázási költsége megegyezik a kapacitás-készlet, amely a kötet tartozik.  Azonban az aktív kötet eltérően pillanatkép felhasználását mérjük felhasznált növekményes függ.  Az Azure NetApp fájlok pillanatképei különbözeti jellegűek. Az adatok változási, attól függően a pillanatképek gyakran sokkal kisebb, mint az aktív kötet logikai kapacitásának kapacitás használnak fel. Vegyük például, hogy csak azokat a különbözeti adatok 10 GB 500 GiB kötetre pillanatképét. A kapacitást, amelyhez a pillanatkép a köteten kvóta van terhelve 10 GB, nem az 500 GB lenne. 

## <a name="next-steps"></a>További lépések

* [Azure-díjszabást ismertető oldalon NetApp fájlok](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Szolgáltatási szintek a NetApp Azure-fájlok](azure-netapp-files-service-levels.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)

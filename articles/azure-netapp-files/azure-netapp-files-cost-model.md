---
title: Az Azure NetApp Files költségmodellje | Microsoft dokumentumok
description: Az Azure NetApp Files költségmodelljének ismertetése a szolgáltatásból származó költségek kezeléséhez.
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995113"
---
# <a name="cost-model-for-azure-netapp-files"></a>Az Azure NetApp Files költségmodellje 

Az Azure NetApp Files költségmodelljének megismerése segít a szolgáltatásból származó költségek kezelésében.

## <a name="calculation-of-capacity-consumption"></a>A kapacitásfelhasználás kiszámítása

Az Azure NetApp-fájlok számlázása a kiépített tárolási kapacitás.  A kiépített kapacitás t kapacitáskészletek létrehozásával osztják fel.  A kapacitáskészletek számlázása a $/provisioned-GiB/month alapján, óránkénti lépésekben. Az egyetlen kapacitáskészlet minimális mérete 4 TiB, és a kapacitáskészletek ezt követően 1-TiB lépésekben bővíthetők. A kötetek kapacitáskészleteken belül jönnek létre.  Minden kötethez hozzá van rendelve egy kvóta, amely a készlet-kiépített kapacitástól levan havazva. A kötetekhez rendelhető kvóta legalább 100 GiB és legfeljebb 100 TiB között mozog.  

Aktív kötet esetén a kvótán alapuló kapacitásfelhasználás logikai (tényleges) kapacitáson alapul.

Ha egy kötet tényleges kapacitásfelhasználása meghaladja a tárolási kvótát, a kötet tovább növekedhet. Az írások továbbra is engedélyezettek maradnak, amíg a tényleges kötetméret kisebb, mint a rendszerkorlát (100 TiB).  

A kapacitáskészletben a kiosztott összeggel szemben iadott teljes kapacitás a hozzárendelt kvóta vagy a készleten belüli összes mennyiség tényleges fogyasztása közül a nagyobb összeg összege: 

   ![Összes felhasznált kapacitás számítása](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Az alábbi ábra ezeket a fogalmakat mutatja be.  
* Van egy kapacitás medence 4 TiB kiépített kapacitás.  A készlet három kötetet tartalmaz.  
    * Az 1.  
    * A 2.  
    * A 3.  
* A kapacitáskészlet 4 TiB kapacitásra van mérhető (a kiosztott összeg).  
    3.8 TiB kapacitást használnak fel (2 TiB és 1 TiB kvóta az 1. és 2. kötetből, és 800 GiB tényleges fogyasztás a 3. kötetre). És még 200 GiB kapacitás maradt.

   ![Kapacitáskészlet három kötettel](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Kapacitásfelépítés  

Ha egy készlet teljes felhasznált kapacitása meghaladja a kiosztott kapacitást, az adatírások továbbra is engedélyezettek.  A türelmi időszak (egy óra) után, ha a készlet felhasznált kapacitása még mindig meghaladja a kiosztott kapacitást, akkor a készlet mérete automatikusan 1 TiB-értékben növekszik, amíg a kiosztott kapacitás meg nem nagyobb, mint a teljes felhasznált kapacitás.  Például a fenti ábrán, ha a 3.  Ennek eredményeként a kiosztott készlet kapacitása (5 TiB) meghaladja a felhasznált kapacitást (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>A készlet méretének manuális módosítása  

Manuálisan növelheti vagy csökkentheti a készlet méretét. A következő korlátozások azonban érvényesek:
* Szolgáltatás minimális és maximális határértékei  
    Lásd az [erőforráskorlátokról szóló cikket.](azure-netapp-files-resource-limits.md)
* 1-TiB növekmény az első 4-TiB minimális vásárlás után
* Egy órás minimális számlázási növekmény
* A kiosztott készlet mérete nem csökkenthető a készlet teljes felhasznált kapacitására.

## <a name="behavior-of-maximum-size-pool-overage"></a>A maximális méretű készlettúllépés viselkedése   

A kapacitáskészlet maximális mérete, amelyet létrehozhat vagy átméretezhet, 500 TiB.  Ha egy kapacitáskészletteljes felhasznált kapacitása meghaladja az 500 TiB-ot, a következő helyzetek fordulnak elő:
* Az adatírástovábbra is engedélyezett (ha a kötet a rendszer legfeljebb 100 TiB-je alatt van).
* Az egyórás türelmi időszak után a készlet automatikusan átméreteződik 1 TiB-es lépésekben, amíg a készlet létesített kapacitása meghaladja a teljes felhasznált kapacitást.
* Az 500 TiB-ot meghaladó további kiépített és számlázott készletkapacitás nem használható kötetkvóta hozzárendelésére. A QoS-korlátok teljesítményének bővítésére sem használható.  
    Tekintse meg a teljesítménykorlátokkal és a QoS méretezésével kapcsolatos [szolgáltatási szinteket.](azure-netapp-files-service-levels.md)

Az alábbi ábra a következő fogalmakat mutatja be:
* Prémium szintű tárolási szinttel és 500 TiB kapacitással rendelkező kapacitáskészlettel rendelkezünk. A készlet kilenc kötetet tartalmaz.
    * Az 1–8.  A teljes felhasznált kapacitás 480 TiB.  
        Minden kötet QoS-korlátja 3,75 GiB/s átviteli (60 TiB * 64 MiB/s).  
    * A 9.  
        A 9-es kötet QoS-korlátja 1,25 GiB/s (20 TiB * 64 MiB/s).
* A 9. Ez birtokol 25 TiB a tényleges fogyasztás.  
    * Az egyórás türelmi időszak után a kapacitáskészlet átméreteződik 505 TiB-re.  
        Ez azt illeti, a teljes felhasznált kapacitás = 8 * 60-TiB kvóta az 1-8 kötetekre, és 25 TiB tényleges fogyasztás a 9.
    * A számlázott kapacitás 505 TiB.
    * A 9. kötet mennyiségi kvótája nem növelhető (mivel a készlethez rendelt teljes kvóta nem haladhatja meg az 500 TiB-t).
    * Előfordulhat, hogy nincs hozzárendelve további QoS-átviteli érték (mivel a készlet teljes QoS-a még mindig 500 TiB-on alapul).

   ![Kapacitáskészlet kilenc kötettel](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Pillanatképek kapacitásfelhasználása 

Az Azure NetApp Files pillanatképeinek kapacitásfelhasználása a szülőkötet kvótája alapján történik.  Ennek eredményeképpen ugyanaz a számlázási díj, mint a kapacitáskészlet, amelyhez a kötet tartozik.  Az aktív kötettől eltérően azonban a pillanatkép-felhasználás a felhasznált növekményes kapacitás alapján történik.  Az Azure NetApp-fájlok pillanatképei eltérő jellegűek. Az adatok változási sebességétől függően a pillanatképek gyakran sokkal kisebb kapacitást használnak, mint az aktív kötet logikai kapacitása. Tegyük fel például, hogy egy 500 GiB-kötet pillanatképét, amely csak 10 GiB különbözeti adatokat tartalmaz. A pillanatkép mennyiségi kvótájára felszámított kapacitás 10 GiB lenne, nem 500 GiB. 

## <a name="next-steps"></a>További lépések

* [Az Azure NetApp Files díjszabási lapja](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
* [Az Azure NetApp Files erőforráskorlátai](azure-netapp-files-resource-limits.md)

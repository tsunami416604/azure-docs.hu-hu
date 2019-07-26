---
title: Válassza ki a megfelelő VM SKU-t az Azure Adatkezelő-fürthöz
description: Ez a cikk az Azure Adatkezelő-fürt optimális SKU-méretének kiválasztását ismerteti.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: b0cf6eab86b0b932e44b6824305c23df01f35808
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383820"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Válassza ki a megfelelő VM SKU-t az Azure Adatkezelő-fürthöz 

Az Azure Adatkezelő több virtuálisgép-SKU-t tartalmaz, amelyek közül választhat új fürt létrehozásakor vagy a fürt optimalizálásával a változó munkaterheléshez. A virtuális gépek gondosan lettek kiválasztva, hogy a lehető legjobb költségeket engedélyezzék bármilyen munkaterheléshez. 

Az Azure Adatkezelő szolgáltatás teljes mértékben felügyeli az adatkezelési fürt méretét és virtuálisgép-tárolóját. Ezt olyan tényezők határozzák meg, mint például a motor virtuálisgép-mérete és a betöltési munkaterhelés. 

A motor fürthöz tartozó virtuálisgép-SKU bármikor módosítható a [fürt skálázásával](manage-cluster-vertical-scaling.md). Ezért érdemes a kezdeti forgatókönyvhöz illeszkedő minimális SKU-mérettel kezdeni. Ne feledje, hogy a fürt felskálázása akár 30 percet is igénybe vehet, amíg a fürt újra létrejön az új virtuális gép SKU-val.

> [!TIP]
> A számítási [ri (fenntartott példányok)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) az Azure adatkezelő-fürtre érvényesek.  

Ez a cikk a virtuálisgép-SKU különböző lehetőségeit ismerteti, és ismerteti azokat a technikai adatokat, amelyek segítségével optimálisan választhat.

## <a name="select-the-cluster-type"></a>Válassza ki a fürt típusát

Az Azure Adatkezelő két típusú fürtöt kínál:

* **Éles üzem**: Az üzemi fürtök két csomópontot tartalmaznak a motor-és az adatkezelési fürtökhöz, és az Azure Adatkezelő [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)-ban működnek.

* **Fejlesztési/tesztelési (SLA nélkül)** : A dev/test-fürtök egyetlen D11_v2-csomóponttal rendelkeznek a motor fürtjéhez, és egyetlen D1 csomópontot az adatkezelési fürthöz. Ez a fürt típusa a legalacsonyabb költség-konfiguráció, mert az alacsony példányszámú, és nincs motor-árrés. Nincs SLA ehhez a fürtkonfiguráció-konfigurációhoz, mert hiányzik a redundancia.

## <a name="sku-types"></a>SKU-típusok

Azure Adatkezelő-fürt létrehozásakor válassza ki az *optimális* VIRTUÁLISGÉP-SKU-t a tervezett munkaterheléshez. Az Azure Adatkezelő két SKU-családtal rendelkezik, amelyek közül választhat:

* **D_V2**: A D SKU a kiszámításra optimalizált és két változatban van megadva.
    * Maga a virtuális gép
    * Premium Storage-lemezekkel rendelkező virtuális gép

* **LS**: Az L SKU a tárolásra optimalizált. Sokkal nagyobb SSD-mérettel rendelkezik, mint a hasonló árú **D** SKU.

Az alábbi táblázat a rendelkezésre álló SKU-típusok közötti fő különbségeket tartalmazza:
 
|**Attribútum** | **D SKU** | **L SKU**
|---|---|---
|**Kisméretű SKU-i**|A minimális méret "11", két maggal|A minimális méret "L4", négy maggal
|**Rendelkezésre állás**|Minden régióban elérhető (a DS + PS verziója korlátozottabb rendelkezésre állással rendelkezik)|Néhány régióban elérhető
|**GB-os gyorsítótár/mag**|Magas a D SKU-val, alacsony a DS + PS-verzióval|Legolcsóbb az utólagos *fizetés* lehetőséggel
|**RI (fenntartott példányok) díjszabása**|Magas árengedmény (több mint 55% a hároméves kötelezettségvállalás esetében)|Alacsonyabb árengedmény (20% a hároméves kötelezettségvállalás esetében)  

## <a name="select-your-cluster-vm"></a>Válassza ki a fürthöz tartozó virtuális gépet 

A fürt virtuális gép kiválasztásához [konfigurálja a vertikális skálázást](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

A különböző virtuálisgép-SKU-beállítások lehetővé teszik a kívánt forgatókönyvhöz szükséges teljesítmény és a gyors gyorsítótár-követelmények költségeinek optimalizálását. Ha a forgatókönyv megköveteli a legoptimálisabb teljesítményt a nagy lekérdezési kötetek esetében, az ideális SKU-t a számításra optimalizált módon kell kiszámítani. Másfelől, ha a forgatókönyv nagy mennyiségű adat lekérdezését igényli viszonylag alacsonyabb lekérdezési terheléssel, akkor a Storage-ra optimalizált SKU csökkenti a költségeket, miközben továbbra is kiváló teljesítményt nyújt.

A kisméretű SKU-k által futtatott példányok száma korlátozott, ezért nagyobb RAM-mal rendelkező nagyobb méretű virtuális gépek használata javasolt. A RAM-méretre szükség van néhány olyan lekérdezési típushoz, amely több igényt helyez el a RAM-erőforráson, például a által használt `joins`lekérdezéseket. Ezért a méretezési lehetőségek megfontolása esetén ajánlott nagyobb SKU-ra horizontálisan felskálázást végezni, több példány hozzáadásával.

## <a name="vm-options"></a>VIRTUÁLIS gépek beállításai

Az alábbi táblázat az Azure Adatkezelő-fürt virtuális gépek műszaki specifikációit tartalmazza:

|**Name**| **Kategória** | **SSD-méret** | **Mag** | **RAM** | **Premium Storage-lemezek (1 TB)**| **Példányok minimális száma egy fürtön** | **Példányok maximális száma egy fürtön**
|---|---|---|---|---|---|---|---
|D11_v2| számításra optimalizált | 75 GB    | 2 | 14 GB | 0 | 1 | 8 (kivéve a dev/test SKU-t, ahol 1)
|D12_v2| számításra optimalizált | 150 GB   | 4 | 28 GB | 0 | 2 | 16
|D13_v2| számításra optimalizált | 307 GB   | 8 | 56 GB | 0 | 2 | 1,000
|D14_v2| számításra optimalizált | 614 GB   | 16| 112 GB | 0 | 2 | 1,000
|DS13_v2 + 1 TB PS| tárolásra optimalizált | 1 TB | 8 | 56 GB | 1 | 2 | 1,000
|DS13_v2 + 2TB PS| tárolásra optimalizált | 2 TB | 8 | 56 GB | 2 | 2 | 1,000
|DS14_v2 + DEDUPLIKÁCIÓNAK PS| tárolásra optimalizált | 3 TB | 16 | 112 GB | 2 | 2 | 1,000
|DS14_v2 + 4TB PS| tárolásra optimalizált | 4 TB | 16 | 112 GB | 4 | 2 | 1,000
|L4s_v1| tárolásra optimalizált | 650 GB | 4 | 32 GB | 0 | 2 | 16
|L8s_v1| tárolásra optimalizált | 1,3 TB | 8 | 64 GB | 0 | 2 | 1,000
|L16s_1| tárolásra optimalizált | 2,6 TB | 16| 128 GB | 0 | 2 | 1,000

* Az Azure Adatkezelő [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)segítségével régiónként megtekintheti a VIRTUÁLISGÉP-SKU-k frissített listáját. 
* További információ a [különböző számítási SKU](/azure/virtual-machines/windows/sizes-compute)-ról. 

## <a name="next-steps"></a>További lépések

* A motor fürtjét bármikor [fel-vagy](manage-cluster-vertical-scaling.md) lekicsinyítheti, ha a VM SKU-t eltérő igényekre módosítja. 

* A motortérben méretének méretezése a [-ben és](manage-cluster-horizontal-scaling.md) a szolgáltatásban a változó igényeknek megfelelő kapacitás megváltoztatásához.


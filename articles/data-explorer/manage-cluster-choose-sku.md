---
title: Válassza ki a megfelelő VM SKU-t az Azure Adatkezelő-fürthöz
description: Ez a cikk az Azure Adatkezelő-fürt optimális SKU-méretének kiválasztását ismerteti.
author: avnera
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2eb23a65196ac4f6456f50dbbbfd9e4b484ad171
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515726"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Válassza ki a megfelelő VM SKU-t az Azure Adatkezelő-fürthöz 

Amikor új fürtöt hoz létre, vagy optimalizálja a fürtöt a változó számítási feladatokhoz, az Azure Adatkezelő több virtuális gép (VM) SKU-t is kínál, amelyek közül választhat. A virtuális gépek gondosan lettek kiválasztva, hogy minden számítási feladathoz optimális költségeket biztosítson. 

Az Azure Adatkezelő szolgáltatás teljes mértékben felügyeli az adatkezelési fürt méretét és virtuálisgép-tárolóját. Ezeket olyan tényezők határozzák meg, mint a motor virtuálisgép-mérete és a betöltési munkaterhelés. 

A fürthöz tartozó virtuális gép SKU-jának bármikor módosítható a [fürt méretezésével](manage-cluster-vertical-scaling.md). A legjobb megoldás, ha a legkisebb SKU-mérettel kezdődik, amely megfelel a kezdeti forgatókönyvnek. Ne feledje, hogy a fürt felskálázása akár 30 percet is igénybe vehet, amíg a fürt újra létrejön az új virtuális gép SKU-val.

> [!TIP]
> A számítási [fenntartott példányok (ri)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) az Azure adatkezelő-fürtre érvényesek.  

Ez a cikk a virtuális gépekhez tartozó SKU különböző lehetőségeit ismerteti, és ismerteti azokat a technikai részleteket, amelyek segítségével a lehető legjobb választást teheti.

## <a name="select-a-cluster-type"></a>Fürttípus kiválasztása

Az Azure Adatkezelő két típusú fürtöt kínál:

* **Éles üzem**: Az üzemi fürtök két csomópontot tartalmaznak a motor-és az adatkezelési fürtökhöz, és az Azure Adatkezelő [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)-ban működnek.

* **Fejlesztési/tesztelési (SLA nélkül)** : A dev/test-fürtök egyetlen D11 v2 csomóponttal rendelkeznek a motor fürtjéhez és egyetlen D1 csomóponthoz az adatkezelési fürthöz. Ez a fürt típusa a legalacsonyabb költségű konfiguráció, mert az alacsony példányszámú, és nincs motor-árrés. Ez a fürtkonfiguráció nem biztosít SLA-t, mert hiányzik a redundancia.

## <a name="sku-types"></a>SKU-típusok

Amikor létrehoz egy Azure Adatkezelő-fürtöt, válassza ki az *optimális* VIRTUÁLISGÉP-SKU-t a tervezett munkaterheléshez. A következő két Azure Adatkezelő SKU-család közül választhat:

* **D v2**: A D SKU a számításra optimalizált, és két módon jön el:
    * Maga a virtuális gép
    * A Premium Storage-lemezekkel csomagolt virtuális gép

* **LS**: Az L SKU tárolásra optimalizált. Sokkal nagyobb SSD-mérettel rendelkezik, mint a hasonló árú D SKU.

Az elérhető SKU-típusok közötti fő eltéréseket az alábbi táblázat ismerteti:
 
| Attribútum | D SKU | L SKU |
|---|---|---
|**Kisméretű SKU-i**|A minimális méret D11 két maggal|A minimális méret L4 négy maggal |
|**Rendelkezésre állás**|Minden régióban elérhető (a DS + PS verziója korlátozottabb rendelkezésre állással rendelkezik)|Néhány régióban elérhető |
|**&nbsp;GB-os gyorsítótár/mag**|Magas a D SKU-val, alacsony a DS + PS-verzióval|A legalacsonyabb az utólagos elszámolású lehetőséggel |
|**Fenntartott példányok (RI) díjszabása**|Magas árengedmény (a hároméves&nbsp;kötelezettségvállalás több mint 55%-ában)|Alacsonyabb árengedmény (20&nbsp;százalék a hároméves kötelezettségvállalás esetében) |  

## <a name="select-your-cluster-vm"></a>Válassza ki a fürthöz tartozó virtuális gépet 

A fürt virtuális gép kiválasztásához [konfigurálja a vertikális skálázást](manage-cluster-vertical-scaling.md#configure-vertical-scaling). 

A különböző virtuálisgép-SKU-beállítások közül választhat, hogy optimalizálni tudja a forgatókönyv teljesítményére és a gyors gyorsítótárra vonatkozó követelményeit. 
* Ha egy nagy lekérdezési kötethez a legoptimálisabb teljesítményre van szüksége, az ideális SKU-t számításra optimalizált értékre kell állítani. 
* Ha nagy mennyiségű adat lekérdezésére van szükség viszonylag alacsonyabb lekérdezési terheléssel, a tárterületre optimalizált SKU segít csökkenteni a költségeket, és továbbra is kiváló teljesítményt nyújt.

Mivel a kisméretű SKU-k esetében a példányok száma korlátozott, a nagyobb RAM-mal rendelkező nagyobb méretű virtuális gépek használata javasolt. Több RAM szükséges néhány olyan lekérdezési típushoz, amelyek több igényt helyeznek a RAM-erőforrásra, `joins`például a által használt lekérdezésekre. Ezért ha a méretezési lehetőségeket fontolgatja, javasoljuk, hogy nagyobb méretű SKU-ra horizontális felskálázást biztosítson, és ne bővítse több példány hozzáadásával.

## <a name="vm-options"></a>VIRTUÁLIS gépek beállításai

Az Azure Adatkezelő-fürt virtuális gépek műszaki specifikációit az alábbi táblázat ismerteti:

|**Name**| **Kategória** | **SSD-méret** | **Mag** | **RAM** | **Premium Storage-lemezek (&nbsp;1 TB)**| **Példányok minimális száma egy fürtön** | **Példányok maximális száma egy fürtön**
|---|---|---|---|---|---|---|---
|D11 v2| számításra optimalizált | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (a dev/test SKU kivételével, amely 1)
|D12 v2| számításra optimalizált | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| számításra optimalizált | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| számításra optimalizált | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| tárolásra optimalizált | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| tárolásra optimalizált | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| tárolásra optimalizált | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| tárolásra optimalizált | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| tárolásra optimalizált | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| tárolásra optimalizált | 1,3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| tárolásra optimalizált | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* Régiónként az Azure Adatkezelő [LISTSKUS API](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet)használatával megtekintheti a VIRTUÁLISGÉP-SKU-k frissített listáját. 
* További információ a [különböző számítási SKU](/azure/virtual-machines/windows/sizes-compute)-ról. 

## <a name="next-steps"></a>További lépések

* A motor fürtjét bármikor felnagyíthatja [vagy méretezheti](manage-cluster-vertical-scaling.md) úgy, hogy megváltoztatja a VIRTUÁLISGÉP-SKU-t, a különböző igényektől függően. 

* A motor [-](manage-cluster-horizontal-scaling.md) fürt méretének méretezése vagy felskálázása a kapacitás módosításához, a változó igényektől függően.


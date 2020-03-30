---
title: Válassza ki a megfelelő virtuálisgép-termékváltozatot az Azure Data Explorer-fürthöz
description: Ez a cikk ismerteti, hogyan válassza ki az optimális termékváltozat mérete az Azure Data Explorer-fürthöz.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/14/2019
ms.openlocfilehash: 2d078f9715a0cfa171f0c88776a4ab78c15215a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77561850"
---
# <a name="select-the-correct-vm-sku-for-your-azure-data-explorer-cluster"></a>Válassza ki a megfelelő virtuálisgép-termékváltozatot az Azure Data Explorer-fürthöz 

Amikor új fürtöt hoz létre, vagy egy fürtöt a változó számítási feladatokhoz optimalizál, az Azure Data Explorer több virtuálisgép-ska közül választhat. A virtuális gépek gondosan kiválasztott, hogy a legoptimálisabb költséget bármilyen számítási feladatok. 

Az adatkezelési fürt méretét és virtuálisgép-termékváltozatát az Azure Data Explorer szolgáltatás teljes körűen kezeli. Ezeket olyan tényezők határozzák meg, mint a motor virtuális gép mérete és a betöltési munkaterhelés. 

A motorfürt virtuálisgép-termékváltozatát bármikor módosíthatja [a fürt felskálázásával.](manage-cluster-vertical-scaling.md) A legjobb, ha a kezdeti forgatókönyvhöz illeszkedő legkisebb termékváltozat-mérettel kezdi. Ne feledje, hogy a fürt felskálázása akár 30 perces állásidőt eredményez, amíg a fürt újra létrejön az új virtuálisgép-termékváltozattal.

> [!TIP]
> A számítási [fenntartott példányok (RI)](https://docs.microsoft.com/azure/virtual-machines/windows/prepay-reserved-vm-instances) az Azure Data Explorer-fürtre vonatkozik.  

Ez a cikk ismerteti a különböző virtuális gép termékváltozat-beállításokat, és a technikai részleteket, amelyek segítségével a legjobb választás.

## <a name="select-a-cluster-type"></a>Fürttípus kiválasztása

Az Azure Data Explorer kétféle fürtöt kínál:

* **Termelés**: A termelési fürtök két csomópontot tartalmaznak a motor- és adatkezelési fürtökhöz, és az Azure Data Explorer [SLA](https://azure.microsoft.com/support/legal/sla/data-explorer/v1_0/)alatt működnek.

* **Fejlesztési és tesztelési (nincs SLA)**: A fejlesztői/tesztelési fürtök egyetlen D11 v2-csomóponttal rendelkeznek a motorfürthöz és egyetlen D1-csomóponttal az adatkezelési fürthöz. Ez a fürttípus a legalacsonyabb költségű konfiguráció, mivel alacsony a példányszáma, és nincs motorjelölési díja. Ehhez a fürtkonfigurációhoz nincs SLA, mert hiányzik belőle a redundancia.

## <a name="sku-types"></a>Termékváltozat-típusok

Amikor létrehoz egy Azure Data Explorer-fürtöt, válassza ki az *optimális* virtuálisgép-termékváltozatot a tervezett számítási feladathoz. Az alábbi két Azure Data Explorer termékcsalád közül választhat:

* **D v2**: A D Termékváltozat számításra optimalizált, és két ízben kapható:
    * Maga a virtuális gép
    * A prémium szintű tárolólemezekkel együtt mellékelt virtuális gép

* **LS**: Az L Termékváltozat tárhelyre optimalizált. Ez egy sokkal nagyobb SSD mérete, mint a hasonlóan árú D SKU.

A rendelkezésre álló termékváltozat-típusok közötti főbb különbségeket az alábbi táblázat ismerteti:
 
| Attribútum | D Termékváltozat | L Termékváltozat |
|---|---|---
|**Kis SKUs**|A minimális méret A D11 két maggal|A minimális méret L4 négy maggal |
|**Rendelkezésre állás**|Minden régióban elérhető (a DS+PS verzió korlátozottabb rendelkezésre állással rendelkezik)|Néhány régióban elérhető |
|**Gb-os gyorsítótáronkénti&nbsp;költség magonként**|Magas a D Termékváltozattal, alacsony a DS+PS verzióval|A legalacsonyabb a kiosztóni opcióval |
|**Fenntartott példányok (RI) árképzése**|Magas kedvezmény (több&nbsp;mint 55 százalék egy hároméves kötelezettségvállalás)|Alacsonyabb kedvezmény (20&nbsp;százalék egy hároméves kötelezettségvállalásesetén) |  

## <a name="select-your-cluster-vm"></a>Válassza ki a fürt virtuális gépét 

A fürt virtuális gépének kiválasztásához konfigurálja a [vertikális skálázást.](manage-cluster-vertical-scaling.md#configure-vertical-scaling) 

A különböző virtuálisgép-termékváltozat-beállítások közül választhat, optimalizálhatja a költségeket a teljesítmény és a gyakori gyorsítótár-követelmények a forgatókönyvhöz. 
* Ha a legnagyobb optimális teljesítményre van szüksége egy nagy lekérdezési mennyiséghez, az ideális termékváltozatnak számításra optimalizálnia kell. 
* Ha viszonylag kisebb lekérdezési terheléssel nagy mennyiségű adatot kell lekérdeznie, a tárhelyre optimalizált termékváltozat segíthet a költségek csökkentésében, és továbbra is kiváló teljesítményt nyújt.

Mivel a kis sk-ok fürtenkénti példányainak száma korlátozott, célszerű nagyobb virtuális gépeket használni, amelyek nagyobb RAM-mal rendelkeznek. Több RAM-ra van szükség egyes lekérdezéstípusokhoz, amelyek nagyobb igényt `joins`igényelnek a RAM-erőforrásra, például a használó lekérdezésekre. Ezért ha méretezési lehetőségek et fontolgat, azt javasoljuk, hogy egy nagyobb termékváltozatra skálázható, ne pedig horizontális felskálázás további példányok hozzáadásával.

## <a name="vm-options"></a>Virtuális gép beállításai

Az Azure Data Explorer fürt virtuális gépeiműszaki specifikációit az alábbi táblázat ismerteti:

|**Név**| **Kategória** | **SSD-méret** | **Cores** | **Ram** | **Prémium szintű tárolólemezek&nbsp;(1 TB)**| **Minimális példányszám fürtenként** | **A példányok maximális száma fürtenként**
|---|---|---|---|---|---|---|---
|D11 v2| számításra optimalizált | 75&nbsp;GB    | 2 | 14&nbsp;GB | 0 | 1 | 8 (kivéve a fejlesztési/tesztelési termékváltozatot, amely 1)
|D12 v2| számításra optimalizált | 150&nbsp;GB   | 4 | 28&nbsp;GB | 0 | 2 | 16
|D13 v2| számításra optimalizált | 307&nbsp;GB   | 8 | 56&nbsp;GB | 0 | 2 | 1,000
|D14 v2| számításra optimalizált | 614&nbsp;GB   | 16| 112&nbsp;GB | 0 | 2 | 1,000
|DS13 v2 + 1&nbsp;TB&nbsp;PS| tárolásra optimalizált | 1&nbsp;TB | 8 | 56&nbsp;GB | 1 | 2 | 1,000
|DS13 v2 + 2&nbsp;TB&nbsp;PS| tárolásra optimalizált | 2&nbsp;TB | 8 | 56&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 3&nbsp;TB&nbsp;PS| tárolásra optimalizált | 3&nbsp;TB | 16 | 112&nbsp;GB | 2 | 2 | 1,000
|DS14 v2 + 4&nbsp;TB&nbsp;PS| tárolásra optimalizált | 4&nbsp;TB | 16 | 112&nbsp;GB | 4 | 2 | 1,000
|L4s v1| tárolásra optimalizált | 650&nbsp;GB | 4 | 32&nbsp;GB | 0 | 2 | 16
|L8s v1| tárolásra optimalizált | 1.3&nbsp;TB | 8 | 64&nbsp;GB | 0 | 2 | 1,000
|L16s_1| tárolásra optimalizált | 2,6&nbsp;TB | 16| 128&nbsp;GB | 0 | 2 | 1,000

* A frissített virtuálisgép-termékváltozat-listát régiónként tekintheti meg az Azure Data Explorer [ListSkus API használatával.](/dotnet/api/microsoft.azure.management.kusto.clustersoperationsextensions.listskus?view=azure-dotnet) 
* További információ a [különböző ska-król.](/azure/virtual-machines/windows/sizes) 

## <a name="next-steps"></a>További lépések

* A virtuális gép termékváltozatának módosításával bármikor [felskálázhatja vagy csökkentheti](manage-cluster-vertical-scaling.md) a motorfürtöt, a különböző igényektől függően. 

* A motorfürt méretét a változó igényektől függően [méretezheti vagy kicsinyítheti](manage-cluster-horizontal-scaling.md) a kapacitás módosításához.


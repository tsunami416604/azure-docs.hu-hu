---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: a506c696cdb9ca6c6221b54c63d2446b7cb86a69
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430564"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Azure Cosmos DB által kínált öt konzisztenciamodell az Azure Cosmos DB SQL API natív módon támogatottak. Azure Cosmos DB használata esetén az SQL API az alapértelmezett érték. 

Az Azure Cosmos DB natív támogatást is nyújt átviteli protokoll-kompatibilis API-k népszerű adatbázisok számára. Adatbázisok belefoglalása a MongoDB, Apache Cassandra, Gremlin és Azure Table storage. Ezek az adatbázisok pontosan a meghatározott konzisztenciamodellt kínál vagy a konzisztenciaszintek garanciát SLA-alapú nem kínálnak. Ezek általában az Azure Cosmos DB által kínált öt konzisztenciamodell csak egy adott részét biztosítják. Az SQL API-t, a Gremlin API-t és a Table API az Azure Cosmos DB-fiókot konfigurálni az alapértelmezett konzisztenciaszint használható. 

A következő szakaszok bemutatják az adatok konzisztenciáját, az Apache Cassandra egy nyílt Forráskódú ügyfél-illesztőprogram által kért közötti leképezést 4.x-es és a MongoDB 3.4-es. Ez a dokumentum az Azure Cosmos DB megfelelő konzisztenciaszintek is bemutatja az Apache Cassandra- és mongodb-hez.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Ez a táblázat az Apache Cassandra és a konzisztenciaszint közötti konzisztencia leképezése az Azure Cosmos DB. Minden Cassandra olvasási és írási konzisztenciaszintet Cosmos DB konzisztencia szintnek megfelelő biztosít erősebb, azaz a szigorúbb garanciát.


<table>
<tr> 
  <th rowspan="2">Cassandra Konzisztenciaszint</th> 
  <th rowspan="2">A cosmos DB Konzisztenciaszint</th> 
  <th colspan="3">Írási konzisztencia leképezés</th> 
  <th colspan="3">Olvassa el a konzisztencia-leképezés</th> 
</tr> 


 
 <tr> 
  <th>Cassandra</th> 
  <th>Cosmos DB</th> 
  <th>Garancia</th> 
  <th>A Cassandra</th> 
  <th>To Cosmos DB</th> 
  <th>Garancia</th> 
 </tr> 
 
  <tr> 
  <td rowspan="6">Összes</td> 
  <td rowspan="6">Erős</td> 
  <td>Összes</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
  <td>MINDEN, A KVÓRUM, SOROS, LOCAL_QUORUM, LOCAL_SERIAL, HÁROM, KÉT, EGY, LOCAL_ONE</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Erős</td> 
  <td rowspan="2">Linearizálhatóság</td> 
  <td>MINDEN, A KVÓRUM, SOROS, LOCAL_QUORUM, LOCAL_SERIAL, HÁROM, A KÉT</td> 
  <td>Erős</td> 
  <td >Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ÉS AZ EGYIK</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 

 <tr> 
  <td rowspan="2">SOROS KVÓRUM</td> 
  <td rowspan="2">Erős</td> 
  <td rowspan="2">Linearizálhatóság</td> 
  <td>AZ ÖSSZES, KVÓRUM SOROZAT</td> 
  <td>Erős</td> 
  <td >Linearizálhatóság</td> 
 </tr> 

 <tr>
   <td>LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM</td>
   <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 
 
 <tr> 
 <td>LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, <b>ANY</b></td> 
  <td>Konzisztens előtag</td> 
  <td>Globális konzisztens előtag</td> 
  <td>LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM</td> 
  <td>Konzisztens előtag</td> 
  <td>Globális konzisztens előtag</td>
 </tr> 
 
 
  <tr> 
  <td rowspan="6">EACH_QUORUM</td> 
  <td rowspan="6">Erős</td> 
  <td rowspan="2">EACH_QUORUM</td> 
  <td rowspan="2">Erős</td> 
  <td rowspan="2">Linearizálhatóság</td> 
  <td>MINDEN, A KVÓRUM, SOROS, LOCAL_QUORUM, LOCAL_SERIAL, HÁROM, A KÉT</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ÉS AZ EGYIK</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 
 
 
 <tr> 
  <td rowspan="2">SOROS KVÓRUM</td> 
  <td rowspan="2">Erős</td> 
  <td rowspan="2">Linearizálhatóság</td> 
  <td>AZ ÖSSZES, KVÓRUM SOROZAT</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 
 
  <tr> 
  <td rowspan="2">LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, BÁRMELY</td> 
  <td rowspan="2">Konzisztens előtag</td> 
  <td rowspan="2">Globális konzisztens előtag</td> 
  <td>Összes</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>


  <tr> 
  <td rowspan="4">KVÓRUM</td> 
  <td rowspan="4">Erős</td> 
  <td rowspan="2">SOROS KVÓRUM</td> 
  <td rowspan="2">Erős</td> 
  <td rowspan="2">Linearizálhatóság</td> 
  <td>AZ ÖSSZES, KVÓRUM SOROZAT</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 
 
 <tr> 
  <td rowspan="2">LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, BÁRMELY</td> 
  <td rowspan="2">Konzisztens előtag </td> 
  <td rowspan="2">Globális konzisztens előtag </td> 
  <td>Összes</td> 
  <td>Erős</td> 
  <td>Linearizálhatóság</td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM</td>
  <td>Konzisztens előtag</td>
   <td>Globális konzisztens előtag</td>
 </tr>
 
 <tr> 
  <td rowspan="4">LOCAL_QUORUM, HÁROM, KÉT</td> 
  <td rowspan="4">Kötött elavulás</td> 
  <td rowspan="2">LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td> 
  <td rowspan="2">Kötött elavulás</td> 
  <td rowspan="2">Korlátozott frissesség.<br/>
A legtöbb K verziók vagy t idő mögött.<br/>
Olvassa el a régióban található legutóbbi véglegesített érték. 
</td> 
  
  <td>QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE</td> 
  <td>Kötött elavulás</td> 
  <td>Korlátozott frissesség.<br/>
A legtöbb K verziók vagy t idő mögött. <br/>
Olvassa el a régióban található legutóbbi véglegesített érték. </td> 
 </tr> 
 
 <tr>
 <td>LOCAL_ONE, ÉS AZ EGYIK</td>
  <td>Konzisztens előtag</td>
   <td>Régiók szerinti konzisztens előtag</td>
 </tr>
 
 
 <tr> 
  <td>EGY LOCAL_ONE, BÁRMELY</td> 
  <td>Konzisztens előtag </td> 
  <td >Régiók szerinti konzisztens előtag </td> 
  <td>LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM</td> 
  <td>Konzisztens előtag</td> 
  <td>Régiók szerinti konzisztens előtag</td> 
 </tr> 
</table>

## <a id="mongo-mapping"></a>A MongoDB 3.4-es és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Az alábbi táblázat az Azure Cosmos DB MongoDB 3.4-es és az alapértelmezett konzisztenciaszint között "aggályokat olvasása" hozzárendelést mutatja be. A táblázat több régióban, és egyetlen régióban üzemelő példányok.

| **MongoDB 3.4** | **Az Azure Cosmos DB (többrégiós)** | **Az Azure Cosmos DB (egyetlen régióban)** |
| - | - | - |
| Linearizable | Erős | Erős |
| Csomóponttöbbséget használó | Korlátozott frissesség | Erős |
| Helyi | Konzisztens előtag | Konzisztens előtag |

## <a name="next-steps"></a>További lépések

További információ a konzisztenciaszintek és az Azure Cosmos DB API-k a nyílt forráskódú API-kkal közötti kompatibilitást. Lásd az alábbi cikkeket:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A mongodb-hez az Azure Cosmos DB API által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciók](cassandra-support.md)
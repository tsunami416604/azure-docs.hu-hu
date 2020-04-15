---
title: Az Azure Cosmos DB konzisztenciája, rendelkezésre állása és teljesítmény-kompromisszumai
description: Rendelkezésre állási és teljesítmény-kompromisszumok az Azure Cosmos DB különböző konzisztenciaszintekhez.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.reviewer: sngun
ms.openlocfilehash: 7cdaa9699b15000359c438bcc410e300415b759a
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81379965"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisszumok a konzisztencia, a rendelkezésre állás és a teljesítmény között

Az olyan elosztott adatbázisok esetében, amelyek replikációt használnak a magas rendelkezésre állás, az alacsony késleltetés vagy mindkettő eléréséhez, kompromisszumokra van szükség. Ezek az olvasási konzisztenciára, illetve a rendelkezésre állásra, a késleltetésre és az átviteli sebességre vonatkoznak.

Az Azure Cosmos DB az adatok konzisztenciáját a választási lehetőségek spektrumaként közelíti meg. Ez a megközelítés több lehetőséget tartalmaz, mint az erős és végleges konzisztencia két szélsősége. A konzisztenciaspektrum öt jól meghatározott szintje közül választhat. A legerősebbtől a leggyengébbig a szintek a következők:

- *Erős*
- *Határolt frissesség*
- *Munkamenet*
- *Konzisztens előtag*
- *Végleges*

Minden szint rendelkezésre állási és teljesítmény-kompromisszumokat biztosít, és átfogó SLA-k állnak a háttérben.

## <a name="consistency-levels-and-latency"></a>Konzisztenciaszintek és késés

Az olvasási késés minden konzisztenciaszintek mindig garantáltan kevesebb, mint 10 ezredmásodperc a 99 percentilis. Ezt az olvasási késést az SLA támogatja. Az átlagos olvasási késés, az 50. percentilisnél általában 4 ezredmásodperc vagy annál kevesebb.

Az írási késés minden konzisztenciaszintek mindig garantáltan kevesebb, mint 10 ezredmásodperc a 99 percentilis. Ezt az írási késést az SLA támogatja. Az átlagos írási késés, az 50. percentilis, általában 5 ezredmásodperc vagy annál kevesebb. Ez alól a garancia alól kivételt képeznek az Azure Cosmos-fiókok, amelyek több régióra terjednek ki, és erős konzisztenciával vannak konfigurálva.

### <a name="write-latency-and-strong-consistency"></a>Írási késés és erős konzisztencia

Az Azure Cosmos-fiókok konfigurált erős konzisztencia több régióban, az írási késés egyenlő két alkalommal oda-vissza idő (RTT) a két legtávolabbi régiók között, plusz 10 ezredmásodperc a 99 percentilis. A régiók közötti magas hálózati RTT nagyobb késést eredményez a Cosmos DB-kérelmek számára, mivel az erős konzisztencia csak akkor fejezi be a műveletet, ha meggyőződött arról, hogy az egy fiók on belüli összes régióban véglegesítve van.

A pontos RTT késés a fénysebesség távolságának és az Azure hálózati topológiájának függvénye. Az Azure-hálózatkezelés nem biztosít késéses SLA-kat az RTT-hez bármely két Azure-régió között. Az Azure Cosmos-fiók replikációs késések jelennek meg az Azure Portalon. Használhatja az Azure Portalon (nyissa meg a Metrikák panel, válassza a Konzisztencia lapot) az Azure Cosmos-fiókhoz társított különböző régiók közötti replikációs késések figyeléséhez.

> [!IMPORTANT]
> Az 5000 mérföldnél (8000 kilométert) meghaladó régiókkal rendelkező fiókok erős konzisztenciája alapértelmezés szerint blokkolva van a magas írási késés miatt. A funkció engedélyezéséhez forduljon az ügyfélszolgálathoz.

## <a name="consistency-levels-and-throughput"></a>Konzisztenciaszintek és átviteli

- Erős és határolt állottság esetén az olvasások egy négy replikakészlet (kisebbségi kvórum) két replikájával szemben történnek, hogy konzisztenciagaranciákat biztosítsanak. Munkamenet, konzisztens előtag és végleges nem egyetlen replika olvasás. Az eredmény az, hogy azonos számú kérelemegység esetén az erős és a kötött frissesség olvasási átviteli igénye a többi konzisztenciaszint fele.

- Egy adott típusú írási művelet, például beszúrás, csere, upsert és törlés esetén a kérelemegységek írási átviteli igénye megegyezik az összes konzisztenciaszint esetében.

|**Konzisztenciaszint**|**Kvórum olvasása**|**Kvórum írása**|
|--|--|--|
|**Erős**|Helyi kisebbség|Globális többség|
|**Kötött elavulás**|Helyi kisebbség|Helyi többség|
|**Munkamenet**|Egyetlen replika (munkamenet-token használatával)|Helyi többség|
|**Konzisztens előtag**|Egyetlen kópia|Helyi többség|
|**Végleges**|Egyetlen kópia|Helyi többség|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Konzisztenciaszintek és az adatok tartóssága

Egy globálisan elosztott adatbázis-környezetben közvetlen kapcsolat van a konzisztenciaszint és az adatok tartóssága között egy régiószintű kimaradás jelenlétében. Az üzletmenet-folytonossági terv kidolgozásakor meg kell értenie a maximális elfogadható időt, mielőtt az alkalmazás teljesen helyreáll egy zavaró esemény után. A kérelem teljes helyreállításához szükséges idő **a helyreállítási idő célkitűzése** **(RTO**). Azt is meg kell értenie, hogy a legutóbbi adatfrissítések maximális időtartama az alkalmazás tolerálhatja a elveszítést, ha egy zavaró esemény után helyreáll. A frissítések elvesztési időszaka helyreállítási **pont célkitűzésnek** **(RPO**) néven ismert.

Az alábbi táblázat határozza meg a konzisztenciamodell és az adatok tartóssága közötti kapcsolatot egy régiószintű kimaradás jelenlétében. Fontos megjegyezni, hogy egy elosztott rendszerben, még erős konzisztenciával is, lehetetlen, hogy a CAP-tétel miatt egy elosztott adatbázis nulla legyen. Ha többet szeretne megtudni arról, hogy miért, lásd: [Konzisztenciaszintek az Azure Cosmos DB.To](consistency-levels.md)learn about why, see Consistency levels in Azure Cosmos DB.

|**Régió(k)**|**Replikációs mód**|**Konzisztenciaszint**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Egy- vagy többmesteres|Bármilyen konzisztenciaszint|< 240 perc|<1 hét|
|>1.|Egyetlen mesteroldal|Munkamenet, Konzisztens előtag, Végleges|< 15 perc|< 15 perc|
|>1.|Egyetlen mesteroldal|Kötött elavulás|*K* & *T*|< 15 perc|
|>1.|Egyetlen mesteroldal|Erős|0|< 15 perc|
|>1.|Többmesteres|Munkamenet, Konzisztens előtag, Végleges|< 15 perc|0|
|>1.|Többmesteres|Kötött elavulás|*K* & *T*|0|

*K* = egy elem *"K"* verzióinak (azaz frissítéseinek) száma.

*T* = A *"T"* időintervallum az utolsó frissítés óta.

## <a name="strong-consistency-and-multi-master"></a>Erős konzisztencia és többmesteres

A többfőkiszolgálós cosmos-fiókok nem konfigurálhatók erős konzisztencia érdekében, mivel egy elosztott rendszer nem biztosít nulla rpo-t és nulla RTO-t. Emellett nincsenek írási késés előnyei a többfőkiszolgálós erős konzisztencia használatával, mivel bármely régióba történő írást replikálni kell, és a fiókon belüli összes konfigurált régióra le kell kötni. Ennek eredménye az azonos írási késés, mint egy fő számla.

## <a name="next-steps"></a>További lépések

További információ a globális disztribúcióról és az általános konzisztencia-kompromisszumokról az elosztott rendszerekben. Lásd az alábbi cikkeket:

- [Konzisztencia kompromisszumok a modern elosztott adatbázis-rendszerek tervezésében](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Magas rendelkezésre állás](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

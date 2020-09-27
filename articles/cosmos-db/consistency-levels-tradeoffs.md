---
title: Azure Cosmos DB a konzisztencia, a rendelkezésre állás és a teljesítménybeli kompromisszumok
description: Rendelkezésre állási és teljesítménybeli kompromisszumok a Azure Cosmos DB különböző konzisztenciáji szintjeihez.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 5046e40ea15a27e80f4e92ebf36488dedeee1821
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396008"
---
# <a name="latency-availability-and-performance-tradeoffs-with-different-azure-cosmos-db-consistency-levels"></a>Késés, rendelkezésre állás és teljesítménybeli kompromisszumok különböző Azure Cosmos DB konzisztencia-szintekkel

Az olyan elosztott adatbázisok esetében, amelyek replikációt használnak a magas rendelkezésre állás, az alacsony késleltetés vagy mindkettő eléréséhez, kompromisszumokra van szükség. Ezek az olvasási konzisztenciára, illetve a rendelkezésre állásra, a késleltetésre és az átviteli sebességre vonatkoznak.

A Azure Cosmos DB az adatkonzisztencia a választási lehetőségek spektrumát közelíti meg. Ez a megközelítés több lehetőséget is kínál, mint az erős és a végleges konzisztencia két véglete. Öt jól definiált szint közül választhat a konzisztencia-spektrumon. A legerősebbtól a leggyengébbig a szintek a következők:

- *Erős*
- *Kötött elavultság*
- *Munkamenet*
- *Konzisztens előtag*
- *Esetleges*

Minden szint rendelkezésre állási és teljesítménybeli kompromisszumokat biztosít, és átfogó SLA-kat támogat.

## <a name="consistency-levels-and-latency"></a>Konzisztencia-szintek és késés

Az összes konzisztencia-érték olvasási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Ezt az olvasási késést az SLA támogatja. Az átlagos olvasási késés (50%-os százalék) általában 4 ezredmásodperc vagy kevesebb.

Az összes konzisztencia-érték írási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Ezt az írási késést az SLA támogatja. Az átlagos írási késleltetés 50%-os százalékban általában 5 ezredmásodperc vagy kevesebb. A számos régióra kiterjedő Azure Cosmos-fiókok, amelyek erős konzisztencia-konfigurációval rendelkeznek, kivételt jelentenek ez a garancia.

### <a name="write-latency-and-strong-consistency"></a>Írási késés és erős konzisztencia

Az olyan Azure Cosmos-fiókok esetében, amelyeknek több régióval való erős konzisztencia van beállítva, az írási késleltetés egyenlő a két legtávolabbi régió bármelyike között, és 10 ezredmásodpercet a esetek 99% percentilis esetében (RTT). A régiók közötti nagy hálózati RTT a Cosmos DB kérelmeknél nagyobb késéssel jár, mivel az erős konzisztencia csak azt követően végezhető el, hogy a fiókon belül minden régióban elkötelezett a művelet.

A pontos RTT késés a fénysebességi távolság és az Azure hálózati topológia függvénye. Az Azure Networking nem biztosít késési SLA-kat a két Azure-régió közötti RTT. Az Azure Cosmos-fiók esetében a replikációs késések megjelennek a Azure Portal. Az Azure Cosmos-fiókjához társított különböző régiók közötti replikációs késések figyeléséhez használhatja a Azure Portalt (ugrás a metrikák panelre, válassza a konzisztencia lapot).

> [!IMPORTANT]
> A magas írási késés miatt a több mint 5000 mérföldet (8000 kilométert) felölelő tartományokkal rendelkező fiókok esetében az erős konzisztencia blokkolása alapértelmezés szerint le van tiltva. A funkció engedélyezéséhez forduljon az ügyfélszolgálathoz.

## <a name="consistency-levels-and-throughput"></a>Konzisztencia-szintek és átviteli sebesség

- Az erős és a határos elavulás esetében az olvasások egy négy replikakészlet (kisebbségi kvórum) két replikáján hajthatók végre a konzisztencia-garanciák biztosításához. Munkamenet, konzisztens előtag és végleges művelet egyetlen replika olvasásakor. Ennek az az oka, hogy az adott számú kérelem esetében az erős és a határos elavulás esetében az olvasási teljesítmény az egyéb konzisztencia-szintek felét jelenti.

- Egy adott írási művelet (például INSERT, replace, upsert és DELETE) esetében az írási sebesség a kérelmek egységeinek esetében azonos minden konzisztencia-szinten.

|**Konzisztencia szintje**|**Kvórum olvasási**|**Kvórum írása**|
|--|--|--|
|**Erős**|Helyi kisebbség|Globális többség|
|**Kötött elavultság**|Helyi kisebbség|Helyi többség|
|**Munkamenet**|Egyetlen replika (munkamenet-jogkivonat használatával)|Helyi többség|
|**Konzisztens előtag**|Egyetlen replika|Helyi többség|
|**Esetleges**|Egyetlen replika|Helyi többség|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>A konzisztencia szintjei és az adattartósság

Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a **helyreállítási időre vonatkozó célkitűzés** (**RTO**). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. A frissítések elvesztésének időpontját a **helyreállítási pont célkitűzésének** (**RPO**) nevezzük.

Az alábbi táblázat a konzisztencia-modell és az adattartósság közötti kapcsolatot határozza meg egy régióra kiterjedő leállás jelenlétében. Fontos megjegyezni, hogy egy elosztott rendszeren, még erős konzisztencia esetén is előfordulhat, hogy a CAP-tétel miatt nem lehet RPO és nulla RTO rendelkező elosztott adatbázis. Ha többet szeretne megtudni arról, hogy miért, tekintse meg [a Azure Cosmos db egységességi szintjei](consistency-levels.md)című témakört.

|**Régió (k)**|**Replikálási mód**|**Konzisztenciaszint**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Egy vagy több főkiszolgáló|Bármely konzisztencia-szint|< 240 perc|<1 hét|
|>1|Egyetlen főkiszolgáló|Munkamenet, konzisztens előtag, végleges|< 15 perc|< 15 perc|
|>1|Egyetlen főkiszolgáló|Korlátozott frissesség|*K*  &  *T*|< 15 perc|
|>1|Egyetlen főkiszolgáló|Erős|0|< 15 perc|
|>1|Több főkiszolgáló|Munkamenet, konzisztens előtag, végleges|< 15 perc|0|
|>1|Több főkiszolgáló|Korlátozott frissesség|*K*  &  *T*|0|

*K* = egy elem *"k"* verziója (azaz frissítései) száma.

*T* = a legutóbbi frissítés óta eltelt idő *"t"* .

## <a name="strong-consistency-and-multi-master"></a>Erős konzisztencia és több főkiszolgáló

A több főkiszolgálós rendszerhez konfigurált Cosmos-fiókok nem konfigurálhatók erős konzisztencia használatára, mert az elosztott rendszerek nem képesek nulla RPO és nulla RTO. Emellett nincs olyan írási késési előny, amely a több főkiszolgálóval való erős konzisztencia használatával történik, mivel bármely régióba való írást replikálni kell, és el kell véglegesíteni a fiókban lévő összes konfigurált régióban. Ez ugyanazokat az írási késést eredményezi, mint egyetlen fő fiók.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az elosztott rendszerek globális eloszlásáról és az általános konzisztencia-kompromisszumokról. Lásd az alábbi cikkeket:

- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Magas rendelkezésre állás](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

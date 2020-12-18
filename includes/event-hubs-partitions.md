---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/24/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ce906ad62b51956cb85f854846740fa09e06895d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2020
ms.locfileid: "97665195"
---
Az Event hub egy vagy több partícióba rendezi az események szakaszait. Ahogy újabb események érkeznek, a rendszer hozzáadja a sorozatot a végéhez. A partíció elképzelhető egy „véglegesítési naplóként”.

A partíciók az esemény törzsét tartalmazó eseményvezérelt adatokat, a felhasználó által definiált, az eseményt leíró, valamint a metaadatokat, például a partíción belüli eltolást, az adatfolyam-sorrend számát és a szolgáltatás-oldali időbélyeget tartalmazzák.

![Diagram, amely megjeleníti a régebbi és újabb események eseménysorozat.](./media/event-hubs-partitions/partition.png)

Event Hubs úgy lett kialakítva, hogy segítse a nagyon nagy mennyiségű esemény feldolgozását, és a particionálás a következő két módon segít:

Először is, bár Event Hubs egy Pásti szolgáltatás, van egy fizikai valóság, és az események sorrendjének megőrzéséhez szükséges napló fenntartása megköveteli, hogy ezeket az eseményeket a mögöttes tárolóban és a replikákban is megőrizze, ami egy ilyen napló teljesítményének felső határát eredményezi. A particionálás lehetővé teszi, hogy több párhuzamos naplót lehessen használni ugyanahhoz az Event hub-hoz, és így megszorozza a rendelkezésre álló nyers IO-átviteli kapacitást.

Másodszor, a saját alkalmazásainak képesnek kell lenniük az Event hub-ba küldött események mennyiségének feldolgozására. Ez elég összetett lehet, és jelentős, kibővített, párhuzamos feldolgozási kapacitást igényel. A partíciók indoklása ugyanaz, mint a fenti: az események kezelésére szolgáló egyetlen folyamat kapacitása korlátozott, ezért több folyamatra van szükség, és a partíciók a megoldás a folyamatokat is kihasználják, és egyúttal gondoskodnak arról, hogy minden eseménynek legyen egyértelmű feldolgozó tulajdonosa. 

Event Hubs megőrzi az összes partícióra vonatkozó beállított megőrzési idő eseményeit. Az események automatikusan törlődnek a megőrzési időtartam elérésekor. Ha egy nap megőrzési időtartamot ad meg, az esemény az elfogadás után nem lesz elérhető pontosan 24 órával. Az eseményeket nem lehet explicit módon törölni. 

Az engedélyezett megőrzési idő legfeljebb 7 nap Event Hubs standard és legfeljebb 90 nap dedikált Event Hubs számára. Ha az eseményeket az engedélyezett megőrzési időtartamon túl kell archiválni, automatikusan az [Azure Storage-ban vagy a Azure Data Lakeban is tárolhatók a Event Hubs rögzítési funkciójának bekapcsolásával](../articles/event-hubs/event-hubs-capture-overview.md), és ha ilyen mély archívumokat kell keresnie vagy elemezni, [egyszerűen importálhatja őket az Azure szinapszisba](../articles/event-hubs/store-captured-data-data-warehouse.md) vagy más hasonló áruházakba és elemzési platformokba. 

A Event Hubs "az adatok megőrzésének időbeli korlátozásának oka az, hogy megakadályozza a nagy mennyiségű korábbi ügyféladatok betöltését egy olyan mély tárolóban, amely csak egy időbélyeg által indexelt, és csak szekvenciális hozzáférést tesz lehetővé. Az építészeti filozófia itt azt mutatja be, hogy a múltbeli adatindexelésnek és a Event Hubs vagy Kafka által nyújtott valós idejű eseményeknek jobb közvetlen hozzáférésre van szüksége. Az Event stream-motorok nem alkalmasak arra, hogy az események beszerzéséhez szükséges adattavak vagy hosszú távú archívumok szerepét játsszák. 

Mivel a partíciók függetlenek egymástól, és saját adatsorozataikat tartalmazzák, gyakran különböző ütemben nőnek. Event Hubs, ez nem vonatkozik arra, hogy a felügyeleti beavatkozást igényli, mivel például Apache Kafka, de az egyenetlen eloszlás az alsóbb rétegbeli esemény-processzorok egyenetlen betöltését eredményezi.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

A partíciók száma a létrehozáskor van megadva, és Event Hubs standardban 1 és 32 között kell lennie. A partíciók száma a dedikált Event Hubs kapacitás egységenként legfeljebb 2000 partíció lehet. 

Azt javasoljuk, hogy legalább annyi partíciót válasszon ki, amennyit várt, hogy az adott Event hub esetében az alkalmazás maximális terhelése alatt szükséges legyen a tartós [átviteli egységek (TU)](../articles/event-hubs/event-hubs-faq.md#what-are-event-hubs-throughput-units) . Egyetlen partícióval kell kiszámítani, amelynek kapacitása 1 TU (1 MB, 2 MB kimenő). A névtér vagy a fürt kapacitási egységei a partíciók számától függetlenül méretezhetők. Egy 32 partícióval rendelkező Event hub vagy 1 partícióval rendelkező Event hub esetében ugyanaz a költség, ha a névtér 1 TU kapacitásra van beállítva. 

Az alkalmazások a következő három módszer egyikével vezérlik az események a partíciókhoz való hozzárendelését:

- A partíciós kulcs megadásával, amely konzisztens módon van leképezve (kivonatoló függvény használatával) az egyik rendelkezésre álló partícióhoz. 
- Ha nem ad meg egy partíciós kulcsot, amely lehetővé teszi, hogy a közvetítő véletlenszerűen válasszon egy partíciót egy adott eseményhez.
- Explicit módon küldi el az eseményeket egy adott partícióra.

A partíciós kulcs megadásával a kapcsolódó események együtt ugyanabban a partícióban és pontosan abban a sorrendben jelennek meg, amelyben elküldték őket. A partíciós kulcs egy olyan karakterlánc, amely az alkalmazási környezetből származik, és az események kapcsolatát azonosítja.

Egy partíciós kulcs által azonosított események egy része egy *adatfolyam*. A partíciók több ilyen streamhez készült, multiplexes naplófájlok. 

Az Event hub partícióinak száma megnövelhető az Event hub létrejötte után, de a partíciók közötti elosztás megváltozhat, amikor a partíciós kulcsok a partíciók változásaihoz való leképezése történik, ezért a változtatások elkerülése érdekében nehéz elkerülni az ilyen módosításokat, ha az alkalmazásban az események relatív sorrendje is fennáll.

A maximálisan megengedett értékhez tartozó partíciók számának beállítása csábító, de mindig vegye figyelembe, hogy az esemény-adatfolyamokat strukturálni kell, hogy valóban több partíciót is kihasználhassanak. Ha az összes esemény vagy csak néhány ALStream esetében abszolút megrendelés-megőrzésre van szüksége, akkor előfordulhat, hogy nem tudja kihasználni a sok partíciót. Emellett sok partíció is összetettebbvé teszi a feldolgozási oldalt. 

A partíciók közvetlenül is elküldhetők, ezért nem ajánlott. Ehelyett az [esemény-közzétevők](../articles/event-hubs/event-hubs-features.md#event-publishers) szakaszban bemutatott magasabb szintű szerkezeteket is használhat. 

További információt a partíciókról és a rendelkezésre állás és a megbízhatóság közötti kellő egyensúly kialakításáról az [Event Hubs programozási útmutatójában](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) és az [Event Hubs rendelkezésre állásával és következetességével](../articles/event-hubs/event-hubs-availability-and-consistency.md) foglalkozó cikkben talál.

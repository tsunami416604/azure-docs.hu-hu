---
title: Azure Data Lake Store Storm teljesítményének hangolása irányelvek |} Microsoft Docs
description: Azure Data Lake Store Storm teljesítményének hangolása irányelvek
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 5ebca90ffd679de1c30d1bc324bf4f1c3b9f6f70
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-store"></a>Útmutató a Storm on HDInsight és az Azure Data Lake Store teljesítményhangolása

Ismerje meg a tényezőket kell figyelembe venni, amikor egy Azure Storm-topológia teljesítményének beállításakor. Például fontos tudni, hogy a spoutokkal kapcsolatban, és a boltokhoz (a munkahelyi-e i/o- vagy memóriaigényes) dolgozott jellemzőit. Ez a cikk foglalkozik számos különböző teljesítményének hangolása útmutatást, beleértve a gyakori problémák elhárításához.

## <a name="prerequisites"></a>Előfeltételek

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. A fióklétrehozás módjával kapcsolatban tekintse meg [az Azure Data Lake Store használatának első lépéseit ismertető](data-lake-store-get-started-portal.md) témakört.
* **Egy Azure HDInsight fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Győződjön meg arról, hogy a fürt számára engedélyezi a távoli asztal.
* **A Data Lake Store egy Storm-fürt futtató**. További információkért lásd: [HDInsight alatt futó Storm](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Teljesítményhangolás irányelvek a Data Lake Store**.  Általános teljesítmény fogalmakat, lásd: [Data Lake Store teljesítmény hangolása útmutatást](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance).  

## <a name="tune-the-parallelism-of-the-topology"></a>A topológia párhuzamosságát hangolása

Előfordulhat, hogy növeli a Data Lake Store érkező vagy oda irányuló i/o feldolgozási teljesítmény javítása érdekében. A Storm-topológia párhuzamosságát meghatározó konfigurációk rendelkezik:
* Munkavégző folyamatok (a munkavállalók egyenlően elosztott a virtuális gépek) száma.
* Spout végrehajtó példányok száma.
* Bolt végrehajtó példányok száma.
* Spout feladatok száma.
* Bolt feladatok száma.

Például egy fürtön 4 virtuális gépek és 4 munkavégző folyamatok, 32 spout végrehajtója és 32 spout feladatokat, és 256 bolt végrehajtója és 512 bolt feladatok, vegye figyelembe a következőket:

Minden egyes felügyelő, amely a munkavégző csomópont, a Java virtuális gép (JVM) folyamat egyetlen worker rendelkezik. A JVM folyamat 4 spout szál és 64 bolt szál kezeli. Minden egyes szál belül feladatok egymás után futnak. Az előző konfigurációval minden spout szál 1 tevékenység rendelkezik, és minden egyes bolt szál 2 tevékenységet tartalmaz.

A Storm Itt a különböző összetevőt használnak, és a szint van párhuzamossági hatásuk:
* Az átjárócsomópont (Storm Nimbus néven) segítségével küldje el, majd a feladatok kezelése. Ezek a csomópontok befolyásolni párhuzamossági fokát.
* A felügyeleti csomópontok. A Hdinsightban Ez megfelel a munkavégző csomópont Azure virtuális Gépen.
* A munkavégző feladatokat a virtuális gépeken futó Storm folyamatok is. Minden egyes munkavégző feladat felel meg a JVM példánya. A Storm osztja el a lehető egyenlően osztható meg a munkavégző csomópontokhoz munkavégző folyamatok száma.
* Spout és boltok végrehajtó példányok. Egy szál a munkavállalók (JVMs) belül futó minden végrehajtó példány felel meg.
* A Storm feladatok. Ezek a logikai feladatok, hogy minden egyes szálait futtatható. Ez nem módosítja a szint párhuzamossági, ki kell értékelnie, hogy több feladat végrehajtója / kell-e.

### <a name="get-the-best-performance-from-data-lake-store"></a>A legjobb teljesítmény eléréséhez Data Lake Store-ból

A Data Lake Store használatakor a lehető legjobb teljesítményt elérhetővé ellenkező esetben a következő:
* Nyílt meg kis hozzáfűzi a nagyobb méretű (ideális esetben 4 MB).
* Hajtsa végre, mint a sok egyidejű kéréssel. Minden egyes bolt szál blokkoló olvasások végez műveletet, mert szeretné valahol core szálak 8 – 12 tartományba. Így a hálózati adapter és a CPU-t is alkalmazhatja. A nagyobb virtuális gépek lehetővé teszi több egyidejű kérelmet.  

### <a name="example-topology"></a>Példa topológia

Tegyük fel, hogy rendelkezik egy 8 munkavégző csomópontot tartalmazó fürtben D13v2 Azure virtuális gépen. A virtuális gép rendelkezik 8 processzormag, 8 munkavégző csomópontok közötti 64 teljes mag van.

Tegyük fel, 8 bolt szálak száma core végezzük. A megadott 64 mag, amely azt jelenti, hogy azt szeretnénk, ha 512 teljes bolt végrehajtó példányok (Ez azt jelenti, hogy a szál). Ebben az esetben tegyük fel, azt egy JVM-et virtuális gépenként kezdődnie, és főként használja a szál egyidejűségi belül a JVM-et egyidejűségi eléréséhez. Ez azt jelenti, hogy 8 munkavégző feladatokat (egy Azure virtuális gépenként), és 512 bolt végrehajtója kell. Ebben a konfigurációban a Storm megpróbálja terjeszteni a munkavállalók egyenletesen munkavégző csomópontokhoz (más néven a felügyelő csomópontok) keresztül jogosultságot ad az egyes feldolgozó csomópontok 1 JVM-et. A felügyelők belül Storm próbál egyenletesen közötti felügyelők végrehajtója terjesztése, most jogosultságot ad az egyes felügyelő (Ez azt jelenti, hogy JVM) 8 szálait egyes.

## <a name="tune-additional-parameters"></a>További paraméterek hangolása
Miután az alapszintű topológia, fontolja meg hogy szeretné-e végeznünk a paraméterek egyikét:
* **Egyes feldolgozó csomópontok JVMs száma.** Ha nagy adatszerkezet (például egy keresési tábla) a memóriában, minden egyes JVM állomás szükséges egy külön példányát. Azt is megteheti használhatja a adatszerkezet sok szálakon Ha kevesebb JVMs rendelkezik. I/o. a bolt JVMs száma tegye ugyanennyi különbség a között ezek JVMs hozzáadott szálak számának. Az egyszerűség kedvéért célszerű egy JVM / munkavégző rendelkezik. Attól függően, hogy milyen módon tartalmaz a bolt vagy milyen alkalmazás feldolgozása az Ön igényelnek, bár ez a szám módosítani szeretne.
* **Spout végrehajtója száma.** Mivel az előző példában boltokhoz Data Lake Store írásához használja, a spoutokkal kapcsolatban nincs közvetlenül kapcsolódik a bolt teljesítményét. Azonban feldolgozás vagy i/o történik a spout a mennyiségétől függően célszerű egy finomhangolhatják a spoutokkal kapcsolatban, a legjobb teljesítmény érdekében. Győződjön meg arról, hogy rendelkezik-e elegendő szeretné megtartani a boltokhoz foglalt spoutokkal kapcsolatban. A kimeneti sebességű a spoutokkal kapcsolatban meg kell felelnie az átviteli sebessége a boltokhoz. A tényleges konfiguráció a spout függ.
* **Feladatok száma.** Minden egyes bolt egyetlen szálon futtatja. További feladatok / bolt nem ad meg semmilyen további egyidejűségi. Csak azok előnyös, ha a folyamat a rekord igazolása időt vesz igénybe a bolt végrehajtási idő nagy részét. Célszerű a csoporthoz be egy nagyobb sok rekordokat hozzáfűzése a tartalmaz a bolt nyugtázást elküldése előtt. Így a legtöbb esetben több feladat nincs további előnye adja meg.
* **Helyi vagy véletlen csoportosítása.** Ha ez a beállítás engedélyezve van, rekordokat küldése a boltokhoz belül az azonos munkavégző folyamatot. Ez csökkenti a folyamatok közötti kommunikációt és a hálózati hívások. Ez a legtöbb topológiák javasolt.

A alapvető forgatókönyv, az jó kiindulási pont. Tesztelje a optimális teljesítmény érdekében az előző paraméterek végeznünk a saját adataival.

## <a name="tune-the-spout"></a>A spout hangolása

A következő beállításokat a spout hangolásához módosíthatja.

- **Rekord időtúllépés: topology.message.timeout.secs**. Ez a beállítás határozza meg egy üzenetet, majd nyugtázása, fogadásához szükséges idő előtt figyelembe veendő, nem sikerült.

- **Munkavégző folyamatok maximális memória: worker.childopts**. Ez a beállítás lehetővé teszi a Java Worker parancssori paraméter. A leggyakrabban használt beállítás itt XmX, amely megadja, hogy a maximális halommemória a JVM kiosztott memória.

- **Függőben lévő maximális spout: topology.max.spout.pending**. Ez a beállítás határozza meg, amely a felhőszolgáltató közötti átviteléhez (a topológia összes csomópontjának, még nem nyugtázott) spout szálankénti bármikor rekordjainak számát.

 Egy jó számítás ehhez a rekordokat méretének becsléséhez. Majd mérje fel, hogy mennyi memória egy spout szál. A szálhoz, ez az érték osztva lefoglalt teljes memória biztosítani fogja a felső határa a függőben lévő paraméter maximális spout.

## <a name="tune-the-bolt"></a>A bolt hangolása
Ha a Data Lake Store ír le, beállított mérete szinkronizálási házirend (ügyféloldali puffer) 4 MB. Csak akkor, ha a pufferméret majd végre a könyvelési vagy hsync() a következő ezt az értéket. A Data Lake Store illesztőprogram be a feldolgozón VM automatikusan végzi a pufferelés, kivéve, ha egy hsync() explicit módon elvégezhető.

Az alapértelmezett Data Lake Store Storm bolt mérete szinkronizálási házirend paraméter tartozik (fileBufferSize) Ez a paraméter hangolására használható.

Az I/O-igényes topológia esetén is célszerű minden egyes bolt szál írni a saját fájlba lesz, és fájl Elforgatás házirend (fileRotationSize). Ha a fájl elér egy adott méretet, a stream automatikusan ki van ürítve nevével, és egy új fájlt. A váltakozó használatának ajánlott fájl mérete 1 GB.

### <a name="handle-tuple-data"></a>Rekord adatok kezelése

A Storm egy spout fenn rekordot amíg explicit módon tartalmaz a bolt által elfogadott. Ha egy rekord tartalmaz a bolt által beolvasása megtörtént, de még nem nyugtázták, a spout előfordulhat, hogy nem rendelkezik állandóként létrehozni a Data Lake Store háttér. A nyugtázott egy rekord, miután a spout garantálható adatmegőrzési tartalmaz a bolt által, és majd törölheti az adatok bármilyen forrásból történt a olvasásakor.  

A legjobb teljesítmény elérése érdekében a Data Lake Store rendelkezik tartalmaz a bolt rekordot adatok 4 MB-os puffer. Majd visszaírni a Data Lake Store célból egy 4 MB-os írhatóként. Miután az adatok írása sikeresen befejeződött az áruház (hívó hflush()) tartalmaz a bolt is jelenti az adatokat a spout. Ez az itt megadott példa tartalmaz a bolt funkciója. Egyúttal elfogadható előtt a hflush() kezdeményezték a rekordokat és a rekordokat a nyugtázott nagyobb számú tárolásához. Azonban ez növeli a spout tárolásához szükséges, és ezért növeli a szükséges memória mérete / JVM útban rekordjainak számát.

> [!NOTE]
Előfordulhat, hogy az alkalmazások számára, hogy tudomásul gyakrabban (a adatok mérete MB-nál kisebb 4) rekordokat más nem teljesítményének javítására szolgál. Azonban, hogy a tároló háttér az i/o átviteli hatással lehetnek. Gondosan mérjük ez kompromisszumot tartalmaz a bolt i/o-teljesítmény ellen.

Ha a listának beérkezési sebessége nem magas, a 4 MB-os puffer kitöltésének hosszú ideig tart, érdemes kiküszöböléséhez ezt úgy:
* Boltokhoz számának csökkentése, így nincsenek kevesebb pufferek kitöltéséhez.
* Minden kiürítéseinek x vagy minden y ezredmásodperc rendelkező időalapú vagy count-alapú szabályzat, ahol egy hflush() van elindítva, és a rekordokat, amennyiben halmozott ismernek vissza.

Fontos megjegyezni, hogy az átviteli sebesség a jelen esetben ez alacsonyabb események lassú sebesség, maximális átviteli sebesség célja nem a legnagyobb ennek ellenére is. A megoldást, amely egy rekordot az áruház áramlása érdekében szükséges teljes idő csökkentése érdekében. Ez lehet, hogy számít, ha azt szeretné, hogy még egy kis események száma a valós idejű folyamat. Ne feledje, hogy ha a bejövő rekordot aránya alacsony, kell beállítani a topology.message.timeout_secs paraméter, a rekordok nem túllépi az időkorlátot, miközben azok első pufferelt vagy nem dolgozható fel.

## <a name="monitor-your-topology-in-storm"></a>A topológia a Storm figyelése  
A topológia futása közben, a figyelheti a Storm felhasználói felületén. Az alábbiakban a fő paraméterek közül:

* **Teljes folyamat végrehajtása késés.** Ez az egy rekordot a spout által kibocsátott, a bolt által feldolgozott és nyugtázott szükséges átlagos idő.

* **Teljes bolt folyamat várakozási ideje** Ez az az átlagos feldolgozási idő által a rekord tartalmaz a bolt, amíg nem kap nyugtázást.

* **Teljes bolt késés hajtható végre.** Ez az az execute metódus a bolt által töltött átlagos idő.

* **Hibák száma.** Ez vonatkozik, amelyek nem tudta teljesen feldolgozni ahhoz, azok túllépte az időkorlátot rekordjainak számát.

* **A kapacitás.** Ez a méri, hogy foglalt a rendszer. Ha ez a szám 1, a boltokhoz gyors a következőkre dolgozik. Ha 1-nél kisebb, növelheti a párhuzamosságot. Ha nagyobb, mint 1, csökkentse a párhuzamos végrehajtás.

## <a name="troubleshoot-common-problems"></a>Gyakori hibák elhárítása
Az alábbiakban néhány gyakori hibaelhárítási forgatókönyveket.
* **Sok listának vannak időtúllépés miatt.** Tekintse meg a topológia annak megállapításához, ahol a szűk keresztmetszetek minden csomópontja. Ennek leggyakoribb oka az, hogy a boltokhoz nem képesek a spoutokkal kapcsolatban tartani. Ennek eredménye a belső puffer eltömődés közben feldolgozásra váró rekordokat. Vegye figyelembe az időkorlát értékének növelésével vagy csökkentésével a maximális spout függőben.

* **Nincs, a magas teljes folyamat végrehajtása késés, de egy kis bolt folyamat várakozási ideje.** Ebben az esetben is lehet, hogy a rekordok nem alatt ismernek elég gyorsan. Ellenőrizze, hogy vannak-e elegendő acknowledgers. Egy másik lehetőség, hogy várnak a várakozási sorban túl hosszú őket a boltokhoz előtt. A függőben lévő maximális spout csökkentése.

* **Nincs a magas bolt késés hajtható végre.** Ez azt jelenti, hogy a bolt az execute() metódus metódusában túl sokáig tart. Optimalizálni a kódot, vagy írási méretben keresse meg és ürítse ki a viselkedést.

### <a name="data-lake-store-throttling"></a>Data Lake Store-szabályozás
Elérte a Data Lake Store által biztosított sávszélesség korlátai, ha a feladat sikertelen jelenhet meg. Tekintse meg a feladat naplókat hibák szabályozás. A párhuzamos tároló méretének növelésével csökkenthető.    

Ellenőrizze, hogy Ön első szabályozott, engedélyezze a hibakeresési naplózás ügyféloldali:

1. A **Ambari** > **Storm** > **Config** > **storm-worker-log4j speciális**, módosítsa **&lt;gyökér szintű = "Infó"&gt;** való  **&lt;legfelső szintű = "debug"&gt;**. Indítsa újra az összes a csomópontok/szolgáltatást a konfigurációjának érvénybe léptetéséhez.
2. A Storm-topológia bejelentkezik a munkavégző csomópont monitor (alatt /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) a Data Lake Store szabályozási kivételeket.

## <a name="next-steps"></a>További lépések
További teljesítményhangolás, a Storm hivatkozható [ebben a blogban](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

További például futtatásához, [erre a Githubon](https://github.com/hdinsight/storm-performance-automation).

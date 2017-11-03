---
title: "Teljesítmény és méretezhetőség tartós funkciókkal – Azure"
description: "Bevezetés az Azure Functions a tartós funkciók bővítmény."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 10ce74097388a0283797e4692126c5039e8d4dd0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>Teljesítmény és méretezhetőség tartós funkciókkal (az Azure Functions)

Optimalizálható a teljesítmény és méretezhetőség, fontos tudni, hogy az egyedi méretezési jellemzői [tartós funkciók](durable-functions-overview.md).

Jobban átláthatja a méretezési viselkedését, akkor az Azure Storage Alapszolgáltató tartós funkciók által használt részleteit megismerését.

## <a name="history-table"></a>Előzménytábla

A előzménytábla egy Azure Storage példányainak vezénylési az előzmények eseményeket tartalmazó tábla. Példányokat futtatni, mert ez a táblázat új sorok kerülnek. A partíciós kulcs ezt a táblázatot a vezénylési Példányazonosítója származik. Ezek értékei a legtöbb esetben véletlenszerű amely biztosítja, hogy optimális terjesztési belső Azure Storage a partíciók száma.

## <a name="internal-queue-triggers"></a>Belső eseményindítók

Tevékenység és az orchestrator függvények mindkét váltja ki a függvény alkalmazás alapértelmezett tárfiók belső várólistákból. A tartós funkciók várólisták két típusa van: a **vezérlő várólista** és a **munkatétel-várólista**.

### <a name="the-work-item-queue"></a>A cikk várólista

Nincs munkahelyi egy várólista / tartós funkciók feladat csomópontjában. Ez egy egyszerű várólista, és más hasonló módon viselkedik `queueTrigger` az Azure Functions várólista. A várólista használatával indul el, az állapot nélküli *tevékenység funkciók*. Ha a tartós funkciók alkalmazás méretezi ki több virtuális gépekhez, minden virtuális gépeken "versenyeznek" szerezni a munkaelem várólista munka.

### <a name="control-queues"></a>Vezérlő várólisták

A *vezérlő várólista* bonyolultabb, mint a egyszerűbb munkaelem várólistát. Az állapot-nyilvántartó orchestrator funkciók indítására szolgál. Mivel az orchestrator függvény példányai állapot-nyilvántartó singletons, nincs lehetőség a terhelés szétosztását a virtuális gépek versengő fogyasztó modell használatával. Ehelyett az orchestrator üzenetek olyan elosztott terhelésű több vezérlő várólistában. Ezzel kapcsolatban további részletek az ezt követő szakaszok.

Vezérlő várólisták vezénylési életciklus üzenettípusok számos tartalmaznak. Példák [orchestrator vezérlő üzenetek](durable-functions-instance-management.md), tevékenység függvény *válasz* üzenetek és időzítő üzeneteket.

## <a name="orchestrator-scale-out"></a>Az orchestrator kibővített

Tevékenység funkciók állapot nélküli, és adja hozzá a virtuális gépek automatikusan kiterjesztése. Az orchestrator függvények, másrészt olyan *particionált* egy vagy több vezérlő várólistában. A vezérlő várólisták számát rögzített, és létrehozásakor betöltés után nem módosítható.

Amikor kiterjesztése több függvény állomás példány (általában a különböző virtuális gépek), akkor minden példány zárolás a vezérlő várólisták egyik. A zárolás biztosítja, hogy vezénylési példány csak egyetlen virtuális gépen egyszerre. Ez azt jelenti, hogy ha egy feladat hub három vezérlő várólistákkal van konfigurálva, az orchestration példányok lehet elosztott terhelésű három virtuális gépek között. További virtuális gépeket lehet hozzáadni a növelheti tevékenység függvény végrehajtását.  De a további erőforrások nem használható az orchestrator funkciók futtatásához.

A következő ábra szemlélteti, és a tárolási entitások méretezett kimenő környezetben az Azure Functions állomás együttműködését.

![Diagram méretezése](media/durable-functions-perf-and-scale/scale-diagram.png)

Ahogy látja, a minden virtuális gép is "versenyeznek" az üzenetek a munkahelyi várólista. Azonban csak három virtuális gépek lekérheti a vezérlő várólistákból üzeneteket, és minden virtuális gép egyetlen vezérlő várólista zárolja.

Vezénylési példányok elosztott vezérlő várólista példányok egy belső kivonatoló függvényt futtatásával szemben a vezénylési példány azonosítója. Automatikusan létrehozott és a véletlenszerű, amely biztosítja, hogy az összes rendelkezésre álló vezérlési várólistában példányok elosztását alapértelmezés szerint példány azonosítói. Az aktuális támogatott vezérlő várólista partíciók száma alapértelmezett **4**.

> [!NOTE]
> Nincs jelenleg lehetséges a partíciók számának konfigurálása az Azure Functions. [Ez a beállítás támogatásához munkahelyi követett](https://github.com/Azure/azure-functions-durable-extension/issues/73).

Orchestrator funkciók általában kell lenniük a könnyű, és nem kell nagy számítási teljesítményt. Emiatt nincs szükség sok vezérlő várólista partíciók nagy átviteli sebesség eléréséhez létrehozásához. Ehelyett a nagy munka nagyobb része állapot nélküli tevékenységet függvények, amelyek kiterjeszthető végtelenül történik.

## <a name="auto-scale"></a>Automatikus méretezés

Mivel az összes Azure Functions a fogyasztás tervben fut, a tartós funkciók automatikus méretezése keresztül támogatja a [Azure Functions méretezése-vezérlő](https://docs.microsoft.com/azure/azure-functions/functions-scale#runtime-scaling). A skála vezérlő hozzáadása vagy eltávolítása a Virtuálisgép-erőforrások ennek megfelelően a munkaelem várólista hossza és az egyes vezérlő várólisták figyeli. Ha a vezérlő várólista-hosszúságok meggátolják növekszik adott idő alatt, a méretezési vezérlő továbbra is hozzáadása példányok nem éri a vezérlő várólista partíciók száma. Ha munkahelyi elem várólista-hosszúságok meggátolják növekszik adott idő alatt, a méretezési vezérlő továbbra is hozzáadása a Virtuálisgép-erőforrások, amíg azt is megegyezhet a terhelés, függetlenül a vezérlő várólista partíciók száma.

## <a name="thread-usage"></a>A szál kihasználtsága

Az orchestrator funkciók végrehajtása egyetlen szálon. Ez azért szükséges, hogy az orchestrator függvény végrehajtása determinisztikus. Ennek tudatában a fontos soha nem során is garantálják az orchestrator feleslegesen elfoglalt feladatokhoz, mint az i/o (amely a számos okból tiltott) blokkolja, vagy a műveletek forgó szál működik. Munka szükségesek i/o, blokkolja, vagy több szál a tevékenység függvényekké helyezni.

Tevékenység funkciók lehet ugyanazokat viselkedések rendszeres várólista-eseményindítókkal aktivált függvényeket. Ez azt jelenti, hogy biztonságosan do i/o, CPU-intenzív műveleteket, és több szál. Mivel a tevékenység eseményindítók állapotmentes, akkor lehet szabadon horizontálisan virtuális gépek unbounded száma.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Telepítse a tartós funkciók bővítményt, és minták](durable-functions-install.md)

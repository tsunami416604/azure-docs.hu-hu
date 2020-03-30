---
title: Az Azure FXT Edge Filer monitorozása
description: Az Azure FXT Edge Filer hibrid tárolási gyorsítótárának hardverállapotának figyelése
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254870"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Az Azure FXT Edge Filer hardverállapotának figyelése

Az Azure FXT Edge Filer hibrid tárológyorsítótár-rendszer több állapotjelző lámpával rendelkezik a házba építve, hogy a rendszergazdák megértsék a hardver működését.

## <a name="system-health-status"></a>A rendszer állapota

A gyorsítótár-műveletek magasabb szintű figyeléséhez használja a Vezérlőpult **Irányítópult** szoftveres lapját a [Vezérlőpult irányítópult-útmutatójában leírtak szerint.](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Hardver állapot JELZŐK

Ez a szakasz ismerteti az Azure FXT Edge Filer hardverbe épített különböző állapotjelző fényeket.

### <a name="hard-drive-status-leds"></a>Merevlemez állapot JELZŐK

![a merevlemez eleje, vízszintes, feliratos felirattal 2 (bal felső sarok), 1 (bal alsó sarok) és 3 (jobb oldal)](media/fxt-monitor/fxt-drive-callouts.png)

Minden meghajtóhordozónak két állapotJELZŐje van: egy tevékenységjelző (1) és egy állapotjelző (2). 

* A tevékenység LED (1) világít, amikor a meghajtó használatban van.  
* Az állapotjelző LED (2) a meghajtó állapotát jelzi az alábbi táblázatban szereplő kódok használatával.

| Meghajtó állapota LED állapot              | Jelentés  |
|-------------------------------------|----------------------------------------------------------|
| Másodpercenként kétszer zölden villog      | A *meghajtó* vagy a meghajtó azonosítása <br> A meghajtó előkészítése az eltávolításra  |
| Ki (nem megvilágított)                         | A rendszer nem fejeződött be az *indításvagy* <br>A meghajtó készen áll az eltávolításra |
| Zölden, borostyánsárgán és kikapcsolva       | A meghajtó meghibásodása várható   |
| Másodpercenként négyszer villog a borostyánszínnel | A meghajtó nem sikerült   |
| Folyamatos zöld                         | A meghajtó online állapotban van |

A meghajtó jobb oldalán (3) a meghajtó kapacitása és egyéb információi vannak feltüntetve.

A meghajtószámok a meghajtók közötti helyre kerülnek. Az Azure FXT Edge Filer,0 meghajtó a bal felső meghajtó, és az 1-es meghajtó közvetlenül alatta. A számozás ebben a mintában folytatódik. 

![egy merevlemez-rekesz fényképe az FXT alvázban, a meghajtószámokkal és a kapacitáscímkékkel](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Bal oldali vezérlőpult

A bal első vezérlőpanel különböző állapotú LED-kijelzőkkel (1) és nagy, megvilágított rendszerállapot-jelzővel (2) rendelkezik. 

![bal oldali állapotpanel, a bal oldalon 1 címkézési állapotjelző, a jobb oldalon pedig a nagy rendszerállapot-jelző fény e](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>A vezérlőpult állapotjelzői 

A bal oldali állapotjelzők folyamatos borostyánsárga fényt mutatnak, ha hiba van a rendszerben. Az alábbi táblázat a hibák lehetséges okait és megoldásait ismerteti. 

Ha a hiba továbbra is megjelenik a megoldás kipróbálása után, kérjen segítséget az [ügyfélszolgálattól.](fxt-support-ticket.md) 

| Ikon | Leírás | Hibafeltétel | Lehetséges megoldások |
|----------------|---------------|--------------------|----------------------|
| ![meghajtó ikonja](media/fxt-monitor/fxt-hd-icon.jpg) | Meghajtó állapota | Meghajtóhiba | Ellenőrizze a rendszer eseménynaplójában, hogy a meghajtó hibás-e, vagy <br>Futtassa a megfelelő online diagnosztikai tesztet; indítsa újra a rendszert és futtasson beágyazott diagnosztikát (ePSA), vagy <br>Ha a meghajtók RAID-tömbben vannak konfigurálva, indítsa újra a rendszert, és adja meg a gazdacsatoló konfigurációs segédprogramját |
|![hőmérséklet ikon](media/fxt-monitor/fxt-temp-icon.jpg) | Hőmérséklet állapota | Termikus hiba - például egy ventilátor meghibásodott, vagy a környezeti hőmérséklet hatótávolságon kívül van | Ellenőrizze a következő címezhető feltételeket: <br>Hiányzik egy hűtőventilátor, vagy meghibásodott <br>A rendszer fedelét, a légburkolatot, a memóriamodul üres vagy a visszatöltő konzolt eltávolítjuk <br>A környezeti hőmérséklet túl magas <br>A külső légáramlás elvan torlaszolva |
|![a villamos energia ikonja](media/fxt-monitor/fxt-electric-icon.jpg) | Elektromos állapot | Elektromos hiba - például a hatótávolságon kívüli feszültség, a nem bÚV vagy a meghibásodott feszültségszabályozó |  Ellenőrizze a rendszer eseménynaplójában vagy a rendszerüzenetekben az adott problémát. Ha van egy PSU probléma, ellenőrizze a TÁPEGYSÉG állapotát LED és visszaad a Tápegység, ha szükséges. | 
|![memória ikon](media/fxt-monitor/fxt-memory-icon.jpg) | Memória állapota | Memóriahiba | Ellenőrizze a rendszereseménynaplóban vagy a rendszerüzenetekben a meghibásodott memória helyét; a memóriamodult. |
|![A PCIe ikonja](media/fxt-monitor/fxt-pcie-icon.jpg) | PcIe állapota | PCIe-kártyahiba | Indítsa újra a rendszert; pcie kártyaillesztők frissítése; telepítse újra a kártyát |


### <a name="system-health-status-indicator"></a>A rendszer állapotjelzője

A bal oldali vezérlőpult jobb oldalán található nagy megvilágítású gomb jelzi a rendszer általános állapotát, és egységkereső fényként is használható rendszerazonosító üzemmódban.

A rendszerállapot és az azonosító gomb megnyomásával válthat a rendszerazonosító és a rendszerállapot-üzemmód között.

|A rendszer állapotának állapota | Állapot |
|-------------------------------------------|-----------------------------------------------|
| Folyamatos kék | Normál működés: a rendszer be van kapcsolva, normálisan működik, és a rendszerazonosító mód nem aktív. <br/>Ha rendszerazonosító módra szeretne váltani, nyomja meg a rendszerállapot- és azonosítógombot. |
| Villogó kék | A rendszerazonosító mód aktív. Ha rendszerállapot-üzemmódra szeretne váltani, nyomja meg a rendszerállapot- és rendszerazonosító gombot. |
| Tömör borostyán | A rendszer hibamentes módban van. Ha a probléma továbbra is fennáll, [forduljon a Microsoft ügyfélszolgálatához és támogatási szolgálatához.](fxt-support-ticket.md) |
| Villogó sárga | Rendszerhiba. Ellenőrizze a rendszer eseménynaplójában, hogy vannak-e konkrét hibaüzenetek. A rendszer belső vezérlőprogramjának és a rendszerösszetevőket figyelő ügynökök által létrehozott eseményről és hibaüzenetekről a qrl.dell.com hibakód-felkésés oldalán talál. |



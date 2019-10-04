---
title: Az Azure FXT Edge Filer figyelése
description: Az Azure FXT Edge Filer hibrid tárolási gyorsítótár hardver állapotának figyelése
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e6afd90c4e5a1b0759b3de7789ec37db4c04f2c7
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827447"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Azure FXT Edge Filer hardver állapotának figyelése

Az Azure FXT Edge Filer hibrid gyorsítótár tárolórendszer beépített a váz, hogy a rendszergazdák megismerheti, hogyan működik-e a hardver több Állapotjelzők rendelkezik.

## <a name="system-health-status"></a>Rendszer-állapot

A magasabb szintű gyorsítótár-műveleteiről monitorozásához használja a szoftvert Vezérlőpult **irányítópult** lapon ismertetett módon a [Control Panel irányítópult útmutató](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Hardverállapot LED-EK

Ez a szakasz ismerteti a különböző Állapotjelzők Azure FXT Edge Filer hardverre beépített.

### <a name="hard-drive-status-leds"></a>Merevlemez-meghajtó állapotát LED-EK

![merevlemez illeti, vízszintes, képfelirat címkékkel 2 képe (bal felső sarokban), 1 (bal alsó sarokban) és 3 (a jobb oldalon)](media/fxt-monitor/fxt-drive-callouts.png)

Minden meghajtó szolgáltatója LED-eken keresztüli két állapota van: egy tevékenységet jelző (1) és a egy állapotjelzője (2). 

* A tevékenység (1) lámpa vezetett, ha a meghajtó használatban van.  
* Az állapot LED (2) az alábbi táblázat a kódok használatával a meghajtó állapotát jelzi.

| Meghajtó állapota LED állapota              | Jelentés  |
|-------------------------------------|----------------------------------------------------------|
| Zöld másodpercenként kétszer villanás      | Meghajtó azonosítása *vagy* <br> Az Eltávolítás meghajtó előkészítése  |
| Ki (a meg nem világított)                         | Rendszer még nem fejeződött be a rendszerindítási *vagy* <br>Meghajtó eltávolítandó készen áll |
| Villanás zöld, sárga, és ki       | Meghajtó hiba összegyűjtése várható   |
| Villanás sárga négyszer / másodperc | Nem sikerült   |
| Szilárd zöld                         | Meghajtó online állapotban. |

A jobb oldalon, a meghajtó (3) feliratú a a meghajtót és az egyéb információkat.

Meghajtó számok nyomtatott meghajtók közötti távolság. Az Azure FXT Edge szűrőlista 0 meghajtó meghajtó bal felső, és közvetlenül alatta a meghajtó 1 az. A minta számozása folytatódik. 

![fénykép rekeszes a FXT váz, számokat és a kapacitás felirat megjelenítése a meghajtó a egy merevlemez](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>A vezérlőelem bal oldali panel

A bal oldali első Vezérlőpult különböző LED Állapotjelzők (1) és nagy megvilágított rendszer állapotjelző (2) rendelkezik. 

![bal oldali állapot panel, a címkézés, a bal oldalon, a jobb oldali világos nagy méretű rendszer állapotjelző címkézés 2 Állapotjelzők 1](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Vezérlő panel Állapotjelzők 

A bal oldalon az Állapotjelzők megjelenítése egy szilárd sárga világos van-e hiba a rendszerben. Az alábbi táblázat ismerteti a lehetséges okait és megoldásait a hibákat. 

Ha ezek a megoldások próbálkozás után továbbra is fennáll a hiba [forduljon az ügyfélszolgálathoz](fxt-support-ticket.md) segítséget. 

| Ikon | Leírás | Hibafeltétel | A lehetséges megoldásokról |
|----------------|---------------|--------------------|----------------------|
| ![meghajtó ikon](media/fxt-monitor/fxt-hd-icon.jpg) | Meghajtó állapota | Meghajtó-hiba | A rendszer eseménynaplójában található meghatározni, hogy rendelkezik-e a meghajtó hiba, vagy <br>Futtassa a megfelelő online diagnosztikai teszt; Indítsa újra a rendszert, és futtassa a beágyazott diagnosztikai (ePSA), vagy <br>Ha a meghajtókat egy RAID tömbben vannak konfigurálva, indítsa újra a rendszert, és adja meg a gazdagép adapter konfigurációs segédprogram program |
|![hőmérséklet-ikon](media/fxt-monitor/fxt-temp-icon.jpg) | Hőmérséklet-állapot | Hőmérsékleti hiba – például egy ventilátor sikertelen volt, vagy környezeti hőmérséklet je mimo rozsah | Ellenőrizze, hogy a következő címezhető feltételek: <br>Egy hűtési ventilátor hiányzik, vagy nem sikerült <br>A rendszer cover, légi burkolatból, memória modul üres, vagy vissza kitöltő szögletes zárójelet eltávolítása <br>A környezeti hőmérséklet értéke túl magas <br>A probléma külső légmozgás egyik oka |
|![az elektromos áram ikon](media/fxt-monitor/fxt-electric-icon.jpg) | Elektromos állapota | Elektromos hiba – például az engedélyezett tartományon kívül esik, feszültség PSU, vagy egy meghibásodott feszültség szabályozó sikertelen |  Ellenőrizze a rendszer eseménynaplójába vagy a rendszer üzeneteket az adott probléma megoldásához. PSU probléma merül fel, ha a PSU állapotának LED, és távolítsa a PSU, ha szükséges. | 
|![memória ikon](media/fxt-monitor/fxt-memory-icon.jpg) | Memória állapota | Elég memória hiba | Ellenőrizze a rendszer eseménynaplójában vagy a hely meghiúsult memóriamennyiséget; üzenetek Távolítsa a memória modul. |
|![PCIe ikon](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe állapota | PCIe kártya hiba | Indítsa újra a rendszert; PCIe-illesztőprogramok; frissítése Telepítse újra a kártya |


### <a name="system-health-status-indicator"></a>Rendszer állapotjelző állapota

A nagy megvilágítottnak gombra a jobb oldalán a bal oldali Vezérlőpult azt jelzi, hogy a rendszer általános állapota, és a egy lokátor je rendszer azonosító módban is szolgál.

Nyomja le a rendszerállapot és a rendszer azonosítója és a rendszer állapotának üzemmód közötti váltáshoz azonosító gombra.

|Rendszer állapota állapota | Állapot |
|-------------------------------------------|-----------------------------------------------|
| Szilárd kék | Normál működés: a rendszer be van kapcsolva, normál, működő, és a rendszer azonosító mód nem aktív. <br/>Nyomja le a rendszer állapotának és azonosító gombra, ha azt szeretné, rendszer-azonosító módra való váltáshoz. |
| Villogó kék | Rendszer-azonosító mód nem aktív. Nyomja le a rendszerállapot és a rendszer azonosító gombra, ha azt szeretné, a system health módra való váltáshoz. |
| Szilárd sárga | A rendszer a hibamentes módban van. Ha a probléma tartósan fennáll, [forduljon a Microsoft ügyfélszolgálata és](fxt-support-ticket.md). |
| Sárga villogó | Rendszer hibája. Ellenőrizze a rendszer eseménynaplójába hibaüzeneteket. Az események és hibaüzenetek, a rendszer belső vezérlőprogram és az ügynökök, amelyek a rendszer-összetevők által létrehozott kapcsolatos információkért lásd a hiba kikereshető kód qrl.dell.com címen. |



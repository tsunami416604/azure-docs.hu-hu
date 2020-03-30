---
title: fájl belefoglalása
description: fájl belefoglalása
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334639"
---
#### <a name="process-automation"></a>Folyamatautomatizálás

| Erőforrás | Korlát |Megjegyzések|
| --- | --- |---|
| Az Azure Automation-fiókonként 30 másodpercenként elküldhető új feladatok maximális száma (nem ütemezett feladatok) |100 |Ha ezt a korlátot eléri, a feladat létrehozására irányuló későbbi kérelmek sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| Az egyidejű futó feladatok maximális száma ugyanabban az időpontban automation-fiókonként (nem ütemezett feladatok) |200 |Ha ezt a korlátot eléri, a feladat létrehozására irányuló későbbi kérelmek sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| A feladat metaadatainak maximális tárolási mérete egy 30 napos gördülő időszakban | 10 GB (kb. 4 millió munkahely)|Ha ezt a korlátot eléri, a feladat létrehozására irányuló későbbi kérelmek sikertelenek lesznek. |
| Feladatfolyam maximális korlátja|1MB|Egyetlen adatfolyam nem lehet nagyobb 1 MB-nál.|
| Automatizálási fiókonként 30 másodpercenként importálható modulok maximális száma |5 ||
| A modul maximális mérete |100 MB ||
| Feladat futási ideje, ingyenes szint |500 perc előfizetésenként naptári hónaponként ||
| A<sup>homokozónként</sup> 1 engedélyezett maximális lemezterület |1 GB |Csak az Azure sandboxes csak vonatkozik.|
| A<sup>homokozóban</sup> 1 megadott maximális memóriamennyiség |400 MB |Csak az Azure sandboxes csak vonatkozik.|
| A hálózati szoftvercsatornák maximális száma<sup>1</sup> homokozónként |1,000 |Csak az Azure sandboxes csak vonatkozik.|
| Runbook<sup>1-enként</sup> engedélyezett maximális futásidő |3 óra |Csak az Azure sandboxes csak vonatkozik.|
| Automation-fiókok maximális száma egy előfizetésben |Nincs korlátozás ||
| Hibrid feldolgozócsoportok maximális száma automatizálási fiókonként|4,000||
|Egyetlen hibrid runbook-feldolgozón futtatható egyidejű feladatok maximális száma|50 ||
| Runbook-feladat paraméterének maximális mérete   | 512 kilobit||
| Runbook-paraméterek maximális   | 50|Ha eléri az 50 paraméteres korlátot, átadhat egy JSON- vagy XML-karakterláncot egy paraméternek, és elemezheti azt a runbookkal.|
| Maximális webhook hasznos adatméret |  512 kilobit|
| A feladatadatok megőrzésének maximális napjai|30 nap|
| A PowerShell-munkafolyamat állapotának maximális mérete |5 MB| A PowerShell-munkafolyamat runbookokra vonatkozik a munkafolyamat ellenőrzőpont-készítésekekén.|

<sup>1 1</sup> A sandbox olyan megosztott környezet, amelyet több feladat is használhat. Az azonos sandboxot használó feladatokat a sandbox erőforrás-korlátai kötik.

#### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

Az alábbi táblázat a nyomon követett cikkkorlátokat mutatja be gépenként a változások nyomon követéséhez.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Registry|250||
|Windows szoftver|250|Nem tartalmaz szoftverfrissítéseket.|
|Linux csomagok|1,250||
|Szolgáltatások|250||
|Daemon|250||

#### <a name="update-management"></a>Frissítéskezelés

Az alábbi táblázat az Update Management korlátjait mutatja be.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Gépek száma frissítésenként központi telepítésenként|1000||
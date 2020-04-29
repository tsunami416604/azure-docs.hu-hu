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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334639"
---
#### <a name="process-automation"></a>Folyamatautomatizálás

| Erőforrás | Korlát |Megjegyzések|
| --- | --- |---|
| Azure Automation fiókban 30 másodpercenként elküldhető új feladatok maximális száma (nem ütemezett feladatok esetében) |100 |Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| Az egyidejű futó feladatok maximális száma egy Automation-fiókon (nem ütemezett feladatok). |200 |Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. Az ügyfél hibaüzenetet kap.|
| A feladatok metaadatainak maximális tárolási mérete egy 30 napos időszakra | 10 GB (körülbelül 4 000 000 feladat)|Ha eléri ezt a korlátot, a feladat létrehozására vonatkozó további kérések sikertelenek lesznek. |
| Maximális feladatokhoz tartozó adatfolyam-korlát|1MB|Egyetlen Stream nem lehet nagyobb 1 MB-nál.|
| Az Automation-fiókokban 30 másodpercenként importálható modulok maximális száma |5 ||
| Modul maximális mérete |100 MB ||
| Feladatok futási ideje, ingyenes szintű |500 perc/előfizetés naptári havonta ||
| Maximálisan engedélyezett lemezterület a homokozóban<sup>1</sup> |1 GB |Csak az Azure-beli munkapéldányokra vonatkozik.|
| A sandbox<sup>1</sup> számára megadott maximális memória mennyisége |400 MB |Csak az Azure-beli munkapéldányokra vonatkozik.|
| A másodpercenként engedélyezett hálózati szoftvercsatornák maximális száma<sup>1</sup> |1,000 |Csak az Azure-beli munkapéldányokra vonatkozik.|
| Engedélyezett maximális futtatókörnyezet (runbook<sup>1)</sup> |3 óra |Csak az Azure-beli munkapéldányokra vonatkozik.|
| Az előfizetéshez tartozó Automation-fiókok maximális száma |Korlátlan ||
| Hibrid feldolgozói csoportok maximális száma Automation-fiókban|4,000||
|Egyetlen hibrid Runbook-feldolgozón futtatható egyidejű feladatok maximális száma|50 ||
| Runbook-feladatok maximális mérete   | 512 kilobit||
| Maximális runbook paraméterek   | 50|Ha eléri a 50-paraméter korlátot, egy JSON-vagy XML-karakterláncot adhat át egy paraméternek, és elemezheti azt a runbook.|
| Webhook maximális hasznos adatának mérete |  512 kilobit|
| A feladattal megőrzött napok maximális száma|30 nap|
| Maximális PowerShell-munkafolyamat állapotának mérete |5 MB| A PowerShell-munkafolyamat runbookok vonatkozik a munkafolyamat ellenőrzőpontja során.|

<sup>1</sup> A sandbox egy megosztott környezet, amelyet több feladat is használhat. Azokat a feladatokat, amelyek ugyanazt a Sandboxot használják, a sandbox erőforrás-korlátai kötik.

#### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

A következő táblázat a nyomon követett elemek korlátozását mutatja gépenként a változások nyomon követéséhez.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Registry|250||
|Windows-szoftver|250|Nem tartalmaz szoftverfrissítéseket.|
|Linux-csomagok|1 250||
|Szolgáltatások|250||
|Démon|250||

#### <a name="update-management"></a>Frissítéskezelés

A következő táblázat a Update Management korlátozásait mutatja be.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Gépek száma frissítési központi telepítéssel|1000||
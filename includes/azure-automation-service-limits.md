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
ms.openlocfilehash: 2823a33b25812a69ad463433bacd9710655c9176
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179526"
---
#### <a name="process-automation"></a>Folyamatautomatizálás

| Resource | Felső korlát |Megjegyzések|
| --- | --- |---|
| Így el lehet egy Azure Automation-fiók (nonscheduled feladat) 30 másodpercenként új feladatok maximális száma |100 |Ha eléri ezt a korlátot, sikertelen, hozzon létre egy feladatot a későbbi kérelmeket. Az ügyfél hibaüzenetet kap.|
| Az Automation-fiók (nonscheduled feladat) ideje példányát, egyidejűleg futó feladatok maximális száma |200 |Ha eléri ezt a korlátot, sikertelen, hozzon létre egy feladatot a későbbi kérelmeket. Az ügyfél hibaüzenetet kap.|
| Feladat-metaadatok egy 30 napos gördülő időszakra maximális mérete | 10 GB (4 millió feladatok megközelítőleg)|Ha eléri ezt a korlátot, sikertelen, hozzon létre egy feladatot a későbbi kérelmeket. |
| Maximális stream korlát|1MB|Egyetlen streammel nem lehet nagyobb, mint 1 MB.|
| Modulok egy Automation-fiók 30 másodpercenként importálható maximális száma |5 ||
| Modulok maximális mérete |100 MB ||
| Feladat futási ideje, ingyenes szint |előfizetés naptári hónapban 500 perc ||
| Lemezterület védőfalat engedélyezett maximális mennyisége<sup>1</sup> |1 GB |Csak Azure próbakörnyezetbe lefordítja vonatkozik.|
| A védőfal megadott maximális memóriamennyiség<sup>1</sup> |400 MB |Csak Azure próbakörnyezetbe lefordítja vonatkozik.|
| Maximálisan engedélyezett tesztkörnyezet hálózati szoftvercsatornák számát<sup>1</sup> |1,000 |Csak Azure próbakörnyezetbe lefordítja vonatkozik.|
| Runbook engedélyezett maximális futásideje<sup>1</sup> |3 óra |Csak Azure próbakörnyezetbe lefordítja vonatkozik.|
| Egy adott előfizetés az Automation-fiókok maximális száma |Korlátlan ||
| Automation-fiókonként Hibridfeldolgozó-csoportok maximális száma|4,000||
|Egyetlen hibrid Runbook-feldolgozón futtatható egyidejű feladatok maximális száma|50 ||
| Maximális runbook feladat paraméter mérete   | legalább 512 kilobit||
| Maximális runbook-paraméterek   | 50|Ha eléri az 50-paraméter korlátot, JSON vagy XML-karakterlánc át a paramétert, és elemezni, a runbook.|
| Maximális webhook hasznos adatainak mérete |  legalább 512 kilobit|
| Feladat adatainak megőrzött napok maximális száma|30 nap|
| Maximális PowerShell munkafolyamat-állapot mérete |5 MB| PowerShell munkafolyamat forgatókönyvekkel vonatkozik, ha ellenőrzőpont-munkafolyamat.|

<sup>1</sup>egy tesztkörnyezet-e, hogy több feladat is lehet megosztott környezetben. Az azonos tesztkörnyezetben használó feladatok által az erőforrás-korlátozások, a védőfal vannak kötve.

#### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

Az alábbi táblázat a change Tracking szolgáltatáshoz gép követett elemek vonatkozó korlátok.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Beállításjegyzék|250||
|Windows szoftverek|250|Nem tartalmazza a szoftverfrissítéseket.|
|Linux-csomagok|1,250||
|Szolgáltatások|250||
|Démon|250||

#### <a name="update-management"></a>Frissítéskezelés

Az alábbi táblázat a korlátok az Update Management.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Frissítéstelepítés / gépek száma|1000||
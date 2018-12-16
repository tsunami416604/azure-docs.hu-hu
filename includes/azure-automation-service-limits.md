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
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444168"
---
#### <a name="process-automation"></a>Folyamatautomatizálás

| Erőforrás | Felső korlát |Megjegyzések|
| --- | --- |---|
| Így el lehet egy Automation-fiók (nem ütemezett feladatok) 30 másodpercenként új feladatok maximális száma |100 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
| Az Automation-fiók (nem ütemezett feladatok) ideje példányát, egyidejűleg futó feladatok maximális száma |200 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
| Feladat-metaadatok a működés közbeni egy 30 napos időszak maximális tárolómérete. | 10GB (4 millió feladatok megközelítőleg)|Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. |
| Modulok egy Automation-fiók 30 másodpercenként importálható maximális száma |5 ||
| Modulok maximális mérete |100 MB ||
| Feladat futtatási idő – ingyenes |előfizetés naptári hónapban 500 perc ||
| Lemezterület védőfalat engedélyezett maximális mennyiséget  **<sup>1</sup>** |1 GB |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| A védőfal megadott memória maximális mennyisége  **<sup>1</sup>** |400 MB |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| Hálózati szoftvercsatorna védőfal engedélyezett maximális száma  **<sup>1</sup>** |1000 |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| Runbook engedélyezett maximális futásideje  **<sup>1</sup>** |3 óra |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| Maximális száma az Automation-fiókok egy előfizetésben |Korlátlan ||
|Maximális számú párhuzamosan futó feladatok száma, amelyek futtathat egy egyszeri hibrid Runbook-feldolgozón|50 ||
| Runbook-feladat maximális paraméterek mérete   | 512 kb||
| Maximális Runbook-paraméterek   | 50|JSON vagy XML-karakterlánc át a paramétert, és elemezni kell azt a runbookot, ha eléri a 50 paraméter korlát|
| Maximális száma a webhook hasznos adatainak mérete |  512 kb|
| Feladat adatainak megőrzött napok maximális száma|30 nap|
| PowerShell-munkafolyamat maximális állapot mérete |5 MB| PowerShell-munkafolyamati runbookok vonatkozik, ha ellenőrzőpont-munkafolyamat.|

**<sup>1</sup>**  egy tesztkörnyezet több feladat által használt megosztott környezetben, a feladatokat az ugyanazon a védőfal által az erőforrás-korlátozások, a védőfal vannak kötve.

#### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

Az alábbi táblázat bemutatja a gépek nyomon követett elemek vonatkozó korlátok a Change Tracking megoldásba.

| **Erőforrás** | **Korlát**| **Megjegyzések** |
|---|---|---|
|Fájl|500||
|Beállításjegyzék|250||
|Windows szoftverek|250|Nem tartalmaz szoftverfrissítéseket|
|Linux-csomagok|1250||
|Szolgáltatások|250||
|Démon|250||

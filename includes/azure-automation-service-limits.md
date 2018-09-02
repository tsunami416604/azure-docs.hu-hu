---
title: fájl belefoglalása
description: fájl belefoglalása
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 04/05/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: e0ab3476713b1fc491c7c8f2e02263c283d42440
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "43435629"
---
| Erőforrás | Felső korlát |Megjegyzések|
| --- | --- |---|
| Így el lehet egy Automation-fiók (nem ütemezett feladatok) 30 másodpercenként új feladatok maximális száma |100 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
| Az Automation-fiók (nem ütemezett feladatok) ideje példányát, egyidejűleg futó feladatok maximális száma |200 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
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
| Maximális száma a webhook hasznos adatainak mérete |  512 kb|

**<sup>1</sup>**  egy tesztkörnyezet több feladat által használt megosztott környezetben, a feladatokat az ugyanazon a védőfal által az erőforrás-korlátozások, a védőfal vannak kötve.

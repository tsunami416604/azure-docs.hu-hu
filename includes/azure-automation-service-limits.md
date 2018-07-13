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
ms.openlocfilehash: 6b6e4afa7c8b18c8ce9af8c6abd371b4321e3343
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755929"
---
| Erőforrás | Felső korlát |Megjegyzések|
| --- | --- |---|
| Így el lehet egy Automation-fiók (nem ütemezett feladatok) 30 másodpercenként új feladatok maximális száma |100 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
| Az Automation-fiók (nem ütemezett feladatok) ideje példányát, egyidejűleg futó feladatok maximális száma |200 |Ezt a korlátot, nyomja le, amikor létrehoz egy feladatot a későbbi kérelmeket sikertelen. Az ügyfél hibaüzenetet kap.|
| Modulok egy Automation-fiók 30 másodpercenként importálható maximális száma |5 ||
| Modulok maximális mérete |100 MB ||
| Feladat futtatási idő – ingyenes |előfizetés naptári hónapban 500 perc ||
| Lemezterület védőfalat engedélyezett maximális mennyiséget**<sup>1</sup>** |1 GB |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| A védőfal megadott memória maximális mennyisége**<sup>1</sup>** |400 MB |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| Hálózati szoftvercsatorna védőfal engedélyezett maximális száma**<sup>1</sup>** |1000 |Csak Azure próbakörnyezetbe lefordítja vonatkozik|
| Maximális száma az Automation-fiókok egy előfizetésben |Korlátlan ||
|Maximális számú párhuzamosan futó feladatok száma, amelyek futtathat egy egyszeri hibrid Runbook-feldolgozón|50 ||

**<sup>1</sup>**  egy tesztkörnyezet több feladat által használt megosztott környezetben, a feladatokat az ugyanazon a védőfal által az erőforrás-korlátozások, a védőfal vannak kötve.

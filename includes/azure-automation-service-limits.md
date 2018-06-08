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
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34852041"
---
| Erőforrás | Felső korlát |Megjegyzések|
| --- | --- |---|
| Új Automation-fiók (nem ütemezett feladatok) / 30 másodpercenként küldheti el feladatok maximális száma |100 |Ha ezt a határt, találat, a későbbi kérelmek feladat létrehozása sikertelen. Az ügyfél egy hibaüzenetet kap.|
| Automation-fiók (nem ütemezett feladatok) idő ugyanazt a példányát, egyidejűleg futó feladatok maximális száma |200 |Ha ezt a határt, találat, a későbbi kérelmek feladat létrehozása sikertelen. Az ügyfél egy hibaüzenetet kap.|
| Automation-fiók / 30 másodpercenként importálható modulok maximális száma |5 ||
| A modul maximális mérete |100 MB ||
| A feladat futtatása időpontja - ingyenes szint |naptári hónaponként előfizetésenként 500 perc ||
| Maximális memóriamennyiség engedélyezett-e a védőfal**<sup>1</sup>** |1 GB |Csak az Azure védőfalak vonatkozik|
| A védőfal megadott memória maximális mennyisége**<sup>1</sup>** |400 MB |Csak az Azure védőfalak vonatkozik|
| Hálózati sockets védőfal telepítésenként engedélyezett maximális száma**<sup>1</sup>** |1000 |Csak az Azure védőfalak vonatkozik|
| Max. eseményenkénti Automation-fiók egy előfizetésben található |Korlátlan ||
|Maximális száma az egyidejű feladatok futtathatók, az egyetlen hibrid forgatókönyv-feldolgozó|50 ||

**<sup>1</sup>**  védőfalat olyan több feladat által használt megosztott környezet, az azonos védőfal használó feladatok kötik az erőforrás-korlátozások, a védőfal.

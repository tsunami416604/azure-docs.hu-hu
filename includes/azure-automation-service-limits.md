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
ms.openlocfilehash: 03a1235b9bf20fa19f378802923d3bab7dbf4900
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
| Erőforrás | Felső korlát |
| --- | --- |
| Új Automation-fiók (nem ütemezett feladatok) / 30 másodpercenként küldheti el feladatok maximális száma |100 |
| Automation-fiók (nem ütemezett feladatok) idő ugyanazt a példányát, egyidejűleg futó feladatok maximális száma |200 |
| Automation-fiók / 30 másodpercenként importálható modulok maximális száma |5 |
| A modul maximális mérete |100 MB |
| A feladat futtatása időpontja - ingyenes szint |naptári hónaponként előfizetésenként 500 perc |
| A feladatok megadott maximális memóriamennyiséget <sup>1</sup> |400 MB |
| Hálózati sockets feladat telepítésenként engedélyezett maximális száma <sup>1</sup> |1000 |
| Max. eseményenkénti Automation-fiók egy előfizetésben található |Korlátlan |

<sup>1</sup> ezek a korlátozások csak az Azure-ban védőfalak alkalmazni, a hibrid feldolgozók ezek csak korlátozza a gép, ahol a hibrid feldolgozó képességeit.
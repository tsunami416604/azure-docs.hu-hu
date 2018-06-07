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
ms.openlocfilehash: 8242f2bb16b52e8c319027788d5b937d7f79ad3d
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34665062"
---
| Erőforrás | Felső korlát |
| --- | --- |
| Új Automation-fiók (nem ütemezett feladatok) / 30 másodpercenként küldheti el feladatok maximális száma |100 |
| Automation-fiók (nem ütemezett feladatok) idő ugyanazt a példányát, egyidejűleg futó feladatok maximális száma |200 |
| Automation-fiók / 30 másodpercenként importálható modulok maximális száma |5 |
| A modul maximális mérete |100 MB |
| A feladat futtatása időpontja - ingyenes szint |naptári hónaponként előfizetésenként 500 perc |
| A feladatok megadott maximális memóriamennyiséget  **<sup>1</sup>** |400 MB |
| Hálózati sockets feladat telepítésenként engedélyezett maximális száma  **<sup>1</sup>** |1000 |
| Max. eseményenkénti Automation-fiók egy előfizetésben található |Korlátlan |

**<sup>1</sup>**  ezek a korlátozások csak az Azure-ban védőfalak alkalmazni, a hibrid feldolgozók ezek csak korlátozza a gép, ahol a hibrid feldolgozó képességeit.
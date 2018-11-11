---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 04/30/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 2425d9455606f5eabba4b89cfa238b7a928be30e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285690"
---
####

Az alábbi korlátozások érvényesek a rendszer Event Grid-témakörök és egyéni üzenettémákat, *nem* esemény tartományok.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök Azure-előfizetésenként | 100 |
| A témakör eseményfeliratkozások | 500 |
| Közzététele egy egyéni témakör (bejövő) arány | a témakör másodpercenként 5 000 események |

####

Az alábbi korlátozások kizárólag esemény tartományok vonatkozik.

| Erőforrás | Korlát |
| --- | --- |
| Esemény tartományonként kapcsolatos témakörök | nyilvános előzetes verzió ideje alatt 1000 |
| A témakör egy tartományon belüli Eseményfeliratkozások | a nyilvános előzetes verzióban 50 |
| Tartományi hatókör esemény-előfizetések | a nyilvános előzetes verzióban 50 |
| Forgalmi közzététele egy esemény-tartomány (bejövő) | 5000 események száma másodpercenként a nyilvános előzetes verzióban |
---
title: fájl belefoglalása
description: fájl belefoglalása
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 3f94481e6a8550479788d92c744327e1dc3b58c4
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66376919"
---
Az alábbi korlátok vonatkoznak az Azure Event Grid rendszer témaköröket és egyéni témakörök *nem* esemény tartományok.

| Resource | Korlát |
| --- | --- |
| Egyéni témakörök Azure-előfizetésenként | 100 |
| A témakör eseményfeliratkozások | 500 |
| Közzététele egy egyéni témakör (bejövő) arány | a témakör másodpercenként 5 000 események |
| Kérelem közzététele | 250 másodpercenként |
| Esemény mérete | Támogatja a 64 KB-os általános rendelkezésre állás (GA). 1 MB-támogatása jelenleg előzetes verzióban érhető el. |

Az alábbi korlátok vonatkoznak, az csak az esemény-tartományok.

| Resource | Korlát |
| --- | --- |
| Esemény tartományonként kapcsolatos témakörök | nyilvános előzetes verzió ideje alatt 1000 |
| A témakör egy tartományon belüli eseményfeliratkozások | a nyilvános előzetes verzióban 50 |
| Tartományi hatókör esemény-előfizetések | a nyilvános előzetes verzióban 50 |
| Egy esemény-tartomány (bejövő) tartozó közzététele | 5000 események száma másodpercenként a nyilvános előzetes verzióban |
| Kérelem közzététele | 250 másodpercenként |
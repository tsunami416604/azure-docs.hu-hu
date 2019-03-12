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
ms.openlocfilehash: 443db1b4609e62fb7c57de417e42a2b4d0737ada
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553623"
---
Az alábbi korlátok vonatkoznak az Azure Event Grid rendszer témaköröket és egyéni témakörök *nem* esemény tartományok.

| Erőforrás | Korlát |
| --- | --- |
| Egyéni témakörök Azure-előfizetésenként | 100 |
| A témakör eseményfeliratkozások | 500 |
| Közzététele egy egyéni témakör (bejövő) arány | a témakör másodpercenként 5 000 események |

Az alábbi korlátok vonatkoznak, az csak az esemény-tartományok.

| Erőforrás | Korlát |
| --- | --- |
| Esemény tartományonként kapcsolatos témakörök | nyilvános előzetes verzió ideje alatt 1000 |
| A témakör egy tartományon belüli eseményfeliratkozások | a nyilvános előzetes verzióban 50 |
| Tartományi hatókör esemény-előfizetések | a nyilvános előzetes verzióban 50 |
| Egy esemény-tartomány (bejövő) tartozó közzététele | 5000 események száma másodpercenként a nyilvános előzetes verzióban |
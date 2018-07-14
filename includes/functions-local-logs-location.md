---
title: fájl belefoglalása
description: fájl belefoglalása
services: functions
author: ggailey777
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/01/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 88c01e8e57d4a92478b8b1ca0689ff0f8e499b39
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38739495"
---
Amikor a függvények gazdagépen helyileg fut, naplók ír a következő elérési út:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

A Windows `<DefaultTempDirectory>` az első megtalált értéke a TMP, TEMP, USERPROFILE környezeti változókat, vagy a Windows könyvtárban.
MacOS vagy Linux-alapú `<DefaultTempDirectory>` a TMPDIR környezeti változó.

> [!NOTE]
> A Functions gazdagép indításakor, felülírja a meglévő fájlstruktúra a címtárban.
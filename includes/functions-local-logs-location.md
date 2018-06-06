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
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "33814628"
---
Ha a funkciók állomás helyileg futtatja, naplók ír a következő elérési út:

```
<DefaultTempDirectory>\LogFiles\Application\Functions
```

A Windows rendszeren `<DefaultTempDirectory>` az első megtalált érték a TMP, TEMP, USERPROFILE környezeti változók vagy a Windows-könyvtár.
MacOS vagy Linux `<DefaultTempDirectory>` a TMPDIR környezeti változó.

> [!NOTE]
> A funkciók gazdagép indításakor felülírja a meglévő fájlstruktúra a címtárban.
---
title: fájl belefoglalása
description: fájl belefoglalása
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>A Visual STUDIO Code kiterjesztésű hibakeresési eszközök inicializálása
Először a kód projekt konfigurálásához, így a fejlesztési környezetet az Azure-ban a Visual STUDIO Code kommunikál. A Visual STUDIO Code kiterjesztése Azure fejlesztői szóközt tartalmaz egy segítő parancsot a hibakeresési konfiguráció beállítása. 

Nyissa meg a **parancs paletta** (használja a **nézet |} A parancs paletta** menü), és automatikusan végre írja be, és válassza ki ezt a parancsot használja: `Azure Dev Spaces: Create configuration files for connected development`. 

Ez biztosítja, hogy a Azure fejlesztői szóközöket a hibakeresési konfigurációját a `.vscode` mappát.

![](../media/common/command-palette.png)

> [!Important]
> Egy hiba miatt azonban zárja be és nyissa meg újra a folytatás előtt VS kódot.
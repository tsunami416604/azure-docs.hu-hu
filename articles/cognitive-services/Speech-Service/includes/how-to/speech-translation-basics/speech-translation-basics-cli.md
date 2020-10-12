---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: v-demjoh
ms.openlocfilehash: f28dd3e94db9d16645bf0d63841a17ee66defd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776654"
---
A Speech Service egyik fő funkciója az emberi beszéd felismerése és más nyelvekre való lefordítása. Ebből a rövid útmutatóból megtudhatja, hogyan használhatja a Speech SDK-t az alkalmazásaiban és termékeiben, hogy kiváló minőségű hangfordítást végezzen. Ez a rövid útmutató lefordítja a mikrofonról származó beszédet más nyelvű szövegre.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy rendelkezik Azure-fiókkal és Speech Service-előfizetéssel. Ha nem rendelkezik fiókkal és előfizetéssel, [próbálja ki ingyenesen a Speech szolgáltatást](../../../get-started.md).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="set-source-and-target-language"></a>Forrás és cél nyelvének beállítása

Ez a parancs meghívja a Speech CLI-t, hogy az olaszról franciára fordítson beszédet a mikrofonból.

```shell
 spx translate --microphone --source it-IT --target fr
```

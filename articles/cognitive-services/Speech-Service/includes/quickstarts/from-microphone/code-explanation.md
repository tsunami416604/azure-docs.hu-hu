---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400770"
---
A beszédfelismerési erőforrás-előfizetési kulcs és a régió beszédkonfigurációs objektum létrehozásához szükséges. A beszédfelismerő objektum példányosításához a konfigurációs objektumszükséges.

A felismerő példány a beszédfelismerés többféle módját teszi elérhetővé. Ebben a példában a rendszer egyszer ismeri fel a beszédet. Ez a funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása leállította a beszéd felismerését. Az eredmény után a kód a felismerés okát írja a konzolra.

> [!TIP]
> A beszédsdk alapértelmezés szerint `en-us` felismeri a nyelv használatát, olvassa el a forrásnyelv megadása a [beszéd és a szöveg forrásnyelvét](../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.
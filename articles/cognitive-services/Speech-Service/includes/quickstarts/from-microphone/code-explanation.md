---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: dapine
ms.openlocfilehash: 3125b6203f04b4cabd8cd0822a97317185cbf194
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80638133"
---
A beszédfelismerési erőforrás-előfizetési kulcs és a régió beszédkonfigurációs objektum létrehozásához szükséges. A beszédfelismerő objektum példányosításához a konfigurációs objektumszükséges.

A felismerő példány a beszédfelismerés többféle módját teszi elérhetővé. Ebben a példában a rendszer egyszer ismeri fel a beszédet. Ez a funkció lehetővé teszi, hogy a beszédfelismerési szolgáltatás tudja, hogy egyetlen kifejezést küld a felismeréshez, és hogy miután a kifejezés azonosítása leállította a beszéd felismerését. Az eredmény után a kód a felismerés okát írja a konzolra.

> [!TIP]
> A beszédsdk alapértelmezés szerint `en-us` felismeri a nyelv használatát, olvassa el a forrásnyelv megadása a [beszéd és a szöveg forrásnyelvét](../../../how-to-specify-source-language.md) a forrásnyelv kiválasztásával kapcsolatos információkért.
---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 3b975d9f0953c874c627e30b382ad8c7fa7cfc09
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84307813"
---
## <a name="about-cognitive-services-encryption"></a>Tudnivalók a Cognitive Services titkosításról

Az adattitkosítás és visszafejtés az [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) -kompatibilis [256 bites AES-](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítás használatával történik. A titkosítás és a visszafejtés átlátható, ami azt jelenti, hogy a titkosítás és a hozzáférés felügyelve van. Alapértelmezés szerint az adatai biztonságban vannak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás kihasználása érdekében.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Az előfizetését azonban a saját titkosítási kulcsaival is kezelheti. Az ügyfél által felügyelt kulcsok (CMK) – más néven saját kulcs használata (BYOK) – nagyobb rugalmasságot biztosítanak a hozzáférés-vezérlések létrehozásához, forgatásához, letiltásához és visszavonásához. Az adatai védelme érdekében használt titkosítási kulcsokat is naplózhatja.
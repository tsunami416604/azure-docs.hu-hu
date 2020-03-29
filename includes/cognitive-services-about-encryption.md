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
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372171"
---
## <a name="about-cognitive-services-encryption"></a>A Cognitive Services titkosítása

Az adatok titkosítása és visszafejtése [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) kompatibilis [256 bites AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) titkosítással történik. A titkosítás és a visszafejtés transzparens, ami azt jelenti, hogy a titkosítás és a hozzáférés kezelése az Ön számára. Az adatok alapértelmezés szerint biztonságosak, és nem kell módosítania a kódot vagy az alkalmazásokat a titkosítás előnyeinek kihasználásához.

## <a name="about-encryption-key-management"></a>Titkosítási kulcs kezelése –

Az előfizetés alapértelmezés szerint Microsoft által felügyelt titkosítási kulcsokat használ. Ha olyan tarifacsomagot használ, amely támogatja az ügyfél által felügyelt kulcsokat, az [azure](https://portal.azure.com)portal **titkosítási** szakaszában láthatja az erőforrás titkosítási beállításait, ahogy az az alábbi képen látható.

![Titkosítási beállítások megtekintése](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Olyan előfizetések esetén, amelyek csak a Microsoft által felügyelt titkosítási kulcsokat támogatják, nem lesz **Titkosítás** szakasz.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Ügyfél által felügyelt kulcsok az Azure Key Vault segítségével

Lehetőség van arra is, hogy az előfizetést saját kulcsokkal kezelje. Az ügyfél által felügyelt kulcsok (CMK), más néven a Saját kulcs (BYOK) létrehozása, elforgatása, letiltása és visszavonása nagyobb rugalmasságot biztosít a hozzáférés-vezérlők létrehozásához, elforgatásához, letiltásához és visszavonásához. Az adatok védelmére használt titkosítási kulcsokat is naplózhatja.

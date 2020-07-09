---
title: Felhasználói beállítások tárolása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan tárolhatja a felhasználó beállításait.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486907"
---
# <a name="how-to-store-user-preferences"></a>Felhasználói beállítások tárolása

Ez a cikk bemutatja, hogyan tárolhatja a felhasználó beállításait. Ez a funkció másodlagos eszközként szolgál a felhasználó beállításainak tárolásához abban az esetben, ha a cookie-k használata nem kívánatos vagy lehetséges.

## <a name="how-to-enable-storing-user-preferences"></a>Felhasználói beállítások tárolásának engedélyezése

A `options` paraméter tartalmazza a `onPreferencesChanged` visszahívást. Ezt a függvényt akkor kell meghívni, amikor a felhasználó megváltoztatja a beállításait (például megváltoztatja a szöveg méretét, a téma színét, a betűtípust stb.). A `value` paraméter egy karakterláncot tartalmaz, amely a felhasználó aktuális beállításait jelöli. Ezt a karakterláncot tetszőleges, tetszőleges mechanizmussal lehet tárolni.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Felhasználói beállítások betöltése a felhasználóbarát olvasóba

A paraméter használatával továbbítsa a felhasználó beállításait a magára az olvasóhoz `preferences` . A felhasználó preferenciáinak tárolására és betöltésére szolgáló triviális példa a következő:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>További lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
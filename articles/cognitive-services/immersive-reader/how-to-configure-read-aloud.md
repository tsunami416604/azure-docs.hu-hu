---
title: Felolvasás konfigurálása
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a beolvasás különböző beállításait.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 648227521e5e4e8feecd864d3e572a4758e551ca
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633263"
---
# <a name="how-to-configure-read-aloud"></a>A hangos olvasás konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a különböző beállításokat a részletes olvasóba való olvasáshoz.

## <a name="automatically-start-read-aloud"></a>A felolvasás automatikus indítása

A `options` paraméter tartalmazza az összes olyan jelzőt, amely a beolvasás konfigurálására használható. A be értékre állításával `autoplay` `true` engedélyezheti az automatikus olvasást, miután elindította a lebilincselő olvasót.

```typescript
const options = {
    readAloudOptions: {
        autoplay: true
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

> [!NOTE]
> A böngésző korlátozásai miatt a Safari nem támogatja az Automatikus lejátszást.

## <a name="configure-the-voice"></a>A hang konfigurálása

A következőre van beállítva: `voice` `male` vagy `female` . Nem minden nyelv támogatja mindkét hang használatát. További információ a [nyelvi támogatás](./language-support.md) oldalon található.

```typescript
const options = {
    readAloudOptions: {
        voice: 'female'
    }
};
```

## <a name="configure-playback-speed"></a>Lejátszás sebességének konfigurálása

Értékre állítva `speed` `0.5` (50%) és `2.5` (250%) határokat is beleértve. Az ezen a tartományon kívüli értékek a 0,5 vagy a 2,5 értékre lesznek rögzítve.

```typescript
const options = {
    readAloudOptions: {
        speed: 1.5
    }
};
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
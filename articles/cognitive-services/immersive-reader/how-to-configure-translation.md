---
title: Fordítás konfigurálása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan konfigurálhatja a fordítás különböző lehetőségeit.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 0a6ed6ecea216a36cfc9da4ef36a2bddc69cc6a8
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633144"
---
# <a name="how-to-configure-translation"></a>A fordítás konfigurálása

Ez a cikk bemutatja, hogyan konfigurálhatja a különböző fordítási lehetőségeket a teljes olvasóban.

## <a name="configure-translation-language"></a>Fordítási nyelv konfigurálása

A `options` paraméter tartalmazza az összes olyan jelzőt, amely a fordítás konfigurálására használható. Állítsa be a `language` paramétert arra a nyelvre, amelyet le szeretne fordítani. A támogatott nyelvek teljes listájáért tekintse meg a [nyelvi támogatást](./language-support.md) .

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>A dokumentum automatikus lefordítása betöltéskor

A értékre állítva `autoEnableDocumentTranslation` `true` engedélyezheti a teljes dokumentum automatikus lefordítását, amikor az olvasó betöltődik.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>A Word fordítás automatikus engedélyezése

Állítsa be az lehetőséget `autoEnableWordTranslation` `true` az egyszavas fordítás engedélyezéséhez.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
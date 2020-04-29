---
title: A megölelő olvasó gomb testreszabása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a teljes olvasót indító gombot.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946209"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>A megölelő olvasó gomb testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre a teljes olvasót, hogy illeszkedjen az alkalmazás igényeihez.

## <a name="add-the-immersive-reader-button"></a>A magával ejtő olvasó gomb hozzáadása

A lebilincselő olvasó SDK alapértelmezett stílust biztosít a magával ragadó olvasót indító gombhoz. A `immersive-reader-button` Class attribútum használatával engedélyezze ezt a stílust.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>A gomb stílusának testreszabása

A gomb `data-button-style` stílusának beállításához használja az attribútumot. A megengedett értékek `icon` `text`:, és `iconAndText`. Az alapértelmezett érték `icon`.

### <a name="icon-button"></a>Ikon gomb

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Ez a következőt jeleníti meg:

![Ikon gomb](./media/button-icon.png)

### <a name="text-button"></a>Text (szöveg) gomb

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Ez a következőt jeleníti meg:

![Ikon gomb](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ikon és szöveg gomb

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Ez a következőt jeleníti meg:

![Ikon gomb](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>A gomb szövegének testreszabása

Konfigurálja a gomb nyelvét és az ALT szövegét az `data-locale` attribútum használatával. Az alapértelmezett nyelv az angol.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Az ikon méretének testreszabása

A lebilincselő olvasó ikonjának mérete az `data-icon-px-size` attribútum használatával konfigurálható. Ezzel beállítja az ikon méretét képpontban megadva. Az alapértelmezett méret a 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>További lépések

* Ismerje meg az [olvasói SDK-referenciát](./reference.md)
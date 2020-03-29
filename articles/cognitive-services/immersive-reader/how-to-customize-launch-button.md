---
title: A Magával ragadó olvasó gomb testreszabása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a bemerítést elolvasó gombot.
services: cognitive-services
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 6461cc60a237cf834226b07db5e1ea6fbbea7a92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946209"
---
# <a name="how-to-customize-the-immersive-reader-button"></a>A Magával ragadó olvasó gomb testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre a gombot, amely elindítja a Magával ragadó olvasó, hogy megfeleljen az alkalmazás igényeinek.

## <a name="add-the-immersive-reader-button"></a>A Magával ragadó olvasó gomb hozzáadása

A Magával ragadó Reader SDK biztosítja az alapértelmezett stílus a gombot, amely elindítja a magával ragadó olvasó. A `immersive-reader-button` stílus engedélyezéséhez használja az osztályattribútumot.

```html
<div class='immersive-reader-button'></div>
```

## <a name="customize-the-button-style"></a>A gombstílus testreszabása

Az `data-button-style` attribútum segítségével állítsa be a gomb stílusát. Az engedélyezett `icon`értékek `text`a `iconAndText`, és a . Az alapértelmezett érték `icon`.

### <a name="icon-button"></a>Ikon gomb

```html
<div class='immersive-reader-button' data-button-style='icon'></div>
```

Ez a következőket teszi:

![Ikon gomb](./media/button-icon.png)

### <a name="text-button"></a>Szöveg gomb

```html
<div class='immersive-reader-button' data-button-style='text'></div>
```

Ez a következőket teszi:

![Ikon gomb](./media/button-text.png)

### <a name="icon-and-text-button"></a>Ikon és szöveg gomb

```html
<div class='immersive-reader-button' data-button-style='iconAndText'></div>
```

Ez a következőket teszi:

![Ikon gomb](./media/button-icon-and-text.png)

## <a name="customize-the-button-text"></a>A gomb szövegének testreszabása

Konfigurálja a gomb nyelvét és helyettesítő `data-locale` szövegét az attribútum használatával. Az alapértelmezett nyelv az angol.

```html
<div class='immersive-reader-button' data-locale='fr-FR'></div>
```

## <a name="customize-the-size-of-the-icon"></a>Az ikon méretének testreszabása

A Immersive Reader ikon mérete az `data-icon-px-size` attribútum használatával konfigurálható. Ezzel az ikon méretét képpontban állítja be. Az alapértelmezett méret 20px.

```html
<div class='immersive-reader-button' data-icon-px-size='50'></div>
```

## <a name="next-steps"></a>További lépések

* Fedezze fel a [magával ragadó Reader SDK-referenciát](./reference.md)
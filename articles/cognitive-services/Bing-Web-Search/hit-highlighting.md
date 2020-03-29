---
title: Dekorációjelölők használata szöveg kiemelésére - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként használhatja a szöveges dekorációkat és a leütési kiemelést a keresési eredmények között a Bing Web Search API használatával.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854012"
---
# <a name="using-decoration-markers-to-highlight-text"></a>Szöveg kiemelése dekorációs jelölőkkel

A Bing támogatja a leütéskiemelését, amely a lekérdezési kifejezéseket (vagy a Bing által relevánsnak talált egyéb kifejezéseket) jelöli meg egyes válaszok megjelenítési karakterláncaiban. Előfordulhat például, hogy egy `name` `displayUrl`weblap `snippet` eredményének , és mezői megjelölt lekérdezési kifejezéseket tartalmaznak. 

Alapértelmezés szerint a Bing nem tartalmaz kiemelő jelölőket a megjelenítési karakterláncokban. A jelölők engedélyezéséhez `textDecorations` adja meg a lekérdezési `true`paramétert a kérésben, és állítsa a beállítását .

## <a name="hit-highlighting-example"></a>Példa a találatok kiemelése

A következő példa a `Sailing Dinghy`webes eredményt mutatja be. A Bing az E000 és az E001 Unicode karakterekkel jelölte meg a lekérdezési kifejezés kezdetét és végét.
  
![Találat kiemelése](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

Mielőtt az eredményt a felhasználói felületen megjelenítené, cserélje le a Unicode karaktereket azokra, amelyek megfelelnek a megjelenítési formátumnak.

## <a name="marker-formatting"></a>Jelölő formázása

A Bing lehetővé teszi, hogy Unicode karaktereket vagy HTML-címkéket használjon jelölőként. A használni kívánt jelölők megadásához adja meg a [textFormat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat) lekérdezésparamétert: 

| Érték             | Marker                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | Unicode karakterek (alapértelmezett) |
| `textFormat=HTML` | HTML-karakterek              |

## <a name="additional-text-decorations"></a>További szövegdekorációk

A Bing több különböző szövegdekorációt is visszaadhat. A `Computation` válasz tartalmazhat például a `log(2)` `expression` mezőben lévő lekérdezési kifejezés alsó indexeit.

![számítási jelölők](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

Ha a kérelem nem tartalmaz `expression` dekorációt, `log10(2)`a mező a . 

Ha `textDecorations` `true`van, a Bing a következő jelölőket is felveheti a válaszok megjelenítési karakterláncaiba. Ha nincs egyenértékű HTML-címke, a táblázatcella üres.

|Unicode|HTML|Leírás
|-|-|-
|U+E000|\<b>|A lekérdezési kifejezés kezdetét jelöli (találat kiemelése)
|U+E001|\</b>|A lekérdezési kifejezés végét jelöli
|U+E002|\<>|Jelzi a dőlt tartalom kezdetét 
|U+E003|\</i>|Jelzi a dőlt tartalom végét
|U+E004|\<br/>|Sortörés tjelöl
|U+E005||Egy telefonszám kezdetét jelöli
|U+E006||Egy telefonszám végét jelöli
|U+E007||Cím kezdetét jelöli
|U+E008||Egy cím végét jelöli
|U+E009|\&nbsp;|Nem törik szóközt jelöl meg
|U+E00C|\<erős>|A félkövér tartalom kezdetét jelöli
|U+E00D|\</erős>|A félkövér tartalom végét jelzi
|U+E00E||Azon tartalom kezdetét jelöli, amelynek hátterének világosabbnak kell lennie, mint a környező háttérnek
|U+E00F||Azon tartalom végét jelöli, amelynek háttere világosabb, mint a környező háttéré
|U+E010||Azon tartalom kezdetét jelöli, amelynek háttere sötétebb legyen a környező háttérnél
|U+E011||Azon tartalom végét jelöli, amelynek háttere sötétebb legyen a környező háttérnél
|U+E012|\<del>|Megjelöli a tartalom kezdetét, amelyet át kell
|U+E013|\</del>|Jelzi a tartalom végét, amelyet át kell
|U+E016|\<al>|Megjelöli az alsó index tartalmának kezdetét
|U+E017|\</al>|Az alsó index tartalmának végét jelöli
|U+E018|\<sup>|A felső index tartalmának kezdetét jelöli
|U+E019|\</sup>|A felső index tartalmának végét jelöli

## <a name="next-steps"></a>További lépések

* [Mi a Bing Web Search API?](overview.md) 
* [Miniatűrök átméretezése és körülvágása](resize-and-crop-thumbnails.md)
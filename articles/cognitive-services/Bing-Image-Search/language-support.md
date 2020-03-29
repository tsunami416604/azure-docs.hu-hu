---
title: Nyelvi támogatás – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy mely országokat/régiókat és nyelveket támogatja a Bing Image Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881927"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>A Bing Image Search API nyelvi és területi támogatása

A Bing Image Search API több mint három tucat országot/régiót támogat, amelyek közül sok több nyelvet is támogat. Ha egy országot/régiót egy lekérdezéssel ad meg, az elsősorban az adott országban/régióban lévő érdeklődési körök alapján finomítja a keresési eredményeket. Ezenkívül az eredmények tartalmazhatnak bingre mutató hivatkozásokat, és ezek a hivatkozások a bingfelhasználói élményt a megadott ország/régió vagy nyelv szerint honosíthatják.

Az ország/régió és nyelv megadásához állítsa be a `mkt` (piaci) lekérdezési paramétert egy kódra az alábbi **Piacok** táblázatból. A piac egy országot/régiót és nyelvet is meghatároz. Ha a felhasználó a megjelenített szöveget más `setLang` nyelven szeretné látni, állítsa a lekérdezési paramétert a megfelelő nyelvi kódra.

Azt is megteheti, hogy megadja `cc` az országot/régiót a lekérdezési paraméter használatával. Ha ország/régió megadását adja meg, akkor egy vagy `Accept-Language` több nyelvkódot is meg kell adnia a HTTP fejléc használatával. A támogatott nyelvek országonként/régiónként eltérőek; a Piacok táblázatban minden egyes országra/régióra vonatkozóan meg vannak adva.

> [!NOTE]
> A Felkapott képek API jelenleg csak a következő piacokat támogatja:
> - en-US (English, Amerikai Egyesült Államok)
> - en-CA (angol, Kanada)
> - en-AU (Angol, Ausztrália)
> - zh-CN (kínai, Kína)

## <a name="countriesregions"></a>Országok/régiók

|Ország/régió|Kód|
|-------|----|
|Argentína|AR|
|Ausztrália|AU|
|Ausztria|AT|
|Belgium|BE|
|Brazília|BR|
|Kanada|CA|
|Chile|CL|
|Dánia|DK|
|Finnország|FI|
|Franciaország|JK|
|Németország|DE|
|Hongkong (KKT)|HK|
|India|IN|
|Indonézia|ID (Azonosító)|
|Olaszország|IT|
|Japán|JP|
|Dél-Korea|KR|
|Malajzia|MY|
|Mexikó|MX|
|Hollandia|NL|
|Új-Zéland|NZ|
|Norvégia|NO|
|Kína|CN|
|Lengyelország|PL|
|Portugália|PT|
|Fülöp-szigetek|PH|
|Oroszország|RU|
|Szaúd-Arábia|SA|
|Dél-afrikai Köztársaság|ZA|
|Spanyolország|ES|
|Svédország|SE|
|Svájc|CH|
|Tajvan|TW|
|Törökország|TR|
|Egyesült Királyság|GB|
|Egyesült Államok|USA|


## <a name="markets"></a>Piacok

|Ország/régió|Nyelv|Piaci kód|
|-------|--------|-----------|
|Argentína|Spanyol|es-AR|
|Ausztrália|Angol|en-AU|
|Ausztria|Német|de-AT|
|Belgium|Holland|nl-BE|
|Belgium|Francia|fr-BE|
|Brazília|Portugál|pt-BR|
|Kanada|Angol|en-CA|
|Kanada|Francia|fr-CA|
|Chile|Spanyol|es-CL|
|Dánia|Dán|da-DK|
|Finnország|Finn|fi-FI|
|Franciaország|Francia|fr-FR|
|Németország|Német|de-DE|
|Hongkong (KKT)|Kínai (hagyományos)|zh-HK|
|India|Angol|en-IN|
|Indonézia|Angol|en-ID|
|Olaszország|Olasz|it-IT|
|Japán|Japán|ja-JP|
|Dél-Korea|Koreai|ko-KR|
|Malajzia|Angol|hu-ÉN|
|Mexikó|Spanyol|es-MX|
|Hollandia|Holland|nl-NL|
|Új-Zéland|Angol|en-NZ|
|Kína|Kínai|zh-CN|
|Lengyelország|Lengyel|pl-PL|
|Portugália|Portugál|pt-PT|
|Fülöp-szigetek|Angol|en-PH|
|Oroszország|Orosz|ru-RU|
|Szaúd-Arábia|Arab|ar-SA|
|Dél-afrikai Köztársaság|Angol|en-ZA|
|Spanyolország|Spanyol|es-ES|
|Svédország|Svéd|sv-SE|
|Svájc|Francia|fr-CH|
|Svájc|Német|de-CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|Török|tr-TR|
|Egyesült Királyság|Angol|hu-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|Spanyol|es-US|

## <a name="next-steps"></a>További lépések
A Bing News Search végpontjairól a [Hírek képkeresési API v7-es hivatkozása című témakörben talál](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)további információt.

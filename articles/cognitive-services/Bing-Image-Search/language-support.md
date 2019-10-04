---
title: Nyelvi támogatás – Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Megtudhatja, mely országokat/régiókat és nyelveket támogatja a Bing Image Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: ca3821b6088e45730334d1b0971e270b1d86dfce
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881927"
---
# <a name="language-and-region-support-for-the-bing-image-search-api"></a>A Bing Image Search API nyelvi és területi támogatása

Az Bing Image Search API több mint három tucat országot/régiót támogat, több mint egy nyelvet. A lekérdezéssel rendelkező ország/régió megadásával elsődlegesen az adott országban vagy régióban lévő érdekeltségek alapján pontosíthatja a keresési eredményeket. Emellett az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, és ezek a hivatkozások a megadott ország/régiók vagy nyelv alapján lokalizálják a Bing felhasználói élményt.

Az ország/régió és a nyelv megadásához állítsa `mkt` be a (piaci) lekérdezési paramétert az alábbi **piacok** táblából származó kódra. A piac egy országot/régiót és nyelvet is meghatároz. Ha a felhasználó egy másik nyelven szeretné megtekinteni a megjelenítendő szöveget, állítsa `setLang` a lekérdezési paramétert a megfelelő nyelvi kódra.

Azt is megteheti, hogy a `cc` lekérdezési paraméterrel megadhatja az országot/régiót. Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` HTTP-fejléc használatával. A támogatott nyelvek ország/régió szerint változnak; a piacok táblában minden országhoz/régióhoz meg vannak adva.

> [!NOTE]
> A trend images API jelenleg csak a következő piacokat támogatja:
> - en-US (angol, Egyesült Államok)
> - en-CA (angol, Kanada)
> - EN-AU (angol, Ausztrália)
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
|Indonézia|id|
|Olaszország|IT|
|Japán|JP|
|Korea|KR|
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
|Dél-Afrika|ZA|
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
|Argentína|spanyol|es-AR|
|Ausztrália|Angol|en-Ausztrália|
|Ausztria|német|Németország-AT|
|Belgium|holland|nl-BE|
|Belgium|francia|fr-BE|
|Brazília|portugál|pt-BR|
|Kanada|Angol|en-hitelesítésszolgáltató|
|Kanada|francia|FR-hitelesítésszolgáltató|
|Chile|spanyol|es-CL|
|Dánia|dán|da-DK|
|Finnország|finn|fi-FI|
|Franciaország|francia|FR-FR|
|Németország|német|de-DE|
|Hongkong (KKT)|Hagyományos kínai|zh-HK|
|India|Angol|en-IN|
|Indonézia|Angol|EN-ID|
|Olaszország|olasz|it-IT|
|Japán|japán|ja-JP|
|Korea|koreai|ko-KR|
|Malajzia|Angol|en-MY|
|Mexikó|spanyol|es-MX|
|Hollandia|holland|NL-NL|
|Új-Zéland|Angol|en-NZ|
|Kína|kínai|zh-CN|
|Lengyelország|lengyel|pl-PL|
|Portugália|portugál|PT-PT|
|Fülöp-szigetek|Angol|EN-PH|
|Oroszország|orosz|ru-RU|
|Szaúd-Arábia|arab|ar-SA|
|Dél-Afrika|Angol|en-ZA|
|Spanyolország|spanyol|es-ES|
|Svédország|svéd|SV-SE|
|Svájc|francia|FR-CH|
|Svájc|német|Németország – CH|
|Tajvan|Hagyományos kínai|zh-TW|
|Törökország|török|tr-TR|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|spanyol|es-US|

## <a name="next-steps"></a>További lépések
További információ az Bing News Search-végpontokról: [hírek Image Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).

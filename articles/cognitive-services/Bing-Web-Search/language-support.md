---
title: Nyelvi támogatás – Bing Web Search API
titleSuffix: Azure Cognitive Services
description: A Bing News Search API által támogatott természetes nyelvek, országok és régiók listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 9425de6e75a9a46d71ff85ce49b0650c8e7a9a16
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882676"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>A Bing Web Search API nyelvi és területi támogatása

Az Bing Web Search API több mint három tucat országot vagy régiót támogat, több mint egy nyelvet. Ha egy olyan országot vagy régiót ad meg, amely egy lekérdezéssel segíti a keresési eredmények pontosítását az adott ország vagy régió érdekei alapján. Az eredmények tartalmazhatnak a Bingre mutató hivatkozásokat, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján honosítják meg a Bing felhasználói élményt.

A `cc` lekérdezési paraméter segítségével megadhat egy országot vagy régiót. Ha meg van adva egy ország vagy régió, meg kell adnia egy vagy több, a [ `Accept-Language` fejlécet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)tartalmazó nyelvi kódot. Az egyes [piacokon](#markets) támogatott nyelvek listáját a Markets (piacok) táblában végezheti el.

Másik lehetőségként megadhatja a piacot `mkt` a lekérdezési paraméterrel, valamint egy kódot a **piacok** táblából. A piac egyidejű megadása megadja az országot vagy régiót, valamint az előnyben részesített nyelvet. Explicit módon beállíthatja a nyelvet `setLang` a lekérdezési paraméterrel.

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
|Norvégia|norvég|nem – nem|
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

* [Bing Image Search API – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

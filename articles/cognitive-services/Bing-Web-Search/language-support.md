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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68882676"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>A Bing Web Search API nyelvi és területi támogatása

Az Bing Web Search API több mint három tucat országot vagy régiót támogat, több mint egy nyelvet. Ha egy olyan országot vagy régiót ad meg, amely egy lekérdezéssel segíti a keresési eredmények pontosítását az adott ország vagy régió érdekei alapján. Az eredmények tartalmazhatnak a Bingre mutató hivatkozásokat, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján honosítják meg a Bing felhasználói élményt.

A `cc` lekérdezési paraméter segítségével megadhat egy országot vagy régiót. Ha meg van adva egy ország vagy régió, meg kell adnia egy vagy több, a [ `Accept-Language` fejlécet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#headers)tartalmazó nyelvi kódot. Az egyes piacokon támogatott nyelvek listáját a [Markets (piacok) táblában](#markets) végezheti el.

Másik lehetőségként megadhatja a piacot `mkt` a lekérdezési paraméterrel, valamint egy kódot a **piacok** táblából. A piac egyidejű megadása megadja az országot vagy régiót, valamint az előnyben részesített nyelvet. Explicit módon beállíthatja a nyelvet a `setLang` lekérdezési paraméterrel.

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
|Ausztrália|Angol|EN-AU|
|Ausztria|Német|de-AT|
|Belgium|Holland|nl-BE|
|Belgium|Francia|fr-BE|
|Brazília|Portugál|pt-BR|
|Kanada|Angol|en-CA|
|Kanada|Francia|fr – CA|
|Chile|Spanyol|es – CL|
|Dánia|Dán|da-DK|
|Finnország|Finn|fi-FI|
|Franciaország|Francia|fr-FR|
|Németország|Német|de-DE|
|Hongkong (KKT)|Kínai (hagyományos)|ZH-HK|
|India|Angol|EN-IN|
|Indonézia|Angol|EN-ID|
|Olaszország|Olasz|it-IT|
|Japán|Japán|ja-JP|
|Dél-Korea|Koreai|ko-KR|
|Malajzia|Angol|EN-MY|
|Mexikó|Spanyol|es-MX|
|Hollandia|Holland|nl-NL|
|Új-Zéland|Angol|EN-NZ|
|Norvégia|Norvég|nem – nem|
|Kína|Kínai|zh-CN|
|Lengyelország|Lengyel|pl-PL|
|Portugália|Portugál|pt-PT|
|Fülöp-szigetek|Angol|EN-PH|
|Oroszország|Orosz|ru-RU|
|Szaúd-Arábia|Arab|ar-SA|
|Dél-afrikai Köztársaság|Angol|EN-ZA|
|Spanyolország|Spanyol|es-ES|
|Svédország|Svéd|sv-SE|
|Svájc|Francia|fr – CH|
|Svájc|Német|de-CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|Török|tr-TR|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|Spanyol|es – USA|

## <a name="next-steps"></a>További lépések

* [Bing Image Search API – referencia](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)

---
title: Nyelvi támogatás – a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Természetes nyelvek, ország és a Bing News Search API által támogatott régiók listáját.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 9cd9adfd9d882f3781f519846ce22767123da36c
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652263"
---
# <a name="language-and-region-support-for-the-bing-web-search-api"></a>A Bing Web Search API nyelvéhez és régiójához támogatása

A Bing Web Search API több mint három tucat országokban vagy régiókban, számos, az egynél több nyelvet támogat. Adjon meg egy ország vagy régió lekérdezéssel adott ország vagy régió kimutatott érdeklődések alapján találatok szűkítése segítségével. Az eredmények tartalmazhatják a Bing mutató hivatkozásokat, és ezeket a hivatkozásokat is honosítani a Bing felhasználói élmény az adott ország/régió vagy nyelv szerint.

Ország vagy régió használatával megadhatja a `cc` lekérdezési paraméter. Ha egy ország vagy régió van megadva, meg kell adnia egy vagy több, a nyelvi kódot a [ `Accept-Language` fejléc](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#headers). Használja a [piacok tábla](#markets) az adott piacon támogatott nyelvek listáját.

Másik lehetőségként megadhatja a piac a `mkt` lekérdezési paraméter, és a egy kódot a **piacok** tábla. Adja meg a piacon egyidejűleg megadja egy ország vagy régió és a egy előnyben részesített nyelvi. Explicit módon beállíthat a nyelvet, de a `setLang` lekérdezési paraméter.

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
|Hongkong KKT|HK|
|India|IN|
|Indonézia|ID (Azonosító)|
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
|Belgium|francia|FR-lehet|
|Brazília|portugál|pt-BR|
|Kanada|Angol|en-hitelesítésszolgáltató|
|Kanada|francia|FR-hitelesítésszolgáltató|
|Chile|spanyol|es-CL|
|Dánia|dán|da-DK|
|Finnország|finn|fi-FI|
|Franciaország|francia|FR-FR|
|Németország|német|de-DE|
|Hongkong KKT|Kínai (hagyományos)|zh-HK|
|India|Angol|en-IN|
|Indonézia|Angol|en-azonosító|
|Olaszország|olasz|it-IT|
|Japán|japán|ja-JP|
|Korea|koreai|ko-KR|
|Malajzia|Angol|en-MY|
|Mexikó|spanyol|es-MX|
|Hollandia|holland|NL-NL|
|Új-Zéland|Angol|en-NZ|
|Norvégia|norvég|no-NO|
|Kína|kínai|zh-CN|
|Lengyelország|lengyel|pl-PL|
|Portugália|portugál|PT-PT|
|Fülöp-szigetek|Angol|en-PH|
|Oroszország|orosz|ru-RU|
|Szaúd-Arábia|arab|ar-SA|
|Dél-Afrika|Angol|en-ZA|
|Spanyolország|spanyol|es-ES|
|Svédország|svéd|SV-SE|
|Svájc|francia|FR-CH|
|Svájc|német|Németország – CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|török|tr-TR|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|spanyol|es-US|

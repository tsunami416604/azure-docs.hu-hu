---
title: Nyelvi támogatás – Bing Custom Search API
titleSuffix: Azure Cognitive Services
description: Támogatott nyelvek és a Bing Custom Search API régiók listáját.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: 8fa70705d1dff5c3150fa5868938a3c50ef863fe
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815563"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>A Bing Custom Search API nyelvéhez és régiójához támogatása

A Bing Custom Search API több mint három tucat országok/régiók, számos, az egynél több nyelvet támogat.

Bár nem kötelező, a kérésnek meg kell határoznia a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ahol azt szeretné, hogy az eredményeket kell meghatároznia. Opcionális lekérdezési paraméterek listáját lásd: [lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Megadhatja, hogy egy ország/régió használata a `cc` lekérdezési paraméter. Ha megad egy adott ország/régió, meg kell adnia egy vagy több nyelvi kódot használ a `Accept-Language` fejléc. Ország/régió; szerint változik a támogatott nyelvek minden ország/régió a generálnak számukra a **piacok** tábla.

A `Accept-Language` fejlécet és a `setLang` lekérdezési paraméter, egymást kölcsönösen kizáró – ne adja meg mindkettőt. További információkért lásd: [Accept-nyelv](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countries"></a>Országok

|Ország/régió|Kód|
|-------|----|
|Argentína|AR|
|Ausztrália|AUSZTRÁLIA|
|Ausztria|AT|
|Belgium|LEHET|
|Brazília|BR|
|Kanada|CA|
|Chile|CL|
|Dánia|DK|
|Finnország|FI|
|Franciaország|JK|
|Németország|NÉMETORSZÁG|
|Hongkong KKT|HK|
|India|INDIA|
|Indonézia|ID (Azonosító)|
|Olaszország|IT|
|Japán|JP|
|Korea|KOREA|
|Malajzia|SAJÁT|
|Mexikó|MX|
|Hollandia|NL|
|Új-Zéland|NZ|
|Norvégia|NEM|
|Kína|CN|
|Lengyelország|PL|
|Portugália|CSENDES-ÓCEÁNI IDŐ|
|Fülöp-szigetek|PH|
|Oroszország|KÉRELEMEGYSÉG|
|Szaúd-Arábia|SA|
|Dél-Afrika|ZA|
|Spanyolország|ES|
|Svédország|HASZNÁLATA|
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
|Malajzia|Angol|a saját en|
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
|Egyesült Államok|Angol|hu-HU|
|Egyesült Államok|spanyol|es-USA|

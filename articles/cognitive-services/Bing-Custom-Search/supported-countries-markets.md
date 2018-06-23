---
title: Támogatott országokból és nyelvek Bing egyéni keresési API-hoz az Azure-on |} Microsoft Docs
description: Megtudhatja, mely országokból és a nyelv által támogatott, a Bing egyéni keresési API.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 10/19/2017
ms.author: v-gedod
ms.openlocfilehash: 7ff309f9b789662c4ebd791dffaa2bc2e440763e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347878"
---
# <a name="bing-custom-search-countries-and-languages"></a>Bing egyéni keresés országokból és nyelvek

Az egyéni Bing keresési API támogatja a több mint három tucat országok, számos egynél több nyelv. 

Bár nem kötelező, a kérelem adja meg a [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#mkt) lekérdezési paraméter, amely azonosítja a piacon, ha azt szeretné, hogy az eredmények származnia. Nem kötelező lekérdezési paraméterek listáját lásd: [lekérdezés-paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters)

Megadhat egy ország használatával a `cc` lekérdezési paraméter. Ha megad egy ország, is meg kell egy vagy több nyelvkódjait használatával a `Accept-Language` fejléc. A támogatott nyelvek változhat ország; az minden olyan országban, számukra a **piacok** tábla.

A `Accept-Language` fejléc és a `setLang` lekérdezésparaméter, egymást kölcsönösen kizáró – nem adható meg egyszerre. További információkért lásd: [elfogadás-nyelv](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#acceptlanguage).

## <a name="countries"></a>Országok

|Ország|Kód|
|-------|----|
|Argentína|AR|
|Ausztrália|AU|
|Ausztria|AT|
|Belgium|LEHET|
|Brazília|BR|
|Kanada|CA|
|Chile|CL|
|Dánia|DK|
|Finnország|FI|
|Franciaország|JK|
|Németország|NÉMETORSZÁG|
|Hongkong|HK|
|India|IN|
|Indonézia|ID (Azonosító)|
|Olaszország|IT|
|Japán|JP|
|Korea|KR|
|Malajzia|A|
|Mexikó|MX|
|Hollandia|NL|
|Új-Zéland|NZ|
|Norvégia|NEM|
|Kína|CN|
|Lengyelország|PL|
|Portugália|PT|
|Fülöp-szigetek|PH|
|Oroszország|RU|
|Szaúd-Arábia|RENDSZERGAZDAI (SA)|
|Dél-Afrika|ZA|
|Spanyolország|ES|
|Svédország|HASZNÁLATA|
|Svájc|CH|
|Tajvan|TW|
|Törökország|TR|
|Egyesült Királyság|GB|
|Egyesült Államok|USA|


## <a name="markets"></a>Piacok

|Ország|Nyelv|Piaci kódot|
|-------|--------|-----------|
|Argentína|spanyol|es-AR|
|Ausztrália|Angol|en-AU|
|Ausztria|német|de-AT|
|Belgium|holland|nl-BE|
|Belgium|francia|FR-kell|
|Brazília|portugál|pt-BR|
|Kanada|Angol|en-Kanada|
|Kanada|francia|FR-Kanada|
|Chile|spanyol|es-CL|
|Dánia|dán|da-DK|
|Finnország|finn|fi-FI|
|Franciaország|francia|FR-FR|
|Németország|német|de-DE|
|Hongkong|Kínai (hagyományos)|zh-HK|
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
|Svájc|német|de-CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|török|tr-TR|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Egyesült Államok|spanyol|es-US|

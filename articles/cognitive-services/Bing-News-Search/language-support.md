---
title: Nyelvi támogatás – Bing News Search API
titleSuffix: Azure Cognitive Services
description: Természetes nyelvek, ország és a Bing News Search API által támogatott régiók listáját.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: e8a7f6d20ed36c4b3e900602fb52e31dceefae88
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799326"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>A Bing News Search API nyelvéhez és régiójához támogatása

A Bing News Search API számos országok/régiók, számos, az egynél több nyelvet támogat. Adjon meg egy adott ország/régió lekérdezéssel elsősorban az ország/régió kimutatott érdeklődések alapján találatok szűkítése szolgálja ki. Ezenkívül az eredmények Bing mutató hivatkozásokat is tartalmazhat, és ezeket a hivatkozásokat is honosítani a Bing felhasználói élmény az adott ország/régió vagy nyelv szerint.

Megadhatja, hogy egy ország/régió használata a `cc` lekérdezési paraméter. Ha megad egy adott ország/régió, meg kell adnia egy vagy több nyelvi kódot használ a `Accept-Language` HTTP-fejléc. A támogatott nyelvek régiónként countr /; minden ország/régió, a piacot táblázatban azok jellegűek.

Másik lehetőségként megadhatja azt a piaci használatával a `mkt` lekérdezési paraméter és a egy kódot a a **piacok** tábla. Egy adott ország/régió és a egy előnyben részesített nyelvi egyidejűleg adja meg a piacon adja meg. A `setLang` lekérdezési paraméter ebben az esetben előfordulhat, hogy állítható be olyan nyelvi kód; általában ez a megadott nyelvre `mkt` , kivéve, ha a felhasználó, tekintse meg a Bing más nyelvet részesíti előnyben.

## <a name="supported-markets-for-news-search-endpoint"></a>Támogatott piacok news search-végpont

Az a `/news/search` végpont, a következő táblázat felsorolja a piaci kód használatával adjon meg értékeket a `mkt` lekérdezési paraméter. A Bing csak ezek piacok tartalmát adja vissza. A lista a változhat.  

Az ország/régió listáját, amelyek kódjai, előfordulhat, hogy adja meg a `cc` lekérdezési paraméter, lásd: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|dán|da-DK|
|Ausztria|német|Németország-AT|
|Svájc|német|Németország – CH|
|Németország|német|de-DE|
|Ausztrália|angol|en-Ausztrália|
|Kanada|angol|en-hitelesítésszolgáltató|
|Egyesült Királyság|angol|en-GB|
|Indonézia|angol|en-azonosító|
|Írország|angol|en-IE|
|India|angol|en-IN|
|Malajzia|angol|en-MY|
|Új-Zéland|angol|en-NZ|
|Fülöp-szigeteki Köztársaság|angol|en-PH|
|Szingapúr|angol|en-SG|
|Egyesült Államok|angol|en-US|
|angol|Általános|en-WW|
|angol|Általános|en-XA|
|Dél-Afrika|angol|en-ZA|
|Argentína|spanyol|es-AR|
|Chile|spanyol|es-CL|
|Spanyolország|spanyol|es-ES|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US|
|spanyol|Általános|es-XL|
|Finnország|finn|fi-FI|  
|Franciaország|francia|FR-lehet|
|Kanada|francia|FR-hitelesítésszolgáltató|
|Belgium|holland|nl-BE|
|Svájc|francia|FR-CH|
|Franciaország|francia|FR-FR|  
|Olaszország|olasz|it-IT|
|Hongkong (KKT)|Kínai (hagyományos)|zh-HK|  
|Tajvan|Kínai (hagyományos)|zh-TW|
|Japán|japán|ja-JP|  
|Korea|koreai|ko-KR|  
|Hollandia|holland|NL-NL|  
|Kínai Népköztársaság|kínai|zh-CN|  
|Brazília|portugál|pt-BR|
|Oroszország|orosz|ru-RU|  
|Svédország|svéd|SV-SE|  
|Törökország|török|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>Hírek végpont támogatott piac
Az a `/news` végpont, a következő táblázat felsorolja a piaci kód használatával adjon meg értékeket a `mkt` lekérdezési paraméter. A Bing csak ezek piacok tartalmát adja vissza. A lista a változhat.  

Az ország/régió listáját, amelyek kódjai, előfordulhat, hogy adja meg a `cc` lekérdezési paraméter, lásd: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|dán|da-DK|
|Németország|német|de-DE|
|Ausztrália|angol|en-Ausztrália|
|Egyesült Királyság|angol|en-GB|
|Egyesült Államok|angol|en-US|
|angol|Általános|en-WW|
|Chile|spanyol|es-CL|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US|
|Finnország|finn|fi-FI|  
|Kanada|francia|FR-hitelesítésszolgáltató|
|Franciaország|francia|FR-FR|  
|Olaszország|olasz|it-IT|
|Brazília|portugál|pt-BR|
|Kínai Népköztársaság|kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacon népszerű hírek-végpont
Az a `/news/trendingtopics` végpont, a következő táblázat felsorolja a piaci kód használatával adjon meg értékeket a `mkt` lekérdezési paraméter. A Bing csak ezek piacok tartalmát adja vissza. A lista a változhat.  

Az ország/régió listáját, amelyek kódjai, előfordulhat, hogy adja meg a `cc` lekérdezési paraméter, lásd: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Németország|német|de-DE|
|Ausztrália|angol|en-Ausztrália|
|Egyesült Királyság|angol|en-GB|
|Egyesült Államok|angol|en-US|
|Kanada|angol|en-hitelesítésszolgáltató|
|India|angol|en-IN|
|Franciaország|francia|FR-FR|
|Kanada|francia|FR-hitelesítésszolgáltató|
|Brazília|portugál|pt-BR|
|Kínai Népköztársaság|kínai|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Országkódok  

Az alábbiakban a ország/régió kódokat, amelyeket lehet, hogy a `cc` lekérdezési paraméter. A lista a változhat.  

|Ország/régió|Országkód|  
|---------------------|------------------|  
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
|Indonézia|azonosító|  
|Olaszország|IT|  
|Japán|JP|  
|Korea|KR|  
|Malajzia|MY|  
|Mexikó|MX|  
|Hollandia|NL|  
|Új-Zéland|NZ|  
|Norvégia|NEM|  
|Kínai Népköztársaság|CN|  
|Lengyelország|PL|  
|Portugália|PT|  
|Fülöp-szigeteki Köztársaság|PH|  
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

## <a name="next-steps"></a>További lépések
A Bing News Search végpontok kapcsolatos további információkért lásd: [News Search API 7-es referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

---
title: Nyelvi támogatás – Bing News Search API
titleSuffix: Azure Cognitive Services
description: Természetes nyelvek, ország és a Bing News Search API által támogatott régiók listáját.
services: cognitive-services
author: MikeDodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: v-gedod
ms.openlocfilehash: a8f7ccdb199f934011b6d64d813eb36cc9dff952
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48804548"
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
|Ausztrália|Angol|en-Ausztrália|
|Kanada|Angol|en-hitelesítésszolgáltató|
|Egyesült Királyság|Angol|en-GB|
|Indonézia|Angol|en-azonosító|
|Írország|Angol|en-IE|
|India|Angol|en-IN|
|Malajzia|Angol|a saját en|
|Új-Zéland|Angol|en-NZ|
|Fülöp-szigeteki Köztársaság|Angol|en-PH|
|Szingapúr|Angol|en-SG|
|Egyesült Államok|Angol|hu-HU|
|Angol|Általános|en-WW|
|Angol|Általános|en-XA|
|Dél-Afrika|Angol|en-ZA|
|Argentína|spanyol|es-AR|
|Chile|spanyol|es-CL|
|Spanyolország|spanyol|es-ES|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-USA|
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
|Ausztrália|Angol|en-Ausztrália|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Angol|Általános|en-WW|
|Chile|spanyol|es-CL|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-USA|
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
|Ausztrália|Angol|en-Ausztrália|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Kanada|Angol|en-hitelesítésszolgáltató|
|India|Angol|en-IN|
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
|Hongkong (KKT)|HK|  
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
|Kínai Népköztársaság|CN|  
|Lengyelország|PL|  
|Portugália|CSENDES-ÓCEÁNI IDŐ|  
|Fülöp-szigeteki Köztársaság|PH|  
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

## <a name="next-steps"></a>További lépések
A Bing News Search végpontok kapcsolatos további információkért lásd: [News Search API 7-es referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).

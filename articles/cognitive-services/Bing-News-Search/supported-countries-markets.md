---
title: Támogatott országokból és nyelvek Bing hírek keresési API-hoz az Azure-on |} Microsoft Docs
description: Megtudhatja, mely országokból és a nyelv által támogatott, a lemezkép Bing keresési API.
services: cognitive-services
author: MikeDodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 12/20/2017
ms.author: v-gedod
ms.openlocfilehash: 80326d66e509b64efd5d386fe793bc9942b29ae3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347218"
---
# <a name="bing-news-search-countries-and-languages"></a>Bing hírek keresési országokból és nyelvek

A Bing hírek Search API számos országok, számos egynél több nyelv támogatja. Egy ország megadó lekérdezés szolgál elsősorban az adott ország érdeklődések alapján keresési eredmények szűkítéséhez. Emellett az eredmények Bing mutató hivatkozásokat is tartalmaznak, és ezeket a hivatkozásokat is localize a Bing felhasználói élményt a megadott ország vagy nyelv szerint.

Megadhat egy ország használatával a `cc` lekérdezési paraméter. Ha megad egy ország, is meg kell egy vagy több nyelvkódjait használatával a `Accept-Language` HTTP-fejléc. A támogatott nyelvek változhat ország; a piacon tábla minden egyes ország, számukra.

Azt is megteheti, előfordulhat, hogy megadja a piaci használatával a `mkt` lekérdezési paraméter és a kódot a **piacok** tábla. A piacon egyidejűleg megadásával határozza meg, egy ország és a kívánt nyelvet. A `setLang` lekérdezésparaméter ebben az esetben előfordulhat, hogy állítható be egy nyelvi kódot; általában ez a megadott azonos nyelv `mkt` kivéve, ha a felhasználó inkább tekintse meg a Bing más nyelven.

## <a name="supported-markets-for-news-search-endpoint"></a>Támogatott piacok hírek keresési végpont

Az a `/news/search` a végponthoz, a következő táblázat felsorolja a piaci kód értékek, amelyek segítségével adja meg a `mkt` lekérdezési paraméter. Bing csak e piacokra tartalmat adja vissza. A lista létrehozási változhat.  
  
Az ország listája kódok megadása, előfordulhat, hogy adja meg a `cc` lekérdezésparaméter című [országhívószámok](#countrycodes).  
  
|Ország/régió|Nyelv|Piaci kódot|  
|---------------------|--------------|-----------------| 
|Dánia|dán|da-DK|
|Ausztria|német|de-AT| 
|Svájc|német|de-CH|
|Németország|német|de-DE|
|Ausztrália|Angol|en-AU|
|Kanada|Angol|en-Kanada|
|Egyesült Királyság|Angol|en-GB|
|Indonézia|Angol|en-azonosító|
|Írország|Angol|en-IE|
|India|Angol|en-IN|
|Malajzia|Angol|a saját en|
|Új-Zéland|Angol|en-NZ|
|Fülöp-szigeteki Köztársaság|Angol|en-PH|
|Szingapúr|Angol|en-SG|
|Egyesült Államok|Angol|hu-HU|
|Angol|Általános|en-hh|
|Angol|Általános|en-XA|
|Dél-Afrika|Angol|en-ZA|
|Argentína|spanyol|es-AR|
|Chile|spanyol|es-CL|
|Spanyolország|spanyol|es-ES|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US| 
|spanyol|Általános|es-XL csomag|
|Finnország|finn|fi-FI|  
|Franciaország|francia|FR-kell|
|Kanada|francia|FR-Kanada| 
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

## <a name="supported-markets-for-news-endpoint"></a>Támogatott piacok hírek végpont
Az a `/news` a végponthoz, a következő táblázat felsorolja a piaci kód értékek, amelyek segítségével adja meg a `mkt` lekérdezési paraméter. Bing csak e piacokra tartalmat adja vissza. A lista létrehozási változhat.  
  
Az ország listája kódok megadása, előfordulhat, hogy adja meg a `cc` lekérdezésparaméter című [országhívószámok](#countrycodes).  
  
|Ország/régió|Nyelv|Piaci kódot|  
|---------------------|--------------|-----------------| 
|Dánia|dán|da-DK|
|Németország|német|de-DE|
|Ausztrália|Angol|en-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Angol|Általános|en-hh|
|Chile|spanyol|es-CL|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US| 
|Finnország|finn|fi-FI|  
|Kanada|francia|FR-Kanada|
|Franciaország|francia|FR-FR|  
|Olaszország|olasz|it-IT| 
|Brazília|portugál|pt-BR|
|Kínai Népköztársaság|kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacok hírek trendekkel végpont
Az a `/news/trendingtopics` a végponthoz, a következő táblázat felsorolja a piaci kód értékek, amelyek segítségével adja meg a `mkt` lekérdezési paraméter. Bing csak e piacokra tartalmat adja vissza. A lista létrehozási változhat.  
  
Az ország listája kódok megadása, előfordulhat, hogy adja meg a `cc` lekérdezésparaméter című [országhívószámok](#countrycodes).  
  
|Ország/régió|Nyelv|Piaci kódot|  
|---------------------|--------------|-----------------| 
|Németország|német|de-DE|
|Ausztrália|Angol|en-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Kanada|Angol|en-Kanada|
|India|Angol|en-IN|
|Franciaország|francia|FR-FR|
|Kanada|francia|FR-Kanada|
|Brazília|portugál|pt-BR|
|Kínai Népköztársaság|kínai|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Országkódok  

Az alábbiakban az országhívószámok lehet adni a `cc` lekérdezési paraméter. A lista létrehozási változhat.  
  
|Ország/régió|Országkód|  
|---------------------|------------------|  
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
|Hongkong (KKT)|HK|  
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
|Kínai Népköztársaság|CN|  
|Lengyelország|PL|  
|Portugália|PT|  
|Fülöp-szigeteki Köztársaság|PH|  
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

## <a name="next-steps"></a>További lépések
A Bing hírek keresési végpontok kapcsolatos további információkért lásd: [hírek keresési API-v7 referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference).
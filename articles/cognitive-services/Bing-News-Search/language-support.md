---
title: Nyelvi támogatás – Bing News Search API
titleSuffix: Azure Cognitive Services
description: A Bing News Search API által támogatott természetes nyelvek, országok és régiók listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: conceptual
ms.date: 1/10/2019
ms.author: aahi
ms.openlocfilehash: d15058126f43fff328acfc563ffd081164a69a90
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78385076"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>A Bing News Search API nyelvi és területi támogatása

A Bing News Search API számos országot/régiót támogat, több mint egy nyelvet. A lekérdezéssel rendelkező ország/régió megadásával elsődlegesen az adott országban vagy régióban lévő érdekeltségek alapján pontosíthatja a keresési eredményeket. Emellett az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján lokalizálják a Bing felhasználói élményt.

Megadhat egy országot/régiót a `cc` lekérdezési paraméterrel. Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` HTTP-fejléc használatával. A támogatott nyelvek a Counter/régió szerint változnak; a piacok táblában minden országhoz/régióhoz meg vannak adva.

Azt is megteheti, hogy a piacot a `mkt` lekérdezési paraméterrel és a **piacok** táblából származó kóddal határozza meg. A piac egyidejű megadása megadja az országot/régiót és az előnyben részesített nyelvet. Ebben az esetben a `setLang` lekérdezési paramétert nyelvi kódra lehet beállítani. Ez általában a `mkt` által megadott nyelv, kivéve, ha a felhasználó egy másik nyelven szeretné megtekinteni a Binget.

## <a name="supported-markets-for-news-search-endpoint"></a>A Hírek keresési végpontjának támogatott piacai

Az `/news/search` végpont esetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

Az `cc` lekérdezési paraméterben megadható ország-/régiókódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|dán|da-DK|
|Ausztria|német|Németország-AT|
|Svájc|német|Németország – CH|
|Németország|német|de-DE|
|Ausztrália|Angol|en-Ausztrália|
|Kanada|Angol|en-hitelesítésszolgáltató|
|Egyesült Királyság|Angol|en-GB|
|Indonézia|Angol|EN-ID|
|Írország|Angol|en-IE|
|India|Angol|en-IN|
|Malajzia|Angol|en-MY|
|Új-Zéland|Angol|en-NZ|
|Fülöp-szigeteki Köztársaság|Angol|EN-PH|
|Szingapúr|Angol|EN-SG|
|Egyesült Államok|Angol|hu-HU|
|Angol|általános|EN-WW|
|Angol|általános|en-XA|
|Dél-afrikai Köztársaság|Angol|en-ZA|
|Argentína|spanyol|es-AR|
|Chile|spanyol|es-CL|
|Spanyolország|spanyol|es-ES|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US|
|spanyol|általános|es-XL|
|Finnország|finn|fi-FI|  
|Franciaország|francia|fr-BE|
|Kanada|francia|FR-hitelesítésszolgáltató|
|Belgium|holland|nl-BE|
|Svájc|francia|FR-CH|
|Franciaország|francia|FR-FR|  
|Olaszország|olasz|it-IT|
|Hongkong (KKT)|Hagyományos kínai|zh-HK|  
|Tajvan|Hagyományos kínai|zh-TW|
|Japán|japán|ja-JP|  
|Dél-Korea|koreai|ko-KR|  
|Hollandia|holland|NL-NL|  
|Kínai Népköztársaság|kínai|zh-CN|  
|Brazília|portugál|pt-BR|
|Oroszország|Orosz|ru-RU|  
|Svédország|svéd|SV-SE|  
|Törökország|török|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>A hírek végpontjának támogatott piacai
Az `/news` végpont esetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

Az `cc` lekérdezési paraméterben megadható ország-/régiókódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|dán|da-DK|
|Németország|német|de-DE|
|Ausztrália|Angol|en-Ausztrália|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|hu-HU|
|Angol|általános|EN-WW|
|Chile|spanyol|es-CL|
|Mexikó|spanyol|es-MX|
|Egyesült Államok|spanyol|es-US|
|Finnország|finn|fi-FI|  
|Kanada|francia|FR-hitelesítésszolgáltató|
|Franciaország|francia|FR-FR|  
|Olaszország|olasz|it-IT|
|Brazília|portugál|pt-BR|
|Kínai Népköztársaság|kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacok a hírek trendi végpontja számára
Az `/news/trendingtopics` végpont esetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

Az `cc` lekérdezési paraméterben megadható ország-/régiókódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

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

A `cc` lekérdezési paraméterben megadható ország-és régiókódok az alábbiak. A lista módosítása változhat.  

|Ország/régió|Országhívó szám|  
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
|Indonézia|ID (Azonosító)|  
|Olaszország|IT|  
|Japán|JP|  
|Dél-Korea|KR|  
|Malajzia|MY|  
|Mexikó|MX|  
|Hollandia|NL|  
|Új-Zéland|NZ|  
|Norvégia|NO|  
|Kínai Népköztársaság|CN|  
|Lengyelország|PL|  
|Portugália|PT|  
|Fülöp-szigeteki Köztársaság|PH|  
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

## <a name="next-steps"></a>Következő lépések
További információ az Bing News Search-végpontokról: [News Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

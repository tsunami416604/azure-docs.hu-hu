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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "79220192"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>A Bing News Search API nyelvi és területi támogatása

A Bing News Search API számos országot/régiót támogat, több mint egy nyelvet. A lekérdezéssel rendelkező ország/régió megadásával elsődlegesen az adott országban vagy régióban lévő érdekeltségek alapján pontosíthatja a keresési eredményeket. Emellett az eredmények a Bingre mutató hivatkozásokat is tartalmazhatnak, és ezek a hivatkozások a megadott ország/régió vagy nyelv alapján lokalizálják a Bing felhasználói élményt.

A `cc` lekérdezési paraméter segítségével megadhat egy országot/régiót. Ha országot vagy régiót ad meg, meg kell adnia egy vagy több nyelvi kódot is a `Accept-Language` HTTP-fejléc használatával. A támogatott nyelvek a Counter/régió szerint változnak; a piacok táblában minden országhoz/régióhoz meg vannak adva.

Azt is megteheti, hogy megadta `mkt` a piacot a lekérdezési paraméter és a **piacok** tábla kódjának használatával. A piac egyidejű megadása megadja az országot/régiót és az előnyben részesített nyelvet. Ebben `setLang` az esetben a lekérdezési paramétert nyelvi kódra lehet beállítani. Ez általában ugyanaz a nyelv, `mkt` ha a felhasználó egy másik nyelven szeretné megtekinteni a Bing-t.

## <a name="supported-markets-for-news-search-endpoint"></a>A Hírek keresési végpontjának támogatott piacai

A `/news/search` végpont esetében a következő táblázat a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A `cc` lekérdezési paraméterben megadható ország/régió kódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Ausztria|Német|de-AT|
|Svájc|Német|de-CH|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Kanada|Angol|en-CA|
|Egyesült Királyság|Angol|en-GB|
|Indonézia|Angol|EN-ID|
|Írország|Angol|EN-IE|
|India|Angol|EN-IN|
|Malajzia|Angol|EN-MY|
|Új-Zéland|Angol|EN-NZ|
|Fülöp-szigeteki Köztársaság|Angol|EN-PH|
|Szingapúr|Angol|EN-SG|
|Egyesült Államok|Angol|en-US|
|Angol|általános|EN-WW|
|Angol|általános|EN-XA|
|Dél-afrikai Köztársaság|Angol|EN-ZA|
|Argentína|Spanyol|es-AR|
|Chile|Spanyol|es – CL|
|Spanyolország|Spanyol|es-ES|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es – USA|
|Spanyol|általános|es-XL|
|Finnország|Finn|fi-FI|  
|Franciaország|Francia|fr-BE|
|Kanada|Francia|fr – CA|
|Belgium|Holland|nl-BE|
|Svájc|Francia|fr – CH|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Hongkong (KKT)|Kínai (hagyományos)|ZH-HK|  
|Tajvan|Kínai (hagyományos)|zh-TW|
|Japán|Japán|ja-JP|  
|Dél-Korea|Koreai|ko-KR|  
|Hollandia|Holland|nl-NL|  
|Kínai Népköztársaság|Kínai|zh-CN|  
|Brazília|Portugál|pt-BR|
|Oroszország|Orosz|ru-RU|  
|Svédország|Svéd|sv-SE|  
|Törökország|Török|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>A hírek végpontjának támogatott piacai
A `/news` végpont esetében a következő táblázat a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A `cc` lekérdezési paraméterben megadható ország/régió kódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Angol|általános|EN-WW|
|Chile|Spanyol|es – CL|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es – USA|
|Finnország|Finn|fi-FI|  
|Kanada|Francia|fr – CA|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacok a hírek trendi végpontja számára
A `/news/trendingtopics` végpont esetében a következő táblázat a `mkt` lekérdezési paraméter megadásához használható piaci kódok értékeit sorolja fel. A Bing csak ezekre a piacokra vonatkozó tartalmat adja vissza. A lista módosítása változhat.  

A `cc` lekérdezési paraméterben megadható ország/régió kódok listáját itt tekintheti meg: [országkódok](#countrycodes).  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Németország|Német|de-DE|
|Ausztrália|Angol|EN-AU|
|Egyesült Királyság|Angol|en-GB|
|Egyesült Államok|Angol|en-US|
|Kanada|Angol|en-CA|
|India|Angol|EN-IN|
|Franciaország|Francia|fr-FR|
|Kanada|Francia|fr – CA|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Országkódok  

A `cc` lekérdezési paraméterben megadható ország/régió kódok a következők: A lista módosítása változhat.  

|Ország/régió|Országhívószám|  
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

## <a name="next-steps"></a>További lépések
További információ az Bing News Search-végpontokról: [News Search API v7-dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference).

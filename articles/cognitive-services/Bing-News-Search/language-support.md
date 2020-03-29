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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220192"
---
# <a name="language-and-region-support-for-the-bing-news-search-api"></a>A Bing News Search API nyelvi és régiótámogatása

A Bing News Search API számos országot/régiót támogat, amelyek közül sok több nyelvet is támogat. Ha egy országot/régiót egy lekérdezéssel ad meg, az elsősorban az adott országban/régióban lévő érdeklődési körök alapján finomítja a keresési eredményeket. Ezenkívül az eredmények tartalmazhatnak bingre mutató hivatkozásokat, és ezek a hivatkozások a bingfelhasználói élményt a megadott országnak/régiónak vagy nyelvnek megfelelően honosíthatják.

A `cc` lekérdezési paraméter rel megadhat egy országot/régiót. Ha ország/régió megadását adja meg, akkor egy vagy `Accept-Language` több nyelvkódot is meg kell adnia a HTTP fejléc használatával. A támogatott nyelvek száma/régiónként változik; a Piacok táblázatban minden egyes országra/régióra vonatkozóan meg vannak adva.

Másik lehetőségként megadhatja a `mkt` piacot a lekérdezési paraméter és a **Piacok** tábla kódjával. A piac egyidejű megadása egy országot/régiót és egy előnyben részesített nyelvet határoz meg. Ebben `setLang` az esetben a lekérdezési paraméter beállítható egy nyelvkódra; általában ez ugyanaz a nyelv, amelyet `mkt` a felhasználó nem szeretne más nyelven látni.

## <a name="supported-markets-for-news-search-endpoint"></a>A hírkeresési végpontok támogatott piacai

A `/news/search` végpontesetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódértékeket. A Bing csak ezekre a piacokra visszaküld idét. A lista változhat.  

A lekérdezési paraméterben megadható ország-/régiókódok listáját az `cc` Országkódok című [témakörben találja.](#countrycodes)  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Ausztria|Német|de-AT|
|Svájc|Német|de-CH|
|Németország|Német|de-DE|
|Ausztrália|Angol|en-AU|
|Kanada|Angol|en-CA|
|Egyesült Királyság|Angol|hu-GB|
|Indonézia|Angol|en-ID|
|Írország|Angol|en-IE|
|India|Angol|en-IN|
|Malajzia|Angol|hu-ÉN|
|Új-Zéland|Angol|en-NZ|
|Fülöp-szigeteki Köztársaság|Angol|en-PH|
|Szingapúr|Angol|en-SG|
|Egyesült Államok|Angol|en-US|
|Angol|általános|en-WW|
|Angol|általános|en-XA|
|Dél-afrikai Köztársaság|Angol|en-ZA|
|Argentína|Spanyol|es-AR|
|Chile|Spanyol|es-CL|
|Spanyolország|Spanyol|es-ES|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es-US|
|Spanyol|általános|es-XL|
|Finnország|Finn|fi-FI|  
|Franciaország|Francia|fr-BE|
|Kanada|Francia|fr-CA|
|Belgium|Holland|nl-BE|
|Svájc|Francia|fr-CH|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Hongkong (KKT)|Kínai (hagyományos)|zh-HK|  
|Tajvan|Kínai (hagyományos)|zh-TW|
|Japán|Japán|ja-JP|  
|Dél-Korea|Koreai|ko-KR|  
|Hollandia|Holland|nl-NL|  
|Kínai Népköztársaság|Kínai|zh-CN|  
|Brazília|Portugál|pt-BR|
|Oroszország|Orosz|ru-RU|  
|Svédország|Svéd|sv-SE|  
|Törökország|Török|tr-TR|  

## <a name="supported-markets-for-news-endpoint"></a>A hírvégpontok támogatott piacai
A `/news` végpontesetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódértékeket. A Bing csak ezekre a piacokra visszaküld idét. A lista változhat.  

A lekérdezési paraméterben megadható ország-/régiókódok listáját az `cc` Országkódok című [témakörben találja.](#countrycodes)  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Dánia|Dán|da-DK|
|Németország|Német|de-DE|
|Ausztrália|Angol|en-AU|
|Egyesült Királyság|Angol|hu-GB|
|Egyesült Államok|Angol|en-US|
|Angol|általános|en-WW|
|Chile|Spanyol|es-CL|
|Mexikó|Spanyol|es-MX|
|Egyesült Államok|Spanyol|es-US|
|Finnország|Finn|fi-FI|  
|Kanada|Francia|fr-CA|
|Franciaország|Francia|fr-FR|  
|Olaszország|Olasz|it-IT|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|

## <a name="supported-markets-for-news-trending-endpoint"></a>Támogatott piacok a hírek felkapott végpontja számára
A `/news/trendingtopics` végpontesetében az alábbi táblázat felsorolja a `mkt` lekérdezési paraméter megadásához használható piaci kódértékeket. A Bing csak ezekre a piacokra visszaküld idét. A lista változhat.  

A lekérdezési paraméterben megadható ország-/régiókódok listáját az `cc` Országkódok című [témakörben találja.](#countrycodes)  

|Ország/régió|Nyelv|Piaci kód|  
|---------------------|--------------|-----------------|
|Németország|Német|de-DE|
|Ausztrália|Angol|en-AU|
|Egyesült Királyság|Angol|hu-GB|
|Egyesült Államok|Angol|en-US|
|Kanada|Angol|en-CA|
|India|Angol|en-IN|
|Franciaország|Francia|fr-FR|
|Kanada|Francia|fr-CA|
|Brazília|Portugál|pt-BR|
|Kínai Népköztársaság|Kínai|zh-CN|


<a name="countrycodes"></a>   
### <a name="country-codes"></a>Országkódok  

A `cc` lekérdezési paraméterben megadott ország-/régiókódok a következők. A lista változhat.  

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
A Bing News Search végpontjairól a [News Search API v7 referencia című témakörben talál](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)további információt.

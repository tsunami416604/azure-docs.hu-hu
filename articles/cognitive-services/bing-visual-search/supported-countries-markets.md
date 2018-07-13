---
title: Támogatott nyelvek és országok/régiók a Bing Visual Search API-hoz |} A Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ismerje meg, hogy mely országok/régiók és nyelvek a Bing Visual Search API által támogatott.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 068459f4959bf3f8daea8fe949ec324879ee260c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39001228"
---
# <a name="bing-visual-search-countriesregions-and-languages"></a>A Bing Visual Search országok/régiók és nyelvek

A Bing Visual Search API több mint három tucat országok/régiók, számos, az egynél több nyelvet támogat. Minden kérelmet tartalmaznia kell a felhasználó országot/régiót és tetszőleges nyelven. A Bing megfelelő eredményeket adja vissza, hogy a felhasználó piaci segítségével. Ha nem ad meg egy ország/régió és nyelv, a Bing teszi egy ajánlott annak érdekében, hogy a felhasználó ország/régió és nyelv meghatározása. Az eredményeket a Bing hivatkozásokat tartalmazhatnak, mert az ország/régió és nyelv ismerete előfordulhat, hogy egy előnyben részesített honosított Bing felhasználói felületet tudjon biztosítani, ha a felhasználó rákattint a Bing hivatkozásokat.

Adja meg az országot/régiót és a nyelvet, állítsa be a `mkt` (piaci) lekérdezési paraméter a kódot a **piacok** az alábbi táblázat. A piacon ország/régió és nyelv is megadja. Ha a felhasználó részesíti előnyben, hogy különböző nyelveken jelenítse meg, és állítsa `setLang` lekérdezési paraméter, a megfelelő nyelvi kódot.

Másik lehetőségként megadhatja az országot/régiót használ a `cc` lekérdezési paraméter. Ha megad egy adott ország/régió, meg kell adnia egy vagy több nyelvi kódot használ a `Accept-Language` HTTP-fejléc. Ország/régió; szerint változik a támogatott nyelvek a piacon tábla országokra generálnak számukra.



> [!NOTE]
> A következő piaci korlátozások vonatkoznak:
> 
> - Kép felismerés jegyzetek csak angol nyelven érhető el. 
> - Recept, a vásárlás és a lapok – beleértve a insights csak az en-US piacon érhetők el. 


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

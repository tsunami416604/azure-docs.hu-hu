---
title: Nyelvi támogatás – a Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: Természetes nyelvek, ország és a Bing Visual Search API által támogatott régiók listáját. A Bing Visual Search API több mint három tucat országok/régiók, számos, az egynél több nyelvet támogat.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: 0f7d3f8a7a9c7bdd67dc80a98744e1cd6a2433a1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55180823"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>A Bing Visual Search API nyelvéhez és régiójához támogatása

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

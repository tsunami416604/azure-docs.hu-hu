---
title: Támogatott országokból és nyelvek Bing kép keresési API az Azure-on |} Microsoft Docs
description: Megtudhatja, mely országokból és a nyelv által támogatott, a lemezkép Bing keresési API.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: d0d33ee714ba5cd1ce4e846b96c04f755933bee1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348951"
---
# <a name="bing-image-search-countries-and-languages"></a>Bing kép keresési országokból és nyelvek

A kép Bing keresési API támogatja a több mint három tucat országok, számos egynél több nyelv. Egy ország megadó lekérdezés szolgál elsősorban az adott ország érdeklődések alapján keresési eredmények szűkítéséhez. Emellett az eredmények Bing mutató hivatkozásokat is tartalmaznak, és ezeket a hivatkozásokat is localize a Bing felhasználói élményt a megadott ország vagy nyelv szerint.

Adja meg az ország és a nyelv, állítsa be a `mkt` (piaci) lekérdezésparamétert egy kódot a **piacok** az alábbi táblázatban. A piacon egy ország és a nyelv megadása Ha inkább tekintse meg a felhasználó egy másik nyelven jelenítse meg, állítsa `setLang` lekérdezésparaméter a megfelelő nyelvi kód.

Másik megoldásként megadhatja, az ország használatával a `cc` lekérdezési paraméter. Ha megad egy ország, is meg kell egy vagy több nyelvkódjait használatával a `Accept-Language` HTTP-fejléc. A támogatott nyelvek változhat ország; a piacon tábla minden egyes ország, számukra.

> [!NOTE]
> A trendek képek API jelenleg csak a következő piacon támogatja:
> - en-US (angol nyelvű, az Amerikai Egyesült Államok) 
> - en-hitelesítésszolgáltató (angol nyelvű, Kanada) 
> - en-AU (angol nyelvű, Ausztrália) 
> - zh-CN (kínai, Kína)

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

## <a name="next-steps"></a>További lépések
A Bing hírek keresési végpontok kapcsolatos további információkért lásd: [hírek kép keresési API-v7 referencia](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).

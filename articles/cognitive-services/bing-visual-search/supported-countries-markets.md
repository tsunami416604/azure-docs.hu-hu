---
title: Támogatott országokból és nyelvek Bing Visual keresési API-hoz |} Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ismerje meg, mely országokból és nyelvek Bing Visual Search API által támogatott.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 4723d028cc22caf8be3eb294b52506ec112cbab5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348711"
---
# <a name="bing-visual-search-countries-and-languages"></a>Bing Visual keresési országokból és nyelvek

Bing Visual Search API támogatja a több mint három tucat országok, számos egynél több nyelv. Minden egyes kérelem a felhasználó ország és a választott nyelven kell tartalmaznia. A felhasználó piaci ismerete segít a Bing megfelelő eredményeket. Ha nem ad meg egy ország és a nyelv, Bing teszi a lehető legkedvezőbb módon a felhasználó ország és nyelvi meghatározásához. Az eredmények Bing mutató hivatkozásokat is tartalmazhat, mert az ország és a nyelv ismerete előfordulhat, hogy egy előnyben részesített honosított Bing felhasználói felületet tudjon biztosítani, ha a felhasználó kattint a Bing hivatkozásokat.

Adja meg az ország és a nyelv, állítsa be a `mkt` (piaci) lekérdezésparamétert egy kódot a **piacok** az alábbi táblázatban. A piacon egy ország és a nyelv megadása Ha inkább tekintse meg a felhasználó egy másik nyelven jelenítse meg, állítsa `setLang` lekérdezésparaméter a megfelelő nyelvi kód.

Másik megoldásként megadhatja, az ország használatával a `cc` lekérdezési paraméter. Ha megad egy ország, is meg kell egy vagy több nyelvkódjait használatával a `Accept-Language` HTTP-fejléc. A támogatott nyelvek változhat ország; a piacon tábla minden egyes ország, számukra.



> [!NOTE]
> A következő piaci korlátozások vonatkoznak:
> 
> - Kép felismerés jegyzetek csak angol nyelven érhető el. 
> - Módszereivel, a vásárlás és a lapok beleértve insights csak a en-US piacon érhetők el. 


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

---
title: Bing webes keresés API Azure országokból és nyelvek támogatott |} Microsoft Docs
description: Megtudhatja, mely országokból és a nyelv által támogatott, a Bing webes keresés API.
services: cognitive-services
author: v-jerkin
manager: jhubbard
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/06/2017
ms.author: v-jerkin
ms.openlocfilehash: 7b62c4a4feb7144662a8fe4d692f11f1efe5db1b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347155"
---
# <a name="bing-web-search-countries-and-languages"></a>Bing webes keresés országokból és nyelvek

A webes Bing keresési API támogatja a több mint három tucat országok, számos egynél több nyelv. Egy ország megadó lekérdezés szolgál elsősorban az adott ország érdeklődések alapján keresési eredmények szűkítéséhez. Emellett az eredmények Bing mutató hivatkozásokat is tartalmaznak, és ezeket a hivatkozásokat is localize a Bing felhasználói élményt a megadott ország vagy nyelv szerint.

Megadhat egy ország használatával a `cc` lekérdezési paraméter. Ha megad egy ország, is meg kell egy vagy több nyelvkódjait használatával a `Accept-Language` HTTP-fejléc. A támogatott nyelvek változhat ország; a piacon tábla minden egyes ország, számukra.

Azt is megteheti, előfordulhat, hogy megadja a piaci használatával a `mkt` lekérdezési paraméter és a kódot a **piacok** tábla. A piacon egyidejűleg megadásával határozza meg, egy ország és a kívánt nyelvet. A `setLang` lekérdezésparaméter ebben az esetben előfordulhat, hogy állítható be egy nyelvi kódot; általában ez a megadott azonos nyelv `mkt` kivéve, ha a felhasználó inkább tekintse meg a Bing más nyelven.

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
|Norvégia|norvég|no-NO|
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

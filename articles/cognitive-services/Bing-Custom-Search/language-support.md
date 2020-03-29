---
title: Nyelvi támogatás – Bing egyéni keresési API
titleSuffix: Azure Cognitive Services
description: A Bing egyéni keresési API támogatott nyelveinek és régióinak listája.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: aahi
ms.openlocfilehash: 85326ae9166f7ea15ec2f45c01755b8f9ef03aff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66388563"
---
# <a name="language-and-region-support-for-the-bing-custom-search-api"></a>A Bing egyéni keresési API nyelvi és területi támogatása

A Bing egyéni keresési API több mint három tucat országot/régiót támogat, amelyek közül sok több nyelvet is támogat.

Bár ez nem kötelező, a kérelemnek meg kell adnia az [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#mkt) lekérdezési paramétert, amely azonosítja azt a piacot, ahonnan az eredményeket létre szeretné venni. A választható lekérdezési paraméterek listáját a [Lekérdezési paraméterek](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)

A `cc` lekérdezési paraméter rel megadhat egy országot/régiót. Ha ország/régió megadására van szükség, a `Accept-Language` fejléc használatával egy vagy több nyelvkódot is meg kell adnia. A támogatott nyelvek országonként/régiónként eltérőek; a **Piacok** táblázatban minden egyes országra/régióra vonatkozóan meg vannak adva.

A `Accept-Language` fejléc `setLang` és a lekérdezési paraméter kölcsönösen kizárják egymást – ne adja meg mindkettőt. További információt az [Elfogadás nyelve (Accept-Language) (Nyelv elfogadása) témakörben talál.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#acceptlanguage)

## <a name="countriesregions"></a>Országok/régiók

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
|Kína|CN|
|Lengyelország|PL|
|Portugália|PT|
|Fülöp-szigetek|PH|
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


## <a name="markets"></a>Piacok

|Ország/régió|Nyelv|Piaci kód|
|-------|--------|-----------|
|Argentína|Spanyol|es-AR|
|Ausztrália|Angol|en-AU|
|Ausztria|Német|de-AT|
|Belgium|Holland|nl-BE|
|Belgium|Francia|fr-BE|
|Brazília|Portugál|pt-BR|
|Kanada|Angol|en-CA|
|Kanada|Francia|fr-CA|
|Chile|Spanyol|es-CL|
|Dánia|Dán|da-DK|
|Finnország|Finn|fi-FI|
|Franciaország|Francia|fr-FR|
|Németország|Német|de-DE|
|Hongkong, KKT|Kínai (hagyományos)|zh-HK|
|India|Angol|en-IN|
|Indonézia|Angol|en-ID|
|Olaszország|Olasz|it-IT|
|Japán|Japán|ja-JP|
|Dél-Korea|Koreai|ko-KR|
|Malajzia|Angol|hu-ÉN|
|Mexikó|Spanyol|es-MX|
|Hollandia|Holland|nl-NL|
|Új-Zéland|Angol|en-NZ|
|Norvégia|Norvég|no-NO|
|Kína|Kínai|zh-CN|
|Lengyelország|Lengyel|pl-PL|
|Portugália|Portugál|pt-PT|
|Fülöp-szigetek|Angol|en-PH|
|Oroszország|Orosz|ru-RU|
|Szaúd-Arábia|Arab|ar-SA|
|Dél-afrikai Köztársaság|Angol|en-ZA|
|Spanyolország|Spanyol|es-ES|
|Svédország|Svéd|sv-SE|
|Svájc|Francia|fr-CH|
|Svájc|Német|de-CH|
|Tajvan|Kínai (hagyományos)|zh-TW|
|Törökország|Török|tr-TR|
|Egyesült Királyság|Angol|hu-GB|
|Egyesült Államok|Angol|en-US|
|Egyesült Államok|Spanyol|es-US|

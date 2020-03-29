---
title: Nyelvi támogatás – Bing Visual Search API
titleSuffix: Azure Cognitive Services
description: A Bing Visual Search API által támogatott természetes nyelvek, országok és régiók listája. A Bing Visual Search API több mint három tucat országot/régiót támogat, amelyek közül sok több nyelvet is támogat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: scottwhi
ms.openlocfilehash: b17341bc234ff3dfecc2c6dcd84ef77116a95d61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68883547"
---
# <a name="language-and-region-support-for-the-bing-visual-search-api"></a>A Bing Visual Search API nyelvi és területi támogatása

A Bing Visual Search API több mint három tucat országot/régiót támogat, amelyek közül sok több nyelvet is támogat. Minden kérelemnek tartalmaznia kell a felhasználó országát/régióját és a választott nyelvet. A felhasználó piacának ismerete segít a Bingnek a megfelelő eredmények visszaadása. Ha nem ad meg egy országot/régiót és nyelvet, a Bing mindent megtesz a felhasználó országának/régiójának és nyelvének meghatározásához. Mivel az eredmények tartalmazhatnak bingre mutató hivatkozásokat, az ország/régió és a nyelv ismerete előnyös honos Bing felhasználói élményt nyújthat, ha a felhasználó a Bing-hivatkozásokra kattint.

Az ország/régió és nyelv megadásához állítsa be a `mkt` (piaci) lekérdezési paramétert egy kódra az alábbi **Piacok** táblázatból. A piac egy országot/régiót és nyelvet is meghatároz. Ha a felhasználó a megjelenített szöveget más `setLang` nyelven szeretné látni, állítsa a lekérdezési paramétert a megfelelő nyelvi kódra.

Azt is megteheti, hogy megadja `cc` az országot/régiót a lekérdezési paraméter használatával. Ha ország/régió megadását adja meg, akkor egy vagy `Accept-Language` több nyelvkódot is meg kell adnia a HTTP fejléc használatával. A támogatott nyelvek országonként/régiónként eltérőek; a Piacok táblázatban minden egyes országra vonatkozóan meg vannak adva.



> [!NOTE]
> A következő piaci korlátozások vonatkoznak:
>
> - A képfelismerési jegyzetek csak angol nyelven érhetők el.
> - Recept, vásárlás, és az oldalak, beleértve betekintést állnak rendelkezésre az en-US piacon csak.


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
|Hongkong (KKT)|Kínai (hagyományos)|zh-HK|
|India|Angol|en-IN|
|Indonézia|Angol|en-ID|
|Olaszország|Olasz|it-IT|
|Japán|Japán|ja-JP|
|Dél-Korea|Koreai|ko-KR|
|Malajzia|Angol|hu-ÉN|
|Mexikó|Spanyol|es-MX|
|Hollandia|Holland|nl-NL|
|Új-Zéland|Angol|en-NZ|
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

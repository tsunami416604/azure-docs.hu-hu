---
title: Nyelvi támogatás – Text Analytics API
titleSuffix: Azure Cognitive Services
description: A Text Analytics API által támogatott természetes nyelvek listája. Ez a cikk az egyes műveletek által támogatott nyelveket ismerteti.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: c0e71ffcc88a85caf4b76c34940293663c4acf66
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142163"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics API v3 nyelvi támogatás 

> [!IMPORTANT]
> A Text Analytics API 3. x verziója jelenleg nem érhető el a következő régiókban: Közép-India, Fairfax, Egyesült Arab Emírségek, Észak-Dél-Afrika, Észak-Kína 2, Kelet-Kína.


#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójának elindítása: |              Megjegyzések |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Egyszerűsített kínai    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh`szintén elfogadva |
| Kínai – hagyományos   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| dán               |     `da`      |     ✓      |            |                            |                    |
| holland                 |     `nl`      |     ✓      |            |                            |                    |
| angol               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| finn               |     `fi`      |     ✓      |            |                            |                    |
| francia                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| német                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| görög                 |     `el`      |     ✓      |            |                            |                    |
| olasz               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| koreai                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |            |                            |                    |
| lengyel                |     `pl`      |     ✓      |            |                            |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt`szintén elfogadva |
| orosz               |     `ru`      |     ✓      |            |                            |                    |
| spanyol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| svéd               |     `sv`      |     ✓      |            |                            |                    |
| török               |     `tr`      |     ✓      |            |                            |                    |

### <a name="opinion-mining-v31-preview-only"></a>Közvélemény-bányászat (csak előzetes verzió)

| Nyelv              | Nyelvkód | A v3 modell verziójától kezdve: |              Megjegyzések |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| angol               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Elnevezett entitások felismerése](#tab/named-entity-recognition)

> [!NOTE]
> * Az egynyelvű v3 jelenleg csak az angol nyelvet támogatja. Ha más nyelvvel hívja meg a meghívott "v3"-t, az API a v 2.1 eredményeit fogja visszaadni, ha az 2,1-es verzió támogatja a nyelvet.
> * a v 2.1 csak az angol, a kínai – egyszerűsített, francia, német és spanyol nyelven elérhető entitások teljes készletét adja vissza.  A többi támogatott nyelvhez a "person", a "location" és a "Organization" entitást adja vissza a rendszer.

| Nyelv               | Nyelvkód | v 2.1 támogatás | v3 támogatás | A v3 modell verziójától kezdve: |       Megjegyzések        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arab                |     `ar`      |     ✓      |            |                                 |                    |
| cseh                 |     `cs`      |     ✓      |            |                                 |                    |
| Egyszerűsített kínai     |   `zh-hans`   |     ✓      |            |                                 | `zh`szintén elfogadva |
| Kínai – hagyományos   |   `zh-hant`   |     ✓      |            |                                 |                    |
| dán                |     `da`      |     ✓      |            |                                 |                    |
| holland                 |     `nl`      |     ✓      |            |                                 |                    |
| angol                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| finn               |     `fi`      |     ✓      |            |                                 |                    |
| francia                 |     `fr`      |     ✓      |            |                                 |                    |
| német                 |     `de`      |     ✓      |            |                                 |                    |
| héber                |     `he`      |     ✓      |            |                                 |                    |
| magyar             |     `hu`      |     ✓      |            |                                 |                    |
| olasz               |     `it`      |     ✓      |            |                                 |                    |
| Japán              |     `ja`      |     ✓      |            |                                 |                    |
| koreai                |     `ko`      |     ✓      |            |                                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |            |                                 | `nb`szintén elfogadva |
| lengyel                |     `pl`      |     ✓      |            |                                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |            |                                 | `pt`szintén elfogadva |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| orosz              |     `ru`      |     ✓      |            |                                 |                    |
| spanyol               |     `es`      |     ✓      |            |                                 |                    |
| svéd               |     `sv`      |     ✓      |            |                                 |                    |
| török               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Fő kifejezés kibontása](#tab/key-phrase-extraction)

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: |       Megjegyzések        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| holland                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| angol               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| finn               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| francia                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| német                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| olasz               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| koreai                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb`szintén elfogadva |
| lengyel                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt`szintén elfogadva |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| orosz               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| spanyol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| svéd               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitás összekapcsolása](#tab/entity-linking)

| Nyelv | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: | Megjegyzések |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| angol  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| spanyol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes felderíteni.  Nyelvfelismerés a nyelv "szkriptjét" adja vissza. Ha például a "Nekem van egy kutyám" kifejezés, akkor a helyett a értéket fogja visszaadni `en` `en-US` . Az egyetlen különleges eset a kínai, ahol a nyelvi észlelési képesség vissza fog térni, `zh_CHS` vagy `zh_CHT` Ha meg tudja határozni a parancsfájlt a megadott szöveg alapján. Azokban az esetekben, amikor egy kínai dokumentumhoz nem azonosítható egy adott parancsfájl, a rendszer egyszerűen visszaadja `zh` .

Nem tesszük közzé a funkcióhoz tartozó nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és bizonyos regionális/kulturális nyelvet képes felderíteni. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

---

## <a name="see-also"></a>Lásd még

* [Mi a Text Analytics API?](overview.md)   

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
ms.date: 09/12/2020
ms.author: aahi
ms.openlocfilehash: 185a6a26350f7803d109e26d8985510380b8cc16
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2020
ms.locfileid: "90056591"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics API v3 nyelvi támogatás 

> [!IMPORTANT]
> A Text Analytics API 3. x verziója jelenleg nem érhető el a következő régiókban: Közép-India, Észak-Egyesült Arab, Észak-Kína 2, Kelet-Kína.


#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójának elindítása: |              Jegyzetek |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Egyszerűsített kínai    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` szintén elfogadva |
| Kínai – hagyományos   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| Dán               |     `da`      |     ✓      |            |                            |                    |
| Holland                 |     `nl`      |     ✓      |            |                            |                    |
| Angol               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Finn               |     `fi`      |     ✓      |            |                            |                    |
| Francia                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Német                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Görög                 |     `el`      |     ✓      |            |                            |                    |
| Olasz               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreai                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |     ✓       |        2020-07-01         |                    |
| Lengyel                |     `pl`      |     ✓      |            |                            |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` szintén elfogadva |
| Orosz               |     `ru`      |     ✓      |            |                            |                    |
| Spanyol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Svéd               |     `sv`      |     ✓      |            |                            |                    |
| Török               |     `tr`      |     ✓      |     ✓       |         2020-07-01        |                    |

### <a name="opinion-mining-v31-preview-only"></a>Közvélemény-bányászat (csak előzetes verzió)

| Nyelv              | Nyelvkód | A v3 modell verziójától kezdve: |              Jegyzetek |
|:----------------------|:-------------:|:------------------------------------:|-------------------:|
| Angol               |     `en`      |              2020-04-01              |                    |


#### <a name="named-entity-recognition-ner"></a>[Elnevezett entitások felismerése](#tab/named-entity-recognition)

> [!NOTE]
> * Az egynyelvű v3 jelenleg csak az angol és a spanyol nyelvet támogatja. Ha más nyelvvel hívja meg a meghívott "v3"-t, az API a v 2.1 eredményeit fogja visszaadni, ha az 2,1-es verzió támogatja a nyelvet.
> * a v 2.1 csak az angol, a kínai – egyszerűsített, francia, német és spanyol nyelven elérhető entitások teljes készletét adja vissza.  A többi támogatott nyelvhez a "person", a "location" és a "Organization" entitást adja vissza a rendszer.

| Nyelv               | Nyelvkód | v 2.1 támogatás | v3 támogatás | A v3 modell verziójától kezdve: |       Jegyzetek        |
|:-----------------------|:-------------:|:----------:|:----------:|:-------------------------------:|:------------------:|
| Arab                |     `ar`      |     ✓      |            |                                 |                    |
| Cseh                 |     `cs`      |     ✓      |            |                                 |                    |
| Egyszerűsített kínai     |   `zh-hans`   |     ✓      |            |                                 | `zh` szintén elfogadva |
| Kínai – hagyományos   |   `zh-hant`   |     ✓      |            |                                 |                    |
| Dán                |     `da`      |     ✓      |            |                                 |                    |
| Holland                 |     `nl`      |     ✓      |            |                                 |                    |
| Angol                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| Finn               |     `fi`      |     ✓      |            |                                 |                    |
| Francia                 |     `fr`      |     ✓      |            |                                 |                    |
| Német                 |     `de`      |     ✓      |            |                                 |                    |
| Héber                |     `he`      |     ✓      |            |                                 |                    |
| Magyar             |     `hu`      |     ✓      |            |                                 |                    |
| Olasz               |     `it`      |     ✓      |            |                                 |                    |
| Japán              |     `ja`      |     ✓      |            |                                 |                    |
| Koreai                |     `ko`      |     ✓      |            |                                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |            |                                 | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |     ✓      |            |                                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |            |                                 | `pt` szintén elfogadva |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Orosz              |     `ru`      |     ✓      |            |                                 |                    |
| Spanyol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| Svéd               |     `sv`      |     ✓      |            |                                 |                    |
| Török               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/key-phrase-extraction)

> [!NOTE]
> A 2020-07-01 előtti Kulcsszókeresés modellek 64 karakteres korláttal rendelkeznek. Ez a korlát nem szerepel a modell későbbi verzióiban.

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: |       Jegyzetek        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| Holland                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Angol               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Finn               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francia                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Német                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Olasz               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreai                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` szintén elfogadva |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Orosz               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spanyol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Svéd               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitás összekapcsolása](#tab/entity-linking)

| Nyelv | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: | Jegyzetek |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Angol  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spanyol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes felderíteni.  Nyelvfelismerés a nyelv "szkriptjét" adja vissza. Ha például a "Nekem van egy kutyám" kifejezés, akkor a helyett a értéket fogja visszaadni  `en`  `en-US` . Az egyetlen különleges eset a kínai, ahol a nyelvi észlelési képesség vissza fog térni, `zh_CHS` vagy `zh_CHT` Ha meg tudja határozni a parancsfájlt a megadott szöveg alapján. Azokban az esetekben, amikor egy kínai dokumentumhoz nem azonosítható egy adott parancsfájl, a rendszer egyszerűen visszaadja `zh` .

Nem tesszük közzé a funkcióhoz tartozó nyelvek pontos listáját, de számos nyelvet, változatot, dialektust és bizonyos regionális/kulturális nyelvet képes felderíteni. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

---

## <a name="see-also"></a>Lásd még

* [Mi a Text Analytics API?](overview.md)   

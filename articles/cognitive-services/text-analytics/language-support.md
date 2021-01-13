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
ms.date: 12/17/2020
ms.author: aahi
ms.openlocfilehash: a0cd968c90a27d6f1ae79d08fb2bd027169233db
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2021
ms.locfileid: "98133786"
---
# <a name="text-analytics-api-v3-language-support"></a>Text Analytics API v3 nyelvi támogatás 

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment-analysis)

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójának elindítása: |              Jegyzetek |
|:----------------------|:-------------:|:----------:|:----------:|:--------------------------:|-------------------:|
| Chinese-Simplified    |   `zh-hans`   |     ✓      |     ✓      |         2019-10-01         | `zh` szintén elfogadva |
| Chinese-Traditional   |   `zh-hant`   |            |     ✓      |         2019-10-01         |                    |
| dán               |     `da`      |     ✓      |            |                            |                    |
| Holland                 |     `nl`      |     ✓      |            |                            |                    |
| Angol               |     `en`      |     ✓      |     ✓      |         2019-10-01         |                    |
| finn               |     `fi`      |     ✓      |            |                            |                    |
| Francia                |     `fr`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Német                |     `de`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Görög                 |     `el`      |     ✓      |            |                            |                    |
| Hindi                 |     `hi`      |            |      ✓     |          2020-04-01        |                    |
| Olasz               |     `it`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |         2019-10-01         |                    |
| Koreai                |     `ko`      |            |     ✓      |         2019-10-01         |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |     ✓      |         2020-07-01         |                    |
| Lengyel                |     `pl`      |     ✓      |            |                            |                    |
| Portugál (Brazília)   |    `pt-BR`    |            |     ✓      |         2020-04-01         |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |         2019-10-01         | `pt` szintén elfogadva |
| Orosz               |     `ru`      |     ✓      |            |                            |                    |
| Spanyol               |     `es`      |     ✓      |     ✓      |         2019-10-01         |                    |
| svéd               |     `sv`      |     ✓      |            |                            |                    |
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
| Chinese-Simplified     |   `zh-hans`   |     ✓      |            |                                 | `zh` szintén elfogadva |
| Chinese-Traditional   |   `zh-hant`   |     ✓      |            |                                 |                    |
| cseh                 |     `cs`      |     ✓      |            |                                 |                    |
| dán                |     `da`      |     ✓      |            |                                 |                    |
| Holland                 |     `nl`      |     ✓      |            |                                 |                    |
| Angol                |     `en`      |     ✓      |     ✓      |           2019-10-01            |                    |
| finn               |     `fi`      |     ✓      |            |                                 |                    |
| Francia                 |     `fr`      |     ✓      |            |                                 |                    |
| Német                 |     `de`      |     ✓      |            |                                 |                    |
| héber                |     `he`      |     ✓      |            |                                 |                    |
| Magyar             |     `hu`      |     ✓      |            |                                 |                    |
| Olasz               |     `it`      |     ✓      |            |                                 |                    |
| Japán              |     `ja`      |     ✓      |            |                                 |                    |
| Koreai                |     `ko`      |     ✓      |            |                                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |            |                                 | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |     ✓      |            |                                 |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |            |                                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |            |                                 | `pt` szintén elfogadva |
| Orosz              |     `ru`      |     ✓      |            |                                 |                    |
| Spanyol               |     `es`      |     ✓      |     ✓       |              2020-04-01                   |                    |
| svéd               |     `sv`      |     ✓      |            |                                 |                    |
| Török               |     `tr`      |     ✓      |            |                                 |                    |

#### <a name="key-phrase-extraction"></a>[Fő kifejezés kibontása](#tab/key-phrase-extraction)

| Nyelv              | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: |       Jegyzetek        |
|:----------------------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:------------------:|
| dán                |     `da`      |     ✓      |      ✓     |                2019-10-01                 |                    |
| Holland                 |     `nl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Angol               |     `en`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| finn               |     `fi`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Francia                |     `fr`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Német                |     `de`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Olasz               |     `it`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Japán              |     `ja`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Koreai                |     `ko`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Norvég (nyelven)   |     `no`      |     ✓      |     ✓      |                2019-10-01                 | `nb` szintén elfogadva |
| Lengyel                |     `pl`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugál (Brazília)   |    `pt-BR`    |     ✓      |     ✓      |                2019-10-01                 |                    |
| Portugál (Portugália) |    `pt-PT`    |     ✓      |     ✓      |                2019-10-01                 | `pt` szintén elfogadva |
| Orosz               |     `ru`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| Spanyol               |     `es`      |     ✓      |     ✓      |                2019-10-01                 |                    |
| svéd               |     `sv`      |     ✓      |     ✓      |                2019-10-01                 |                    |

#### <a name="entity-linking"></a>[Entitás összekapcsolása](#tab/entity-linking)

| Nyelv | Nyelvkód | v2 támogatás | v3 támogatás | A v3 modell verziójától kezdődően érhető el: | Jegyzetek |
|:---------|:-------------:|:----------:|:----------:|:-----------------------------------------:|:-----:|
| Angol  |     `en`      |     ✓      |     ✓      |                2019-10-01                 |       |
| Spanyol  |     `es`      |     ✓      |     ✓      |                2019-10-01                 |       |

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language-detection)

A Text Analytics API számos nyelvet, változatot, dialektust és néhány regionális/kulturális nyelvet képes észlelni, és az észlelt nyelveket a nevükkel és kóddal kell visszaadnia. Text Analytics Nyelvfelismerés nyelvkód-paraméterek a [BCP-47](https://tools.ietf.org/html/bcp47) szabványnak felelnek meg, és a legtöbbjük megfelel az [ISO-639-1](https://www.iso.org/iso-639-language-codes.html) azonosítóknak. 

Ha olyan tartalommal rendelkezik, amely ritkábban használt nyelven van kifejezve, kipróbálhatja, Nyelvfelismerés, hogy visszaadja-e a kódot. A nem észlelhető nyelvek válasza a következő: `unknown` .

| Nyelv | Nyelvkód | v3 támogatás | A v3 modell verziójától kezdődően érhető el: |
|:-|:-:|:-:|:-:|
| búr | `af` | ✓ |  |
| albán | `sq` | ✓ |  |
| Arab | `ar` | ✓ |  |
| örmény | `hy` | ✓ |  |
| Baszk | `eu` | ✓ |  |
| belorusz | `be` | ✓ |  |
| bengáli | `bn` | ✓ |  |
| boszniai | `bs` | ✓ | 2020-09-01 |
| Bolgár | `bg` | ✓ |  |
| Burmai | `my` | ✓ |  |
| Katalán, valenciai | `ca` | ✓ |  |
| Közép-khmer | `km` | ✓ |  |
| Kínai | `zh` | ✓ |  |
| kínai (egyszerűsített) | `zh_chs` | ✓ |  |
| kínai (hagyományos) | `zh_cht` | ✓ |  |
| Horvát | `hr` | ✓ |  |
| cseh | `cs` | ✓ |  |
| dán | `da` | ✓ |  |
| Dari | `prs` | ✓ | 2020-09-01 |
| Divehi, dhivehi, Maldív | `dv` | ✓ |  |
| Holland, flamand | `nl` | ✓ |  |
| Angol | `en` | ✓ |  |
| Eszperantó | `eo` | ✓ |  |
| Észt | `et` | ✓ |  |
| Fidzsi | `fj` | ✓ | 2020-09-01 |
| finn | `fi` | ✓ |  |
| Francia | `fr` | ✓ |  |
| Gallego | `gl` | ✓ |  |
| grúz | `ka` | ✓ |  |
| Német | `de` | ✓ |  |
| Görög | `el` | ✓ |  |
| gudzsaráti | `gu` | ✓ |  |
| Haiti, Haiti kreol | `ht` | ✓ |  |
| héber | `he` | ✓ |  |
| Hindi | `hi` | ✓ |  |
| Hmong DAW | `mww` | ✓ | 2020-09-01 |
| Magyar | `hu` | ✓ |  |
| Izlandi | `is` | ✓ |  |
| Indonéz | `id` | ✓ |  |
| Inuktitut | `iu` | ✓ |  |
| Ír | `ga` | ✓ |  |
| Olasz | `it` | ✓ |  |
| Japán | `ja` | ✓ |  |
| kannada | `kn` | ✓ |  |
| Kazak | `kk` | ✓ | 2020-09-01 |
| Koreai | `ko` | ✓ |  |
| Kurd | `ku` | ✓ |  |
| Lao | `lo` | ✓ |  |
| Latin betűs | `la` | ✓ |  |
| Lett | `lv` | ✓ |  |
| Litván | `lt` | ✓ |  |
| Macedón | `mk` | ✓ |  |
| Malgas | `mg` | ✓ | 2020-09-01 |
| Maláj | `ms` | ✓ |  |
| malajálam | `ml` | ✓ |  |
| Máltai | `mt` | ✓ |  |
| maori | `mi` | ✓ | 2020-09-01 |
| marathi | `mr` | ✓ | 2020-09-01 |
| Norvég | `no` | ✓ |  |
| Norvég nynorsk | `nn` | ✓ |  |
| Oriya | `or` | ✓ |  |
| Pastu, Pushto | `ps` | ✓ |  |
| perzsa | `fa` | ✓ |  |
| Lengyel | `pl` | ✓ |  |
| Portugál | `pt` | ✓ |  |
| Pandzsábi, pandzsábi | `pa` | ✓ |  |
| Queretaro otomi | `otq` | ✓ | 2020-09-01 |
| Román, Moldova, Moldován | `ro` | ✓ |  |
| Orosz | `ru` | ✓ |  |
| Szamoai | `sm` | ✓ | 2020-09-01 |
| Szerb | `sr` | ✓ |  |
| Szingaléz, szingaléz | `si` | ✓ |  |
| Szlovák | `sk` | ✓ |  |
| Szlovén | `sl` | ✓ |  |
| Szomáli | `so` | ✓ |  |
| Spanyol, kasztíliai | `es` | ✓ |  |
| szuahéli | `sw` | ✓ |  |
| svéd | `sv` | ✓ |  |
| Tagalog | `tl` | ✓ |  |
| Tahitian | `ty` | ✓ | 2020-09-01 |
| tamil | `ta` | ✓ |  |
| telugu | `te` | ✓ |  |
| Thai | `th` | ✓ |  |
| Tongai | `to` | ✓ | 2020-09-01 |
| Török | `tr` | ✓ |  |
| Ukrán | `uk` | ✓ |  |
| urdu | `ur` | ✓ |  |
| Üzbég | `uz` | ✓ |  |
| Vietnámi | `vi` | ✓ |  |
| walesi | `cy` | ✓ |  |
| Jiddis | `yi` | ✓ |  |
| Yucatec Maya | `yua` | ✓ |  |

---

## <a name="see-also"></a>További információ

* [Mi a Text Analytics API?](overview.md)   

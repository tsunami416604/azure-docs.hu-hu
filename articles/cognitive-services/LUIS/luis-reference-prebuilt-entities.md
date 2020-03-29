---
title: Minden előre összeállított entitás - LUIS
titleSuffix: Azure Cognitive Services
description: Ez a cikk a nyelvi ismeretek (LUIS) részét képező előre összeállított entitások listáját tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219719"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitások kultúránként a LUIS-modellben

A language understanding (LUIS) előre összeállított entitásokat biztosít. Ha egy előre összeállított entitás szerepel az alkalmazásban, a LUIS tartalmazza a megfelelő entitás-előrejelzést a végpontválaszban. Minden példa utterances is címkével ellátott az entitás. Az előre összeállított entitások viselkedése **nem** módosítható. Eltérő rendelkezés hiányában az előre összeállított entitások minden LUIS-alkalmazásterületi beállításban (kultúrákban) elérhetők. Az alábbi táblázat az egyes kultúrák számára támogatott előre összeállított entitásokat mutatja be.

|Kulturális környezet|Szubkultúrák|Megjegyzések|
|--|--|--|
|Kínai|[zh-CN](#chinese-entity-support)||
|Holland|[nl-NL](#dutch-entity-support)||
|Angol|[en-US (amerikai)](#english-american-entity-support)||
|Francia|[fr-CA (Kanada),](#french-canadian-entity-support) [fr-FR (Franciaország),](#french-france-entity-support) ||
|Német|[de-DE](#german-entity-support)||
|Olasz|[it-IT](#italian-entity-support)||
|Japán|[ja-JP](#japanese-entity-support)||
|Koreai|[ko-KR](#korean-entity-support)||
|Portugál|[pt-BR (Brazília)](#portuguese-brazil-entity-support)||
|Spanyol|[es-ES (Spanyolország),](#spanish-spain-entity-support) [es-MX (Mexikó)](#spanish-mexico-entity-support)||
|Török|[török](#turkish-entity-support)|Nem támogatja a török öklét|

## <a name="prediction-endpoint-runtime"></a>Előrejelzési végpont futásideje

Egy előre összeállított entitás egy adott nyelven rendelkezésre álló az előrejelzési végpont futásidejű verziója határozza meg.

## <a name="chinese-entity-support"></a>Kínai entitás támogatása

A következő entitások támogatottak:

|Előre összeállított entitás|```zh-CN``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Holland szervezet támogatása

A következő entitások támogatottak:

|Előre összeállított entitás|```nl-NL``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Angol (amerikai) entitás támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```en-US``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Francia (franciaországi) jogalany-támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```fr-FR``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |   -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Francia (kanadai) entitás támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```fr-CA``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Német entitás támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```de-DE``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Olasz entitás támogatás

Az olasz előre elkészített kor, pénznem, dimenzió, szám, százalékos _felbontás_ megváltozott V2 és V3 előnézetről.

A következő entitások támogatottak:

|Előre összeállított entitás|```it-IT``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Japán entitás támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```ja-JP``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, -   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Koreai entitás támogatása

A következő entitások támogatottak:

|Előre összeállított entitás|```ko-KR``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    -   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    -   |
[Datetime](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    -   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Portugál (brazíliai) szervezet támogatása

A következő entitások támogatottak:

|Előre összeállított entitás|```pt-BR``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Spanyol (spanyolországi) jogalany-támogatás

A következő entitások támogatottak:

|Előre összeállított entitás|```es-ES``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    V2, V3   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    V2, V3   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    V2, V3   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    V2, V3   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Spanyol (mexikói) szervezet támogatása

A következő entitások támogatottak:

|Előre összeállított entitás|```es-MX``` |
------|:------:|
[Életkor:](luis-reference-prebuilt-age.md)<br>év<br>hónap<br>hét<br>nap   |    -   |
[Valuta (pénz)](luis-reference-prebuilt-currency.md):<br>Dollár<br>tört egység (pl. penny)  |    -   |
[DateTimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>dátumtartomány<br>time<br>időtartomány   |    -   |
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>Kötet<br>terület<br>Súly<br>információ (pl. bit/bájt)<br>hossz (pl. mérő)<br>sebesség (pl. mérföld per óra)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[FöldrajzV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Kulcsfókifejezés](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Szám](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinális](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Százalék](luis-reference-prebuilt-percentage.md)   |    -   |
[Személyneve](luis-reference-prebuilt-person.md)   |    -   |
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine között<br>delisle<br>celsius   |    -   |
[Url](luis-reference-prebuilt-url.md)   |    V2, V3   |

Az [elavult előre összeállított entitásokra](luis-reference-prebuilt-deprecated.md) vonatkozó megjegyzések megtekintése

A KeyPhrase nem minden portugál (Brazília) szubkultúrákban érhető ```pt-BR```el.

## <a name="turkish-entity-support"></a>Török entitás támogatás

**A török ökelt szervezetek nem támogatottak.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárulás az előre összeállított entitáskultúrákhoz
Az előre összeállított entitások a Recognizers-Text nyílt forráskódú projektben vannak kifejlesztve. [Járuljon hozzá](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt példákat tartalmaz a kulturális pénznemre.

A GeographyV2 és a PersonName nem szerepel nek a Felismerők szövege projektben. Az előre összeállított entitásokkal kapcsolatos problémák esetén nyisson meg egy [támogatási kérelmet.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>További lépések

További információ a [számról,](luis-reference-prebuilt-number.md) [a datetimeV2](luis-reference-prebuilt-datetimev2.md)és [a pénznementitásokról.](luis-reference-prebuilt-currency.md)

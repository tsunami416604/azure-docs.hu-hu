---
title: Az összes előre összeállított entitások
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előre összeállított entitások, a Language Understanding (LUIS) részét képező tartalmaz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 60acd381015a513873c4c414d6f1d9f3adc54b84
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870316"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entitás / kulturális környezetet a LUIS-modell

Language Understanding (LUIS) biztosít az előre összeállított entitások. Ha az alkalmazás tartalmaz egy előre összeállított entitások, LUIS a kapcsolódó entitás előrejelzési a végpont-válasz tartalmazza. Valamennyi példa utterances is az a entitás vannak ellátva. Előre összeállított entitások viselkedését **nem** módosítható. Ha nincs másképp jelölve, előre összeállított entitások érhető el minden, a LUIS alkalmazás területi beállítások (kulturális környezetek). Az alábbi táblázat az előre összeállított entitások minden kulturális környezet által támogatott.

|Kulturális környezet|Tápfolyadékok cseréjét|
|--|--|
|kínai|[zh-CN](#chinese-entity-support)|
|holland|[NL-NL](#dutch-entity-support)|
|Angol|[en-US (amerikai)](#english-american-entity-support)|
|francia|[FR-hitelesítésszolgáltató (Kanada)](#french-canadian-entity-support), [fr-FR (Franciaország)](#french-france-entity-support), |
|német|[de-DE](#german-entity-support)|
|olasz|[It-IT](#italian-entity-support)|
|japán|[ja-JP](#japanese-entity-support)|
|koreai|[ko-KR](#korean-entity-support)|
|portugál|[pt-BR (Brazília)](#portuguese-brazil-entity-support)|
|spanyol|[es-ES (Spanyolország)](#spanish-spain-entity-support), [gen-MX (Mexikó)](#spanish-mexico-entity-support)|

## <a name="chinese-entity-support"></a>Kínai entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```zh-CN``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Holland entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```nl-NL``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Angol (amerikai) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```en-US``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Francia (Franciaország) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```fr-FR``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Francia (kanadai) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```fr-CA``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Német entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```de-DE``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Olasz entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```it-IT``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Japán entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```ja-JP``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Koreai entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```ko-KR``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    -   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Portugál (brazíliai) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```pt-BR``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Spanyol (Spanyolország) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```es-ES``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    ✔   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    ✔   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Spanyol (Mexikó) entitás támogatása

Támogatottak a következő entitásokat:

|Előre összeállított entitások|```es-MX``` |
------|:------:|
[Kor](luis-reference-prebuilt-age.md):<br>év<br>hónap<br>hét<br>nap   |    -   |
[Pénznem](luis-reference-prebuilt-currency.md):<br>dollár<br>törtrész (például: minisztere)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>dátum<br>DateRange<br>time<br>timerange   |    -   | 
[Dimenzió](luis-reference-prebuilt-dimension.md):<br>kötet<br>terület<br>súlyozás<br>információk (például: bit/bájt)<br>hossz (például: az érték)<br>sebesség (például: mérföld / óra)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Szám](luis-reference-prebuilt-number.md)   |    ✔   |  
[Sorszám](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Százalékos aránya](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[Telefonszám](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Hőmérséklet](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL-cím](luis-reference-prebuilt-url.md)   |    ✔   |

Tekintse meg a megjegyzéseit a [elavult előre összeállított entitások](luis-reference-prebuilt-deprecated.md)

Nem áll rendelkezésre, portugál (brazíliai) – összes tápfolyadékok cseréjét KeyPhrase ```pt-BR```.

## <a name="contribute-to-prebuilt-entity-cultures"></a>Hozzájárul az előre összeállított entitások kulturális környezetek
Az előre összeállított entitások lettek kifejlesztve, a felismerő szöveges nyílt forráskódú projekt. [Közreműködés](https://github.com/Microsoft/Recognizers-Text) a projekthez. Ez a projekt a kulturális környezet pénznem vonatkozó példákat tartalmaz. 

GeographyV2 és PersonName nem szerepelnek a szöveges felismerő projekt. Az alábbi előre összeállított entitások problémáival kapcsolatban, nyissa meg a [támogatási kérelem](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>További lépések

További információ a [szám](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), és [pénznem](luis-reference-prebuilt-currency.md) entitásokat. 

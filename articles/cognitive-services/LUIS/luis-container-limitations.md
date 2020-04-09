---
title: Konténer korlátozások - LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló támogatott nyelvei.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7fe773b35c5aba31b2fea66bd2be7b2745eac3ee
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879241"
---
# <a name="language-understanding-luis-container-limitations"></a>Nyelvi ismertetési (LUIS) tároló konkretináta

A LUIS-tárolók néhány jelentős korlátozásokkal rendelkezik. A nem támogatott függőségektől a támogatott nyelvek egy részhalmazától ez a cikk részletezi ezeket a korlátozásokat.

## <a name="supported-dependencies-for-latest-container"></a>Támogatott függőségek `latest` a tárolóhoz

A legújabb LUIS-tároló, amely [a //build/ 2019-ben](https://news.microsoft.com/build2019/)jelent meg, támogatja a következőket:

* [Új előre összeállított tartományok:](luis-reference-prebuilt-domains.md)ezek a vállalati központú tartományok entitásokat, például kimondott szöveget és mintákat tartalmaznak. Bővítse ki ezeket a tartományokat saját használatra.

## <a name="unsupported-dependencies-for-latest-container"></a>Nem támogatott függőségek a tárolóhoz `latest`

A [tároló exportálásához](luis-container-howto.md#export-packaged-app-from-luis)el kell távolítania a nem támogatott függőségeket a LUIS alkalmazásból. Amikor megpróbálja exportálni a tárolót, a LUIS-portál jelenti az eltávolítandó nem támogatott szolgáltatásokat.

Használhatja a LUIS-alkalmazást, ha **nem tartalmazza** az alábbi függőségek egyikét sem:

Nem támogatott alkalmazáskonfigurációk|Részletek|
|--|--|
|Nem támogatott tárolókultúrák| Holland`nl-NL`( )<br>Japán`ja-JP`( )<br>Német csak akkor támogatott a [1.0.2 tokenizer](luis-language-support.md#custom-tokenizer-versions).|
|Nem támogatott entitások minden kultúrához|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) előre elkészített entitás minden kultúrához|
|Nem támogatott entitások`en-US`az angol ( ) kultúrához|[GeographyV2](luis-reference-prebuilt-geographyV2.md) előre összeállított entitások|
|Beszéd alapozása|A külső függőségek nem támogatottak a tárolóban.|
|Hangulatelemzés|A külső függőségek nem támogatottak a tárolóban.|
|Bing helyesírás-ellenőrzés|A külső függőségek nem támogatottak a tárolóban.|

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS-tárolók a LUIS által [támogatott nyelvek](luis-language-support.md#languages-supported) egy részét támogatják. A LUIS-tárolók a következő nyelveken képesek megérteni a kimondott szövegeket:

| Nyelv | Területi beállítás | Előre összeállított tartomány | Előre összeállított entitás | Kifejezéslista-javaslatok | **[Szövegelemzés](../text-analytics/language-support.md)<br>(Hangulat és<br>Kulcsszavak)|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francia (Franciaország) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francia (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Német |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Olasz |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Koreai |`ko-KR` | ✔️ | ❌ | ❌ | *Csak a kulcsmondat* |
| Portugál (Brazília) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem minden szubkultúra |
| Spanyol (Spanyolország) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanyol (Mexikó)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Török | `tr-TR` |✔️| ❌ | ❌ | *Csak hangulat* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
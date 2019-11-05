---
title: Tárolók korlátai – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS-tároló nyelvei támogatottak.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507857"
---
# <a name="language-understanding-luis-container-limitations"></a>Language Understanding (LUIS) tároló korlátozásai

A LUIS-tárolók néhány jelentős korlátozással rendelkeznek. A nem támogatott függőségek közül a jelen cikk ezeket a korlátozásokat részletezi.

## <a name="supported-dependencies-for-latest-container"></a>`latest` tároló támogatott függőségei

A [//build/2019](https://news.microsoft.com/build2019/)-es verzióban kiadott legújabb Luis-tároló a következőket támogatja:

* [Új előre összeépített tartományok](luis-reference-prebuilt-domains.md): ezek a nagyvállalati szintű tartományok többek között entitásokat, például hosszúságú kimondott szöveg és mintákat tartalmaznak. Kiterjesztheti ezeket a tartományokat saját használatra.

## <a name="unsupported-dependencies-for-latest-container"></a>`latest` tárolóhoz tartozó függőségek nem támogatottak

A [tárolóba való exportáláshoz](luis-container-howto.md#export-packaged-app-from-luis)el kell távolítania a nem támogatott függőségeket a Luis-alkalmazásból. Ha tárolóra próbál exportálni, a LUIS-portál ezeket a nem támogatott szolgáltatásokat jelenti, amelyeket el kell távolítania.

LUIS-alkalmazást használhat, ha az **nem tartalmazza** a következő függőségek egyikét sem:

Nem támogatott alkalmazások konfigurációi|Részletek|
|--|--|
|A tároló kulturális környezetei nem támogatottak| Holland (`nl-NL`)<br>Japán (`ja-JP`)<br>A német nyelv csak a [1.0.2-tokenizer](luis-language-support.md#custom-tokenizer-versions)támogatott.|
|Nem támogatott entitások minden kultúrához|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) előre összeépített entitás minden kultúrához|
|Nem támogatott entitások az angol (`en-US`) kulturális környezethez|[GeographyV2](luis-reference-prebuilt-geographyV2.md) előre elkészített entitások|
|Beszéd alapozó|A tárolóban a külső függőségek nem támogatottak.|
|Hangulatelemzés|A tárolóban a külső függőségek nem támogatottak.|
|Bing – helyesírás-ellenőrzés|A tárolóban a külső függőségek nem támogatottak.|

## <a name="languages-supported"></a>Támogatott nyelvek

A LUIS-tárolók támogatják a LUIS megfelelő által [támogatott nyelvek](luis-language-support.md#languages-supported) egy részhalmazát. A LUIS-tárolók képesek megismerni a hosszúságú kimondott szöveg a következő nyelveken:

| Nyelv | Területi beállítás | Előre elkészített tartomány | Előre elkészített entitás | Kifejezések listája – javaslatok | **[text Analytics](../text-analytics/language-support.md)<br>(Hangulat és<br>Kulcsszavak|
|--|--|:--:|:--:|:--:|:--:|
| Amerikai angol | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[kínai](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francia (franciaországi) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francia (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| német |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| olasz |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| koreai |`ko-KR` | ✔️ | ❌ | ❌ | Csak a *kulcs kifejezése* |
| Portugál (brazíliai) |`pt-BR` | ✔️ | ✔️ | ✔️ | nem minden alkultúra |
| Spanyol (Spanyolország) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spanyol (Mexikó)|`es-MX` | ❌ | ❌ |✔️|✔️|
| török | `tr-TR` |✔️| ❌ | ❌ | Csak *hangulat* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
---
title: A kognitív szolgáltatások SKU-és díjszabása
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: cb066ecc1dc11985c0ccb0fa687a15d6b038b9fa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89321618"
---
Tekintse meg az alábbi lista az SKU-ket és a díjszabási információkat. 

#### <a name="multi-service"></a>Több szolgáltatás

| Szolgáltatás                    | Erőforrás                      |
|----------------------------|---------------------------|
| Több szolgáltatás. További információkért tekintse meg a [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/) oldalt.            | `CognitiveServices`     |


#### <a name="vision"></a>Látás

| Szolgáltatás                    | Erőforrás                      |
|----------------------------|---------------------------|
| Computer Vision            | `ComputerVision`          |
| Custom Vision – előrejelzés | `CustomVision.Prediction` |
| Custom Vision – képzés   | `CustomVision.Training`   |
| Arcfelismerés                       | `Face`                    |
| Form Recognizer            | `FormRecognizer`          |
| Ink Recognizer             | `InkRecognizer`           |

#### <a name="search"></a>Keresés

| Szolgáltatás            | Erőforrás                  |
|--------------------|-----------------------|
| Bing Autosuggest   | `Bing.Autosuggest.v7` |
| Bing – Egyéni keresés | `Bing.CustomSearch`   |
| Bing – Entitáskeresés | `Bing.EntitySearch`   |
| Bing kereső        | `Bing.Search.v7`      |
| Bing – Helyesírás-ellenőrzés   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Speech

| Szolgáltatás            | Erőforrás                 |
|--------------------|----------------------|
| Beszédszolgáltatások    | `SpeechServices`     |
| Beszédfelismerés | `SpeakerRecognition` |

#### <a name="language"></a>Nyelv

| Szolgáltatás            | Erőforrás                |
|--------------------|---------------------|
| Űrlap megértése | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Szövegelemzés     | `TextAnalytics`     |
| Szövegfordítás   | `TextTranslation`   |

#### <a name="decision"></a>Döntés

| Szolgáltatás           | Erőforrás               |
|-------------------|--------------------|
| Anomáliadetektor  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizer      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* előre meghatározott számú tranzakció díja. Ennek a számnak a meghaladása esetén a szolgáltatás [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) alapján külön díjat számítunk fel.

> [!NOTE]
> Számos Cognitive Services rendelkezik egy ingyenes szintű lehetőséggel, amellyel kipróbálhatja a szolgáltatást. Az ingyenes szintet az `F0` erőforráshoz tartozó SKU-ként használhatja.
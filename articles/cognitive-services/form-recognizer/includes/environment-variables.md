---
title: Környezeti változók
description: környezeti változók beállítása
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: d63e902a59411a549235c955a39d7dbc4be068ba
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156503"
---
Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:

* `FORM_RECOGNIZER_KEY`– A kérések hitelesítéséhez szükséges erőforrás-kulcs.
* `FORM_RECOGNIZER_ENDPOINT`– Az erőforrás-végpont API-kérelmek küldéséhez. A következőképpen fog kinézni: 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> Az 2019. július 1. után létrehozott erőforrás-végpontok az alábbi egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Az alábbi utasítások segítségével állíthatja be az operációs rendszer környezeti változóit.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

A környezeti változók hozzáadása után zárjuk be, majd nyissa meg újra a konzolablak ablakát.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[macOS](#tab/unix)

Szerkessze a t `.bash_profile` , és adja hozzá a környezeti változót:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

A környezeti változók hozzáadása után futtassa a parancsot a `source .bash_profile` konzol ablakából a módosítások érvénybe léptetéséhez.
***

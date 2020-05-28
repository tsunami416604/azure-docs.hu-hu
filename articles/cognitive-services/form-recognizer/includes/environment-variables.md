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
ms.openlocfilehash: 8bcce81808530c124b73b3527609a08f869b99c4
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997608"
---
Ha a kulcsot és a végpontot a létrehozott erőforrás alapján hozza létre, hozzon létre két környezeti változót a hitelesítéshez:

* `FORM_RECOGNIZER_KEY`– A kérések hitelesítéséhez szükséges erőforrás-kulcs.
* `FORM_RECOGNIZER_ENDPOINT`– Az erőforrás-végpont API-kérelmek küldéséhez. A következőképpen fog kinézni: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

>[!NOTE]
> Az 2019. július 1. után létrehozott, nem próbaverziós erőforrásokhoz használt végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

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
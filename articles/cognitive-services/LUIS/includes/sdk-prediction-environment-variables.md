---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772436"
---
### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A futásidejű kulcs és a futásidejű végpont használatával hozzon létre környezeti változókat a hitelesítéshez és a hozzáféréshez:

* `LUIS_RUNTIME_KEY`– A kérelmek hitelesítéséhez használt futásidejű erőforrás kulcsa.
* `LUIS_RUNTIME_ENDPOINT`– A kulcshoz társított futásidejű végpont.
* `LUIS_APP_ID`– A nyilvános LUIS IoT-alkalmazás azonosítója `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production`vagy`staging`

Ha a rövid útmutató segítségével szeretné elérni a saját alkalmazását, további lépéseket kell tennie:
* Az alkalmazás létrehozása és az alkalmazás AZONOSÍTÓjának beolvasása
* A futásidejű kulcs kiosztása az alkalmazáshoz a LUIS-portálon
* Tesztelje az URL-címet a böngészőben, amelyet elérheti az alkalmazást

Használja az operációs rendszerének utasításait.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

A környezeti változók hozzáadása után indítsa újra a konzolablak ablakát.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

A környezeti változók hozzáadása után futtassa `source ~/.bashrc` a parancsot a konzol ablakából a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[macOS](#tab/unix)

Szerkessze `.bash_profile`a t, és adja hozzá a környezeti változót:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

A környezeti változók hozzáadása után futtassa `source .bash_profile` a parancsot a konzol ablakából a módosítások érvénybe léptetéséhez.

***

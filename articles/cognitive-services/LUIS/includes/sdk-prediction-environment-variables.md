---
title: fájl belefoglalása
description: fájl belefoglalása
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772436"
---
### <a name="create-an-environment-variable"></a>Környezeti változó létrehozása

A futásidejű kulcs és a futásidejű végpont használatával hozzon létre környezeti változókat a hitelesítéshez és a hozzáféréshez:

* `LUIS_RUNTIME_KEY`- A futásidejű erőforrás kulcs a kérések hitelesítéséhez.
* `LUIS_RUNTIME_ENDPOINT`- A kulcshoz társított futásidejű végpont.
* `LUIS_APP_ID`- A nyilvános LUIS IoT `df67dcdb-c37d-46af-88e1-8b97951ca1c2`alkalmazásazonosító .
* `LUIS_APP_SLOT_NAME` - `production`Vagy`staging`

Ha ezt a rövid útmutatót saját alkalmazáseléréséhez kívánja használni, további lépéseket kell tennie:
* Az alkalmazás létrehozása és az alkalmazásazonosító lekérte
* A futásidejű kulcs hozzárendelése az alkalmazáshoz a LUIS-portálon
* Tesztelje az URL-t a böngészővel, hogy el tudja érni az alkalmazást

Használja az operációs rendszerre vonatkozó utasításokat.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

A környezeti változók hozzáadása után indítsa újra a konzolablakot.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

A környezeti változók hozzáadása `source ~/.bashrc` után futtassa a konzolablakból a módosítások hatékonyságának érdekében.

#### <a name="macos"></a>[Macos](#tab/unix)

A program `.bash_profile`szerkesztése és a környezeti változó hozzáadása:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

A környezeti változók hozzáadása `source .bash_profile` után futtassa a konzolablakból a módosítások hatékonyságának érdekében.

***

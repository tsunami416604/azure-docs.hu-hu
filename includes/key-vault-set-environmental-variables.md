---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 6dfc96df3623e44a4ba513c238f21482c2a2989d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87013108"
---
Az alkalmazás DefaultAzureCredential metódusa három környezeti változóra támaszkodik: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` és `AZURE_TENANT_ID` . Állítsa be ezeket a változókat az clientId, a clientSecret és a tenantId értékekre, amelyeket a "szolgáltatásnév létrehozása" lépésben adtak vissza a `export VARNAME=VALUE` formátum használatával. (Ez a módszer csak a rendszerhéjból létrehozott jelenlegi rendszerhéj és folyamatok változóit állítja be, hogy véglegesen hozzáadja ezeket a változókat a környezetéhez, szerkessze a `/etc/environment ` fájlt.) 

A kulcstároló nevét a nevű környezeti változó néven is menteni kell `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

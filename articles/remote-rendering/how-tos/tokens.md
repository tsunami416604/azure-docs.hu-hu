---
title: Szolgáltatás-hozzáférési jogkivonatok lekérése
description: Az ARR REST API-k eléréséhez szükséges jogkivonatok létrehozásának ismertetése
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687069"
---
# <a name="get-service-access-tokens"></a>Szolgáltatás-hozzáférési jogkivonatok lekérése

Az ARR REST API-khoz való hozzáférés csak a jogosult felhasználók számára engedélyezett. Az engedély bizonyításához egy *hozzáférési jogkivonatot* kell küldenie a REST-kérelmekkel együtt. Ezeket a jogkivonatokat a *Secure Token Service* (STS) egy fiókkulcsért cserébe bocsátja ki. A jogkivonatok **élettartama 24 óra,** így a felhasználók számára teljes hozzáférés nélkül adhatók ki.

Ez a cikk ismerteti, hogyan hozhat létre ilyen hozzáférési jogkivonatot.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy ARR-fiókot,](create-an-account.md)ha még nem rendelkezik ilyensel.

## <a name="token-service-rest-api"></a>Tokenszolgáltatás REST API-ja

Hozzáférési jogkivonatok létrehozásához a *biztonságos jogkivonat-szolgáltatás* egyetlen REST API-t biztosít. Az ARR STS szolgáltatás URL-címe https:\//sts.mixedreality.azure.com.

### <a name="get-token-request"></a>"Token bekérése" kérés

| URI | Módszer |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| Fejléc | Érték |
|--------|:------|
| Engedélyezés | **"TulajdonosaccountId**:**accountKey"** |

Cserélje *le a fiókazonosítót* és a *fiókkulcsot* a megfelelő adatokra.

### <a name="get-token-response"></a>"Token bekéselése" válasz

| Állapotkód | JSON hasznos teher | Megjegyzések |
|-----------|:-----------|:-----------|
| 200 | AccessToken: karakterlánc | Sikeres |

| Fejléc | Cél |
|--------|:------|
| MS-CV | Ez az érték a szolgáltatáson belüli hívás nyomon követésére használható. |

## <a name="getting-a-token-using-powershell"></a>Token beszerzése a PowerShell használatával

Az alábbi PowerShell-kód bemutatja, hogyan küldheti el a szükséges REST-kérelmet az STS-nek. Ezután kinyomtatja a jogkivonatot a PowerShell-parancssorba.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

A parancsfájl csak kinyomtatja a tokent a kimenetre, ahonnan & beillesztheti. Egy valódi projekt esetén automatizálnia kell ezt a folyamatot.

## <a name="next-steps"></a>További lépések

* [PowerShell-példaszkriptek](../samples/powershell-example-scripts.md)
* [Azure előtér-API-k](../how-tos/frontend-apis.md)
* [Munkamenet-kezelés REST API](../how-tos/session-rest-api.md)

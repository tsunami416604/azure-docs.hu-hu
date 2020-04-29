---
title: Biztonsági jogkivonat előállítása a IoT való hozzáféréshez Plug and Play előzetes verziójú adattár | Microsoft Docs
description: Létrehoz egy közös hozzáférési aláírási jogkivonatot, amelyet akkor használhat, amikor egy IoT Plug and Play előzetes verziójú modellhez való hozzáférést.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80159200"
---
# <a name="generate-sas-token"></a>SAS-token előállítása

Ez a útmutató azt mutatja be, hogyan lehet programozott módon előállítani egy közös hozzáférési aláírási (SAS-) tokent, amelyet a IoT Plug and Play a minta-adattár API-kkal való használatra.

## <a name="python"></a>Python

Az alábbi kódrészletből megtudhatja, hogyan hozhatja ki SAS-jogkivonatot a Python használatával:

```python
from base64 import b64decode, b64encode
from hashlib import sha256
from hmac import digest
from time import time
from urllib.parse import quote_plus, urlencode

def calculate_sas_token(hostname, repo_id, key_name, key, expiry_in_second):
    expire = int(time() + expiry_in_second)
    sign_value = "{0}\n{1}\n{2}".format(repo_id, quote_plus(hostname), expire)
    sig = b64encode(digest(b64decode(key), sign_value.encode("utf-8"), sha256))
    token = "SharedAccessSignature " + urlencode({
        "sr": hostname, 
        "sig": sig,
        "se": str(expire),
        "skn": key_name,
        "rid": repo_id
        })
    return token
```

## <a name="c"></a>C\#

Az alábbi kódrészletből megtudhatja, hogyan hozhatja ki a\#sas-jogkivonatot a C használatával:

```csharp
public static string generateSasToken(string hostName, string repoId, string key, string keyName, int expiryInSeconds = 3600)
{
    TimeSpan fromEpochStart = DateTime.UtcNow - new DateTime(1970, 1, 1);
    string expiry = Convert.ToString((int)fromEpochStart.TotalSeconds + expiryInSeconds);

    string stringToSign = repoId + "\n" + WebUtility.UrlEncode(hostName) + "\n" + expiry;

    HMACSHA256 hmac = new HMACSHA256(Convert.FromBase64String(key));
    string signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));

    string token = String.Format(
        CultureInfo.InvariantCulture,
        "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}&rid={4}",
        WebUtility.UrlEncode(hostName),
        WebUtility.UrlEncode(signature),
        expiry,
        keyName,
        repoId);

    return token;
}
```

## <a name="use-the-sas-token"></a>Az SAS-jogkivonat használata

Az SAS-token létrehozása után a használatával HTTP POST-kérést is létrehozhat. Például:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Ha SAS-tokent ad egy ügyfélnek, az ügyfél nem rendelkezik az erőforrás elsődleges kulcsával, és nem tudja visszafordítani a kivonatot a beszerzéséhez. Az SAS-token segítségével szabályozhatja, hogy az ügyfél mit tud elérni, és mennyi ideig. Ha módosítja a házirend elsődleges kulcsát, a rendszer a belőle létrehozott SAS-jogkivonatokat érvényteleníti.

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogy milyen biztonsági jogkivonatokat hoz létre a modell IoT Plug and Play előnézeti modellhez való hozzáféréshez, a következő lépés az, hogy további információra van szüksége a [IoT Plug and Play előzetes verziójú modellezés fejlesztői útmutatójában](concepts-developer-guide.md).

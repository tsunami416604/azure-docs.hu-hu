---
title: Biztonsági jogkivonat létrehozása az IoT Plug and Play Preview adattár eléréséhez | Microsoft dokumentumok
description: Hozzon létre egy megosztott hozzáférésű aláírás-jogkivonatot, amelyet az IoT Plug and Play Preview modelltártár programozott módon való elérésekor használhat.
author: Philmea
ms.author: philmea
ms.date: 12/27/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f008627317588467d731ccc03aec7738f58e46e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159200"
---
# <a name="generate-sas-token"></a>SAS-jogkivonat létrehozása

Ez az útmutató bemutatja, hogyan hozhat létre programozott módon egy megosztott hozzáférésű aláírási (SAS) jogkivonatot az IoT Plug and Play Preview modelltártárapi-khoz.

## <a name="python"></a>Python

A következő kódrészlet bemutatja, hogyan hozhat létre SAS-jogkivonatot a Python használatával:

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

A következő kódrészlet bemutatja, hogyan hozhat létre\#SAS-jogkivonatot a C használatával:

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

## <a name="use-the-sas-token"></a>A SAS-jogkivonat használata

SAS-jogkivonat létrehozása után http-posta-kérelem benyújtásához használhatja. Példa:

```text
POST https:///models/{modelId}?repositoryId={repositoryId}&api-version=2019-07-01-preview
```

Ha egy ügyfél egy SAS-jogkivonatot, az ügyfél nem rendelkezik az erőforrás elsődleges kulcsa, és nem tudja visszavonni a kivonatot annak megszerzéséhez. A SAS-jogkivonat segítségével szabályozhatja, hogy az ügyfél mit érhet el, és mennyi ideig. Ha módosítja az elsődleges kulcsot a házirendben, az abból létrehozott SAS-jogkivonatok érvénytelenné válnak.

## <a name="next-steps"></a>További lépések

Most, hogy megismerkedett az IoT Plug and Play Preview modelltárházak eléréséhez használt biztonsági jogkivonatok létrehozásáról, a javasolt következő lépés az [IoT Plug and Play Preview modellezési fejlesztői útmutatójának](concepts-developer-guide.md)további ismertetése.

---
title: Az Azure Key Vault szabályozási útmutatója
description: Key Vault-szabályozás korlátozza, hogy az erőforrások túlzott párhuzamos hívások száma.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: bryanla
ms.openlocfilehash: f119e4a5b5c5f97848c588636a3a707428abbd5b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082526"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault szabályozási útmutatója

Szabályozás az kezdeményez, amely korlátozza az erőforrások túlzott elkerülése érdekében az Azure-szolgáltatás egyidejű beérkező hívásainak számát. Az Azure Key Vault (AKV) célja nagy mennyiségű kérelmet kezelni. Ha túlságosan sok kérelem történik, az ügyfél kérelmek szabályozása segítségével optimális teljesítményének és megbízhatóságának az AKV szolgáltatás karbantartása.

A forgatókönyv szabályozási korlátok függ. Például akkor, ha nagy mennyiségű írást, a szabályozás lehetősége magasabb, mint ha csak végez olvasási.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli a Key Vault a teljesítménye korlátait?

A Key Vaultban szolgáltatási korlátai-e az erőforrások visszaélések megelőzése érdekében, és az összes Key Vault ügyfelek szolgáltatásminőség biztosítja. Egy szolgáltatás küszöbérték túllépésekor a Key Vault korlátozza a további kéréseket az, hogy az ügyfél egy ideig. Ha ez történik, a Key Vault adja vissza a HTTP-állapotkód: 429-es (túl sok kérés), és a kérelmek sikertelenek. Nem sikerült is, a kéréseket, amelyek felé az követi nyomon a Key Vault szabályozási korlátairól 429 számát adja vissza. 

Ha rendelkezik egy újabb szabályozási korlátairól üzleti esetet, lépjen kapcsolatba velünk a következő címen.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szolgáltatásokra vonatkozó korlátozások válaszul szabályozásának megtervezéséhez

Az alábbi **ajánlott eljárások** szabályozási alkalmazását:
- Műveletek kérelmenkénti számának csökkentése.
- Csökkentse a kérelmek gyakoriságát.
- Azonnali újrapróbálkozás elkerülése érdekében. 
    - Összes kérelem lépheti túl a használati korlátok alapján.

Az alkalmazás hibakezelési megvalósításának, a 429-es HTTP-hibakód használatával észleli a ügyféloldali szabályozás. A kérelem újra HTTP 429-es hibaüzenettel meghiúsul, ha csökkenti az Azure-szolgáltatás továbbra is merültek fel. Továbbra is használhatja az ajánlott ügyféloldali metódus szabályozás, amíg be nem fejeződik, a kérelem újbóli megkísérlése.

Exponenciális visszatartással megvalósító kódot alább látható. 
```
     public async Task OnGetAsync()
     {
         Message = "Your application description page.";
         int retries = 0;
         bool retry = false;
         try
         {
             AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
             KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
             var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
             Message = secret.Value;

             /* The below do while logic is to handle throttling errors thrown by Azure Key Vault. It shows how to do exponential backoff which is the recommended client side throttling*/
             do
             {
                 long waitTime = Math.Min(getWaitTime(retries), 2000000);
                 secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                     .ConfigureAwait(false);
                 retry = false;
             } 
             while(retry && (retries++ < 10));
         }
         /// <exception cref="KeyVaultErrorException">
         /// Thrown when the operation returned an invalid status code
         /// </exception>
         catch (KeyVaultErrorException keyVaultException)
         {
             Message = keyVaultException.Message;
             if((int)keyVaultException.Response.StatusCode == 429)
                 retry = true;
         }
     }

     // This method implements exponential backoff incase of 429 errors from Azure Key Vault
     private static long getWaitTime(int retryCount)
     {
         long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
         return waitTime;
     }
```

### <a name="recommended-client-side-throttling-method"></a>Ajánlott az ügyféloldali szabályozási módszer

HTTP 429-es hibakód, a szabályozás, az ügyfél egy exponenciális visszatartással megközelítéssel kezdete:

1. Várjon 1 másodperc, próbálkozzon újra a kérelmet
2. Ha továbbra is szabályozott várjon 2 másodperc, ismételje meg a kérelmet
3. Ha továbbra is szabályozott várakozási 4 másodperc, ismételje meg a kérelmet
4. Ha továbbra is szabályozott várakozási 8 másodperc, ismételje meg a kérelmet
5. Ha továbbra is szabályozott várakozási 16 másodperc, ismételje meg a kérelmet

Ezen a ponton meg kell nem lehet kezdeti HTTP 429-es válaszkódot.

## <a name="see-also"></a>Lásd még

A szabályozást a Microsoft Cloud mélyebb tájolásának, lásd: [szabályozási minta](https://docs.microsoft.com/azure/architecture/patterns/throttling).


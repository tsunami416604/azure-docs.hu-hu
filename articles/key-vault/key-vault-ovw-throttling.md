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
ms.openlocfilehash: 261a735a94aca23fe10bdab461a9619250f67f41
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884980"
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
         do 
         {
             try
             {
                 AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                 KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                 var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                         .ConfigureAwait(false);
                 Message = secret.Value;
             }
             /// <exception cref="KeyVaultErrorException">
             /// Thrown when the operation returned an invalid status code
             /// </exception>
             catch (KeyVaultErrorException keyVaultException)
             {
                 Message = keyVaultException.Message;
                 if ((int)keyVaultException.Response.StatusCode == 429)
                 {
                    retry = true;

                    long waitTime = Math.Min(getWaitTime(retries), 2000000);
                    Thread.Sleep(new TimeSpan(waitTime));
                }
             }
         } while(retry && (retries++ < 10));         
         
     }

     // This method implements exponential backoff in case of 429 errors from Azure Key Vault
     private static long getWaitTime(int retryCount)
     {
        return (long)Math.Pow(2, retryCount) * 100L;
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


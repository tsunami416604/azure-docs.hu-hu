---
title: Az Azure Key Vault szabályozási útmutatója
description: Key Vault-szabályozás korlátozza, hogy az erőforrások túlzott párhuzamos hívások száma.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 0f8aafce4c4feeed742504db84664e4dfd472ca6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787549"
---
# <a name="azure-key-vault-throttling-guidance"></a>Az Azure Key Vault szabályozási útmutatója

Szabályozás az kezdeményez, amely korlátozza az erőforrások túlzott elkerülése érdekében az Azure-szolgáltatás egyidejű beérkező hívásainak számát. Az Azure Key Vault (AKV) célja nagy mennyiségű kérelmet kezelni. Ha túlságosan sok kérelem történik, az ügyfél kérelmek szabályozása segítségével optimális teljesítményének és megbízhatóságának az AKV szolgáltatás karbantartása.

A forgatókönyv szabályozási korlátok függ. Például akkor, ha nagy mennyiségű írást, a szabályozás lehetősége magasabb, mint ha csak végez olvasási.

## <a name="how-does-key-vault-handle-its-limits"></a>Hogyan kezeli a Key Vault a teljesítménye korlátait?

A Key Vaultban szolgáltatási korlátai-e az erőforrások visszaélések megelőzése érdekében, és az összes Key Vault ügyfelek szolgáltatásminőség biztosítja. Egy szolgáltatás küszöbérték túllépésekor a Key Vault korlátozza a további kéréseket az, hogy az ügyfél egy ideig. Ha ez történik, a Key Vault adja vissza a HTTP-állapotkód: 429-es (túl sok kérés), és a kérelmek sikertelenek. Nem sikerült is, a kéréseket, amelyek felé az követi nyomon a Key Vault szabályozási korlátairól 429 számát adja vissza. 

Ha rendelkezik egy újabb szabályozási korlátairól üzleti esetet, lépjen kapcsolatba velünk a következő címen.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Az alkalmazás szolgáltatásokra vonatkozó korlátozások válaszul szabályozásának megtervezéséhez

Az alábbi **ajánlott eljárások** kell megvalósítania, ha a szolgáltatás Tranzakciókorlát elérése:
- Műveletek kérelmenkénti számának csökkentése.
- Csökkentse a kérelmek gyakoriságát.
- Azonnali újrapróbálkozás elkerülése érdekében. 
    - Összes kérelem lépheti túl a használati korlátok alapján.

Az alkalmazás hibakezelési megvalósításának, a 429-es HTTP-hibakód használatával észleli a ügyféloldali szabályozás. A kérelem újra HTTP 429-es hibaüzenettel meghiúsul, ha csökkenti az Azure-szolgáltatás továbbra is merültek fel. Továbbra is használhatja az ajánlott ügyféloldali metódus szabályozás, amíg be nem fejeződik, a kérelem újbóli megkísérlése.

Exponenciális visszatartással megvalósító kódot alább látható. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


A kód használatához az ügyfél C\# alkalmazás nagyon egyszerű. A következő példa bemutatja, hogy a HttpClient osztály használatával.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Ne feledje, hogy ez a kód csak a koncepció igazolása alkalmas. 

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


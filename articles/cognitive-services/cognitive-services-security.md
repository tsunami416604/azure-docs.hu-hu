---
title: Biztonság
titleSuffix: Azure Cognitive Services
description: További információ a Cognitive Services használatának különböző biztonsági szempontjairól.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131536"
---
# <a name="azure-cognitive-services-security"></a>Azure Cognitive Services biztonság

A biztonsági és az összes alkalmazás fejlesztésekor elsődleges prioritásnak kell tekinteni. A mesterséges intelligenciát használó alkalmazások kialakulásával a biztonság még ennél is fontosabb. Ebben a cikkben az Azure Cognitive Services Security különböző szempontjait ismertetjük, például a Transport Layer Security, a Authentication és a bizalmas adatok biztonságos konfigurálását.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A HTTP-n keresztül elérhető összes Cognitive Services-végpont TLS 1,2. A kényszerített biztonsági protokoll használatával az Cognitive Services végpontot meghívó felhasználóknak be kell tartaniuk az alábbi irányelveket:

* Az ügyfél operációs rendszerének (OS) támogatnia kell a TLS 1,2-et
* A HTTP-híváshoz használt nyelv (és platform) a kérelem részeként meg kell határoznia a TLS 1,2-as értéket.
  * A nyelvtől és a platformtól függően a TLS meghatározása implicit módon vagy explicit módon történik

.NET-felhasználók esetén vegye figyelembe a <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security ajánlott <span class="docon docon-navigate-external x-hidden-focus"> </span>eljárásokat </a>.

## <a name="authentication"></a>Hitelesítés

A hitelesítés megvitatásakor számos gyakori tévhit van. A hitelesítés és az engedélyezés gyakran zavaros egymás számára. Az identitás a biztonság egyik fő összetevője is. Az identitás egy <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank">rendszerbiztonsági tag <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>információinak gyűjteménye. Az Identity Providers (identitásszolgáltató) hitelesítő szolgáltatásokhoz biztosít identitásokat. A hitelesítés a felhasználó identitásának ellenőrzése. Az engedélyezés az adott identitáshoz tartozó erőforrásokhoz való hozzáférési jogosultságok és jogosultságok meghatározása. Több Cognitive Services ajánlat, beleértve a szerepköralapú hozzáférés-vezérlést (RBAC). A RBAC felhasználható a rendszerbiztonsági tag manuális kezelésével kapcsolatos ünnepségek egyszerűsítésére. További részletek: [szerepköralapú hozzáférés-vezérlés az Azure-erőforrásokhoz](../role-based-access-control/overview.md).

Az előfizetési kulcsokkal, a hozzáférési jogkivonatokkal és a Azure Active Directorysal (HRE) való hitelesítéssel kapcsolatos további információkért lásd: <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">kérések hitelesítése az Azure Cognitive Servicesban<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

## <a name="environment-variables-and-application-configuration"></a>Környezeti változók és alkalmazás konfigurációja

A környezeti változók név-érték párok, amelyek egy adott környezetben tárolódnak. A bizalmas adatok hardcoded értékeinek használata biztonságosabb alternatíva a környezeti változók használata. A hardcoded értékei nem biztonságosak, ezért kerülendő.

> [!CAUTION]
> Ne **használjon hardcoded** -értékeket a bizalmas adatokhoz, így ez jelentős biztonsági rést jelent.

> [!NOTE]
> Míg a környezeti változók egyszerű szövegként vannak tárolva, azok el vannak különítve egy környezetbe. Ha egy környezet biztonsága sérül, akkor a környezet változói is.

### <a name="set-environment-variable"></a>Környezeti változó beállítása

A környezeti változók megadásához használja a következő parancsokat – ahol a `ENVIRONMENT_VARIABLE_KEY` a megnevezett kulcs, `value` és a környezeti változóban tárolt érték.

# <a name="command-line"></a>[Parancssor](#tab/command-line)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

A **parancssor**új példányában olvassa el a környezeti változót.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```powershell
# Assigns the env var to the value
[System.Environment]::SetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY', 'value', 'User')
```

A **Windows PowerShell**új példányában olvassa el a környezeti változót.

```powershell
# Prints the env var value
[System.Environment]::GetEnvironmentVariable('ENVIRONMENT_VARIABLE_KEY')
```

# <a name="bash"></a>[Bash](#tab/bash)

Megőrzött környezeti változó létrehozása és kiosztása a megadott értékkel.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

A **bash**új példányában olvassa el a környezeti változót.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> A környezeti változók beállítása után indítsa újra az integrált fejlesztői környezetet (IDE), és győződjön meg arról, hogy az újonnan hozzáadott környezeti változók elérhetők.

### <a name="get-environment-variable"></a>Környezeti változó beolvasása

Környezeti változó beszerzéséhez a memóriába kell beolvasni. A használt nyelvtől függően vegye figyelembe az alábbi kódrészleteket. Ezek a kódrészletek bemutatják, hogyan kérheti le `ENVIRONMENT_VARIABLE_KEY` a környezeti változót, és `value`hogyan rendelhet hozzá egy nevű változóhoz.

# <a name="c"></a>[C #](#tab/csharp)

További információ: <a href="https://docs.microsoft.com/dotnet/api/system.environment.getenvironmentvariable" target="_blank"> `Environment.GetEnvironmentVariable` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```csharp
using static System.Environment;

class Program
{
    static void Main()
    {
        // Get the named env var, and assign it to the value variable
        var value =
            GetEnvironmentVariable(
                "ENVIRONMENT_VARIABLE_KEY");
    }
}
```

# <a name="c"></a>[C++](#tab/cpp)

További információ: <a href="https://docs.microsoft.com/cpp/c-runtime-library/reference/getenv-wgetenv" target="_blank"> `getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```cpp
#include <stdlib.h>

int main()
{
    // Get the named env var, and assign it to the value variable
    auto value =
        getenv("ENVIRONMENT_VARIABLE_KEY");
}
```

# <a name="java"></a>[Java](#tab/java)

További információ: <a href="https://docs.oracle.com/javase/7/docs/api/java/lang/System.html#getenv(java.lang.String)" target="_blank"> `System.getenv` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```java
import java.lang.*;

public class Program {
   public static void main(String[] args) throws Exception {
    // Get the named env var, and assign it to the value variable
    String value =
        System.getenv(
            "ENVIRONMENT_VARIABLE_KEY")
   }
}
```

# <a name="nodejs"></a>[Node.js](#tab/node-js)

További információ: <a href="https://nodejs.org/api/process.html#process_process_env" target="_blank"> `process.env` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```javascript
// Get the named env var, and assign it to the value variable
const value =
    process.env.ENVIRONMENT_VARIABLE_KEY;
```

# <a name="python"></a>[Python](#tab/python)

További információ: <a href="https://docs.python.org/2/library/os.html#os.environ" target="_blank"> `os.environ` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```python
import os

# Get the named env var, and assign it to the value variable
value = os.environ['ENVIRONMENT_VARIABLE_KEY']
```

# <a name="objective-c"></a>[Objective-C](#tab/objective-c)

További információ: <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>További lépések

* Ismerkedjen meg a különböző [Cognitive Servicesokkal](welcome.md)
* További információ a [Cognitive Services Virtual Networks](cognitive-services-virtual-networks.md) szolgáltatásról

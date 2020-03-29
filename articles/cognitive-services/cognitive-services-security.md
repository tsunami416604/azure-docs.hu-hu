---
title: Biztonság
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Cognitive Services használatának különböző biztonsági szempontjait.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: dapine
ms.openlocfilehash: c86d806c408c2e8226e632a0b15e1e8729c987f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131536"
---
# <a name="azure-cognitive-services-security"></a>Az Azure Cognitive Services biztonsága

A biztonságot minden alkalmazás fejlesztése során kiemelt fontosságúnak kell tekinteni. A mesterséges intelligencia által lehetővé tett alkalmazások kilábalása még fontosabb a biztonság. Ebben a cikkben az Azure Cognitive Services biztonságának különböző aspektusait ismertetjük, például az átviteli réteg biztonságának használatát, a hitelesítést és a bizalmas adatok biztonságos konfigurálását.

## <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

A Cognitive Services összes HTTP-n keresztül elérhetővé tett végpontja kényszeríti a TLS 1.2-t. Kényszerített biztonsági protokoll esetén a Cognitive Services-végpontot meghívni próbáló fogyasztóknak be kell tartaniuk az alábbi irányelveket:

* Az ügyfél operációs rendszernek (OS) támogatnia kell a TLS 1.2-t
* A HTTP-hívás hoz használt nyelvnek (és platformnak) meg kell adnia a TLS 1.2-t a kérés részeként
  * A nyelvtől és a platformtól függően a TLS megadása implicit vagy explicit módon történik

A .NET-felhasználók számára vegye figyelembe a <a href="https://docs.microsoft.com/dotnet/framework/network-programming/tls" target="_blank">Transport Layer Security ajánlott eljárásokat. <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>

## <a name="authentication"></a>Hitelesítés

A hitelesítés megvitatása során számos gyakori tévhit van. A hitelesítés és az engedélyezés gyakran összekeveredik egymással. Az identitás a biztonság egyik fő összetevője is. A személyazonosság egy <a href="https://en.wikipedia.org/wiki/Principal_(computer_security)" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"> </span>megbízóra </a>vonatkozó információk gyűjteménye. Az identitásszolgáltatók (IdP) identitásokat biztosítanak a hitelesítési szolgáltatásokszámára. A hitelesítés a felhasználó identitásának ellenőrzése. Az engedélyezés az erőforrásokhoz való hozzáférési jogok és jogosultságok megadása egy adott identitáshoz. A Cognitive Services számos ajánlata tartalmazza a szerepköralapú hozzáférés-vezérlést (RBAC). RBAC lehetne használni, hogy egyszerűsítse néhány szertartás részt manuálisan irányító megbízók. További részletek: [Szerepköralapú hozzáférés-vezérlés az Azure-erőforrásokhoz.](../role-based-access-control/overview.md)

Az előfizetési kulcsokkal, hozzáférési jogkivonatokkal és az Azure Active Directoryval (AAD) való hitelesítésről további információt az <a href="https://docs.microsoft.com/azure/cognitive-services/authentication" target="_blank">Azure Cognitive Services hitelesítési kérelmei<span class="docon docon-navigate-external x-hidden-focus"></span></a>című témakörben talál.

## <a name="environment-variables-and-application-configuration"></a>Környezeti változók és alkalmazáskonfiguráció

A környezeti változók név-érték párok, amelyek egy adott környezetben tárolódnak. A bizalmas adatok hoz a kódolatlan értékek biztonságosabb alternatívája a környezeti változók használata. A kódolt értékek nem biztonságosak, ezért el kell kerülni őket.

> [!CAUTION]
> **Ne** használjon kódolt értékeket a bizalmas adatokhoz, ez egy jelentős biztonsági rés.

> [!NOTE]
> Míg a környezeti változók egyszerű szövegben vannak tárolva, egy környezetbe vannak szigetelve. Ha egy környezet sérül, akkor a változók is a környezettel.

### <a name="set-environment-variable"></a>Környezeti változó beállítása

Környezeti változók beállításához használja a következő `ENVIRONMENT_VARIABLE_KEY` parancsokat – `value` ahol az elnevezett kulcs, és a környezeti változóban tárolt érték.

# <a name="command-line"></a>[Parancssori](#tab/command-line)

Az érték kelése és hozzárendelése a megőrzött környezeti változóhoz.

```CMD
:: Assigns the env var to the value
setx ENVIRONMENT_VARIABLE_KEY="value"
```

A **parancssor**új példányában olvassa el a környezeti változót.

```CMD
:: Prints the env var value
echo %ENVIRONMENT_VARIABLE_KEY%
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Az érték kelése és hozzárendelése a megőrzött környezeti változóhoz.

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

Az érték kelése és hozzárendelése a megőrzött környezeti változóhoz.

```Bash
# Assigns the env var to the value
echo export ENVIRONMENT_VARIABLE_KEY="value" >> /etc/environment && source /etc/environment
```

A **Bash**új példányában olvassa el a környezeti változót.

```Bash
# Prints the env var value
echo "${ENVIRONMENT_VARIABLE_KEY}"

# Or use printenv:
# printenv ENVIRONMENT_VARIABLE_KEY
```

---

> [!TIP]
> Környezeti változó beállítása után indítsa újra az integrált fejlesztői környezetet (IDE) annak érdekében, hogy az újonnan hozzáadott környezeti változók elérhetők legyenek.

### <a name="get-environment-variable"></a>Környezeti változó beszereznie

Egy környezeti változó beolvasásához be kell olvasni a memóriába. A használt nyelvtől függően vegye figyelembe az alábbi kódrészleteket. Ezek a kódrészletek bemutatják, hogyan `ENVIRONMENT_VARIABLE_KEY` juthat el a `value`környezeti változóhoz a és rendelni egy .

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

# <a name="objective-c"></a>[Célkitűzés-C](#tab/objective-c)

További információ: <a href="https://developer.apple.com/documentation/foundation/nsprocessinfo/1417911-environment?language=objc" target="_blank"> `environment` <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>.

```objectivec
// Get the named env var, and assign it to the value variable
NSString* value =
    [[[NSProcessInfo processInfo]environment]objectForKey:@"ENVIRONMENT_VARIABLE_KEY"];
```

---

## <a name="next-steps"></a>További lépések

* Fedezze fel a különböző [kognitív szolgáltatásokat](welcome.md)
* További információ a [Cognitive Services virtuális hálózatokról](cognitive-services-virtual-networks.md)

---
ms.openlocfilehash: 1dd9e377a88d8b35cc5ad43f3e8dd1c48dc35d7c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78201947"
---
::: zone pivot="programming-language-csharp"  
## <a name="register-binding-extensions"></a>Kötési bővítmények regisztrálása

A HTTP és az időzítő eseményindítók kivételével a kötések bővítménycsomagokként vannak megvalósítva. Futtassa a következő [dotnet add package](/dotnet/core/tools/dotnet-add-package) parancsot a Terminál ablakban a Storage bővítménycsomag projekthez való hozzáadásához.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

Most hozzáadhatja a tárolási kimenetkötést a projekthez.  
::: zone-end  
---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/05/2019
ms.author: glenga
ms.openlocfilehash: c90587ebd7b0a608e41f19ab600f82dc484bad0a
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949949"
---
## <a name="create-the-local-function-app-project"></a>A helyi függvényalkalmazás-projekt létrehozása

Futtassa a következő parancsot a parancssorból, hogy létrehozzon az aktuális helyi könyvtár `MyFunctionProj` mappájába egy függvényalkalmazás-projektet. A `MyFunctionProj` mappában egy GitHub-adattár is létrejön.

```command
func init MyFunctionProj
```

Amikor a rendszer kéri, válasszon egy feldolgozói futtatókörnyezetet a következő nyelvi lehetőségek közül:

+ `dotnet`: létrehoz egy .NET osztálytárprojektet (.csproj).
+ `node`: egy Node. js-alapú projektet hoz létre. Válassza a `typescript`vagyalehetőséget. `javascript` 
+ `python`: egy Python-projekthez Ehelyett fejezze be a [http által aktivált függvények létrehozását az Azure-ban](../articles/azure-functions/functions-create-first-function-python.md).
+ `powershell`: egy PowerShell-projekthez Ehelyett az [első PowerShell-függvény létrehozása az Azure-ban](../articles/azure-functions/functions-create-first-function-powershell.md). 


A projekt létrehozása után az alábbi parancs használatával navigáljon az új `MyFunctionProj` projekt mappájához.

```command
cd MyFunctionProj
```

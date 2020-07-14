---
title: Azure Functions kötési bővítmények manuális telepítése vagy frissítése
description: Megtudhatja, hogyan telepíthet vagy frissíthet Azure Functions kötési bővítményeket a telepített functions-alkalmazásokhoz.
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: f0705e62adc4acb26797b937a6dd8c684a598ebc
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252623"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Azure Functions kötési bővítmények manuális telepítése vagy frissítése a portálról

A 2. x verziótól kezdődően a Azure Functions futtatókörnyezet kötési kiterjesztéseket használ az eseményindítók és kötések kódjának megvalósításához. A kötési bővítmények a NuGet-csomagokban vannak megadva. A bővítmények regisztrálásához lényegében telepítenie kell egy csomagot. A függvények fejlesztésekor a kötési bővítmények telepítésének módja a fejlesztési környezettől függ. További információ: [kötési bővítmények regisztrálása](./functions-bindings-register.md) az eseményindítók és a kötések cikkben.

Időnként manuálisan kell telepítenie vagy frissítenie a kötési bővítményeket a Azure Portal. Előfordulhat például, hogy frissítenie kell egy regisztrált kötést egy újabb verzióra. Előfordulhat, hogy regisztrálnia kell egy olyan támogatott kötést is, amely nem telepíthető a portál **integrálás** lapján.

## <a name="install-a-binding-extension"></a>Kötési bővítmény telepítése

A bővítmények a portálról történő manuális telepítéséhez és frissítéséhez kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)keresse meg a Function alkalmazást, és válassza ki. Válassza az **Áttekintés** lapot, és válassza a **Leállítás**lehetőséget.  A Function alkalmazás leállítása a fájlok zárolásának feloldása, hogy a módosítások elvégezhető legyen.

1. Válassza a **platform szolgáltatásai** lapot, és a **fejlesztői eszközök** területen válassza a **speciális eszközök (kudu)** lehetőséget. A kudu-végpont ( `https://<APP_NAME>.scm.azurewebsites.net/` ) új ablakban van megnyitva.

1. A kudu ablakban válassza a **Debug konzol**  >  **cmd**elemet.  

1. A parancssorablakban navigáljon a `D:\home\site\wwwroot` következőre, és válassza a törlés ikont a `bin` mappa törléséhez. A törlés megerősítéséhez kattintson **az OK gombra** .

1. Válassza a fájl melletti Szerkesztés ikont `extensions.csproj` , amely meghatározza a Function alkalmazás kötési bővítményeit. A projektfájl az online szerkesztőben nyílik meg.

1. Végezze el a **PackageReference** elemek szükséges kiegészítéseit és frissítéseit a **ItemGroup**, majd válassza a **Mentés**lehetőséget. A támogatott csomag-verziók aktuális listája megtalálható a [Mi szükséges csomagok?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki-cikkben. Mindhárom Azure Storage-kötéshez a Microsoft. Azure. webjobs. Extensions. Storage csomag szükséges.

1. A `wwwroot` mappából futtassa a következő parancsot a hivatkozott szerelvények újraépítéséhez a `bin` mappában.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. A portál **Áttekintés** lapján kattintson a **Start** gombra a Function alkalmazás újraindításához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure functions-eseményindítók és-kötésekről](functions-triggers-bindings.md)

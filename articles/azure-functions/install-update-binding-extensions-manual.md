---
title: Az Azure Functions kötésbővítményeimanuális telepítése vagy frissítése
description: Ismerje meg, hogyan telepítheti vagy frissítheti az Azure Functions kötésbővítményeket az üzembe helyezett függvényalkalmazásokhoz.
ms.topic: reference
ms.date: 09/26/2018
ms.openlocfilehash: e8716f691a5d19ddac7fece47c423e1f7787b9db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75768860"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Az Azure Functions kötésbővítményeimanuális telepítése vagy frissítése a portálról

A 2.x-es verziótól kezdve az Azure Functions futásidejű kötési bővítményeket használ az eseményindítók és kötések kódjának megvalósításához. A kötési bővítmények a NuGet csomagokban találhatók. A bővítmény regisztrálásához lényegében telepítenie kell egy csomagot. A függvények fejlesztése során a kötésbővítmények telepítésének módja a fejlesztői környezettől függ. További információ: [Kötésbővítmények regisztrálása](./functions-bindings-register.md) az eseményindítók és kötések cikkben.

Néha manuálisan kell telepítenie vagy frissítenie a kötési bővítményeket az Azure Portalon. Előfordulhat például, hogy frissítenie kell egy regisztrált kötést egy újabb verzióra. Előfordulhat, hogy olyan támogatott kötést is regisztrálnia kell, amely nem telepíthető a portál **Integrálás** lapjára.

## <a name="install-a-binding-extension"></a>Kötésbővítmény telepítése

A következő lépésekkel manuálisan telepítheti vagy frissítheti a bővítményeket a portálról.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a függvényalkalmazást, és válassza ki. Válassza az **Áttekintés** lapot, majd a **Leállítás**lehetőséget.  A függvényalkalmazás leállítása feloldja a fájlok zárolását, így a módosítások elláthatóak.

1. Válassza a **Platform funkciói** lapot, és a **Fejlesztői eszközök csoportban** válassza **a Speciális eszközök (Kudu) lehetőséget.** A Kudu végpont`https://<APP_NAME>.scm.azurewebsites.net/`( ) új ablakban nyílik meg.

1. A Kudu ablakban válassza a **Debug konzol** > **CMD**lehetőséget.  

1. A parancsablakban keresse `D:\home\site\wwwroot` meg a mappa `bin` törlése ikonját, és válassza ki a törlés ikont. A törlés megerősítéséhez válassza az **OK gombot.**

1. Válassza a `extensions.csproj` fájl melletti szerkesztési ikont, amely meghatározza a függvényalkalmazás kötésbővítményeit. A projektfájl megnyílik az online szerkesztőben.

1. Az **ItemGroup alkalmazásban**adja meg a **PackageReference** elemek szükséges hozzáadását és frissítését, majd válassza a **Mentés**lehetőséget. A támogatott csomagverziók aktuális listája a [Milyen csomagokra van szükségem?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wiki cikkben található. Mindhárom Azure Storage-kötéshez szükség van a Microsoft.Azure.WebJobs.Extensions.Storage csomagra.

1. A `wwwroot` mappából futtassa a következő parancsot a `bin` mappában lévő hivatkozott szerelvények újraépítéséhez.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. A portál **Áttekintés lapján** válassza az **Indítás** lehetőséget a függvényalkalmazás újraindításához.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ az Azure-függvények aktiválásáról és kötéseiről](functions-triggers-bindings.md)

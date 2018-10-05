---
title: Manuálisan telepítse, vagy az Azure Functions kötés bővítmények frissítése
description: Ismerje meg, hogyan kell telepíteni vagy frissíteni az Azure Functions kötési bővítményeket telepített alkalmazások esetében.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, kötési bővítményeket, NuGet, frissítések
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 09/26/2018
ms.author: glenga
ms.openlocfilehash: 77b863bc32442261e220b5dd3f11c0bd33b4fa7a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809718"
---
# <a name="manually-install-or-update-azure-functions-binding-extensions-from-the-portal"></a>Manuálisan telepítse vagy frissítse az Azure Functions kötési bővítményeket a portálról

Az Azure Functions verzió 2.x verziójú futtatókörnyezet kötési bővítményeket használ, az eseményindítók és kötések kód megvalósítását. Kötési bővítményeket NuGet-csomagok vannak megadva. Bővítmények regisztrálásához, lényegében telepít egy csomagot. Függvények fejlesztése, ha a kötési bővítményeket telepíteni módja függ a fejlesztési környezet. További információkért lásd: [regisztrálja a kötési bővítményeket](functions-triggers-bindings.md#register-binding-extensions) az eseményindítók és kötések cikk.

Egyes esetekben kell manuálisan telepíteni vagy frissíteni a kötési bővítményeket az Azure Portalon. Például szükség lehet egy regisztrált kötés frissítése újabb verzióra. Szükség lehet regisztrálni egy támogatott kötést, amely nem telepíthető a **integráció** lapot a portálon.

## <a name="install-a-binding-extension"></a>A kötési bővítmény telepítése

A következő lépések segítségével manuálisan telepíteni, vagy a portálról bővítmények frissítése.

1. Az a [az Azure portal](https://portal.azure.com), keresse meg a függvényalkalmazást, és jelölje ki. Válassza ki a **áttekintése** lapot, és válasszon **leállítása**.  A függvényalkalmazás leállítása feloldja a fájlokat, hogy a módosítás.

1. Válassza ki a **platformfunkciók** lapon és a **Fejlesztőeszközök** kiválasztása **speciális eszközök (Kudu)**. A Kudu-végpont (`https://<APP_NAME>.scm.azurewebsites.net/`) egy új ablakban nyílik meg.

1. Jelölje ki a Kudu ablakban **hibakereső konzol** > **CMD**.  

1. A parancsablakban lépjen `D:\home\site\wwwroot` , és kattintson a Tovább gombra a Törlés ikonra `bin` törli a mappát. Válassza ki **OK** a törlés megerősítéséhez.

1. Kattintson a Tovább gombra a Szerkesztés ikonra a `extensions.csproj` fájlt, amely meghatározza a függvényalkalmazás a kötési bővítményeket. A projekt fájl megnyitása a online szerkesztőjében.

1. Győződjön meg arról, a szükséges hozzáadások és frissítéseinek **PackageReference** elemeket a **ItemGroup**, majd **mentése**. A jelenlegi támogatott alkalmazáscsomag-verziók listája megtalálható a [milyen csomagok van szükségem?](https://github.com/Azure/azure-functions-host/wiki/Updating-your-function-app-extensions#what-nuget-packages-do-i-need) wikicikket. Az összes három Azure Storage-kötések a Microsoft.Azure.WebJobs.Extensions.Storage csomag szükséges.

1. Az a `wwwroot` mappában futtassa a következő parancsot a hivatkozott szerelvények építse újra a `bin` mappát.

    ```cmd
    dotnet build extensions.csproj -o bin --no-incremental --packages D:\home\.nuget
    ```

1. Térjen vissza a **áttekintése** lapra a portálon, majd **Start** , indítsa újra a függvényalkalmazást.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudjon meg többet az Azure functions eseményindítók és kötések](functions-triggers-bindings.md)

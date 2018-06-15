---
title: Ismerkedés a kulcsot tároló kapcsolódó szolgáltatás a Visual Studio (ASP.NET projektek) |} Microsoft Docs
description: Ez az oktatóanyag segítségével megtudhatja, hogyan Key Vault támogatásához egy ASP.NET- vagy ASP.NET Core webalkalmazáshoz.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: cd305801f10c899682aa6d751e48f30b6e8303fa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787401"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Ismerkedés a kulcsot tároló kapcsolódó szolgáltatás a Visual Studio (ASP.NET projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-get-started.md)
> - [mi történt](vs-key-vault-aspnet-what-happened.md)

Ez a cikk útmutatást további Key Vault egy ASP.NET MVC projekt keresztül történő hozzáadása után a **kapcsolódó szolgáltatások hozzáadása** a Visual Studio parancsot. Ha a szolgáltatás már nem felvételét a projekthez, akkor ehhez bármikor utasításait követve [Key Vault hozzáadása a Visual Studio kapcsolódó szolgáltatások használatával webes alkalmazásba való](vs-key-vault-add-connected-service.md).

Lásd: [Mi történt az ASP.NET-projekt?](vs-key-vault-aspnet-core-what-happened.md) amikor a csatlakoztatott szolgáltatás hozzáadása a projekthez végzett módosítások.

## <a name="after-you-connect"></a>Kapcsolódás után

1. Adja hozzá a titkos kulcsot a kulcstároló, az Azure-ban. Ahhoz, hogy a megfelelő helyen, a portálon, kattintson a hivatkozásra a **a Key Vault tárolt titkos kulcsok kezelése**. Ha korábban bezárta a lap, vagy a projekt, lépjen vele a [Azure-portálon](https://portal.azure.com) kiválasztásával **minden szolgáltatás**a **biztonsági**, válassza a **Key Vault**, majd válassza ki az imént létrehozott kulcstároló.

   ![Navigáljon a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozása vagy importálása**.

   ![Titkos kulcs létrehozása és importálása](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adja meg a titkos kulcs, például **MySecret**, és adjon neki egy tesztet, bármilyen karakterlánc típusú értéket, majd kattintson a **létrehozása** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (választható) Adjon meg egy másik titkos, de most tegye közzé egy kategóriát az elnevezési **titkok--MySecret**. Ez a szintaxis adja meg egy kategóriát **titkok** , amely tartalmazza a titkos kulcs **MySecret**.

1. Az alábbiak szerint módosítsa a Web.config fájlban. A kulcsokban helyőrzők, amely a Key Vault titkos kulcsainak értékeivel AzureKeyVault ConfigurationBuilder váltja fel.

   ```xml
     <appSettings configBuilders="AzureKeyVault">
       <add key="webpages:Version" value="3.0.0.0" />
       <add key="webpages:Enabled" value="false" />
       <add key="ClientValidationEnabled" value="true" />
       <add key="UnobtrusiveJavaScriptEnabled" value="true" />
       <add key="MySecret" value="dummy1"/>
       <add key="Secrets--MySecret" value="dummy2"/>
     </appSettings>
   ```

1. A HomeController a jogi tudnivalók megjelenítése Névjegy vezérlő metódusban adja hozzá a következő sorokat beolvasni a titkos kulcsot, és tárolja a ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. A About.cshtml nézetben adja hozzá a következő, a titkos kulcsát (csak tesztelés) értékének megjelenítésére.

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulálunk, mostantól engedélyezve van a webalkalmazás biztonságosan tárolt titkos kulcsok eléréséhez használható a Key Vault.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje a csoportot. Ez a Key Vault és a kapcsolódó erőforrások törlése. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

# <a name="next-steps"></a>További lépések

További tudnivalók az a Key Vault fejlesztése a [Key Vault fejlesztői útmutatója](key-vault-developers-guide.md)
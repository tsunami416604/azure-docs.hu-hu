---
title: Ismerkedés a Key Vault csatlakoztatott szolgáltatás Visual studióban (ASP.NET-projektek) |} A Microsoft Docs
description: Ez az oktatóanyag segítségével elsajátíthatja a Key Vault támogatásának hozzáadása egy ASP.NET- vagy ASP.NET Core-webalkalmazás.
services: key-vault
author: ghogen
manager: douge
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 3ca62d47d8e7682c80985bf5409b8540382fbf45
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056387"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-projects"></a>Ismerkedés a Key Vault csatlakoztatott szolgáltatás Visual studióban (ASP.NET-projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-get-started.md)
> - [mi történt](vs-key-vault-aspnet-what-happened.md)

Ez a cikk további útmutatást nyújt a Key Vault egy ASP.NET MVC-projektben keresztül történő hozzáadása után a **csatlakoztatott szolgáltatás hozzáadása** parancsot a Visual Studióban. Ha még nem hozzáadott a szolgáltatás a projekthez, megteheti bármikor utasításait követve [hozzáadása a Key Vault szeretne az alkalmazáshoz a Visual Studio csatlakoztatott szolgáltatásain](vs-key-vault-add-connected-service.md).

Lásd: [Mi történt az ASP.NET-projektemmel?](vs-key-vault-aspnet-core-what-happened.md) módosítások a projekthez a csatlakoztatott szolgáltatás hozzáadása során.

## <a name="after-you-connect"></a>Kapcsolódás után

1. Titkos kód hozzáadása a Key vaultban az Azure-ban. A portálon jó helyen lekéréséhez kattintson a hivatkozásra a **kezelése a Key vaultban tárolt titkos kulcsok**. Ha már bezárta a lap, vagy a projekthez, navigálhat a a [az Azure portal](https://portal.azure.com) kiválasztásával **minden szolgáltatás**alatt **biztonsági**, válassza a **Key Vault**, majd válassza ki az újonnan létrehozott Key Vault.

   ![Navigálás a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozás/importálás**.

   ![Titkos kulcs létrehozás/importálás](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adja meg a titkos kulcs, például **MySecret**, adjon meg teszteléshez használhat bármilyen karakterlánc típusú értéket, majd válassza a **létrehozás** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (nem kötelező) Adjon meg egy másik titkos, de ezúttal helyezzük el egy kategória szerint elnevezése **titkos kulcsok – MySecret**. Ez a szintaxis megadja egy kategóriát **titkok** , amely tartalmazza a titkos kulcs **MySecret**.

1. Módosítsa a következőképpen web.config. A kulcsokat a helyőrzők, amely a Key Vault titkos értékeket az azurekeyvault értékre van ConfigurationBuilder lesz lecserélve.

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

1. A HomeController a névjegy vezérlő metódusban adja hozzá a következő sorokat beolvasni a titkos kulcsot, és tárolja a ViewBag.
 
   ```csharp
            var secret = ConfigurationManager.AppSettings["MySecret"];
            var secret2 = ConfigurationManager.AppSettings["Secrets--MySecret"];
            ViewBag.Secret = $"Secret: {secret}";
            ViewBag.Secret2 = $"Secret2: {secret2}";
   ```

1. A About.cshtml nézetben adja hozzá a következőt jeleníti meg a titkos kulcsot (csak tesztelés) értékét.

   ```csharp
      <h3>@ViewBag.Secret</h3>
      <h3>@ViewBag.Secret2</h3>
   ```

Gratulálunk, mostantól engedélyezve van a webalkalmazás a Key Vault használatával biztonságosan tárolt titkos kulcsok eléréséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot. Ezzel törli a kulcstartót és a kapcsolódó erőforrásokat. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

# <a name="next-steps"></a>További lépések

A Key Vault fejlesztéséről az [Key Vault fejlesztői útmutató](key-vault-developers-guide.md)
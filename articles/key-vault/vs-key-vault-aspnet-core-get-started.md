---
title: Ismerkedés a Key Vault csatlakoztatott szolgáltatás a Visual Studio (az ASP.NET Core projektek) |} Microsoft Docs
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
ms.openlocfilehash: 64d1a404eac955f47308f01edd56b3d008e250a0
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787513"
---
# <a name="get-started-with-key-vault-connected-service-in-visual-studio-aspnet-core-projects"></a>Ismerkedés a Key Vault csatlakoztatott szolgáltatás a Visual Studio (az ASP.NET Core projektek)

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-core-get-started.md)
> - [mi történt](vs-key-vault-aspnet-core-what-happened.md)

Ez a cikk ismerteti a további útmutatókhoz, az ASP.NET Core projekthez Key Vault hozzáadása után a **kapcsolódó szolgáltatások hozzáadása** a Visual Studio parancsot. Ha a szolgáltatás már nem felvételét a projekthez, akkor ehhez bármikor utasításait követve [Key Vault hozzáadása a Visual Studio kapcsolódó szolgáltatások használatával webes alkalmazásba való](vs-key-vault-add-connected-service.md).

Lásd: [Mi történt az ASP.NET Core projekt?](vs-key-vault-aspnet-core-what-happened.md) amikor a csatlakoztatott szolgáltatás hozzáadása a projekthez végzett módosítások.

## <a name="after-you-connect"></a>Kapcsolódás után

1. Adja hozzá a titkos kulcsot a kulcstároló, az Azure-ban. Ahhoz, hogy a megfelelő helyen, a portálon, a felügyelete titkos kulcsok a Key Vault tárolt hivatkozásra kattintania. Ha korábban bezárta a lap, vagy a projekt, lépjen vele a [Azure-portálon](https://portal.azure.com) kiválasztásával **minden szolgáltatás**a **biztonsági**, válassza a **Key Vault**, majd válassza ki az imént létrehozott kulcstároló.

   ![Navigáljon a portálon](media/vs-key-vault-add-connected-service/manage-secrets-link.jpg)

1. A kulcs a Key Vault szakaszban létrehozott tároló, válassza a **titkok**, majd **létrehozása vagy importálása**.

   ![Titkos kulcs létrehozása és importálása](media/vs-key-vault-add-connected-service/generate-secrets.jpg)

1. Adjon meg egy titkos kulcsot, például a "MySecret", és adjon neki egy tesztet, bármilyen karakterlánc típusú értéket, majd kattintson a **létrehozása** gombra.

   ![Titkos kulcs létrehozása](media/vs-key-vault-add-connected-service/create-a-secret.jpg)
 
1. (választható) Adjon meg egy másik titkos, de most tegye közzé egy kategóriát az elnevezési **titkok--MySecret**. Ez a szintaxis adja meg egy kategóriát **titkok** , amely tartalmazza a titkos kulcs **MySecret.**
1. A Visual Studio projekt hivatkozhat ezeknek a kulcsoknak a kódot az alábbi kifejezések használatával:
 
   ```csharp
      config["MySecret"] // Access a secret without a section
      config["Secrets:MySecret"] // Access a secret in a section
      config.GetSection("Secrets")["MySecret"] // Get the configuration section and access a secret in it.
   ```

Gratulálunk, mostantól engedélyezve van a webalkalmazás biztonságosan tárolt titkos kulcsok eléréséhez használható a Key Vault.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, törölje a csoportot. Ez a Key Vault és a kapcsolódó erőforrások törlése. Az erőforráscsoport törlése a Portalon keresztül:

1. Írja be az erőforráscsoport nevét a Portal tetején található keresőmezőbe. Amikor az eredmények listájában megjelenik az ebben a rövid útmutatóban használt erőforráscsoport, jelölje ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe írja be az erőforráscsoport nevét, és válassza a **Törlés** lehetőséget.

# <a name="next-steps"></a>További lépések

További tudnivalók az a Key Vault fejlesztése a [Key Vault fejlesztői útmutatója](key-vault-developers-guide.md)
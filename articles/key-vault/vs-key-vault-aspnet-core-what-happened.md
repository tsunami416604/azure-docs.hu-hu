---
title: Az ASP.NET Core módosításainak projektre, ha az Azure Key Vaultba Connect |} Microsoft Docs
description: Ismerteti, mi történik, az ASP.NET Core projektjéhez toKey tároló csatlakozik a Visual Studio szolgáltatással történő kapcsolódás esetén.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: 8b6c590344db2997c1a987da14cabba76cdca83b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787373"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Mi történt az ASP.NET Core project (a Visual Studio Key Vault szolgáltatás csatlakozik)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-core-get-started.md)
> - [mi történt](vs-key-vault-aspnet-core-what-happened.md)

Ez a cikk azonosítja a pontos módosításai az ASP.NET-projekt hozzáadásakor a [Key Vault szolgáltatás Visual Studio használatával csatlakoztatott](vs-key-vault-add-connected-service.md).

A csatlakoztatott szolgáltatás munkavégzés további információkért lásd: [bevezetés](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>A hozzáadott hivatkozások

A projekt *.NET fájlhivatkozások és a NuGet csomag hivatkozások érinti.

| Típus | Leírások |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>A hozzáadott fájlok

- ConnectedService.json hozzá, amelyek bizonyos kapcsolatos adatokat rögzíti a csatlakoztatott szolgáltató, a verziót és a hivatkozás a dokumentációs.

## <a name="project-file-changes"></a>Projekt fájlváltozások

- A kapcsolódó szolgáltatások ItemGroup és ConnectedServices.json fájl hozzáadása.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON módosítások

- Az IIS Express profil és a profilt, amely megfelel a webes projekt nevét hozzáadja az alábbi környezeti változó bejegyzéseket:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Változások az Azure-on

- Erőforráscsoport létrehozása (vagy egy meglévő használja).
- A kulcstároló létrehozása a megadott erőforráscsoportban.


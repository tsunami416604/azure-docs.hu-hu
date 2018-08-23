---
title: ASP.NET Core végrehajtott módosítások projektre, ha az Azure Key Vaultba Connect |} A Microsoft Docs
description: Leírja, mi történik, az ASP.NET Core-projekthez toKey Vault használatával a Visual Studio kapcsolódó szolgáltatásaival való csatlakozáskor.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: b7cbe55fa3a524965e0ebc16c5ff350a60d6e440
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42060839"
---
# <a name="what-happened-to-my-aspnet-core-project-visual-studio-key-vault-connected-service"></a>Mi történt az ASP.NET Core-projektemmel (a Visual Studio a Key Vault csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-core-get-started.md)
> - [mi történt](vs-key-vault-aspnet-core-what-happened.md)

Ez a cikk azonosítja a pontos módosításait ASP.NET-projekt hozzáadásakor a [Key Vault csatlakoztatott szolgáltatás Visual studióval](vs-key-vault-add-connected-service.md).

A csatlakoztatott szolgáltatás való használatáról információkért lásd: [bevezetés](vs-key-vault-aspnet-core-get-started.md).

## <a name="added-references"></a>A hozzáadott referenciák

A projekt *.NET fájlhivatkozásokat és alkalmazáscsomag-hivatkozások NuGet érinti.

| Típus | Leírások |
| --- | --- |
| NuGet | Microsoft.AspNetCore.AzureKeyVault.HostingStartup |

## <a name="added-files"></a>Hozzáadott fájlok

- ConnectedService.json hozzáadva, amely rögzíti a csatlakoztatott szolgáltatás szolgáltató, a verzió és a egy hivatkozás a dokumentációs némi információt.

## <a name="project-file-changes"></a>Projekt fájlmódosítás

- A csatlakoztatott szolgáltatások ItemGroup és ConnectedServices.json fájl megtörtént.

## <a name="launchsettingsjson-changes"></a>launchsettings.JSON módosítások

- Adja hozzá a következő környezeti változó bejegyzéseket az IIS Express profil, mind a profilt, amely megegyezik a webes projekt neve:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy erőforráscsoportot (vagy egy meglévő használja).
- A megadott erőforráscsoportban létrehozott egy kulcstartót.


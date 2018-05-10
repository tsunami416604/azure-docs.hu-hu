---
title: Az ASP.NET módosításainak projektre, ha az Azure Key Vaultba Connect |} Microsoft Docs
description: Ismerteti, mi történik az ASP.NET projektben, amikor csatlakozik a Visual Studio szolgáltatások segítségével toKey tároló csatlakozik.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a43f893c7ee87ffb02179c06ea5786715547e93a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Mi történt az ASP.NET-projekt (a Visual Studio Key Vault szolgáltatás csatlakozik)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-get-started.md)
> - [mi történt](vs-key-vault-aspnet-what-happened.md)

Ez a cikk azonosítja a pontos módosításai az ASP.NET-projekt hozzáadásakor a [Key Vault szolgáltatás Visual Studio használatával csatlakoztatott](vs-key-vault-add-connected-service.md).

A csatlakoztatott szolgáltatás munkavégzés további információkért lásd: [bevezetés](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>A hozzáadott hivatkozások

A projekt fájlhivatkozások *.NET van hatással és `packages.config` (NuGet-hivatkozás).

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>A hozzáadott fájlok

- ConnectedService.json hozzá, amelyek bizonyos kapcsolatos adatokat rögzíti a csatlakoztatott szolgáltató, verziót és egy a dokumentációra mutató hivatkozással.

## <a name="project-file-changes"></a>Projekt fájlváltozások

- A kapcsolódó szolgáltatások ItemGroup és ConnectedServices.json fájl hozzáadása.
- A .NET-szerelvények ismertetett hivatkozik a [hozzá hivatkozásokat](#added-references) szakasz.

## <a name="webconfig-or-appconfig-changes"></a>Web.config vagy az App.config fájlt módosítások

- A következő konfigurációs bejegyzések felvételekor:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Változások az Azure-on

- Erőforráscsoport létrehozása (vagy egy meglévő használja).
- A kulcstároló létrehozása a megadott erőforráscsoportban.


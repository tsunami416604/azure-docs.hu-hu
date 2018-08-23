---
title: Az ASP.NET végrehajtott módosítások a projekt mikor, való csatlakozásra szolgáló Azure Key Vault |} A Microsoft Docs
description: Leírja, mi történik, az ASP.NET-projektben, toKey Vault használatával a Visual Studio kapcsolódó szolgáltatásaival való csatlakozáskor.
services: key-vault
author: ghogen
manager: douge
tags: azure-resource-manager
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/15/2018
ms.author: ghogen
ms.openlocfilehash: a74e4e10681f6b91e028067d8985408b0745dcd2
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056378"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-key-vault-connected-service"></a>Mi történt az ASP.NET-projektemmel (a Visual Studio a Key Vault csatlakoztatott szolgáltatás)?

> [!div class="op_single_selector"]
> - [Első lépések](vs-key-vault-aspnet-get-started.md)
> - [mi történt](vs-key-vault-aspnet-what-happened.md)

Ez a cikk azonosítja a pontos módosításait ASP.NET-projekt hozzáadásakor a [Key Vault csatlakoztatott szolgáltatás Visual studióval](vs-key-vault-add-connected-service.md).

A csatlakoztatott szolgáltatás való használatáról információkért lásd: [bevezetés](vs-key-vault-aspnet-get-started.md).

## <a name="added-references"></a>A hozzáadott referenciák

A projekt fájlhivatkozásokat *.NET érinti és `packages.config` (NuGet-hivatkozás).

| Típus | Leírások |
| --- | --- |
| .NET; NuGet | Microsoft.Azure.KeyVault |
| .NET; NuGet | Microsoft.Azure.KeyVault.WebKey |
| .NET; NuGet | Microsoft.Rest.ClientRuntime |
| .NET; NuGet | Microsoft.Rest.ClientRuntime.Azure |

## <a name="added-files"></a>Hozzáadott fájlok

- ConnectedService.json ad hozzá, amely rögzíti a csatlakoztatott szolgáltatás szolgáltató, verzióját és a egy hivatkozást a dokumentációban némi információt.

## <a name="project-file-changes"></a>Projekt fájlmódosítás

- A csatlakoztatott szolgáltatások ItemGroup és ConnectedServices.json fájl megtörtént.
- A .NET-szerelvények ismertetett mutató hivatkozásokat a [hozzá hivatkozásokat](#added-references) szakaszban.

## <a name="webconfig-or-appconfig-changes"></a>Web.config vagy az App.config fájlt módosítások

- Adja hozzá a következő konfigurációs bejegyzéseket:

    ```xml
    <appSettings>
       <add key="vaultName" value="<your Key Vault name>" />
       <add key="vaultUri" value="<the URI to your Key Vault in Azure>" />
    </appSettings>
    ```

## <a name="changes-on-azure"></a>Változások az Azure-ban

- Létrehozott egy erőforráscsoportot (vagy egy meglévő használja).
- A megadott erőforráscsoportban létrehozott egy kulcstartót.


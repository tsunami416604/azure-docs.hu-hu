---
title: Egyéni NuGet-hírcsatorna használata
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Egyéni NuGet-hírcsatornát használhat a NuGet-csomagok eléréséhez és használatához az Azure dev space-ben.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, tárolók
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325721"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Egyéni NuGet-hírcsatorna használata az Azure dev spaces-szel

A NuGet feed kényelmes módot kínál a csomagforrások projektbe való felvételére. Az Azure Dev Spaces-nek hozzá kell férnie ehhez a hírcsatornához ahhoz, hogy a függőségek megfelelően legyenek telepítve a Docker-tárolóban.

## <a name="set-up-a-nuget-feed"></a>NuGet-hírcsatorna beállítása

Adjon hozzá [egy csomaghivatkozást](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) `*.csproj` a függőséghez a csomópont alatti `PackageReference` fájlban. Példa:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Hozzon létre egy [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) fájlt `packageSources` a `packageSourceCredentials` projekt mappában, és állítsa be a NuGet feed szakaszait és szakaszait. A `packageSources` szakasz tartalmazza a hírcsatorna URL-címét, amelynek elérhetőnek kell lennie az AKS-fürtből. A `packageSourceCredentials` a hozzáférési adatok a hírcsatorna eléréséhez. Példa:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Frissítse a Docker-fájlokat a `NuGet.Config` fájl lemezképbe másolásához. Példa:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows rendszerben `NuGet.Config` `Nuget.Config`, `nuget.config` és minden érvényes fájlnévként működik. Linux on, `NuGet.Config` csak egy érvényes fájlnevet a fájlhoz. Mivel az Azure Dev Spaces Dockert és `NuGet.Config`Linuxot használ, ezt a fájlt el kell nevezni. Az elnevezést manuálisan vagy `dotnet restore --configfile nuget.config`a futva javíthatja.


Ha Git-et használ, nem kell a NuGet-hírcsatornához szükséges hitelesítő adatokat használnia a verziókövetésben. Adja `NuGet.Config` hozzá `.gitignore` a projekthez, `NuGet.Config` hogy a fájl ne kerüljön be a verziókövetésbe. Az Azure dev spaces szüksége lesz erre a fájlra a tárolólemezkép-létrehozási folyamat során, de alapértelmezés szerint tiszteletben tartja a szinkronizálásban `.gitignore` és `.dockerignore` a szinkronizálás során meghatározott szabályokat. Az alapértelmezett beállítás módosításához és az `NuGet.Config` Azure Dev `azds.yaml` Spaces fájl szinkronizálásának engedélyezéséhez frissítse a fájlt:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Ha nem használja a Gitet, kihagyhatja ezt a lépést.

A Visual Studio-kód vagy a Visual Studio következő futtatásakor `azds up` vagy találatkor `F5` az Azure Dev Spaces szinkronizálja a `NuGet.Config` csomagfüggőségek telepítéséhez használt fájlt.

## <a name="next-steps"></a>További lépések

További információ a [NuGetről és annak működéséről.](https://docs.microsoft.com/nuget/what-is-nuget)
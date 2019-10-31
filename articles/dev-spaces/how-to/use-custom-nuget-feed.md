---
title: Egyéni NuGet-hírcsatorna használata az Azure dev Spaces szolgáltatásban
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Egyéni NuGet-hírcsatorna használata NuGet-csomagok eléréséhez és használatához az Azure fejlesztői tárhelyén.
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
manager: gwallace
ms.openlocfilehash: 019335cd73e8eaf0ada6897f08c88ef2b8bbf631
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162803"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Egyéni NuGet-hírcsatorna használata az Azure fejlesztői tárhelyén

A NuGet-hírcsatornák kényelmes módszert biztosítanak a projektek csomagjainak belefoglalására. Az Azure dev Spaces szolgáltatásnak el kell érnie ezt a hírcsatornát ahhoz, hogy a függőségeket megfelelően lehessen telepíteni a Docker-tárolóba.

## <a name="set-up-a-nuget-feed"></a>NuGet-hírcsatorna beállítása

Vegyen fel egy [csomag-referenciát](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) a függőséghez a `PackageReference` csomópont alatt található `*.csproj` fájlban. Példa:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Hozzon létre egy [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) fájlt a Project mappában, és állítsa be a NuGet-csatornához tartozó `packageSources` és `packageSourceCredentials` szakaszt. Az `packageSources` szakasz tartalmazza a hírcsatorna URL-címét, amelynek elérhetőnek kell lennie az AK-fürtből. A `packageSourceCredentials` a hírcsatorna eléréséhez szükséges hitelesítő adatok. Példa:

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

Frissítse a Dockerfiles, és másolja a `NuGet.Config` fájlt a rendszerképbe. Példa:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows, `NuGet.Config`, `Nuget.Config`és `nuget.config` minden ugyanúgy működik, mint az érvényes fájlnevek. Linux rendszeren csak a fájl `NuGet.Config` érvényes fájlnév. Mivel az Azure dev Spaces a Docker és a Linux szolgáltatást használja, a fájlnak `NuGet.Config`nevűnek kell lennie. Az elnevezést manuálisan vagy a `dotnet restore --configfile nuget.config`futtatásával is kijavíthatja.


Ha a git-t használja, nem kell a NuGet-csatornához tartozó hitelesítő adatokkal rendelkeznie. Adja hozzá a `NuGet.Config`t a projekt `.gitignore`hoz, hogy a `NuGet.Config`-fájl ne legyen hozzáadva a verziókövetés számára. Az Azure dev Spaces-nek szüksége lesz erre a fájlra a tároló képfordítási folyamata során, de alapértelmezés szerint tiszteletben tartja a szinkronizálás során `.gitignore` és `.dockerignore`ban meghatározott szabályokat. Ha módosítani szeretné az alapértelmezett értéket, és engedélyezi az Azure dev Spaces számára a `NuGet.Config` fájl szinkronizálását, frissítse a `azds.yaml` fájlt:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Ha nem a git-t használja, kihagyhatja ezt a lépést.

Amikor legközelebb futtatja `azds up` vagy megüt `F5` a Visual Studio Code-ban vagy a Visual Studióban, az Azure dev Spaces szinkronizálja a `NuGet.Config` fájlt a csomagok függőségeinek telepítéséhez.

## <a name="next-steps"></a>Következő lépések

További információ a [NuGet és működéséről](https://docs.microsoft.com/nuget/what-is-nuget).
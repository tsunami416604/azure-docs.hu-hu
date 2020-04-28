---
title: Egyéni NuGet-hírcsatorna használata
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Egyéni NuGet-hírcsatorna használata NuGet-csomagok eléréséhez és használatához az Azure fejlesztői tárhelyén.
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "74325721"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Egyéni NuGet-hírcsatorna használata az Azure dev Spaces használatával

A NuGet-hírcsatornák kényelmes módszert biztosítanak a projektek csomagjainak belefoglalására. Az Azure dev Spaces szolgáltatásnak el kell érnie ezt a hírcsatornát ahhoz, hogy a függőségeket megfelelően lehessen telepíteni a Docker-tárolóba.

## <a name="set-up-a-nuget-feed"></a>NuGet-hírcsatorna beállítása

Adjon hozzá egy [csomag-referenciát](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) a függőséghez `*.csproj` a `PackageReference` csomópont alatt található fájlban. Például:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Hozzon létre egy [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) fájlt a Project mappában, és `packageSources` állítsa `packageSourceCredentials` be a és a NuGet-hírcsatornához tartozó szakaszt. A `packageSources` szakasz tartalmazza a hírcsatorna URL-címét, amelynek elérhetőnek kell lennie az AK-fürtből. `packageSourceCredentials` A a hírcsatorna eléréséhez szükséges hitelesítő adatok. Például:

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

Frissítse a Dockerfiles, és másolja `NuGet.Config` a fájlt a rendszerképbe. Például:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows, `NuGet.Config`, `Nuget.Config`, és `nuget.config` minden érvényes fájlnevet működik. Linux rendszeren a fájl `NuGet.Config` csak érvényes fájlnév. Mivel az Azure dev Spaces a Docker és a Linux szolgáltatást használja, `NuGet.Config`ezt a fájlt kell megnevezni. Az elnevezést manuálisan vagy a futtatásával `dotnet restore --configfile nuget.config`is kijavíthatja.


Ha a git-t használja, nem kell a NuGet-csatornához tartozó hitelesítő adatokkal rendelkeznie. Adja `NuGet.Config` hozzá a `.gitignore` projektet a projekthez, hogy `NuGet.Config` a fájl ne legyen felvéve a verziókövetésba. Az Azure dev Spaces-nek szüksége lesz erre a fájlra a tároló képfordítási folyamata során, de alapértelmezés szerint `.gitignore` az `.dockerignore` a és a szinkronizálás során definiált szabályokat is figyelembe veszi. Ha módosítani szeretné az alapértelmezett beállítást, és engedélyezi az Azure dev `NuGet.Config` Spaces számára a `azds.yaml` fájl szinkronizálását, frissítse a fájlt:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Ha nem a git-t használja, kihagyhatja ezt a lépést.

Amikor legközelebb a `azds up` Visual Studio `F5` Code-ban vagy a Visual Studióban futtatja, az Azure dev `NuGet.Config` Spaces szinkronizálja a fájlt a csomagok függőségeinek telepítéséhez.

## <a name="next-steps"></a>További lépések

További információ a [NuGet és működéséről](https://docs.microsoft.com/nuget/what-is-nuget).
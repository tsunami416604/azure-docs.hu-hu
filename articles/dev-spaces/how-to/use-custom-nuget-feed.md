---
title: Egyéni NuGet-hírcsatorna használata az Azure dev Spaces szolgáltatásban
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Egyéni NuGet-hírcsatorna használata NuGet-csomagok eléréséhez és használatához az Azure fejlesztői tárhelyén.
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279953"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Egyéni NuGet-hírcsatorna használata az Azure fejlesztői tárhelyén

A NuGet-hírcsatornák kényelmes módszert biztosítanak a projektek csomagjainak belefoglalására. Az Azure dev Spaces szolgáltatásnak el kell érnie ezt a hírcsatornát ahhoz, hogy a függőségeket megfelelően lehessen telepíteni a Docker-tárolóba.

## <a name="set-up-a-nuget-feed"></a>NuGet-hírcsatorna beállítása

Vegyen fel egy [csomag-referenciát](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) a függőséghez a `PackageReference` csomópont alatt található `*.csproj` fájlban. Például:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Hozzon létre egy [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) fájlt a Project mappában, és állítsa be a NuGet-csatornához tartozó `packageSources` és `packageSourceCredentials` szakaszt. Az `packageSources` szakasz tartalmazza a hírcsatorna URL-címét, amelynek elérhetőnek kell lennie az AK-fürtből. A `packageSourceCredentials` a hírcsatorna eléréséhez szükséges hitelesítő adatok. Például:

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

Frissítse a Dockerfiles, és másolja a `NuGet.Config` fájlt a rendszerképbe. Például:

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
---
title: Egy egyéni NuGet használata az Azure fejlesztési tárolóhelyek hírcsatorna |} A Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Egy egyéni NuGet, hogy eléri és használja a NuGet-csomagok egy Azure-fejlesztési területen hírcsatorna használja.
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Container Service, tárolók
manager: ghogen
ms.openlocfilehash: 21a70100fe186e176dfe8eb7c247d83a5d4705bd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55466404"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Egyéni nugettel hírcsatornaelemet egy Azure-fejlesztési terület

A NuGet-hírcsatorna csomagforrások foglalandó projekt kényelmes módot biztosít. Az Azure fejlesztési tárolóhelyek kell érhetik el erre a hírcsatornára ahhoz, hogy a Docker-tárolóban megfelelően telepítve.

## <a name="set-up-a-nuget-feed"></a>Állítsa be a NuGet-hírcsatorna

A hírcsatorna NuGet beállításához:
1. Adjon hozzá egy [referencia csomag](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) a a `*.csproj` fájlt a `PackageReference` csomópont.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Hozzon létre egy [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) fájlt a projektmappa fájllistájának.
     * Használja a `packageSources` való hivatkozáshoz a NuGet-hírcsatorna helye szakaszban. Fontos: A NuGet-hírcsatorna nyilvánosan elérhetőnek kell lennie.
     * Használja a `packageSourceCredentials` szakaszban beállíthatja a felhasználónév és jelszó hitelesítő adatokat. 

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

3. Verziókövetési használata:
    - Hivatkozás `NuGet.Config` a a `.gitignore` -fájlba, a hitelesítő adatok nem véletlenül véglegesítéséhez, a forrás-tárházhoz.
    - Nyissa meg a `azds.yaml` fájlt a projektben, és keresse meg a `build` szakaszt, és annak érdekében, hogy az alábbi kódrészlet beszúrása a `NuGet.Config` fájl lesznek szinkronizálva az Azure-ba, hogy azt használni, a tároló létrehozási folyamat során. (Alapértelmezés szerint az Azure fejlesztési szóközt nem szinkronizálja a fájlokat, amelyek megfelelnek `.gitignore` és `.dockerignore` szabályok.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>További lépések

Miután végzett a fenti lépéseket, amikor legközelebb futtatja `azds up` (vagy nyomja le `F5` VSCode vagy a Visual Studio), Azure fejlesztői tárolóhelyek szinkronizálja a `NuGet.Config` fájlt az Azure-ban, amely majd egyes `dotnet restore` csomag telepítéséhez a tároló függőségeit.


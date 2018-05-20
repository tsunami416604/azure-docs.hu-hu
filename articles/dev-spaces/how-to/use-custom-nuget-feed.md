---
title: Egy egyéni NuGet használata Azure fejlesztői szóközöket a hírcsatorna |} Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: A nugettel egy egyéni adatcsatorna elérése és Azure fejlesztői térben NuGet-csomagok használata.
keywords: Docker, Kubernetes, Azure, AKS, az Azure Tárolószolgáltatás, tárolók
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Egyéni nugettel Azure fejlesztői térben hírcsatorna

A NuGet-hírcsatorna csomag források szerepeljenek a projekt kényelmes megoldást kínál. Azure fejlesztői szóközt kell érhetik el, hogy ez az adatcsatorna ahhoz, hogy a függőségeket a Docker-tároló megfelelően telepítve.

## <a name="set-up-a-nuget-feed"></a>A hírcsatorna NuGet beállítása

A hírcsatorna NuGet beállítása:
1. Adja hozzá a [hivatkozás csomag](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) a a `*.csproj` a fájlt a `PackageReference` csomópont.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Hozzon létre egy [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) fájl projektmappában.
     * Használja a `packageSources` való hivatkozáshoz a NuGet-hírcsatorna helye szakaszban. Fontos: A NuGet-hírcsatorna nyilvánosan hozzáférhetőnek kell lennie.
     * Használja a `packageSourceCredentials` szakaszban található felhasználónév és jelszó érvényes hitelesítő adatokat. 

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
    - Hivatkozás `NuGet.Config` a a `.gitignore` fájl, a forrás tárházhoz véletlenül nem véglegesítése hitelesítő adatokat.
    - Nyissa meg a `azds.yaml` a projekt fájlt, és keresse meg a `build` szakaszt, és helyezze be a következő kódrészletet annak érdekében, hogy a `NuGet.Config` fájl szinkronizálása az Azure-ba, hogy a tároló lemezkép létrehozása során használt. (Alapértelmezés szerint Azure fejlesztői szóközöket ne szinkronizálja azokat a fájlokat, amelyek megfelelnek `.gitignore` és `.dockerignore` szabályok.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>További lépések

Miután végzett a fenti lépéseket, amikor legközelebb futtatja `azds up` (vagy találati `F5` VSCode vagy a Visual Studio), Azure fejlesztői szóközöket szinkronizálja a `NuGet.Config` fájl az Azure-ba, amely majd alkalmazott `dotnet restore` csomag telepítéséhez függőségek a tárolóban.


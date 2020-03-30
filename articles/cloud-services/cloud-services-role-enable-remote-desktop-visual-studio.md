---
title: A Visual Studio használatával engedélyezze a Távoli asztal szerepkörhöz (Azure Cloud Services)
description: Az Azure felhőszolgáltatási alkalmazás konfigurálása távoli asztali kapcsolatok engedélyezésére
services: cloud-services
author: ghogen
manager: jillfra
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: f4622e44c795182ee68c617f335c9e1651d3adcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294389"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Távoli asztali kapcsolat engedélyezése szerepkörhöz az Azure Cloud Services ben a Visual Studio használatával

> [!div class="op_single_selector"]
> * [Azure-portál](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Vizuális stúdió](cloud-services-role-enable-remote-desktop-visual-studio.md)

A Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. A Távoli asztali kapcsolat segítségével elháríthatja és diagnosztizálhatja az alkalmazással kapcsolatos problémákat futás közben.

A Visual Studio által a felhőszolgáltatásokhoz biztosított közzétételi varázsló lehetőséget biztosít a Távoli asztal engedélyezésére a közzétételi folyamat során, az Ön által megadott hitelesítő adatok használatával. Ezzel a beállítással a Visual Studio 2017 15.4-es vagy korábbi verziójának használatakor használható.

A Visual Studio 2017 15.5-ös és újabb verziójának használata esetén azonban ajánlott elkerülni a Távoli asztal engedélyezését a közzétételi varázslón keresztül, hacsak nem csak egyetlen fejlesztőként dolgozik. Minden olyan helyzetben, amelyben a projekt et más fejlesztők is megnyithatják, ehelyett engedélyezze a Távoli asztalt az Azure Portalon keresztül, a PowerShellen keresztül, vagy egy kiadási folyamatból egy folyamatos üzembe helyezési munkafolyamatban. Ez a javaslat annak köszönhető, hogy a Visual Studio hogyan kommunikál a távoli asztallal a felhőalapú szolgáltatás virtuális gépén, ahogy azt ebben a cikkben ismertetjük.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>Távoli asztal konfigurálása a Visual Studio 2017 15.4-es és korábbi verzióján keresztül

A Visual Studio 2017 15.4-es és korábbi verziójának használatakor használhatja a **Távoli asztal engedélyezése az összes szerepkörhöz** beállítást a közzétételi varázslóban. A varázsló továbbra is használható a Visual Studio 2017 15.5-ös és újabb verziójával, de ne használja a Távoli asztal beállítást.

1. A Visual Studio-ban indítsa el a közzétételi varázslót úgy, hogy a jobb gombbal a felhőszolgáltatás-projektre kattint a Megoldáskezelőben, majd a **Közzététel parancsra**kattint.

2. Ha szükséges, jelentkezzen be Azure-előfizetésébe, és válassza a **Tovább**gombot.

3. A **Beállítások** lapon válassza a **Távoli asztal engedélyezése az összes szerepkörhöz**lehetőséget, majd a **Beállítások...** hivatkozással nyissa meg a **Távoli asztal konfigurálása** párbeszédpanelt.

4. A párbeszédpanel alján válassza a **További beállítások lehetőséget.** Ez a parancs egy legördülő listát jelenít meg, amelyben létrehoz vagy kiválaszt egy tanúsítványt, hogy titkosíthassa a hitelesítő adatokat, amikor távoli asztalon keresztül csatlakozik.

   > [!Note]
   > A távoli asztali kapcsolathoz szükséges tanúsítványok eltérnek a más Azure-műveletekhez használt tanúsítványoktól. A távelérési tanúsítványnak titkos kulccsal kell rendelkeznie.

5. Jelöljön ki egy tanúsítványt a listából, vagy válassza ** &lt;a Létrehozás... &gt;**. Ha új tanúsítványt hoz létre, a rendszer rövid nevet ad az új tanúsítványnak, amikor a rendszer kéri, és válassza az **OK gombot.** Az új tanúsítvány megjelenik a legördülő listában.

6. Adja meg a felhasználónevet és a jelszót. Meglévő fiókot nem használhat. Ne használja a "Rendszergazda" nevet az új fiók felhasználóneveként.

7. Válassza ki azt a dátumot, amikor a fiók lejár, és amely után a távoli asztali kapcsolatok le lesznek tiltva.

8. Miután megadta az összes szükséges információt, válassza az **OK gombot.** A Visual Studio hozzáadja a Távoli `.cscfg` `.csdef` asztal beállításait a projekthez és a fájlokhoz, beleértve a kiválasztott tanúsítvánnyal titkosított jelszót is.

9. Végezze el a további lépéseket a **Tovább** gombbal, majd válassza a **Közzététel** lehetőséget, ha készen áll a felhőszolgáltatás közzétételére. Ha még nem áll készen a közzétételre, válassza **a Mégse lehetőséget,** és válaszoljon **az Igen** gombra, amikor a program a módosítások mentését kéri. Ezekkel a beállításokkal később közzéteheti a felhőszolgáltatást.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>A Távoli asztal konfigurálása a Visual Studio 2017 15.5-ös és újabb verziójának használatakor

A Visual Studio 2017 15.5-ös és újabb verziójával továbbra is használhatja a közzétételi varázslót egy felhőszolgáltatási projekttel. A Távoli asztal engedélyezése az összes szerepkörhöz lehetőséget is **használhatja,** ha csak egyetlen fejlesztőként dolgozik.

Ha egy csapat tagjaként dolgozik, ehelyett engedélyeznie kell a távoli asztalt az Azure felhőszolgáltatásában az [Azure Portal vagy](cloud-services-role-enable-remote-desktop-new-portal.md) a [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)használatával.

Ez a javaslat annak köszönhető, hogy a Visual Studio 2017 15.5-ös és újabb verziója megváltozik a felhőszolgáltatás virtuális gépével. Ha engedélyezi a Távoli asztal szolgáltatást a közzétételi varázslón keresztül, a Visual Studio korábbi verziói az úgynevezett "RDP beépülő modulon" keresztül kommunikálnak a virtuális géppel. A Visual Studio 2017 15.5-ös és újabb verziói biztonságosabb és rugalmasabb "RDP-bővítmény" használatával kommunikálnak. Ez a változás is igazodik azzal a ténnyel, hogy az Azure Portal és a PowerShell-metódusok a Távoli asztal engedélyezéséhez is használja az RDP-bővítményt.

Amikor a Visual Studio kommunikál az RDP-kiterjesztéssel, egyszerű szöveges jelszót továbbít a TLS-en keresztül. A projekt konfigurációs fájljai azonban csak egy titkosított jelszót tárolnak, amely csak a titkosításhoz eredetileg használt helyi tanúsítvánnyal fejthető vissza egyszerű szöveggé.

Ha a felhőszolgáltatás-projektet minden alkalommal ugyanarról a fejlesztői számítógépről telepíti, akkor az adott helyi tanúsítvány elérhető. Ebben az esetben továbbra is használhatja a Távoli asztal engedélyezése az **összes szerepkörhöz** beállítást a közzétételi varázslóban.

Ha ön vagy más fejlesztők szeretné telepíteni a felhőszolgáltatás-projekt különböző számítógépekről, akkor ezek a számítógépek nem rendelkeznek a jelszó visszafejtéséhez szükséges tanúsítvánnyal. Ennek eredményeképpen a következő hibaüzenet jelenik meg:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

A jelszót a felhőszolgáltatás minden egyes telepítésekor módosíthatja, de ez a művelet mindenki számára kényelmetlenné válik, akinek a Távoli asztal t kell használnia.

Ha megosztja a projektet egy csapattal, akkor a legjobb, ha törli a lehetőséget a közzétételi varázslóban, és ehelyett közvetlenül az [Azure Portalon](cloud-services-role-enable-remote-desktop-new-portal.md) keresztül vagy a [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)használatával engedélyezi a Távoli asztalt.

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Telepítés buildkiszolgálóról a Visual Studio 2017 15.5-ös és újabb verziójával

Egy felhőalapú szolgáltatásprojektet üzembe helyezhet egy buildkiszolgálóról (például az Azure DevOps Services szolgáltatással), amelyen a Visual Studio 2017 15.5-ös vagy újabb verziója telepítve van a buildügynökben. Ezzel az elrendezéssel a központi telepítés ugyanattól a számítógépről történik, amelyen a titkosítási tanúsítvány elérhető.

Az Azure DevOps-szolgáltatások RDP-bővítményének használatához adja meg a következő részleteket a build-folyamatban:

1. Az `/p:ForceRDPExtensionOverPlugin=true` MSBuild argumentumok, győződjön meg arról, hogy a központi telepítés az RDP-bővítmény helyett az RDP-bővítmény működik. Példa:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. A buildelési lépések után adja hozzá az **Azure Cloud Service Deployment-lépést,** és állítsa be a tulajdonságait.

1. A központi telepítési lépés után adjon hozzá egy **Azure Powershell-lépést,** állítsa be **a Display name** tulajdonságát "Azure Deployment: Enable RDP Extension" (vagy más megfelelő név) értékre, és válassza ki a megfelelő Azure-előfizetést.

1. Állítsa **a Parancsfájltípusát** "Inline" beállításra, és illessze be az alábbi kódot a **Szövegközi parancsfájl** mezőbe. (Ezzel a parancsfájllal `.ps1` is létrehozhat egy fájlt a projektben, beállíthatja a Parancsfájltípus tikulciagépét "Parancsfájl elérési útja" értékre, és beállíthatja, hogy a Parancsfájl elérési **útja** a fájlra mutasson.) **Script Type**

    ```ps
    Param(
        [Parameter(Mandatory=$True)]
        [string]$username,

        [Parameter(Mandatory=$True)]
        [string]$password,

        [Parameter(Mandatory=$True)]
        [string]$serviceName,

        [Datetime]$expiry = ($(Get-Date).AddYears(1))
    )

    Write-Host "Service Name: $serviceName"
    Write-Host "User Name: $username"
    Write-Host "Expiry: $expiry"

    $securepassword = ConvertTo-SecureString -String $password -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential $username,$securepassword

    # Try to remote existing RDP Extensions
    try
    {
        $existingRDPExtension = Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
        if ($existingRDPExtension -ne $null)
        {
            Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
        }
    }
    catch
    {
    }

    Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry -Verbose
    ```

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Csatlakozás Azure-szerepkörhöz a Távoli asztal használatával

Miután közzétette a felhőszolgáltatást az Azure-ban, és engedélyezte a Távoli asztal szolgáltatást, a Visual Studio Server Explorer segítségével bejelentkezhet a felhőszolgáltatás virtuális gépébe:

1. A Kiszolgálókezelőben bontsa ki az **Azure-csomópontot,** majd bontsa ki egy felhőszolgáltatás csomópontját és annak egyik szerepkörét a példányok listájának megjelenítéséhez.

2. Kattintson a jobb gombbal egy példánycsomópontra, és válassza **a Csatlakozás távoli asztal használatával parancsot.**

3. Adja meg a korábban létrehozott felhasználónevet és jelszót. Most már be van jelentkezve a távoli munkamenetbe.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)

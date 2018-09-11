---
title: Egy szerepkörhöz az Azure Cloud Services távoli asztali kapcsolat engedélyezése
description: Az Azure cloud service-alkalmazás lehetővé teszi a távoli asztali kapcsolatok konfigurálása
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.topic: conceptual
ms.workload: azure-vs
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: 703e969fe31def329be60037cceba27864063b4e
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44304054"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services, a Visual Studio használatával

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A távoli asztal segítségével elérheti az Azure-ban futó szerepkörök asztalát. Az alkalmazással kapcsolatos problémák diagnosztizálására futás közben és használhatja a távoli asztali kapcsolatot.

A közzétételi varázsló, amely a Visual Studio biztosít a felhőalapú szolgáltatások beállítással rendelkezik, amely távoli asztal engedélyezése a közzétételi folyamat során megadott hitelesítő adatokat használ. Ezt a lehetőséget akkor megfelelő, ha a Visual Studio 2017 15.4-es és korábbi használatával.

A Visual Studio 2017 15.5-ös és újabb verziók azonban ajánlott, hogy, ne engedélyezze a távoli asztal a publish varázsló lépéseit, kivéve, ha csak egyetlen fejlesztőként dolgozik. Minden olyan helyzet, amelyben a projekt előfordulhat, hogy nyitható meg a többi fejlesztők, a, ehelyett távoli asztal engedélyezése az Azure portal, PowerShell-lel vagy a folyamatos üzembe helyezést megvalósító munkafolyamat a kibocsátási folyamat segítségével. Ez a javaslat hogyan Visual Studio kommunikál a távoli asztal a felhőszolgáltatásbeli virtuális Gépet, az ebben a cikkben leírtak megváltozása miatt van.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>A Visual Studio 2017 15.4-es és korábbi keresztül a távoli asztal konfigurálása

A Visual Studio 2017 15.4-es és korábbi használata esetén használhatja a **távoli asztal engedélyezése az összes szerepkörhöz** lehetőség a publish varázsló. A varázsló továbbra is használhatja a Visual Studio 2017 15.5-ös és újabb verziók, de ne használja a távoli asztal szolgáltatást.

1. A Visual Studio publish varázsló elindításához kattintson a jobb gombbal a felhőszolgáltatási projektet a Megoldáskezelőben, majd válassza a **közzététel**.

2. Jelentkezzen be az Azure-előfizetése, ha szükséges, és válassza ki **tovább**.

3. Az a **beállítások** lapon jelölje be **távoli asztal engedélyezése az összes szerepkörhöz**, majd válassza a **beállítások...**  címre mutató hivatkozást a **a távoli asztal konfigurálásának** párbeszédpanel bezárásához.

4. A párbeszédpanel alján válassza **további beállítások**. Ez a parancs megjeleníti a legördülő listából válassza ki, amelyben hoz létre, vagy válasszon ki tanúsítványt úgy, hogy a hitelesítő adatok használatával titkosítsa, amikor a távoli asztali kapcsolatra.

   > [!Note]
   > A tanúsítványok, szüksége lesz egy távoli asztali kapcsolatot a tanúsítványokat az Azure műveletnél használó eltérnek. A távelérési tanúsítványnak titkos kulccsal kell rendelkeznie.

5. Válasszon ki egy tanúsítványt a listából, vagy válasszon  **&lt;létrehozása... &gt;**. Ha létrehoz egy új tanúsítványt, adjon meg egy rövid nevet az új tanúsítványt, amikor a rendszer kéri, és válassza ki **OK**. Az új tanúsítvány megjelenik a legördülő listában.

6. Adjon meg egy felhasználónevet és jelszót. Nem használhat egy meglévő fiókot. Ne használja a "Rendszergazda" felhasználói nevet az új fiókhoz.

7. Válassza ki a dátum az a fiók lejár, és melyik távoli asztali kapcsolatok le lesznek tiltva után.

8. Után az összes szükséges információt megadott, válassza ki a **OK**. A Visual Studio a távoli asztal beállításait hozzáadja a projekthez `.cscfg` és `.csdef` fájlok, például a jelszó, amelyet a kiválasztott tanúsítvány van titkosítva.

9. Végezze el a hátralévő lépéseket használatával a **tovább** gombra, majd válassza **közzététel** , amikor készen közzététele a felhőalapú szolgáltatás. Ha Ön nem közzétételre kész, válassza ki a **Mégse** és válasz **Igen** amikor a rendszer kéri a módosítások mentéséhez. A felhőszolgáltatások később közzéteheti ezeket a beállításokat.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>A Visual Studio 2017 15.5-ös vagy újabb verzió használata esetén a távoli asztal konfigurálása

A Visual Studio 2017 15.5-ös és újabb verziói a Közzététel varázslót, és egy felhőszolgáltatás-projekt továbbra is használhatja. Is használhatja a **távoli asztal engedélyezése az összes szerepkörhöz** beállítást, ha csak egyetlen fejlesztőként dolgozik.

Dolgozik a csapat tagjaként, ha meg kell helyette távoli asztal engedélyezése a az Azure cloud Services használatával a [az Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) vagy [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Ez a javaslat hogyan kommunikál a Visual Studio 2017 15.5-ös és újabb verziói a felhőszolgáltatás virtuális gép megváltozása miatt van. A távoli asztal engedélyezése a publish varázsló lépéseit, a Visual Studio korábbi verziói kommunikálni a virtuális Gépeket a mi van neve az "RDP beépülő modult." A Visual Studio 2017 15.5-ös és újabb kommunikál, Ehelyett használja az "RDP-bővítmény" Ez a biztonságosabb és rugalmasabb. Ez a változás is igazodik a azt a tényt, hogy az Azure portal és a távoli asztal engedélyezése a PowerShell módszereket is használhatja a az RDP-bővítmény.

Amikor a Visual Studio kommunikál az RDP-bővítmény, SSL-en keresztül továbbítja egy egyszerű szöveges jelszó. Azonban a projekt konfigurációs fájlok tárolása csak titkosított jelszót, amelyek úgy fejthetők vissza egyszerű szöveggé, csak az eredetileg a titkosításhoz használt helyi tanúsítvánnyal.

Ha minden alkalommal, amikor telepíti a felhőszolgáltatás-projekt ugyanazon a fejlesztési számítógépen, majd a helyi tanúsítványtárolóban érhető el. Ebben az esetben továbbra is használhatja a **távoli asztal engedélyezése az összes szerepkörhöz** lehetőség a publish varázsló.

Ha, vagy más fejlesztők számára telepíteni kívánja a felhőszolgáltatás-projekt különböző számítógépekről, azonban akkor más számítógépek nem rendelkezik a jelszó visszafejtéséhez szükséges tanúsítvány. Ennek eredményeképpen a következő hibaüzenet jelenik meg:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

Sikerült módosítani a jelszót, minden alkalommal, amikor a felhőalapú szolgáltatás telepítéséhez, de a művelet kényelmetlenné válik nyújtson mindenkinek, aki kell használnia a távoli asztal.

Ha a projekt a csapattal, majd, érdemes a publish varázsló bejelölését, és helyette a közvetlenül keresztül a távoli asztal engedélyezése a [az Azure portal](cloud-services-role-enable-remote-desktop-new-portal.md) vagy [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>Üzembe helyezése a Visual Studio 2017 15.5-ös és újabb buildelési kiszolgáló

Telepíthet egy kiszolgálóról egy build (például az Azure DevOps-szolgáltatásokkal) melyik Visual Studio 2017 15.5-ös vagy újabb verziója telepítve van a fordító-ügynökhöz a felhőszolgáltatás-projekt. Ezzel az elrendezéssel az üzembe helyezés még ugyanazon a számítógépen, amelyen a titkosítási tanúsítvány áll rendelkezésre.

Az RDP-bővítmény, az Azure DevOps-szolgáltatások használatához a buildelési folyamat a következő részleteket tartalmazza:

1. Például `/p:ForceRDPExtensionOverPlugin=true` az, hogy az üzembe helyezés működik együtt az RDP-beépülő modul helyett inkább az RDP-bővítmény az MSBuild-argumentumok. Példa:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. A létrehozási lépések után adja hozzá a **Azure Felhőszolgáltatás üzembe helyezésének** lépést, valamint a tulajdonságainak beállításával.

1. A központi telepítési lépés után adja hozzá egy **Azure PowerShell-lel** lépést, és állítsa annak **megjelenített név** tulajdonság "Azure üzembe helyezési: engedélyezze az RDP bővítmény" (vagy egy másik megfelelő nevet), és válassza ki a megfelelő Azure az előfizetés.

1. Állítsa be **Szkripttípus** "Beágyazott", és illessze be az alábbi kódot a **beágyazott parancsfájlja** mező. (Is létrehozhat egy `.ps1` ezzel a parancsprogrammal a projekt fájlban **Szkripttípus** "Parancsprogram-fájl elérési útja", és állítsa be a **parancsprogram elérési útja** a fájlra mutasson.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>Csatlakozás egy Azure-szerepkörhöz a távoli asztal használatával

Miután közzététele a felhőalapú szolgáltatás, az Azure-ban, és a távoli asztal engedélyezve van, használhatja a Visual Studio Server Explorerben a felhőszolgáltatás Virtuálisgép-ba való bejelentkezéshez:

1. A Server Explorerben bontsa ki a **Azure** csomópontot, majd bontsa ki a csomópontot a felhő alapú szolgáltatás és -példányok listájának megjelenítéséhez a szerepkörök egyikét.

2. Kattintson a jobb gombbal egy példány csomópont, és válassza ki **csatlakoztatása a távoli asztal**.

3. Adja meg a felhasználónevet és jelszót, amelyet korábban hozott létre. Most már bejelentkezett a távoli munkamenetet.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)

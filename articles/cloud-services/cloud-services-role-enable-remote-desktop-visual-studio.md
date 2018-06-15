---
title: Engedélyezze a távoli asztali kapcsolat egy szerepkör esetén az Azure Cloud Services csomag
description: Az Azure cloud service alkalmazás távoli asztali kapcsolatok lehetővé tételéhez konfigurálása
services: cloud-services
author: ghogen
manager: douge
ms.assetid: f5727ebe-9f57-4d7d-aff1-58761e8de8c1
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.topic: conceptual
ms.workload: azure
ms.date: 03/06/2018
ms.author: ghogen
ms.openlocfilehash: fe8b2b59616246743b38aa3b7a7972c092529b5d
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788466"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-visual-studio"></a>Engedélyezze a távoli asztali kapcsolat egy szerepkör esetén az Azure Felhőszolgáltatások Visual Studio használatával

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A távoli asztal lehetővé teszi az asztalon, egy Azure-beli szerepkör eléréséhez. A távoli asztali kapcsolat segítségével hibaelhárítását és diagnosztizálását az alkalmazás futtatása során.

A Közzététel varázsló, amely a Visual Studio felhőszolgáltatás biztosít hozzátartozik, hogy egy lehetőség, hogy a távoli asztal engedélyezése a közzétételi folyamat során megadott hitelesítő adatokat. Ez a beállítás akkor megfelelő, amikor a Visual Studio 2017 15,4 és korábbi verzióiban használatával.

A Visual Studio 2017 15.5 és újabb verziók azonban ajánlott elkerülni, a távoli asztal engedélyezése a Közzététel varázsló lépéseinek, kivéve, ha csak egyetlen fejlesztőként dolgozik. Bármely helyzetet, amelyben a projekt előfordulhat, hogy nyithatja meg más fejlesztők számára helyette engedélyeznie a távoli asztal az Azure portálon, a PowerShell segítségével, vagy a folyamatos üzembe helyezés munkafolyamat kiadási-definícióból keresztül. Ez a javaslat hogyan Visual Studio kommunikál a távoli asztal virtuális gép, felhőalapú szolgáltatás, a témakörben leírt változás miatt van.

## <a name="configure-remote-desktop-through-visual-studio-2017-version-154-and-earlier"></a>A Visual Studio 2017 15,4 és korábbi verzióiban a távoli asztal konfigurálása

A Visual Studio 2017 15,4 és korábbi verzióiban használata esetén használhatja a **összes szerepkör távoli asztal engedélyezése** a Közzététel varázsló lehetőséget. A varázsló továbbra is használhatja a Visual Studio 2017 15.5 és újabb verzióit, de nem használja a távoli asztal szolgáltatást.

1. A Visual Studio publish varázsló elindításához kattintson a jobb gombbal a felhőszolgáltatási projektet a Megoldáskezelőben, majd válassza a **közzététel**.

2. Jelentkezzen be az Azure-előfizetéssel, ha szükséges, és válassza ki **következő**.

3. Az a **beállítások** lapon jelölje be **összes szerepkör távoli asztal engedélyezése**, majd jelölje be a **beállítások...**  hivatkozások megnyithassanak a **a távoli asztal konfigurálásának** párbeszédpanel megnyitásához.

4. A párbeszédpanel alján válassza **további beállítások**. A parancs megjeleníti a legördülő listából válassza ki, amelyben létrehozása, vagy válasszon egy tanúsítványt, így amikor a távoli asztali kapcsolatra hitelesítő adatok titkosíthatók.

   > [!Note]
   > A tanúsítványokat, amelyekre szüksége van a távoli asztali kapcsolat eltérnek a többi Azure művelet használt tanúsítvány. A távelérési tanúsítványnak rendelkeznie kell egy titkos kulccsal.

5. Jelöljön ki egy tanúsítványt a listából, vagy válasszon  **&lt;létrehozása... &gt;**. Ha egy új tanúsítvány létrehozása, adja meg egy rövid nevet az új tanúsítványt, amikor a rendszer kéri, majd válasszon **OK**. Az új tanúsítvány megjelenik a legördülő listában.

6. Adjon meg egy felhasználónevet és jelszót. Nem használhat egy meglévő fiókkal. Ne használja a "Rendszergazda" az új fiók felhasználónevet.

7. Válassza ki a dátum a fiók jár és után, mely a távoli asztali kapcsolatok le lesz tiltva.

8. A szükséges adatokat a megadott, után válassza ki **OK**. A Visual Studio a távoli asztal beállításait hozzáadja a projekt `.cscfg` és `.csdef` fájlokat, beleértve a jelszavát, amelyet a kiválasztott tanúsítvány használatával titkosított.

9. Végezze el minden fennmaradó lépéseit használatával a **tovább** gombra, majd válassza ki **közzététel** amikor készen áll a felhőalapú szolgáltatást teszik közzé. Ha nem Ön elkészült, válassza ki a **Mégse** és válasz **Igen** felkéréskor menti a módosításokat. A felhőalapú szolgáltatás később közzéteheti ezeket a beállításokat.

## <a name="configure-remote-desktop-when-using-visual-studio-2017-version-155-and-later"></a>A távoli asztal konfigurálása, a Visual Studio 2017 15.5 vagy újabb verziója használatakor

A Visual Studio 2017 15.5 és újabb verziók továbbra is használhatja a egy felhőszolgáltatás-projekt közzététele varázsló. Használhatja a **távoli asztal engedélyezése az összes szerepkör** lehetőséget, ha csak egyetlen fejlesztőként dolgozik.

Ha egy csoport részeként dolgozik, helyette engedélyezze-e be a távoli asztal Azure felhőalapú szolgáltatás használatával vagy a [Azure-portálon](cloud-services-role-enable-remote-desktop-new-portal.md) vagy [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

Ez a javaslat hogyan kommunikál a Visual Studio 2017 15.5 és újabb verzióit a felhőalapú szolgáltatás VM változás miatt van. A távoli asztal engedélyezése a Közzététel varázsló használatával, a Visual Studio korábbi verzióiban kommunikálni a virtuális gép keresztül mi hívta meg a "RDP beépülő modul." A Visual Studio 2017 15.5 és újabb verzióit kommunikál, inkább a "RDP bővítményének" használatával, amely biztonságosabb és rugalmasabb. Ez a változás is azt a tényt, hogy az Azure portál és a távoli asztal engedélyezése a PowerShell módszereket is használhatja a az RDP-bővítmény igazodik.

Ha a Visual Studio kommunikál az RDP-bővítmény, SSL-en keresztül továbbít egy egyszerű szöveges jelszó. A projekt konfigurációs fájlok azonban csak egy titkosított jelszót, és csak a tanúsítvánnyal helyi titkosításához használt egyszerű szöveggé visszafejthető tárolja.

Ha minden alkalommal, amikor telepíti a felhőszolgáltatás-projekt ugyanazon a fejlesztési számítógépen, majd a helyi tanúsítványtárolóban érhető el. Ebben az esetben továbbra is használhatja a **távoli asztal engedélyezése az összes szerepkör** a Közzététel varázsló lehetőséget.

Ha Ön vagy más fejlesztők számára telepíteni kívánja a felhőszolgáltatás-projekt különböző számítógépekről, azonban majd más számítógépeken nem kell a jelszó visszafejtéséhez szükséges tanúsítvány. Ennek eredményeképpen a következő hibaüzenet jelenik meg:

```output
Applying remote desktop protocol (RDP) extension.
Certificate with thumbprint [thumbprint] doesn't exist.
```

A jelszó is módosíthatja, minden alkalommal, amikor a felhőalapú szolgáltatás telepítéséhez, de a művelet kényelmetlenné válik összes felhasználója számára a távoli asztal használata szükséges.

Ha megosztja a projekt egy csapattal, majd, érdemes törölje a közzététel varázslóban, és helyette a közvetlenül a távoli asztal engedélyezése a [Azure-portálon](cloud-services-role-enable-remote-desktop-new-portal.md) vagy használatával [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md).

### <a name="deploying-from-a-build-server-with-visual-studio-2017-version-155-and-later"></a>A Visual Studio 2017 15.5 és újabb verzióit a build server telepítése

Egy felhőszolgáltatás-projekt egy kiszolgálóról build (például a Visual Studio Team Services) mely Visual Studio 2017 15.5 vagy újabb verziója telepítve van a build-ügynök telepítése. Ezzel az elrendezéssel telepítése történik, ugyanazon a számítógépen, amelyen a titkosítási tanúsítvány áll rendelkezésre.

A Visual Studio Team Services RDP bővítményt használja, a következőket foglalja a build definícióban:

1. Tartalmaznak `/p:ForceRDPExtensionOverPlugin=true` az MSBuild-argumentumok győződjön meg arról, hogy a központi telepítés együttműködve biztosítja a RDP beépülő modul helyett az RDP-bővítmény a. Példa:

    ```
    msbuild AzureCloudService5.ccproj /t:Publish /p:TargetProfile=Cloud /p:DebugType=None
        /p:SkipInvalidConfigurations=true /p:ForceRDPExtensionOverPlugin=true
    ```

1. A build lépések után adja hozzá a **Azure Cloud Service telepítési** . lépés:, és adja meg az tulajdonságait.

1. A központi telepítési lépés után adja hozzá egy **Azure Powershell** . lépés:, állítsa be a **megjelenített név** tulajdonságot "Azure központi telepítés: engedélyezze az RDP bővítménye" (vagy egy másik megfelelő nevet), és válassza ki a megfelelő Azure előfizetés.

1. Állítsa be **parancsfájl típusa** "Beágyazott" való és illessze be az alábbi kódot a **beágyazott parancsfájlja** mező. (Is létrehozhat egy `.ps1` ezt a parancsfájlt a projekt fájlt, és állítsa **parancsfájl** "Parancsfájl elérési útja", és állítsa a **parancsfájl elérési útján** a fájlra mutasson.)

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

## <a name="connect-to-an-azure-role-by-using-remote-desktop"></a>A távoli asztal használatával csatlakoznak az Azure-szerepkör

Miután közzététele a felhőalapú szolgáltatás, az Azure-on, és engedélyezte a távoli asztal, használhatja a Visual Studio Server Explorer a felhőszolgáltatás Virtuálisgép jelentkezni:

1. A Server Explorer eszközben bontsa ki a **Azure** csomópontot, majd bontsa ki a felhőszolgáltatás és a szerepkörök és példányok jelenítse meg egyik csomópontját.

2. Kattintson a jobb gombbal egy példány csomópont, és válassza ki **csatlakozzon a távoli asztal**.

3. Adja meg a felhasználónevet és jelszót, amelyet korábban hozott létre. Most jelentkezett be a távoli munkamenethez.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
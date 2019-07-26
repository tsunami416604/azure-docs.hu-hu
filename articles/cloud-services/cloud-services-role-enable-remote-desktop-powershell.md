---
title: Távoli asztali kapcsolat engedélyezése az Azure Cloud Services szerepkörhöz a PowerShell használatával
description: Azure Cloud Service-alkalmazás konfigurálása a PowerShell használatával a távoli asztali kapcsolatok engedélyezéséhez
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: gwallace
ms.openlocfilehash: b466cb866889edcdc2bd02373a5567a7b53ae18d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68358992"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Távoli asztali kapcsolat engedélyezése az Azure Cloud Services szerepkörhöz a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. Az alkalmazással kapcsolatos problémák elhárításához és diagnosztizálásához Távoli asztal-kapcsolatban is használhatja a szolgáltatást.

Ez a cikk azt ismerteti, hogyan engedélyezhető a távoli asztal a Cloud Service-szerepkörökön a PowerShell használatával. Tekintse meg, [hogyan telepítheti és konfigurálhatja a Azure PowerShell](/powershell/azure/overview) a cikkhez szükséges előfeltételekhez. A PowerShell a Távoli asztal bővítményt használja, így Távoli asztal az alkalmazás telepítése után engedélyezheti.

## <a name="configure-remote-desktop-from-powershell"></a>Távoli asztal konfigurálása a PowerShellből
A [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag lehetővé teszi, hogy az távoli asztal a megadott szerepkörökön vagy a Cloud Service-telepítés összes szerepkörén engedélyezze. A parancsmag segítségével megadhatja a távoli asztal felhasználójának felhasználónevét és jelszavát a *hitelesítő adatok* paraméterrel, amely elfogadja a PSCredential objektumot.

Ha interaktívan használja a PowerShellt, a [Get-hitelesítőadats](https://technet.microsoft.com/library/hh849815.aspx) parancsmag meghívásával egyszerűen beállíthatja a PSCredential objektumot.

```powershell
$remoteusercredentials = Get-Credential
```

Ez a parancs egy párbeszédpanelt jelenít meg, amely lehetővé teszi, hogy biztonságos módon adja meg a távoli felhasználó felhasználónevét és jelszavát.

Mivel a PowerShell segítséget nyújt az automatizálási forgatókönyvekben, a **PSCredential** objektumot úgy is beállíthatja, hogy ne kelljen felhasználói beavatkozást végeznie. Először be kell állítania egy biztonságos jelszót. Egy egyszerű szöveges jelszó megadásával kezdődik a [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)használatával történő biztonságos karakterláncra konvertálása. Ezután a biztonságos karakterláncot egy titkosított szabványos karakterlánccá kell konvertálnia a [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx)használatával. Most már mentheti a titkosított szabványos karakterláncot egy fájlra a [set-Content](https://technet.microsoft.com/library/ee176959.aspx)paranccsal.

Létrehozhat egy biztonságos jelszavas fájlt is, így nem kell minden alkalommal beírnia a jelszót. Emellett a biztonságos jelszavas fájl is jobb, mint egy egyszerű szövegfájl. A következő PowerShell használatával hozzon létre egy biztonságos jelszavas fájlt:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> A jelszó beállításakor győződjön meg arról, hogy megfelel az [összetettségi követelményeknek](https://technet.microsoft.com/library/cc786468.aspx).

Ahhoz, hogy a hitelesítő adatokat a biztonságos jelszó fájlból hozza létre, el kell olvasnia a fájl tartalmát, és vissza kell alakítania azokat egy biztonságos karakterláncra az [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)használatával.

A [set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag egy lejárati paramétert is elfogad, amely meghatározza azt a **dátumot** és időpontot, amikor a felhasználói fiók lejár. Például beállíthatja, hogy a fiók az aktuális dátumtól és időponttól néhány napig lejárjon.

Ez a PowerShell-példa bemutatja, hogyan állíthatja be a Távoli asztal-bővítményt egy felhőalapú szolgáltatásban:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Igény szerint megadhatja az üzembe helyezési tárolóhelyet és a Távoli asztalt engedélyező szerepköröket is. Ha ezek a paraméterek nincsenek megadva, a parancsmag lehetővé teszi a távoli asztal használatát az **éles** üzembe helyezési pont összes szerepkörén.

A Távoli asztal bővítmény társítva van egy központi telepítéshez. Ha új központi telepítést hoz létre a szolgáltatáshoz, engedélyeznie kell a Távoli asztalt az adott központi telepítésben. Ha mindig engedélyezni szeretné a távoli asztal használatát, érdemes megfontolnia a PowerShell-parancsfájlok integrálását a telepítési munkafolyamatba.

## <a name="remote-desktop-into-a-role-instance"></a>Távoli asztal egy szerepkör-példányba

A [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag használatával a távoli asztal a felhőalapú szolgáltatás egy adott szerepkör-példányában található. Az RDP-fájl helyi letöltéséhez használhatja a *LocalPath* paramétert. Vagy a *Launch* paraméterrel közvetlenül is elindíthatja a távoli asztali kapcsolat párbeszédpanelt a Cloud Service szerepkör-példány eléréséhez.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Ellenőrizze, hogy a Távoli asztal-bővítmény engedélyezve van-e a szolgáltatáson

A [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag azt jeleníti meg, hogy a távoli asztal engedélyezve van vagy le van tiltva egy szolgáltatás központi telepítésén. A parancsmag visszaadja a távoli asztal felhasználójának felhasználónevét, valamint azokat a szerepköröket, amelyekhez a távoli asztal bővítmény engedélyezve van. Alapértelmezés szerint ez az üzembe helyezési ponton történik, és az átmeneti tárolóhely használata is megadható.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Távoli asztal bővítmény eltávolítása a szolgáltatásból

Ha már engedélyezte a távoli asztal bővítményt egy központi telepítésben, és frissítenie kell a távoli asztal beállításait, először távolítsa el a bővítményt. És engedélyezze újra az új beállításokkal. Ha például új jelszót szeretne beállítani a távoli felhasználói fiókhoz, vagy lejárt a fiók. Ehhez szükség van a távoli asztal bővítményt használó meglévő központi telepítések esetén. Új központi telepítések esetén egyszerűen közvetlenül is alkalmazhatja a bővítményt.

A távoli asztali bővítmény a telepítésből való eltávolításához használhatja a Remove [-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmagot. Igény szerint megadhatja azt a telepítési tárolóhelyet és szerepkört is, amelyből el szeretné távolítani a távoli asztali bővítményt.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> A bővítmény konfigurációjának teljes eltávolításához hívja meg a *Remove* parancsmagot a **UninstallConfiguration** paraméterrel.
>
> A **UninstallConfiguration** paraméter eltávolítja a szolgáltatásra alkalmazott bővítmények konfigurációját. Minden bővítmény-konfiguráció a szolgáltatás konfigurációjával van társítva. A *Remove* parancsmag meghívása anélkül, hogy a **UninstallConfiguration** leválasztja a <mark>központi telepítést</mark> a bővítmény konfigurációjától, így gyakorlatilag eltávolítja a bővítményt. A bővítmény konfigurációja azonban továbbra is hozzá van rendelve a szolgáltatáshoz.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
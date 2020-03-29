---
title: Szerepkör távoli asztalának engedélyezése a PowerShell használatával
titleSuffix: Azure Cloud Services
description: Az azure-felhőszolgáltatás-alkalmazás konfigurálása a PowerShell használatával távoli asztali kapcsolatok engedélyezéséhez
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386119"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Távoli asztali kapcsolat engedélyezése szerepkörhöz az Azure Cloud Servicesben a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure-portál](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Vizuális stúdió](cloud-services-role-enable-remote-desktop-visual-studio.md)

A Távoli asztal lehetővé teszi az Azure-ban futó szerepkör asztalának elérését. A Távoli asztali kapcsolat segítségével elháríthatja és diagnosztizálhatja az alkalmazással kapcsolatos problémákat futás közben.

Ez a cikk ismerteti, hogyan engedélyezheti a távoli asztal a Felhőszolgáltatási szerepkörök a PowerShell használatával. Olvassa [el az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview) a cikkhez szükséges előfeltételekhez. A PowerShell a Távoli asztali bővítményt használja, így az alkalmazás telepítése után engedélyezheti a Távoli asztalt.

## <a name="configure-remote-desktop-from-powershell"></a>Távoli asztal konfigurálása a PowerShellből
A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag lehetővé teszi a Távoli asztal engedélyezését a megadott szerepkörökön vagy a felhőszolgáltatás üzembe helyezésének összes szerepkörén. A parancsmag lehetővé teszi, hogy a PSCredential objektumot elfogadó *Hitelesítő adatok* paraméteren keresztül adja meg a távoli asztali felhasználó felhasználónevét és jelszavát.

Ha a PowerShell tanoni módon használja, egyszerűen beállíthatja a PSCredential objektumot a [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) parancsmag hívásával.

```powershell
$remoteusercredentials = Get-Credential
```

Ez a parancs egy párbeszédpanelt jelenít meg, amelyen biztonságosmódon megadhatja a távoli felhasználó felhasználónevét és jelszavát.

Mivel a PowerShell segít az automatizálási forgatókönyvekben, a **PSCredential** objektumot is beállíthatja olyan módon, amely nem igényel felhasználói beavatkozást. Először biztonságos jelszót kell beállítania. Először is meg kell adnia egy egyszerű szöveges jelszót, amely biztonságos karakterláncmá konvertálja [a ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)segítségével. Ezután ezt a biztonságos karakterláncot titkosított szabványos karakterláncgá kell konvertálnia [a ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx)használatával. Most már mentheti ezt a titkosított szabványos karakterláncot egy fájlba a [Set-Content](https://technet.microsoft.com/library/ee176959.aspx)használatával.

Létrehozhat egy biztonságos jelszófájlt is, így nem kell minden alkalommal beírnia a jelszót. Is, egy biztosít jelszó reszelő van jobb mint egy sima szöveg reszelő. Biztonságos jelszófájl létrehozásához használja a következő PowerShell-fájlt:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> A jelszó beállításakor győződjön meg arról, hogy megfelel az [összetettségi követelményeknek.](https://technet.microsoft.com/library/cc786468.aspx)

Ha a hitelesítő adatokobjektumot a biztonságos jelszófájlból szeretné létrehozni, el kell olvasnia a fájl tartalmát, és vissza kell konvertálnia őket egy biztonságos karakterláncba [a ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx)segítségével.

A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag egy *lejárati* paramétert is elfogad, amely megadja azt a **DateTime-ot,** amelyen a felhasználói fiók lejár. Beállíthatja például, hogy a fiók az aktuális dátumtól és időponttól számított néhány napon belül lejárjon.

Ez a PowerShell-példa bemutatja, hogyan állíthatja be a Távoli asztali bővítményt egy felhőszolgáltatásban:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Megadhatja azt a telepítési helyet és szerepköröket is, amelyeken engedélyezni szeretné a távoli asztalt. Ha ezek a paraméterek nincsenek megadva, a parancsmag engedélyezi a távoli asztalt az **éles** telepítési hely összes szerepkörén.

A Távoli asztal bővítmény egy központi telepítéshez van társítva. Ha új központi telepítést hoz létre a szolgáltatáshoz, engedélyeznie kell a távoli asztalt az adott központi telepítésen. Ha mindig azt szeretné, hogy a távoli asztal engedélyezve legyen, akkor érdemes lehet a PowerShell-parancsfájlok integrálása a központi telepítési munkafolyamatba.

## <a name="remote-desktop-into-a-role-instance"></a>Távoli asztal szerepkörpéldánylá

A [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag a távoli asztal a felhőszolgáltatás egy adott szerepkörpéldányába. A *LocalPath* paraméterrel helyileg töltheti le az RDP-fájlt. Vagy *használhatja* az Indítás paramétert a Távoli asztali kapcsolat párbeszédpanel közvetlen elindításához a felhőszolgáltatási szerepkör-példány eléréséhez.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Annak ellenőrzése, hogy a Távoli asztal bővítmény engedélyezve van-e egy szolgáltatásban

A [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag azt jeleníti meg, hogy a távoli asztal engedélyezve van vagy le van tiltva egy szolgáltatás központi telepítésén. A parancsmag a távoli asztali felhasználó felhasználónevét és azokat a szerepköröket adja vissza, amelyekhez a távoli asztali bővítmény engedélyezve van. Alapértelmezés szerint ez történik a központi telepítési tárolóhelyen, és választhatja, hogy használja az átmeneti tárolóhely helyett.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Távoli asztal bővítmény eltávolítása egy szolgáltatásból

Ha már engedélyezte a távoli asztal bővítményt egy központi telepítésen, és frissítenie kell a távoli asztal beállításait, először távolítsa el a bővítményt. És engedélyezze újra az új beállításokkal. Ha például új jelszót szeretne beállítani a távoli felhasználói fiókhoz, vagy a fiók lejárt. Ehhez olyan meglévő telepítésekesetén van szükség, amelyeken engedélyezve van a távoli asztali bővítmény. Az új központi telepítések, egyszerűen alkalmazhatja a bővítményt közvetlenül.

A távoli asztali bővítmény eltávolításához használja az [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag. Megadhatja azt a telepítési helyet és szerepkört is, amelyről el szeretné távolítani a távoli asztalbővítményt.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> A bővítmény konfigurációjának teljes eltávolításához hívja meg az *eltávolítási* parancsot az **UninstallConfiguration** paraméterrel.
>
> Az **UninstallConfiguration** paraméter eltávolítja a szolgáltatásra alkalmazott bővítménykonfigurációt. Minden bővítménykonfiguráció a szolgáltatáskonfigurációhoz van társítva. Az *eltávolítási* parancsmag **meghívása az EltávolításKonfiguráció** nélkül leválasztja a <mark>központi telepítést</mark> a bővítménykonfigurációtól, így hatékonyan eltávolítja a bővítményt. A bővítmény konfigurációja azonban továbbra is a szolgáltatáshoz van társítva.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)



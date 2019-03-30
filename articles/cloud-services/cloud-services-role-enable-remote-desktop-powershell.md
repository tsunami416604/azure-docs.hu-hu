---
title: Távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services szolgáltatással a PowerShell használatával
description: A távoli asztali kapcsolatok engedélyezése a PowerShell segítségével az azure cloud service-alkalmazás konfigurálása
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 43ccc8e53c30219630ad10ee66a4db38656818e6
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58651005"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Távoli asztali kapcsolat engedélyezése egy szerepkörhöz az Azure Cloud Services szolgáltatással a PowerShell használatával

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

A távoli asztal segítségével elérheti az Azure-ban futó szerepkörök asztalát. Az alkalmazással kapcsolatos problémák diagnosztizálására futás közben és használhatja a távoli asztali kapcsolatot.

Ez a cikk ismerteti a távoli asztal engedélyezése a PowerShell használatával a Felhőszolgáltatás szerepköreit. Lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket. PowerShell a távoli asztal bővítményt használja, a távoli asztal, ahol engedélyezheti, az alkalmazás üzembe helyezése után.

## <a name="configure-remote-desktop-from-powershell"></a>A PowerShellben a távoli asztal konfigurálása
A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag lehetővé teszi a távoli asztal engedélyezése a megadott szerepkörök vagy a felhőszolgáltatás üzembe helyezésének összes szerepkörének. A parancsmag segítségével adja meg a felhasználónevet és jelszót a távoli asztali felhasználó leállította a *Credential* paraméter, amely fogad egy PSCredential objektumot.

Ha PowerShell interaktív módon használja, egyszerűen beállíthatja a PSCredential objektum meghívásával a [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) parancsmagot.

```powershell
$remoteusercredentials = Get-Credential
```

Ez a parancs egy párbeszédpanel, lehetővé téve, hogy adja meg a felhasználónevet és jelszót a távoli felhasználó biztonságos módon jeleníti meg.

Mivel a PowerShell segítségével az automation-forgatókönyvek, is beállíthat a **PSCredential** objektumot úgy, hogy nincs szükség felhasználói beavatkozásra. Először egy biztonságos jelszó beállításához. Kezdje egy egyszerű szöveges jelszó alakíthatja át egy biztonságos karakterláncra az megadása [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Ezután egy titkosított szabványos karakterláncok használatával konvertálja a biztonságos karakterláncot kell [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Most egy fájl segítségével mentheti a titkosított szabványos karakterláncok [Set-tartalom](https://technet.microsoft.com/library/ee176959.aspx).

Egy biztonságos jelszó fájlt is létrehozhat, így nem kell minden alkalommal meg a jelszót. Biztonságos jelszó fájl is jobb, mint az egyszerű szöveges fájlt. A következő PowerShell segítségével hozzon létre egy biztonságos jelszó fájlt:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Amikor a jelszó beállítását, győződjön meg arról, hogy megfelel a [összetettségi követelményeknek](https://technet.microsoft.com/library/cc786468.aspx).

Hozza létre a hitelesítő objektumot a biztonságos jelszó-fájlból, kell olvasni a fájl tartalmát, és konvertálja azokat egy biztonságos karakterláncra az [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag is fogad egy *lejárati* paramétert, amely meghatározza egy **DateTime** , amely a felhasználói fiók lejár. Ha például sikerült beállítani a fiók lejár az aktuális dátum és idő néhány nap.

Ez a PowerShell-példa bemutatja, hogyan állíthatja be a távoli asztali bővítmény egy felhőszolgáltatáson:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Üzembe helyezési pont és a távoli asztal engedélyezése a kívánt szerepkörök is megadható. Ha ezek a paraméterek nincsenek megadva, a parancsmag lehetővé teszi, hogy az összes szerepkört a távoli asztal a **éles** üzembe helyezési pont.

A távoli asztali bővítmény társítva egy központi telepítést. Ha a szolgáltatás új központi telepítést hoz létre, azok távoli asztal engedélyezése a a telepítés. Ha mindig szeretne a távoli asztal engedélyezve van, majd vegye figyelembe a PowerShell-parancsfájlok integrálása a telepítési munkafolyamat.

## <a name="remote-desktop-into-a-role-instance"></a>Egy szerepkörpéldány távoli asztal

A [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag egy adott szerepkörpéldány a felhőszolgáltatás használatban a távoli asztal. Használhatja a *LocalPath* paraméter használatával töltse le az RDP fájlt helyileg. Vagy használhatja a *indítsa el a* közvetlenül indítsa el a távoli asztali kapcsolat párbeszédpanel eléréséhez a felhőszolgáltatás szerepkörpéldánya paramétert.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Ellenőrizze, hogy engedélyezve van-e a távoli asztal bővítmény egy szolgáltatásban

A [Get-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag megjeleníti, hogy a távoli asztal engedélyezve van-e a szolgáltatások üzembe helyezéséhez. A parancsmag visszaadja a felhasználónév, a távoli asztali felhasználói és a szerepköröket, amelyek a távoli asztali bővítmény engedélyezve van. Alapértelmezés szerint ez az üzembe helyezési pont történik, és Ön kiválaszthatja, használja helyette az előkészítési pontot.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Egy szolgáltatás a távoli asztal bővítmény eltávolítása

Ha már engedélyezve van a távoli asztali futtatására szolgáló bővítmény egy központi telepítést, és frissítenie kell a távoli asztal beállításait, először távolítsa el a bővítményt. Majd engedélyezze újra az új beállításokkal. Ha például szeretné állítani a távoli felhasználói fiók egy új jelszót, vagy a fiók lejárt. Ez meglévő üzemelő példányok esetében, amelyeken engedélyezve van a távoli asztali bővítmény szükséges. Az új központi telepítéseknél egyszerűen alkalmazhat a bővítmény közvetlenül.

A távoli asztali bővítmény eltávolítása a központi telepítést, használhatja a [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmagot. Opcionálisan megadhatja az üzembe helyezési pont és a szerepkör, amelyből el kívánja távolítani a távoli asztali bővítmény.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> A bővítmény konfigurációja teljes eltávolításához, meg kell hívnia a *eltávolítása* parancsmagot a **UninstallConfiguration** paraméter.
>
> A **UninstallConfiguration** paraméter eltávolítja a bővítmény-konfigurációra, amely a szolgáltatás érvényes. Minden bővítmény konfigurációs társítva a szolgáltatás konfigurációját. Hívása a *eltávolítása* nélkül parancsmag **UninstallConfiguration** disassociates a <mark>üzembe helyezési</mark> a bővítmény-konfigurációból, így hatékonyan eltávolítása a bővítmény. Azonban a bővítmény konfigurációja továbbra is társítva a szolgáltatáshoz.

## <a name="additional-resources"></a>További források

[A Cloud Services Konfigurálása](cloud-services-how-to-configure-portal.md)
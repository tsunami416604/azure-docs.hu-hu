---
title: "Azure Cloud Services PowerShell használatával a szerepkör távoli asztali kapcsolat engedélyezése"
description: "Konfigurálása az azure cloud service alkalmazás távoli asztali kapcsolatok engedélyezése a PowerShell használatával"
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 171f27c92ee9de14301ebb664e9ba3bcd98c394d
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Azure Cloud Services PowerShell használatával a szerepkör távoli asztali kapcsolat engedélyezése
> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [klasszikus Azure portál](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)
>
>

A távoli asztal lehetővé teszi az asztalon, egy Azure-beli szerepkör eléréséhez. A távoli asztali kapcsolat segítségével hibaelhárítását és diagnosztizálását az alkalmazás futtatása során.

Ez a cikk ismerteti a távoli asztal engedélyezése a PowerShell használatával a Felhőszolgáltatás szerepköreit. Lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) esetében ez a cikk szükséges előfeltételeket. PowerShell használja a távoli asztali bővítmény, ahol engedélyezheti a távoli asztal, az alkalmazás telepítése után.

## <a name="configure-remote-desktop-from-powershell"></a>A PowerShell távoli asztal konfigurálása
A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag lehetővé teszi a távoli asztal engedélyezése a megadott szerepkörök vagy a cloud service-környezet minden szerepkört. A parancsmag lehetővé teszi, hogy a felhasználónév és jelszó megadása a távoli asztali felhasználó leállította a *Credential* paraméter, amely fogad egy PSCredential objektumot.

Ha PowerShell interaktív módon használja, egyszerűen beállíthatja a PSCredential objektum meghívásával a [Get-hitelesítő adatok](https://technet.microsoft.com/library/hh849815.aspx) parancsmag.

```
$remoteusercredentials = Get-Credential
```

Ez a parancs megjelenít egy párbeszédpanelt, hogy lehetővé teszi a biztonságos módon a távoli felhasználót a felhasználónév és jelszó megadása.

Automation-forgatókönyvek PowerShell hozzájárul, mivel is beállíthat a **PSCredential** objektum oly módon, hogy nincs szükség felhasználói beavatkozásra. Először akkor be kell állítania egy biztonságos jelszó. Egy egyszerű szöveges jelszó átalakítása a egy biztonságos karakterláncot használó megadásával megkezdése [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Ezután kell konvertálni a biztonságos karakterláncot kell megadnia egy titkosított szabványos karakterláncok használatával [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Most egy fájl használatával mentheti a titkosított szabványos karakterlánc [Set-tartalom](https://technet.microsoft.com/library/ee176959.aspx).

Biztonságos jelszó fájl is létrehozhat, így nem kell minden alkalommal meg a jelszót. Biztonságos jelszó fájl is nagyobb, mint egy egyszerű szöveges fájl. A következő PowerShell segítségével hozzon létre egy biztonságos jelszó fájlt:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Amikor a jelszó beállítását, győződjön meg arról, hogy teljesülnek a [bonyolult](https://technet.microsoft.com/library/cc786468.aspx).
>
>

A hitelesítő objektum létrehozása a biztonságos jelszó fájlból, kell a fájl tartalmának olvasása és alakíthatja át őket egy biztonságos karakterláncot használni [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

A [Set-AzureServiceRemoteDesktopExtension](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag is fogad el egy *lejárati* paraméter, amely megadja egy **DateTime** , amely a felhasználói fiók lejár. Például beállíthat a fiók az aktuális dátum és idő néhány nap múlva lejár.

A PowerShell-példa bemutatja, hogyan állítsa be a távoli asztali bővítmény egy felhőalapú szolgáltatás:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcionálisan kiegészítheti az üzembe helyezési pont és a távoli asztal engedélyezése a kívánt szerepkörök. Ha ezek a paraméterek nincsenek megadva, a parancsmag lehetővé teszi, hogy az összes szerepkör a távoli asztal a **éles** üzembe helyezési pont.

A távoli asztali bővítmény társítva a központi telepítés. Ha létrehoz egy új központi telepítést, a szolgáltatás számára, hogy távoli asztal engedélyezése a központi telepítést. Ha azt szeretné, hogy az engedélyezve van a távoli asztal, majd vegye figyelembe a PowerShell-parancsfájlok integrálása a telepítési munkafolyamat.

## <a name="remote-desktop-into-a-role-instance"></a>Távoli asztali kapcsolatot egy szerepkörpéldányt
A [Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag használatban a távoli asztali kapcsolatot egy adott szerepkör példánya a felhőalapú szolgáltatás. Használhatja a *LocalPath* paraméter töltse le az RDP a fájlt helyileg. Vagy használhatja a *indítása* paraméter segítségével közvetlenül indítsa el a távoli asztali kapcsolat párbeszédpanel a felhőalapú szolgáltatás szerepkör példánya eléréséhez.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Ellenőrizze, hogy engedélyezve van-e a távoli asztal bővítmény egy szolgáltatásra
A [Get-AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) parancsmag jeleníti meg, hogy a távoli asztal engedélyezve van-e a szolgáltatás üzemelő példányon. A parancsmag visszaadja a felhasználónév, a távoli asztal felhasználóját, és a szerepköröket, amelyek a távoli asztali bővítmény engedélyezve van. Alapértelmezés szerint ez az üzembe helyezési pont történik, és esetén dönthet úgy, hogy az átmeneti helyet használja helyette.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>A szolgáltatás a távoli asztal-kiterjesztés eltávolítása
Ha már engedélyezve van a távoli asztali bővítmény üzemelő példányon, és frissítenie kell a távoli asztal beállításait, először távolítsa el a bővítményt. Majd engedélyezze újra az új beállításokkal. Ha például azt szeretné, hogy a távoli felhasználói fiókhoz tartozó új jelszót állíthat be, vagy a fiók lejárt. Ezzel a meglévő telepítések engedélyezve van a távoli asztali kiterjesztésű szükséges. Az új központi telepítéseknél egyszerűen alkalmazhatja a bővítmény közvetlenül.

A központi telepítést a távoli asztali bővítmény eltávolításához használja a [Remove-AzureServiceRemoteDesktopExtension](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) parancsmag. Opcionálisan kiegészítheti az üzembe helyezési pont és a szerepkör, amelyről el szeretné távolítani a távoli asztali bővítmény.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> A bővítmény konfigurációja teljes eltávolításához meg kell hívnia a *eltávolítása* parancsmagot a **UninstallConfiguration** paraméter.
>
> A **UninstallConfiguration** paraméter eltávolítja a szolgáltatás alkalmazott bővítmény beállításra. Minden bővítménykonfiguráció nem tartozik a szolgáltatás konfigurációját. Hívja a *eltávolítása* nélkül parancsmag **UninstallConfiguration** megszünteti a <mark>telepítési</mark> a bővítménykonfiguráció, így gyakorlatilag kivonja a a bővítmény. Azonban a bővítmény konfigurációja továbbra is társítva van a szolgáltatás.
>
>

## <a name="additional-resources"></a>További források

[Felhőalapú szolgáltatások konfigurálása](cloud-services-how-to-configure.md)
[Cloud services – gyakori kérdések – a távoli asztal](cloud-services-faq.md)

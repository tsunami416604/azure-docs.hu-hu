---
title: Alaphelyzetbe állítja a jelszót egy Windows Azure-ban a távoli asztal konfigurálásának |} Microsoft Docs
description: 'Útmutató: a fiók jelszavát, vagy a távoli asztali szolgáltatások a Windows virtuális gép létrehozása a klasszikus telepítési modellt az Azure-portálon vagy az Azure PowerShell használatával.'
services: virtual-machines-windows
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 516cb740f9acad19dac77db0239341b42a2b27fe
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
ms.locfileid: "30917649"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>A távoli asztal szolgáltatás vagy egy Windows virtuális gépre, klasszikus telepítési modellel létrehozott bejelentkezési jelszava alaphelyzetbe állítása
> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [hajtsa végre ezeket a lépéseket a Resource Manager üzembe helyezési modellel létrehozott virtuális gépek](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Ha nem tud csatlakozni a Windows rendszerű virtuális gép (VM), a helyi rendszergazda jelszavát, vagy állítsa a távoli asztal szolgáltatás konfigurációját. Használhatja az Azure-portálon vagy a virtuális gép hozzáférési bővítményét az Azure PowerShell a jelszó alaphelyzetbe állításához.

## <a name="ways-to-reset-configuration-or-credentials"></a>Konfiguráció vagy a hitelesítő adatok alaphelyzetbe módjai
Alaphelyzetbe állíthatja a távoli asztali szolgáltatások és a hitelesítő adatokat néhány más-más módon igényeitől függően:

- [Az Azure portál segítségével vissza](#azure-portal)
- [Alaphelyzetbe állítja az Azure PowerShell használatával](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Használhatja a [Azure-portálon](https://portal.azure.com) a távoli asztal szolgáltatás alaphelyzetbe állítása. Bontsa ki a portál menüjében, kattintson a három sávok bal felső sarokban, és kattintson a **virtuális gépek (klasszikus)**:

![Tallózással keresse meg az Azure virtuális gép](./media/reset-rdp/Portal-Select-Classic-VM.png)

Válassza ki a Windows virtuális gépet, és kattintson a **távoli alaphelyzetbe állítása...** . A következő párbeszédpanel jelenik meg a távoli asztal konfigurálásának visszaállítása:

![RDP-konfiguráció lapon alaphelyzetbe állítása](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

A felhasználónevet és jelszót a helyi rendszergazdai fiók is alaphelyzetbe állíthatja. A virtuális Gépet, kattintson **támogatási + hibaelhárítás** > **jelszó-átállítási**. A jelszó alaphelyzetbe állítása panelen jelennek meg:

![Jelszó alaphelyzetbe állítása lap](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Miután megadta az új felhasználónevet és jelszót, kattintson a **mentése**.

## <a name="vmaccess-extension-and-powershell"></a>VMAccess bővítmény és a PowerShell
Győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van a virtuális gépen. A VMAccess bővítmény nem kell telepíteni, használatba vétele előtt mindaddig, amíg a VM-ügynök nem érhető el. Győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van a következő parancs használatával. (Helyére "myCloudService" és "myVM" a virtuális Gépet, és a felhőszolgáltatás nevét kulcsattribútumokkal. Ezeket a neveket is megtudhat futtatásával `Get-AzureVM` paraméter nélkül.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **write-host** parancs megjeleníti **igaz**, a Virtuálisgép-ügynök telepítve van. Jelez, ha **hamis**, az utasításokat, és a letölthető mutató hivatkozást a [ügynök és Virtuálisgép-bővítmények – 2. rész](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure blogbejegyzést.

Ha a portál használatával hozott létre a virtuális géphez, ellenőrizze, hogy `$vm.GetInstance().ProvisionGuestAgent` adja vissza **igaz**. Ha nem, ez a parancs használatával állíthatja be:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Ez a parancs megakadályozza, hogy a következő hiba jelenik meg a **Set-AzureVMExtension** parancsot a következő lépésekben: "Rendelkezés Vendégügynök engedélyezni kell a Virtuálisgép-objektum infrastruktúra-szolgáltatási virtuális gép hozzáférési bővítmény beállítása előtt."

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának visszaállítása**
Bejelentkezési hitelesítő adatok létrehozása az aktuális helyi rendszergazda fiók nevet és egy új jelszót, és futtassa a `Set-AzureVMAccessExtension` az alábbiak szerint.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Írja be a neve eltér a jelenlegi fiókot, ha a VMAccess bővítmény átnevezi a helyi rendszergazdai fiók, a jelszót a fiókhoz hozzárendel, és kijelentkezési a távoli asztal kiállított. Ha a helyi rendszergazdai fiók le van tiltva, a VMAccess bővítmény lehetővé teszi.

Ezek a parancsok is alaphelyzetbe állíthatja a távoli asztal szolgáltatás konfigurációját.

### <a name="reset-the-remote-desktop-service-configuration"></a>**Állítsa a távoli asztal szolgáltatás konfigurációját.**
A távoli asztal szolgáltatás konfigurációját visszaállításához futtassa a következő parancsot:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

A VMAccess bővítmény két parancsot futtatja a virtuális gépen:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Ez a parancs lehetővé teszi, hogy a beépített Windows tűzfal-csoport, amely lehetővé teszi a távoli asztal bejövő forgalmat, amely a 3389-es TCP-portot használja.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Ez a parancs beállítja a fDenyTSConnections beállításazonosítót 0, amely lehetővé teszi a távoli asztali kapcsolatok.

## <a name="next-steps"></a>További lépések
Ha az Azure virtuális gép hozzáférési bővítmény nem válaszol, és nem tudja alaphelyzetbe állítani a jelszót, akkor [a helyi Windows-jelszó alaphelyzetbe állítása offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a módszer egy speciális folyamat, és megköveteli, hogy a virtuális merevlemez a problematikus VM kapcsolódjon egy másik virtuális gép. Kövesse a jelen cikkben leírt első lépéseket, és csak kísérlet a kapcsolat nélküli jelszó alaphelyzetbe állítása módszer utolsó lehetőségként.

[Az Azure Virtuálisgép-bővítmények és szolgáltatások](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Egy Azure virtuális gép RDP- vagy SSH kapcsolódni](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[A Windows-alapú Azure virtuális géphez a távoli asztali kapcsolatok hibáinak elhárítása](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


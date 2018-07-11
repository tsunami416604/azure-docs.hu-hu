---
title: A jelszó vagy távoli asztal konfigurálásának a az Azure-beli Windows virtuális gép alaphelyzetbe állítása |} A Microsoft Docs
description: Ismerje meg, hogyan alaphelyzetbe állíthatja a fiókja jelszavát, vagy a távoli asztali szolgáltatások a Windows virtuális gép létrehozása az Azure portal vagy Azure PowerShell használatával, a klasszikus üzemi modell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: bbe8059b3a239570c2c9b25586dae9adbe25312d
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931378"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm-created-using-the-classic-deployment-model"></a>A távoli asztali szolgáltatás vagy a bejelentkezési jelszót a klasszikus üzemi modellel létrehozott Windows virtuális gép visszaállítása
> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Emellett [ezeket a lépéseket a Resource Manager üzemi modellel létrehozott virtuális gépek](../reset-rdp.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]


Ha nem tud csatlakozni egy Windows virtuális gép (VM), a helyi rendszergazdai jelszó visszaállítása, vagy alaphelyzetbe állítása a távoli asztal szolgáltatás konfigurációját. Használhatja az Azure Portalon vagy a Virtuálisgép-hozzáférési bővítmény az Azure PowerShell a jelszó alaphelyzetbe állítása.

## <a name="ways-to-reset-configuration-or-credentials"></a>Konfiguráció vagy a hitelesítő adatok alaphelyzetbe állítása módjai
Visszaállíthatja a távoli asztali szolgáltatások és a hitelesítő adatok néhány más-más módon igényeitől függően:

- [Alaphelyzetbe állítása, az Azure portal használatával](#azure-portal)
- [Új Azure PowerShell-lel](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Használhatja a [az Azure portal](https://portal.azure.com) a távoli asztal szolgáltatás alaphelyzetbe állítása. Bontsa ki a menü a portálon, kattintson a bal felső sarkában található három sávra, és kattintson a **virtuális gépek (klasszikus)**:

![Keresse meg az Azure virtuális gép](./media/reset-rdp/Portal-Select-Classic-VM.png)

Válassza ki a Windows virtuális gépet, és kattintson a **távoli alaphelyzetbe állítása...** . Az alábbi párbeszédablak jelenik meg a távoli asztal konfigurálásának alaphelyzetbe állítása:

![RDP-konfigurációs oldalára](./media/reset-rdp/Portal-RDP-Reset-Windows.png)

A felhasználónév és a helyi rendszergazdai fiók jelszavát is alaphelyzetbe állíthatja. A virtuális gépről, kattintson a **támogatás + hibaelhárítás** > **jelszó alaphelyzetbe állítása**. A jelszó alaphelyzetbe állítása panel jelenik meg:

![Jelszó alaphelyzetbe állítására szolgáló lap](./media/reset-rdp/Portal-PW-Reset-Windows.png)

Miután megadta az új felhasználónevet és jelszót, kattintson a **mentése**.

## <a name="vmaccess-extension-and-powershell"></a>VMAccess bővítmény és a PowerShell
Győződjön meg arról, hogy a Virtuálisgép-ügynök telepítve van a virtuális gépen. A VMAccess bővítmény nem kell telepíteni ahhoz is használhatja, mindaddig, amíg a Virtuálisgép-ügynök nem érhető el. Győződjön meg arról, hogy a Virtuálisgép-ügynök már telepítve van a következő parancs segítségével. (A cserélje le "myCloudService" és "myVM" a cloud Services és a virtuális gép neve rendre. Ezeket a neveket futtatásával további `Get-AzureVM` paraméterek nélkül.)

```powershell
$vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
write-host $vm.VM.ProvisionGuestAgent
```

Ha a **write-host** parancs megjeleníti **igaz**, a Virtuálisgép-ügynök telepítve van. Ha megjeleníti **hamis**, tekintse meg az utasításokat, és a a letöltésére mutató hivatkozást a [Virtuálisgép-ügynök és -bővítmények – 2. rész](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) Azure blog bejegyzésében.

A portál használatával létrehozta a virtuális géphez, ellenőrizze, hogy `$vm.GetInstance().ProvisionGuestAgent` adja vissza **igaz**. Ha nem, beállíthatja a következő paranccsal:

```powershell
$vm.GetInstance().ProvisionGuestAgent = $true
```

Ez a parancs a következő hiba megakadályozza, amikor futtatja a **Set-AzureVMExtension** parancsot a következő lépésekben: "Kiépítése Vendégügynök engedélyezni kell a Virtuálisgép-objektum IaaS Virtuálisgép-hozzáférési bővítmény beállítása előtt."

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**
Jelentkezzen be hitelesítő adatok létrehozása az aktuális helyi rendszergazdai fiók nevét és a egy új jelszót, és futtassa a `Set-AzureVMAccessExtension` módon.

```powershell
$cred=Get-Credential
Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password  | Update-AzureVM
```

Ha egy másik nevet, mint a jelenlegi fiókot, a VMAccess bővítmény átnevezi a helyi rendszergazdai fiók, rendeli hozzá a jelszót a fiókhoz, és kiad egy távoli asztali kijelentkezési. Ha a helyi rendszergazdai fiók le van tiltva, a VMAccess bővítmény lehetővé teszi azt.

Ezek a parancsok is alaphelyzetbe állíthatja a távoli asztal szolgáltatás konfigurációját.

### <a name="reset-the-remote-desktop-service-configuration"></a>**A távoli asztal szolgáltatás konfigurációját alaphelyzetbe állítása**
Alaphelyzetbe állítja a távoli asztal szolgáltatás konfigurációját, futtassa a következő parancsot:

```powershell
Set-AzureVMAccessExtension –vm $vm | Update-AzureVM
```

A VMAccess bővítmény két parancs futtatható a virtuális gépen:

```powershell
netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes
```

Ez a parancs lehetővé teszi, hogy a beépített Windows tűzfal-csoport, amely lehetővé teszi a bejövő távoli asztali forgalmat, amely a 3389-es TCP-portot használja.

```powershell
Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0
```

Ez a parancs 0 értékre állítja a fDenyTSConnections beállításazonosítót, távoli asztali kapcsolatok engedélyezése.

## <a name="next-steps"></a>További lépések
Ha az Azure-beli Virtuálisgép-hozzáférési bővítmény nem válaszol, és nem tudja alaphelyzetbe állítani a jelszót, [a helyi Windows-jelszó alaphelyzetbe állítása offline](../reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a módszer egy speciális folyamat, és a virtuális merevlemez a problémás virtuális gép egy másik virtuális Géphez való csatlakozáshoz szükséges. Kövesse a jelen cikkben leírt első lépéseket, és csak végső megoldásként az offline jelszó alaphelyzetbe állítása metódus.

[Az Azure Virtuálisgép-bővítmények és szolgáltatások](../extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Csatlakozhat egy Azure virtuális gépekhez RDP vagy SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Egy Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](../troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


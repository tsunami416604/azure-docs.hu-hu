---
title: Távoli asztali szolgáltatások vagy rendszergazdai jelszavának alaphelyzetbe állítása Windows rendszerű virtuális gépen | Microsoft Docs
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a fiók jelszavát vagy Távoli asztali szolgáltatások egy Windows rendszerű virtuális gépen a Azure Portal vagy a Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: 580ec443dc087f270e30856c336a5699bbf1ae71
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "71058445"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Távoli asztali szolgáltatások vagy rendszergazdai jelszavának alaphelyzetbe állítása Windows rendszerű virtuális gépen
Ha nem tud csatlakozni egy Windows rendszerű virtuális géphez (VM), alaphelyzetbe állíthatja a helyi rendszergazda jelszavát, vagy alaphelyzetbe állíthatja a Távoli asztali szolgáltatások konfigurációt (Windows-tartományvezérlőkön nem támogatott). Új jelszó kéréséhez használja az Azure Portalt vagy az Azure PowerShell virtuálisgép-hozzáférési bővítményét. Miután bejelentkezett a virtuális gépre, kérjen új jelszót a helyi rendszergazda számára.  
Ha a PowerShellt használja, győződjön meg arról, hogy [telepítve van és konfigurálva van a legújabb PowerShell-modul](/powershell/azure/overview) , és be van jelentkezve az Azure-előfizetésbe. Ezeket a [lépéseket a klasszikus üzemi modellel létrehozott virtuális gépeknél is elvégezheti](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

A távoli asztali szolgáltatásokat és a hitelesítő adatokat a következő módon állíthatja vissza:

- [Alaphelyzetbe állítás a Azure Portal használatával](#reset-by-using-the-azure-portal)

- [Alaphelyzetbe állítás a VMAccess-bővítmény és a PowerShell használatával](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Alaphelyzetbe állítás a Azure Portal használatával

Először jelentkezzen be a [Azure Portal](https://portal.azure.com) , majd a bal oldali menüben válassza a **virtuális gépek** lehetőséget. 

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

1. Válassza ki a Windows rendszerű virtuális gépet, majd válassza a **jelszó alaphelyzetbe állítása** a **támogatás + hibaelhárítás**lehetőséget. Megjelenik a **jelszó alaphelyzetbe állítása** ablak.

2. Válassza a **jelszó alaphelyzetbe állítása**lehetőséget, adjon meg egy felhasználónevet és egy jelszót, majd válassza a **frissítés**lehetőséget. 

3. Próbálkozzon újra a virtuális géphez való csatlakozással.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A Távoli asztali szolgáltatások konfigurációjának alaphelyzetbe állítása**

Ez a folyamat engedélyezi Távoli asztal szolgáltatást a virtuális gépen, és hozzon létre egy tűzfalszabály-szabályt az alapértelmezett RDP-porthoz: 3389.

1. Válassza ki a Windows rendszerű virtuális gépet, majd válassza a **jelszó alaphelyzetbe állítása** a **támogatás + hibaelhárítás**lehetőséget. Megjelenik a **jelszó alaphelyzetbe állítása** ablak. 

2. Válassza a **csak konfiguráció alaphelyzetbe állítása** lehetőséget, majd válassza a **frissítés**lehetőséget. 

3. Próbálkozzon újra a virtuális géphez való csatlakozással.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Alaphelyzetbe állítás a VMAccess-bővítmény és a PowerShell használatával

Először is győződjön meg arról, hogy a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) van, és be van jelentkezve az Azure-előfizetésbe a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

- Állítsa vissza a rendszergazdai jelszót vagy a felhasználónevet a [set-AzVMAccessExtension PowerShell-](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) parancsmaggal. A `typeHandlerVersion` beállításnak 2,0 vagy nagyobbnak kell lennie, mivel az 1. verzió elavult. 

    ```powershell
    $SubID = "<SUBSCRIPTION ID>" 
    $RgName = "<RESOURCE GROUP NAME>" 
    $VmName = "<VM NAME>" 
    $Location = "<LOCATION>" 
 
    Connect-AzAccount 
    Select-AzSubscription -SubscriptionId $SubID 
    Set-AzVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
    ```

    > [!NOTE] 
    > Ha a virtuális gépen a jelenlegi helyi rendszergazdai fióktól eltérő nevet ad meg, a VMAccess-bővítmény hozzá fog adni egy helyi rendszergazdai fiókot az adott névvel, és a megadott jelszót hozzárendeli a fiókhoz. Ha a virtuális gépen létezik a helyi rendszergazdai fiók, akkor a VMAccess-bővítmény visszaállítja a jelszót. Ha a fiók le van tiltva, akkor a VMAccess-bővítmény engedélyezi azt.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A Távoli asztali szolgáltatások konfigurációjának alaphelyzetbe állítása**

1. Állítsa vissza a virtuális gép távoli elérését a [set-AzVMAccessExtension PowerShell-](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) parancsmaggal. A következő példa alaphelyzetbe állítja a nevű hozzáférési bővítményt az `myVMAccess` erőforráscsoport nevű virtuális gépen `myVM` `myResourceGroup` :

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Egy virtuális gépnek csak egyetlen VM-hozzáférési ügynöke lehet. A virtuális gép hozzáférési ügynökének tulajdonságainak beállításához használja a következőt: `-ForceRerun` . A használatakor `-ForceRerun` Győződjön meg arról, hogy ugyanazt a nevet használja a virtuálisgép-hozzáférési ügynökhöz, amelyet az előző parancsokban használt.

1. Ha továbbra sem tud távolról kapcsolódni a virtuális géphez, tekintse meg a [Windows-alapú Azure-beli virtuális gépek távoli asztal kapcsolatainak hibaelhárításával foglalkozó](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)témakört. Ha elveszíti a Windows tartományvezérlőhöz való kapcsolódást, vissza kell állítania a tartományvezérlő biztonsági másolatából.

## <a name="next-steps"></a>További lépések

- Ha az Azure virtuálisgép-hozzáférési bővítmény nem válaszol, és nem tudja alaphelyzetbe állítani a jelszót, [alaphelyzetbe állíthatja a helyi Windows-jelszót](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a módszer fejlettebb, és megköveteli a problémás virtuális gép virtuális merevlemezének egy másik virtuális géphez való összekapcsolását. Kövesse az ebben a cikkben ismertetett lépéseket először, és csak akkor próbálkozzon az offline jelszó-visszaállítási módszerekkel, ha ezek a lépések nem működnek.

- [Ismerje meg az Azure-beli virtuálisgép-bővítményeket és-funkciókat](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Kapcsolódás Azure-beli virtuális GÉPHEZ RDP vagy SSH használatával](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Windows-alapú Azure-beli virtuális gépekkel létesített távoli asztal-kapcsolatok hibáinak megoldása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


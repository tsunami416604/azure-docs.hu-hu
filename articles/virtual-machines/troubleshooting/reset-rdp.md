---
title: Távoli asztali szolgáltatások alaphelyzetbe állítása vagy rendszergazdai jelszavának visszaállítása Windows virtuális gépben | Microsoft dokumentumok
description: Megtudhatja, hogyan állíthatja alaphelyzetbe a fiók jelszavát vagy a Távoli asztali szolgáltatásokat egy Windows virtuális gépen az Azure Portalon vagy az Azure PowerShellen keresztül.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058445"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Távoli asztali szolgáltatások vagy rendszergazdai jelszó alaphelyzetbe állítása Windows virtuális gépben
Ha nem tud csatlakozni egy Windows virtuális géphez, alaphelyzetbe állíthatja a helyi rendszergazdai jelszót, vagy alaphelyzetbe állíthatja a Távoli asztali szolgáltatások konfigurációját (a Windows tartományvezérlők nem támogatják). Új jelszó kéréséhez használja az Azure Portalt vagy az Azure PowerShell virtuálisgép-hozzáférési bővítményét. Miután bejelentkezett a virtuális gépre, kérjen új jelszót a helyi rendszergazda számára.  
PowerShell használata esetén győződjön meg arról, hogy a [legújabb PowerShell-modul telepítve van és konfigurálva](/powershell/azure/overview) van, és be van jelentkezve az Azure-előfizetésbe. Ezeket a [lépéseket a klasszikus üzemi modellel létrehozott virtuális gépeknél is elvégezheti](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

A távoli asztali szolgáltatásokat és a hitelesítő adatokat a következő módon állíthatja vissza:

- [Alaphelyzetbe állítás az Azure Portal használatával](#reset-by-using-the-azure-portal)

- [Alaphelyzetbe állítás a VMAccess-bővítmény és a PowerShell használatával](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Alaphelyzetbe állítás az Azure Portal használatával

Először jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és válassza **a virtuális gépek** a bal oldali menüben. 

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

1. Jelölje ki a Windows virtuális gépét, majd válassza **a Jelszó alaphelyzetbe állítása** csoportban a **Támogatás + Hibaelhárítás lehetőséget.** Megjelenik **a Jelszó alaphelyzetbe állítása** ablak.

2. Válassza **a Jelszó alaphelyzetbe állítása**lehetőséget, adjon meg egy felhasználónevet és egy jelszót, majd válassza **a Frissítés**lehetőséget. 

3. Próbálkozzon újra a virtuális géphez való csatlakozáshoz.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A Távoli asztali szolgáltatások konfigurációjának alaphelyzetbe állítása**

Ez a folyamat engedélyezi a távoli asztali szolgáltatást a virtuális gépben, és tűzfalszabályt hoz létre az alapértelmezett 3389-es RDP-porthoz.

1. Jelölje ki a Windows virtuális gépét, majd válassza **a Jelszó alaphelyzetbe állítása** csoportban a **Támogatás + Hibaelhárítás lehetőséget.** Megjelenik **a Jelszó alaphelyzetbe állítása** ablak. 

2. Válassza **a Csak konfiguráció alaphelyzetbe állítása,** majd a **Frissítés**lehetőséget. 

3. Próbálkozzon újra a virtuális géphez való csatlakozáshoz.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Alaphelyzetbe állítás a VMAccess-bővítmény és a PowerShell használatával

Először győződjön meg arról, hogy a [legújabb PowerShell-modul telepítve van és konfigurálva](/powershell/azure/overview) van, és a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával be van jelentkezve az Azure-előfizetésbe.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

- Állítsa alaphelyzetbe a rendszergazdai jelszót vagy a felhasználónevet a [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell-parancsmaggal. A `typeHandlerVersion` beállításnak legalább 2.0-nak kell lennie, mert az 1. 

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
    > Ha a virtuális gépaktuális helyi rendszergazdai fiókjától eltérő nevet ad meg, a VMAccess-bővítmény hozzáad egy helyi rendszergazdai fiókot ezzel a névvel, és hozzárendeli a megadott jelszót a fiókhoz. Ha a helyi rendszergazdai fiók a virtuális gép létezik, a VMAccess-bővítmény alaphelyzetbe állítja a jelszót. Ha a fiók le van tiltva, a VMAccess-bővítmény engedélyezi azt.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A Távoli asztali szolgáltatások konfigurációjának alaphelyzetbe állítása**

1. Állítsa alaphelyzetbe a virtuális gép távoli elérését a [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell-parancsmaggal. A következő példa visszaállítja `myVMAccess` az `myVM` `myResourceGroup` erőforráscsoportban megnevezett virtuális gépen megnevezett hozzáférési bővítményt:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > A virtuális gép bármikor csak egy virtuális gép hozzáférési ügynöke rendelkezhet. A virtuális gép hozzáférési ügynök tulajdonságainak beállításához használja ezt a `-ForceRerun` beállítást. Ha a `-ForceRerun`használata, győződjön meg arról, hogy ugyanazt a nevet használja a virtuális gép hozzáférési ügynök, amely bármely korábbi parancsokban használt.

1. Ha továbbra sem tud távolról csatlakozni a virtuális géphez, olvassa el a [Távoli asztali kapcsolatok hibaelhárítása Windows-alapú Azure-virtuális géppel című témakört.](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Ha megszakad a kapcsolat a Windows tartományvezérlővel, vissza kell állítania a tartományvezérlő biztonsági másolatából.

## <a name="next-steps"></a>További lépések

- Ha az Azure VM access bővítmény nem válaszol, és nem tudja alaphelyzetbe állítani a jelszót, [offline állapotba állíthatja a helyi Windows-jelszót.](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Ez a módszer fejlettebb, és megköveteli, hogy csatlakoztassa a problémás virtuális gép virtuális merevlemezét egy másik virtuális géphez. Először kövesse a cikkben ismertetett lépéseket, és csak akkor kísérelje meg az offline jelszó-visszaállítási módszert, ha ezek a lépések nem működnek.

- [Ismerje meg az Azure virtuálisgép-bővítményeket és -funkciókat.](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

- [Csatlakozzon egy Azure virtuális géprdp vagy SSH.](https://msdn.microsoft.com/library/azure/dn535788.aspx)

- [A Windows-alapú Azure virtuális géppel létesített távoli asztali kapcsolatok – problémamegoldás](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


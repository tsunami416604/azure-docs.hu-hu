---
title: Távoli asztali szolgáltatások vagy a rendszergazdai jelszót egy Windows virtuális gép alaphelyzetbe állítása |} A Microsoft Docs
description: Ismerje meg, hogy egy fiók jelszavát, vagy a távoli asztali szolgáltatások a Windows virtuális gép visszaállítása az Azure portal vagy Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/25/2019
ms.author: genli
ms.openlocfilehash: c61e606bde4b50fa10c194c76c79a3d8a27a4b8e
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407691"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Távoli asztali szolgáltatások vagy a rendszergazdai jelszót egy Windows virtuális gép visszaállítása
Ha nem tud csatlakozni egy Windows virtuális gép (VM), új helyi rendszergazdai jelszót, vagy alaphelyzetbe állítása a távoli asztali szolgáltatások-konfiguráció (Windows rendszerű tartományvezérlőkön nem támogatott). Új jelszó kéréséhez használja az Azure Portalt vagy az Azure PowerShell virtuálisgép-hozzáférési bővítményét. Miután bejelentkezett a virtuális gépre, kérjen új jelszót a helyi rendszergazda számára.  
Ha a PowerShell használata esetén győződjön meg arról, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) és az Azure-előfizetéshez van bejelentkezve. Ezeket a [lépéseket a klasszikus üzemi modellel létrehozott virtuális gépeknél is elvégezheti](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

A következő módokon állíthatják alaphelyzetbe a távoli asztali szolgáltatások és a hitelesítő adatokat:

- [Állítsa alaphelyzetbe az Azure portal használatával](#reset-by-using-the-azure-portal)

- [Állítsa alaphelyzetbe a VMAccess bővítmény és a PowerShell használatával](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Állítsa alaphelyzetbe az Azure portal használatával

Első lépésként jelentkezzen be a [az Azure portal](https://portal.azure.com) majd **virtuális gépek** a bal oldali menüben. 

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

1. Válassza ki a Windows virtuális Gépet, majd **jelszó alaphelyzetbe állítása** alatt **támogatás + hibaelhárítás**. A **jelszó alaphelyzetbe állítása** ablak jelenik meg.

2. Válassza ki **jelszó alaphelyzetbe állítása**, és adjon meg egy felhasználónevet és jelszót, majd válassza ki **frissítés**. 

3. Próbáljon újra kapcsolódni a virtuális Géphez.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A távoli asztali szolgáltatások-konfiguráció visszaállítása**

Ez a folyamat engedélyezi a távoli asztali szolgáltatás a virtuális gépen, és hozzon létre egy tűzfalszabályt az alapértelmezett RDP-portra 3389-es.

1. Válassza ki a Windows virtuális Gépet, majd **jelszó alaphelyzetbe állítása** alatt **támogatás + hibaelhárítás**. A **jelszó alaphelyzetbe állítása** ablak jelenik meg. 

2. Válassza ki **csak a konfiguráció alaphelyzetbe állítása** majd **frissítés**. 

3. Próbáljon újra kapcsolódni a virtuális Géphez.

## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Állítsa alaphelyzetbe a VMAccess bővítmény és a PowerShell használatával

Győződjön meg róla, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) bejelentkezett az Azure-előfizetés használatával, és a [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmagot.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

- A rendszergazdai jelszót vagy a felhasználó elé alaphelyzetbe állítása a [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell-parancsmagot. A `typeHandlerVersion` beállítás kell lennie 2.0-s vagy újabb verziójú, mert az 1. verzió elavult. 

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
    > Ha a virtuális Gépen egy másik nevet, mint a jelenlegi helyi rendszergazdai fiókot ad meg, a VMAccess bővítmény fog adjon hozzá egy helyi rendszergazdai fiók, ezt a nevet, és rendelje hozzá a megadott jelszót a fiókhoz. Ha a virtuális Gépen a helyi rendszergazdai fiók létezik, a VMAccess bővítmény alaphelyzetbe állítja a jelszót. Ha a fiók le van tiltva, a VMAccess bővítmény engedélyezi azt.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A távoli asztali szolgáltatások-konfiguráció visszaállítása**

1. Állítsa alaphelyzetbe a távelérést a virtuális géphez a [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell-parancsmagot. A következő példa alaphelyzetbe állítja a hozzáférési bővítmény nevű `myVMAccess` nevű virtuális gépre `myVM` a a `myResourceGroup` erőforráscsoportot:

    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
    ```

    > [!TIP]
    > Bármikor a virtuális gép csak egyetlen Virtuálisgép-hozzáférési ügynök rendelkezhet. A Virtuálisgép-hozzáférési ügynök tulajdonságainak beállításához használja a `-ForceRerun` lehetőséget. Ha használ `-ForceRerun`, győződjön meg arról, használhatja ugyanazt a nevet a Virtuálisgép-hozzáférési ügynök, amely az előző parancs használhat.

1. Ha még nem sikerül távolról a virtuális géphez, tekintse meg [távoli asztali kapcsolatainak hibaelhárításával egy Windows-alapú Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ha megszakad a kapcsolat Windows tartományvezérlőn, szüksége lesz egy tartomány tartományvezérlői biztonsági másolatból állítsa vissza.

## <a name="next-steps"></a>További lépések

- Ha az Azure-beli Virtuálisgép-hozzáférési bővítmény nem válaszol, és nem tud a jelszó alaphelyzetbe állítása, [a helyi Windows-jelszó alaphelyzetbe állítása offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a módszer van speciális, és a virtuális merevlemez a problémás virtuális gép egy másik virtuális Géphez való csatlakozáshoz szükséges. Először ebben a cikkben leírt lépéseket követve, és próbálja meg a kapcsolat nélküli jelszó alaphelyzetbe állítása metódus csak akkor, ha ezek a lépések nem működnek.

- [További tudnivalók az Azure-beli Virtuálisgép-bővítmények és szolgáltatások](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

- [Csatlakozhat egy Azure virtuális gépekhez RDP vagy SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx).

- [Egy Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 470834da9e9a571594789dedcbb0a67b55abd799
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49953712"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Távoli asztali szolgáltatások vagy a rendszergazdai jelszót egy Windows virtuális gép visszaállítása
Ha nem tud csatlakozni egy Windows virtuális gép (VM), új helyi rendszergazdai jelszót, vagy alaphelyzetbe állítása a távoli asztali szolgáltatások-konfiguráció (Windows rendszerű tartományvezérlőkön nem támogatott). A jelszó alaphelyzetbe állítása, használhatja az Azure PowerShell vagy az Azure Portalon, vagy a Virtuálisgép-hozzáférési bővítmény. Miután bejelentkezett a virtuális géphez, a jelszó alaphelyzetbe, hogy helyi rendszergazda.  
Ha a PowerShell használata esetén győződjön meg arról, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) és az Azure-előfizetéshez van bejelentkezve. Emellett [ezeket a lépéseket a klasszikus üzemi modellel létrehozott virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

A következő módokon állíthatják alaphelyzetbe a távoli asztali szolgáltatások és a hitelesítő adatokat:

- [Állítsa alaphelyzetbe az Azure portal használatával](#reset-by-using-the-azure-portal)
- [Állítsa alaphelyzetbe a VMAccess bővítmény és a PowerShell használatával](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Állítsa alaphelyzetbe az Azure portal használatával
Jelentkezzen be az Azure Portalon, majd **virtuális gépek** a bal oldali menüben.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

Válassza ki a Windows virtuális Gépet, majd **jelszó alaphelyzetbe állítása** alatt **támogatás + hibaelhárítás**. A **jelszó alaphelyzetbe állítása** ablak jelenik meg.

Válassza ki **jelszó alaphelyzetbe állítása**, és adjon meg egy felhasználónevet és jelszót, majd válassza ki **frissítés**. Próbáljon újra kapcsolódni a virtuális Géphez.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A távoli asztali szolgáltatások-konfiguráció visszaállítása**

Válassza ki a Windows virtuális Gépet, majd **jelszó alaphelyzetbe állítása** alatt **támogatás + hibaelhárítás**. A **jelszó alaphelyzetbe állítása** ablak jelenik meg. 

Válassza ki **csak a konfiguráció alaphelyzetbe állítása** majd **frissítés**. Próbáljon újra kapcsolódni a virtuális Géphez.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>Állítsa alaphelyzetbe a VMAccess bővítmény és a PowerShell használatával
Győződjön meg arról, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) bejelentkezett az Azure-előfizetés használatával, és a [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) parancsmagot.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**
A rendszergazdai jelszót vagy a felhasználó elé alaphelyzetbe állítása a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. A `typeHandlerVersion` beállítás kell lennie 2.0-s vagy újabb verziójú, mert az 1. verzió elavult. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> Ha a virtuális Gépen egy másik nevet, mint a jelenlegi helyi rendszergazdai fiókot ad meg, a VMAccess bővítmény fog adjon hozzá egy helyi rendszergazdai fiók, ezt a nevet, és rendelje hozzá a megadott jelszót a fiókhoz. Ha a virtuális Gépen a helyi rendszergazdai fiók létezik, a VMAccess bővítmény alaphelyzetbe állítja a jelszót. Ha a fiók le van tiltva, a VMAccess bővítmény engedélyezi azt.

### <a name="reset-the-remote-desktop-services-configuration"></a>**A távoli asztali szolgáltatások-konfiguráció visszaállítása**
Állítsa alaphelyzetbe a távelérést a virtuális géphez a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. A következő példa alaphelyzetbe állítja a hozzáférési bővítmény nevű `myVMAccess` nevű virtuális gépre `myVM` a a `myResourceGroup` erőforráscsoportot:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> Bármikor a virtuális gép csak egyetlen Virtuálisgép-hozzáférési ügynök rendelkezhet. A Virtuálisgép-hozzáférési ügynök tulajdonságainak beállításához használja a `-ForceRerun` lehetőséget. Ha használ `-ForceRerun`, győződjön meg arról, használhatja ugyanazt a nevet a Virtuálisgép-hozzáférési ügynök, amely az előző parancs használhat.

Ha még nem sikerül távolról a virtuális géphez, tekintse meg [távoli asztali kapcsolatainak hibaelhárításával egy Windows-alapú Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ha megszakad a kapcsolat Windows tartományvezérlőn, szüksége lesz egy tartomány tartományvezérlői biztonsági másolatból állítsa vissza.

## <a name="next-steps"></a>További lépések
Ha az Azure-beli Virtuálisgép-hozzáférési bővítmény nem válaszol, és nem tud a jelszó alaphelyzetbe állítása, [a helyi Windows-jelszó alaphelyzetbe állítása offline](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ez a módszer van speciális, és a virtuális merevlemez a problémás virtuális gép egy másik virtuális Géphez való csatlakozáshoz szükséges. Először ebben a cikkben leírt lépéseket követve, és próbálja meg a kapcsolat nélküli jelszó alaphelyzetbe állítása metódus csak akkor, ha ezek a lépések nem működnek.

[További tudnivalók az Azure-beli Virtuálisgép-bővítmények és szolgáltatások](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Csatlakozhat egy Azure virtuális gépekhez RDP vagy SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Egy Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


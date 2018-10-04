---
title: A jelszó vagy távoli asztal konfigurálásának a Windows virtuális gép alaphelyzetbe állítása |} A Microsoft Docs
description: Ismerje meg, hogy alaphelyzetbe állítása, a fiók jelszavát, vagy a távoli asztali szolgáltatások Windows virtuális gép az Azure portal vagy Azure PowerShell használatával.
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
ms.date: 03/23/2018
ms.author: genli
ms.openlocfilehash: 08461811203232d5db1ae9c8f34f4ac180b6b0ce
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268300"
---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>A távoli asztali szolgáltatás vagy a bejelentkezési jelszót egy Windows virtuális gép visszaállítása
Ha nem tud csatlakozni egy Windows virtuális gép (VM), a helyi rendszergazdai jelszó visszaállítása, vagy alaphelyzetbe állítása a távoli asztal szolgáltatás konfigurációját (a Windows rendszerű tartományvezérlők nem támogatott). Használhatja az Azure Portalon vagy a Virtuálisgép-hozzáférési bővítmény az Azure PowerShell a jelszó alaphelyzetbe állítása. Miután bejelentkezett a virtuális gép, vissza kell állítania, hogy a felhasználó jelszavát.  
Ha a Powershellt használ, győződjön meg arról, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) és az Azure-előfizetéshez van bejelentkezve. Emellett [ezeket a lépéseket a klasszikus üzemi modellel létrehozott virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp).

## <a name="ways-to-reset-configuration-or-credentials"></a>Konfiguráció vagy a hitelesítő adatok alaphelyzetbe állítása módjai
Visszaállíthatja a távoli asztali szolgáltatások és a hitelesítő adatok néhány más-más módon igényeitől függően:

- [Alaphelyzetbe állítása, az Azure portal használatával](#azure-portal)
- [Új Azure PowerShell-lel](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure Portal
Bontsa ki a menü a portálon, kattintson a bal felső sarkában található három sávra, és kattintson a **virtuális gépek**:

![Keresse meg az Azure virtuális gép](./media/reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**

Válassza ki a Windows virtuális gépet, majd kattintson a **támogatás + hibaelhárítás** > **jelszó alaphelyzetbe állítása**. A jelszó alaphelyzetbe állítása panel jelenik meg:

![Jelszó alaphelyzetbe állítására szolgáló lap](./media/reset-rdp/Portal-RM-PW-Reset-Windows.png)

Adja meg a felhasználónevet és a egy új jelszót, majd kattintson a **frissítés**. Próbáljon újra kapcsolódni a virtuális Géphez.

### <a name="reset-the-remote-desktop-service-configuration"></a>**A távoli asztal szolgáltatás konfigurációját alaphelyzetbe állítása**

Válassza ki a Windows virtuális gépet, majd kattintson a **támogatás + hibaelhárítás** > **jelszó alaphelyzetbe állítása**. A jelszó alaphelyzetbe állítása panel jelenik meg. 

![RDP-konfiguráció visszaállítása](./media/reset-rdp/Portal-RM-RDP-Reset.png)

Válassza ki **csak a konfiguráció alaphelyzetbe állítása** elemet a legördülő menüben, majd kattintson **frissítés**. Próbáljon újra kapcsolódni a virtuális Géphez.


## <a name="vmaccess-extension-and-powershell"></a>VMAccess bővítmény és a PowerShell
Győződjön meg arról, hogy rendelkezik-e a [legújabb PowerShell-modul telepítve és konfigurálva](/powershell/azure/overview) van bejelentkezve az Azure-előfizetésbe, és a `Connect-AzureRmAccount` parancsmagot.

### <a name="reset-the-local-administrator-account-password"></a>**A helyi rendszergazdai fiók jelszavának alaphelyzetbe állítása**
A rendszergazdai jelszót vagy a felhasználó elé alaphelyzetbe állítása a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. 1. verzió elavult a typeHandlerVersion 2.0-s vagy újabb, kell lennie. 

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
> Ha az aktuális helyi rendszergazdai fiók, mint egy másik nevet a virtuális Gépen, a VMAccess bővítmény fog adjon hozzá egy helyi rendszergazdai fiók, ezt a nevet, és rendelje hozzá a megadott jelszót a fiókhoz. Ha a virtuális Gépen a helyi rendszergazdai fiók létezik, azt alaphelyzetbe állítja a jelszót, és a fiók le van tiltva, ha a VMAccess bővítmény lehetővé teszi.

### <a name="reset-the-remote-desktop-service-configuration"></a>**A távoli asztal szolgáltatás konfigurációját alaphelyzetbe állítása**
Állítsa alaphelyzetbe a távelérést a virtuális géphez a [Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell-parancsmagot. A következő példa alaphelyzetbe állítja a hozzáférési bővítmény nevű `myVMAccess` nevű virtuális gépre `myVM` a a `myResourceGroup` erőforráscsoportot:

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> Bármikor a virtuális gép csak egyetlen Virtuálisgép-hozzáférési ügynök rendelkezhet. A Virtuálisgép-hozzáférési ügynök tulajdonságainak beállítása sikeresen megtörtént, a `-ForceRerun` lehetőség használható. Használata esetén `-ForceRerun`, ügyeljen arra, hogy ugyanazt a nevet a Virtuálisgép-hozzáférési ügynök, mint az előző parancs használata.

Ha még nem sikerül távolról a virtuális géphez, tekintse meg a további lépés szükséges próbálja meg [távoli asztali kapcsolatainak hibaelhárításával egy Windows-alapú Azure virtuális gép](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Ha a Windows tartományvezérlő kapcsolat megszakad, szüksége lesz egy tartomány tartományvezérlői biztonsági másolatból állítsa vissza.

## <a name="next-steps"></a>További lépések
Ha az Azure-beli Virtuálisgép-hozzáférési bővítmény nem válaszol, és nem tudja alaphelyzetbe állítani a jelszót, [a helyi Windows-jelszó alaphelyzetbe állítása offline](../windows/reset-local-password-without-agent.md). Ez a módszer egy speciális folyamat, és a virtuális merevlemez a problémás virtuális gép egy másik virtuális Géphez való csatlakozáshoz szükséges. Kövesse a jelen cikkben leírt első lépéseket, és csak végső megoldásként az offline jelszó alaphelyzetbe állítása metódus.

[Az Azure Virtuálisgép-bővítmények és szolgáltatások](../extensions/features-windows.md)

[Csatlakozhat egy Azure virtuális gépekhez RDP vagy SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Egy Windows-alapú Azure virtuális gép távoli asztali kapcsolatok hibaelhárítása](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


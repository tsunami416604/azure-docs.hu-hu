---
title: A virtuális gépek kezelése az Azure PowerShell használatával |} Microsoft Docs
description: Ismerje meg, amely segítségével automatizálhatja a feladatokat a virtuális gépek kezelése a parancsok.
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
ms.openlocfilehash: 942141fad09e6233efc7f850212a73f8a39c163c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>A virtuális gépek kezelése az Azure PowerShell-lel
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Általános PowerShell-parancsokat a Resource Manager modellt használja, lásd: [Itt](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Ezt megteheti, hogy a virtuális gépek kezelése a napi feladatok automatizálhatók Azure PowerShell-parancsmagok használatával. Ez a cikk ad Példaparancsok egyszerűbb feladatokat, és hivatkozásokat tartalmaz, amelyek megjelenítik a parancsokat az összetettebb feladatok cikkek.

> [!NOTE]
> Ha még nem telepített és konfigurált Azure PowerShell, még a cikkben szereplő útmutatást kaphat [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>A példa parancsok használata
Cserélje le a parancsokban a keresett szöveg, amely a környezetnek megfelelő lesz szüksége. A < és > szimbólumokat kell cserélnie Szöveg jelzi. Ha a szöveg, távolítsa el a szimbólumok, de hagyja meg az ajánlat közötti helyen.

## <a name="get-a-vm"></a>A virtuális gép beolvasása
Ez a gyakran használt egyszerű feladat. Ezzel a virtuális gép adatainak beolvasása, a feladatok végrehajtása a virtuális gép vagy a tárolható egy változóban az beszerzése kimeneti.

A virtuális gép kapcsolatos információért futtassa ezt a parancsot, cseréje az ajánlatokat, beleértve a tartalmát a < és > karakter:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

A kimeneti $vm változó tárolja, futtassa:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Jelentkezzen be a Windows-alapú virtuális gépek
Futtassa az alábbi parancsokat:

> [!NOTE]
> A virtuális gép és felhő szolgáltatásnév lekérheti a megjelenítéséhez használatos a **Get-AzureVM** parancsot.
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "< meghajtót és mappaútvonalat a letöltött RDP-fájl tárolási helyét példa: c:\temp >" $localFile = $localPath + "\" $vmname +".rdp"Get-AzureRemoteDesktopFile - ServiceName $svcName-Name $vmName - LocalPath $localFile-elindítása
> 
> 

## <a name="stop-a-vm"></a>Virtuális gép leállítása
Futtassa ezt a parancsot:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Ez a paraméter használatával tartani a virtuális IP-cím (VIP) a felhőalapú szolgáltatás, amely a felhőszolgáltatás utolsó virtuális gép esetén. <br><br> Ha a StayProvisioned paraméter használata esetén a fogjuk továbbra is számlázni a virtuális gép.
> 
> 

## <a name="start-a-vm"></a>Virtuális gép elindítása
Futtassa ezt a parancsot:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Ez a feladat néhány lépést igényel. Először is használhatja a x Add-AzureDataDisk x parancsmag használatával adja hozzá a lemezt a $vm objektum. Ezután **frissítés-AzureVM** parancsmag használatával frissítse a virtuális gép konfigurációját.

El kell döntenie, hogy új vagy adatokat már tartalmazó lemezt szeretne-e csatolni. Új lemez a parancs a .vhd fájlt hoz létre, és csatolja.

Új lemez csatolásához futtassa ezt a parancsot:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Már meglévő adatlemez csatolásakor futtassa ezt a parancsot:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

A blob Storage tárolóban meglévő .vhd fájl adatlemezt csatolni futtassa ezt a parancsot:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Windows-alapú virtuális gép létrehozása
Hozzon létre egy új Windows-alapú virtuális gépet az Azure-ban, kövesse az utasításokat a [Azure PowerShell használata hozhat létre és konfigurálja a Windows-alapú virtuális gépek előre](create-powershell.md). Ez a témakör lépéseit meg kell létrehozni az Azure PowerShell paranccsal állítsa be, amely létrehoz egy Windows-alapú virtuális Gépet, amely megfelelően előre beállíthatók:

* Az Active Directory tartományi tagságát.
* A további lemezek.
* Elosztott terhelésű meglévő tagjaként beállítása.
* Statikus IP-címmel.


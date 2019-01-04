---
title: A virtuális gépek kezelése az Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, amelyek segítségével automatizálhatja a feladatokat a virtuális gépek kezeléséhez.
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
ms.openlocfilehash: b7fafa148417ba1667ec0277b414105f95e428ce
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971784"
---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>A virtuális gépek kezelése az Azure PowerShell-lel
> [!IMPORTANT] 
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Gyakori PowerShell-parancsok a Resource Manager modellt használja, lásd: [Itt](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Számos feladatot végezhet el, minden nap, a virtuális gépek kezelése az Azure PowerShell-parancsmagok használatával automatizálható. Ez a cikk megtalálható Példaparancsok egyszerűbb feladatokat, és hivatkozásokat tartalmaz olyan cikkekre, amelyek a parancsok az összetettebb feladatok megjelenítése.

> [!NOTE]
> Ha még nem telepítette és konfigurálta az Azure PowerShell, még a cikk útmutatást kaphat [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).
> 
> 

## <a name="how-to-use-the-example-commands"></a>A példa parancsok használata
Cserélje le a parancsokban szereplő a keresett szöveg, amely a környezetnek megfelelő kell. A < és > szimbólumok azt jelzik, ki kell cserélni a szöveget. Ha lecseréli a szöveget, távolítsa el szimbólumok, de hagyja meg az ajánlat együtt helyen.

## <a name="get-a-vm"></a>A virtuális gép lekérése
Ez a alapvető feladat gyakran fogja használni. Használhatja egy virtuális gép adatainak lekérése, a virtuális gép feladatait vagy első kimeneti tárolja egy változóban.

Információt szeretne kapni a virtuális gép, futtassa a parancsot, és mindent az ajánlatokat, többek között a < és > karakterek:

    Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

A kimenet egy $vm változóban tárolja, futtassa:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Jelentkezzen be a Windows-alapú virtuális gép
Futtassa a következő parancsokat:

> [!NOTE]
> A virtuális gép és a felhőalapú szolgáltatás neve kérhet le a megjelenítését a **Get-AzureVM** parancsot.
> 
> $svcName = `"<cloud service name>"` $vmName = `"<virtual machine name>"` $localPath = `"<drive and folder location to store the downloaded RDP file, example: c:\temp >"` $localFile = $localPath + "\" $vmname +".rdp"get-AzureRemoteDesktopFile - ServiceName $svcName-$vmName – LocalPath $localFile nevezze-indítása
> 
> 

## <a name="stop-a-vm"></a>Virtuális gép leállítása
Futtassa ezt a parancsot:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> Használja ezt a paramétert, hogy a virtuális IP-cím (VIP) a felhőalapú szolgáltatás, abban az esetben ez az utolsó virtuális gép található, amely a felhőszolgáltatás. <br><br> A StayProvisioned paraméter használatakor a fog továbbra is felszámítjuk a virtuális Gépet.
> 
> 

## <a name="start-a-vm"></a>Virtuális gép elindítása
Futtassa ezt a parancsot:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Adatlemez csatolása
Ez a feladat néhány lépést igényel. Először is használhatja az x Add-AzureDataDisk x parancsmag használatával adja hozzá a lemezt a $vm objektum. Ezután **Update-AzureVM** parancsmag frissíti a virtuális gép konfigurációját.

El kell döntenie, hogy új vagy adatokat már tartalmazó lemezt szeretne-e csatolni. Új lemez a parancs a .vhd fájlt hoz létre, és csatolja azt.

Új lemez csatolásához futtassa ezt a parancsot:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Már meglévő adatlemez csatolásakor futtassa ezt a parancsot:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Adatlemez csatolása a blob storage-ban egy meglévő .vhd fájlból, a következő parancs futtatásával:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Windows-alapú virtuális gép létrehozása
Egy új Windows-alapú virtuális gép létrehozása az Azure-ban, használja a következő témakör utasításait [hozhat létre, és előre konfigurálása a Windows-alapú virtuális gépek Azure PowerShell használata](create-powershell.md). Ez a témakör lépéseit létrehozása az Azure PowerShell paranccsal állítsa be, amely bemutatja, hogyan hoz létre a megfelelően előre beállíthatók Windows-alapú virtuális Gépet:

* Az Active Directory tartományi tagságát.
* A további lemezeket.
* Egy meglévő elosztott terhelésű tagjaként beállítása.
* Egy statikus IP-címmel.


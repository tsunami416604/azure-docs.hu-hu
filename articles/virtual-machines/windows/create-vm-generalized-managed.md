---
title: Virtuális gép létrehozása felügyelt rendszerképből az Azure-ban |} A Microsoft Docs
description: Hozzon létre egy Windows virtuális gép rendszerképéből felügyelt Azure PowerShell vagy az Azure portal használatával a Resource Manager-alapú üzemi modellben.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 8acbb33b396aa617936eb0333bd68fea60532425
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404656"
---
# <a name="create-a-vm-from-a-managed-image"></a>Virtuális gép létrehozása felügyelt rendszerképből

Több virtuális gép (VM) az Azure felügyelt virtuális gépből hozhat létre az Azure portal vagy a PowerShell használatával kép. Egy felügyelt Virtuálisgép-rendszerképet a virtuális gép, beleértve az operációs rendszer és az adatlemezek létrehozásához szükséges információkat tartalmazza. A virtuális merevlemezek (VHD) a lemezképet, beleértve az operációsrendszer-lemez és bármely adatlemez alkotó felügyelt lemezként tárolt. 

Egy új virtuális Gépet létrehozni, mielőtt kell [felügyelt Virtuálisgép-lemezkép létrehozása](capture-image-resource.md) forrásaként használandó. 

## <a name="use-the-portal"></a>A portál használata

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. A bal oldali menüben válassza ki a **összes erőforrás**. Rendezheti az erőforrásokat a **típus** használatával könnyedén megtalálhatja a lemezképeket.
3. Válassza ki a listából a használni kívánt kép. A kép **áttekintése** lap megnyitásakor.
4. Válassza ki **hozzon létre virtuális gép** a menüből.
5. Adja meg a virtuális gép adatait. A felhasználónév és jelszó itt megadott jelentkezzen be a virtuális gépen használható. Amikor végzett, válassza ki a **OK**. Az új virtuális gép létrehozása egy meglévő erőforráscsoportot, vagy válasszon **új létrehozása** , hozzon létre egy új erőforráscsoportot a virtuális gép tárolásához.
6. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. 
7. A **beállítások**, szükség esetén végezze el a módosításokat, és válassza ki **OK**. 
8. Az összefoglalás lapon kell megjelennie a rendszerkép neve szerepel egy **privát rendszerkép**. Válassza ki **Ok** a virtuális gép üzembe helyezésének megkezdéséhez.


## <a name="use-powershell"></a>A PowerShell használata

Virtuális gép létrehozása egy rendszerképből az egyszerűsített paraméter beállított használhatja a Powershellt a [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) parancsmagot. A kép kell lennie, amelyeken kíván létrehozni a virtuális gép ugyanabban az erőforráscsoportban.

Ebben a példában van szükség az AzureRM-modul 5.6.0-s vagy újabb. A verzió azonosításához futtassa a következőt: ` Get-Module -ListAvailable AzureRM`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

Az egyszerűsített paraméterkészlet a [New-AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm) csak követeli meg, hogy biztosítson egy név, erőforráscsoport és rendszerkép neve hozhat létre virtuális Gépet egy rendszerképből. Új-AzureRmVm értékét fogja használni a **-név** paraméter az összes olyan erőforrást, amely automatikusan hoz létre nevet. Ebben a példában azt adja meg a részletes nevét az erőforrásokat, de lehetővé teszik a parancsmag automatikus létrehozása. Is létre erőforrásokat előzetesen, például a virtuális hálózathoz, és adja át az erőforrás neve a parancsmagba. Új-AzureRmVm fogja használni a meglévő erőforrások, ha akkor is neve szerint találja.

A következő példában létrehozunk egy nevű virtuális Gépet *myVMFromImage*, a a *myResourceGroup* erőforráscsoport rendszerkép *myImage*. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```



## <a name="next-steps"></a>További lépések
[Hozzon létre, és a Windows virtuális gépek kezelése az Azure PowerShell modullal](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


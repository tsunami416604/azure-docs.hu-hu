---
title: "Zoned Windows virtuális gép létrehozása az Azure portállal |} Microsoft Docs"
description: "Windows virtuális gép létrehozása az Azure portállal egy rendelkezésre állási zónában"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: dlepow
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: danlep
ms.custom: 
ms.openlocfilehash: 63c2baa2f3924cf2274608df98b854683bc2baa8
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="create-a-windows-virtual-machine-in-an-availability-zone-with-the-azure-portal"></a>Hozzon létre egy Windows rendszerű virtuális gép egy rendelkezésre állási zónát az Azure-portálon

Ez a cikk lépéseit az Azure portál használatával egy virtuális gép létrehozása az Azure rendelkezésre állási zónában. A [rendelkezésre állási zónák](../../availability-zones/az-overview.md) egy Azure-régió fizikailag elkülönített zónáit jelentik. Az alkalmazások és az adatok védelmét rendelkezésre állási zónákkal biztosíthatja nem várt hibák bekövetkezése, illetve a teljes adatközpont elérhetetlenné válása esetére.

[!INCLUDE [availability-zones-preview-statement.md](../../../includes/availability-zones-preview-statement.md)]


## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Kattintson a **hozzon létre egy erőforrást** az Azure portál bal felső sarokban.

2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. 

3. Adja meg a virtuális gép adatait. Az itt megadott felhasználónévvel és jelszóval bejelentkezhet a virtuális gépbe. Amikor végzett, kattintson az **OK** gombra.

    ![Írja be a virtuális gép alapvető adatait a portálpanelen](./media/create-portal-availability-zone/create-windows-vm-portal-basic-blade.png)

4. Válasszon méretet a virtuális gép számára. További méretek megjelenítéséhez válassza **Az összes megtekintése** lehetőséget, vagy módosítsa a **Támogatott lemeztípus** szűrőt. Ügyeljen arra, válasszon ki egy rendelkezésre állási zónák Preview, például a támogatott méreteknek *DS1_v2 szabványos*. 

    ![Képernyőkép a virtuális gépek méreteivel](./media/create-portal-availability-zone/create-linux-vm-portal-sizes.png)  

5. A **beállítások** > **magas rendelkezésre állású**, válasszon egyet a számozott zónák a **rendelkezésre állási zóna** legördülő menüből, hagyja a további alapértelmezett beállításokat, és Kattintson a **OK**.

    ![Válassza ki egy rendelkezésre állási zóna](./media/create-portal-availability-zone/create-linux-vm-portal-availability-zone.png)

6. Az összefoglalás lapon kattintson a **beszerzési** elindítani a virtuális gépek telepítése során.

7. A virtuális gép rögzítve lesz az Azure Portal irányítópultján. Az üzembe helyezés befejeztével a virtuális gép összefoglalás panelje automatikusan megnyílik.


## <a name="zone-for-ip-address-and-managed-disk"></a>IP-cím- és felügyeltlemez-zóna

Telepítésekor a virtuális gép egy rendelkezésre állási zónában, az IP-cím és a felügyelt-erőforrást telepít ugyanazon a rendelkezésre állási területen. Ellenőrizheti az Azure PowerShell zónájából. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

A következő példák az erőforrások adatainak beolvasása nevű erőforráscsoportban *myResourceGroup*. Helyettesítse be a virtuális gép létrehozásához használt erőforráscsoport nevét.

A nyilvános IP-cím a zóna található [Get-AzureRmPublicIpAddress](/en-us/powershell/module/azurerm.network/get-azurermpublicipaddress):

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup
```
A `Zones` kimenet beállítás mutatja, hogy a nyilvános IP-címet a virtuális géppel ugyanazon rendelkezésre állási területen:

```powershell
Name                     : myVM-ip
ResourceGroupName        : myResourceGroup
Location                 : eastus2
Id                       : /subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/danlep0911/providers/Micr
                           osoft.Network/publicIPAddresses/myVM-ip
Etag                     : W/"b67e14c0-7e8a-4d12-91c5-da2a5dfad132"
ResourceGuid             : 314bf57d-9b25-4474-9282-db3561d536aa
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.68.16.25
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/e44f251c-c67e-4760-9ed6-bf99a306ecff/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVM11842/ipConfigurations/ipconfig1"
                           }
DnsSettings              : null
Zones                    : {2}
```


A virtuális gép felügyeltlemez-erőforrása szintén ugyanebben a rendelkezésre állási zónában lett létrehozva. Ezt a [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk) használatával ellenőrizheti:

```powershell
Get-AzureRmDisk -ResourceGroupName myResourceGroup
```

A kimenetben az látható, hogy a felügyelt lemez és a virtuális gép azonos rendelkezésre állási zónában található:

```powershell
ResourceGroupName  : myResourceGroup
AccountType        : PremiumLRS
OwnerId            : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
ManagedBy          : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/virtualMachines/myVM
Sku                : Microsoft.Azure.Management.Compute.Models.DiskSku
Zones              : {2}
TimeCreated        : 9/7/2017 6:57:26 PM
OsType             : Windows
CreationData       : Microsoft.Azure.Management.Compute.Models.CreationData
DiskSizeGB         : 127
EncryptionSettings :
ProvisioningState  : Succeeded
Id                 : /subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.
                     Compute/disks/myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Name               : myVM_OsDisk_1_bd921920bb0a4650becfc2d830000000
Type               : Microsoft.Compute/disks
Location           : eastus2
Tags               : {}
```

## <a name="next-steps"></a>További lépések

Ebből a cikkből megtudhatta, hogyan hozható létre virtuális gép egy rendelkezésre állási zónában. Itt további információkat talál az Azure-beli virtuális gépek [régióiról és rendelkezésre állásáról](regions-and-availability.md).

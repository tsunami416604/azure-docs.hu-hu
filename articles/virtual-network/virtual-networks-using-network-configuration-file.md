---
title: Egy Azure virtuális hálózat konfigurálása (klasszikus) – hálózati konfigurációs fájl |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, és módosítsa a virtuális hálózatok (klasszikus), módosítás, importálása és exportálása révén a hálózati konfigurációs fájlt.
services: virtual-network
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: genli
ms.custom: ''
ms.openlocfilehash: 57ad5541bb7b61f8d26002168bb069fad3058965
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43339074"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Konfigurálja a hálózati konfigurációs fájl használatával virtuális hálózat (klasszikus)
> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzemi modell.

Hozzon létre, és a egy virtuális hálózat (klasszikus) konfigurálja a hálózati konfigurációs fájlt az Azure parancssori felület (CLI), 1.0-s vagy az Azure PowerShell használatával. Nem hozható létre, vagy módosítsa a virtuális hálózat az Azure Resource Manager-alapú üzemi modellel, a hálózati konfigurációs fájl használatával. Az Azure Portalon létrehozása vagy módosítása egy virtuális hálózat (klasszikus) használatával a hálózati konfigurációs fájlt az Azure portal segítségével hozzon létre egy virtuális hálózat (klasszikus), azonban nem használható a hálózati konfigurációs fájl nélkül.

Létrehozása és konfigurálása virtuális hálózat (klasszikus) egy hálózati konfigurációs fájllal szükséges exportálása, módosítása és a fájl importálásakor.

## <a name="export"></a>Exportálhatja a hálózati konfigurációs fájlt

PowerShell vagy az Azure CLI segítségével exportálhatja a hálózati konfigurációs fájlt. PowerShell XML-fájlba exportálja, amíg az Azure CLI exportálja egy json-fájlt.

### <a name="powershell"></a>PowerShell
 
1. [Telepítse az Azure Powershellt, és jelentkezzen be az Azure-bA](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Módosítsa a könyvtárat (és győződjön meg arról, hogy létezik-e) és a következő paranccsal igény szerint, majd futtassa a parancsot, exportálhatja a hálózati konfigurációs fájlt:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Az Azure CLI 1.0 telepítése](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Fejezze be az Azure CLI 1.0 parancsot egy parancssorból a hátralévő lépéseket.
2. Jelentkezzen be az Azure-bA írja be a `azure login` parancsot.
3. Győződjön meg arról, verziójában asm mód megadásával Ön a `azure config mode asm` parancsot.
4. Módosítsa a könyvtárat (és győződjön meg arról, hogy létezik-e) és a következő paranccsal igény szerint, majd futtassa a parancsot, exportálhatja a hálózati konfigurációs fájlt:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Hozzon létre vagy módosítsa a hálózati konfigurációs fájlt

A hálózati konfigurációs fájlt egy XML-fájlt (Ha a PowerShell használatával) vagy egy json-fájlt (használata esetén az Azure CLI). Szerkesztheti a fájlt bármilyen szöveget vagy XML vagy json-szerkesztőt. A [hálózati konfigurációs fájl séma beállítások](https://msdn.microsoft.com/library/azure/jj157100.aspx) cikk összes beállítások részleteit tartalmazza. A beállítások további ismertetése: [virtuális hálózatok és a beállítások megtekintéséhez](manage-virtual-network.md#view-virtual-networks-and-settings). A módosításokat a fájlhoz:

- Meg kell felelnie a séma vagy importálása sikertelen lesz, a hálózati konfigurációs fájlt.
- Az előfizetéshez tartozó minden olyan meglévő hálózati beállításokat, ezért járjon el körültekintően, ha a végzett módosítások. A példa hálózati konfigurációs fájlokat, amelyek például hivatkozhat. Tegyük fel, hogy az eredeti fájl található, két **VirtualNetworkSite** példányok, és módosítani, a példákban szemléltetett módon. Amikor importálja a fájlt, az Azure törli a virtuális hálózat számára a **VirtualNetworkSite** példány eltávolította a fájlban. Ezen egyszerű forgatókönyv feltételezi, hogy nincsenek erőforrások a virtuális hálózat is, ha vannak, a virtuális hálózat nem törölhető, és az importálás sikertelen lesz.

> [!IMPORTANT]
> Az Azure úgy ítéli meg, egy alhálózatot, amelyen rajta, valami **használatban**. Ha egy alhálózat használatban van, nem lehet módosítani. Alhálózati információ a hálózati konfigurációs fájl módosítása, előtt helyezze át, amelyeket az alhálózathoz, egy másik alhálózatot, amely nem módosított telepítése. Lásd: [áthelyezése egy másik alhálózatot a virtuális gép vagy Szerepkörpéldány](virtual-networks-move-vm-role-to-subnet.md) részleteiről.

### <a name="example-xml-for-use-with-powershell"></a>Például XML, a PowerShell segítségével

A következő példa hálózati konfigurációs fájlt egy nevű virtuális hálózatot hoz létre *myVirtualNetwork* -címtere a *10.0.0.0/16* a a *USA keleti Régiójában* Azure régió. A virtuális hálózat tartalmaz nevű alhálózattal *mySubnet* címelőtaggal rendelkező *10.0.0.0/24*.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
  <VirtualNetworkConfiguration>
    <Dns />
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myVirtualNetwork" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="mySubnet">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
  </VirtualNetworkConfiguration>
</NetworkConfiguration>
```

Ha a hálózati konfigurációs fájlt exportált nincs tartalom tartalmaz, az előző példában az XML-fájl másolása, és illessze be egy új fájlt.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Példa JSON-ban az Azure CLI 1.0 való használatra

A következő példa hálózati konfigurációs fájlt egy nevű virtuális hálózatot hoz létre *myVirtualNetwork* -címtere a *10.0.0.0/16* a a *USA keleti Régiójában* Azure régió. A virtuális hálózat tartalmaz nevű alhálózattal *mySubnet* címelőtaggal rendelkező *10.0.0.0/24*.

```json
{
   "VirtualNetworkConfiguration" : {
      "Dns" : "",
      "VirtualNetworkSites" : [
         {
            "AddressSpace" : [ "10.0.0.0/16" ],
            "Location" : "East US",
            "Name" : "myVirtualNetwork",
            "Subnets" : [
               {
                  "AddressPrefix" : "10.0.0.0/24",
                  "Name" : "mySubnet"
               }
            ]
         }
      ]
   }
}
```

Ha a hálózati konfigurációs fájlt exportált nincs tartalmát, másolja a json az előző példában, és illessze be egy új fájlt.

## <a name="import"></a>Hálózati konfigurációs fájl importálása

PowerShell vagy az Azure CLI használatával importálhatja a hálózati konfigurációs fájlt. PowerShell XML-fájlba importálja, bár az Azure CLI importál egy json-fájlt. Ha az importálás sikertelen lesz, ellenőrizze, hogy a fájl megfelel a [network konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Telepítse az Azure Powershellt, és jelentkezzen be az Azure-bA](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install).
2. Módosítsa a könyvtárat és a fájlnév szükség szerint, az alábbi parancsban majd a parancs futtatásával importálhatja a hálózati konfigurációs fájlt:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Az Azure CLI 1.0 telepítése](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Fejezze be az Azure CLI 1.0 parancsot egy parancssorból a hátralévő lépéseket.
2. Jelentkezzen be az Azure-bA írja be a `azure login` parancsot.
3. Győződjön meg arról, verziójában asm mód megadásával Ön a `azure config mode asm` parancsot.
4. Módosítsa a könyvtárat és a fájlnév szükség szerint, az alábbi parancsban majd a parancs futtatásával importálhatja a hálózati konfigurációs fájlt:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

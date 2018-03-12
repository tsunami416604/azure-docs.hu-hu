---
title: "Az Azure virtuális hálózat (klasszikus) - hálózati konfigurációs fájl konfigurálása |} Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre, és módosítsa a virtuális hálózatok (klasszikus), módosítása, importálása és exportálása révén a hálózati konfigurációs fájl."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c29b9059-22b0-444e-bbfe-3e35f83cde2f
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/23/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 1bb75b8bd1d525f3104fb517cb4a09b2e33caaca
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>A hálózati konfigurációs fájl segítségével virtuális hálózat (klasszikus) konfigurálása
> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén használja a Resource Manager üzembe helyezési modellben.

Hozzon létre, és konfigurálja a virtuális hálózat (klasszikus) a hálózati konfigurációs fájl az Azure parancssori felület (CLI) 1.0-s vagy az Azure PowerShell használatával. Nem lehet létrehozni vagy módosítani a virtuális hálózatot az Azure Resource Manager telepítési modell hálózati konfigurációs fájl használatával. Az Azure-portálon létrehozni vagy módosítani a hálózati konfigurációs fájllal, virtuális hálózat (klasszikus) Azure-portálon hozhat létre egy virtuális hálózat (klasszikus), de nem használható a hálózati konfigurációs fájl nélkül.

Létrehozása és konfigurálása a virtuális hálózat (klasszikus) a hálózati konfigurációs fájllal szükséges exportálása, módosítása és a fájl importálásakor.

## <a name="export"></a>A hálózati konfigurációs fájl exportálása

PowerShell vagy az Azure parancssori felület használatával exportálja a konfigurációs fájlt. PowerShell egy XML-fájl exportálása, az Azure parancssori felület egy json-fájl exportálása során.

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell telepítése, és jelentkezzen be Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Módosítsa a könyvtárat (és győződjön meg arról, hogy létezik-e) és a következő parancsot a kívánt módon működjenek, majd futtassa a parancsot a hálózati konfigurációs fájl exportálása:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Telepítse az Azure parancssori felület 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Végezze el az Azure CLI 1.0 parancssorból a hátralévő lépéseket.
2. Jelentkezzen be az Azure írja be a `azure login` parancsot.
3. Nyissa meg a címterület-kezelési mód megadásával a `azure config mode asm` parancsot.
4. Módosítsa a könyvtárat (és győződjön meg arról, hogy létezik-e) és a következő parancsot a kívánt módon működjenek, majd futtassa a parancsot a hálózati konfigurációs fájl exportálása:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Hozzon létre vagy módosítsa a hálózati konfigurációs fájlt.

A hálózati konfigurációs fájl esetén egy XML-fájl (amikor a PowerShell használatával) vagy egy json-fájl (az Azure parancssori felület használatával). Szerkesztheti a fájlt a szöveg vagy XML/json-szerkesztő. A [hálózati konfigurációs fájl séma beállításai](https://msdn.microsoft.com/library/azure/jj157100.aspx) cikk tartalmazza az összes beállítást. A beállítások további ismertetése [virtuális hálózatok és beállítások megtekintéséhez](manage-virtual-network.md#view-virtual-networks-and-settings). A módosítások a fájlt:

- Meg kell felelnie a séma vagy importálása sikertelen lesz, a hálózati konfigurációs fájlt.
- Felülírja a meglévő hálózati beállításait az előfizetés, ezért rendkívül körültekintően járjon el módosításakor. A példa hálózati konfigurációs fájlokat, az alábbi például hivatkozhat. Tegyük fel például, az eredeti fájl tartalmazta két **VirtualNetworkSite** példányok, és megváltozott, a példákban látható módon. Amikor importálja a fájlt, Azure törli a virtuális hálózatot a **VirtualNetworkSite** példány eltávolította a fájlban. Egyszerűsített ebben a forgatókönyvben azt feltételezi, hogy nincsenek erőforrások volt a virtuális hálózatban, ha vannak, a virtuális hálózat nem törölhető, és az importálás sikertelen lesz.

> [!IMPORTANT]
> Azure úgy ítéli meg, amely valamilyen rá telepítve rendelkezik alhálózat **használatban**. Ha egy alhálózat használatban van, nem lehet módosítani. Módosítása az alhálózati adatokat a hálózati konfigurációs fájlban, előtt helyezze át, amelyeket egy másik alhálózatot, amely nem módosított alhálózatnak telepítette. Lásd: [virtuális gép vagy Szerepkörpéldány áthelyezése egy másik alhálózat](virtual-networks-move-vm-role-to-subnet.md) részleteiről.

### <a name="example-xml-for-use-with-powershell"></a>Példa XML PowerShell való használatra

A következő példa hálózati konfigurációs fájl létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a cím rendelkező *10.0.0.0/16* a a *USA keleti régiója* Azure-régiót. A virtuális hálózat tartalmaz egy alhálózat nevű *mySubnet* rendelkező egy címelőtagot *10.0.0.0/24*.

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

Ha a hálózati konfigurációs fájlt az exportált nem tartalmát, az XML-fájl másolása az előző példában szereplő, és illessze be egy új fájlt.

### <a name="example-json-for-use-with-the-azure-cli-10"></a>Példa JSON az Azure CLI 1.0 való használatra

A következő példa hálózati konfigurációs fájl létrehoz egy virtuális hálózatot nevű *myVirtualNetwork* a cím rendelkező *10.0.0.0/16* a a *USA keleti régiója* Azure-régiót. A virtuális hálózat tartalmaz egy alhálózat nevű *mySubnet* rendelkező egy címelőtagot *10.0.0.0/24*.

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

Ha a hálózati konfigurációs fájlt az exportált nem tartalmát, másolja át a json az előző példában, és illessze be egy új fájlt.

## <a name="import"></a>A hálózati konfigurációs fájl importálása

PowerShell vagy az Azure CLI segítségével importálja a hálózati konfigurációs fájlt. PowerShell importálja az XML-fájl, amíg az Azure parancssori felület importálja a json-fájl. Ha az importálás sikertelen lesz, győződjön meg arról, hogy a fájl megfelel a [hálózati konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Azure PowerShell telepítése, és jelentkezzen be Azure](/powershell/azure/install-azure-ps?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Módosítsa a könyvtárat és a fájlnév szükség szerint, a következő parancsot a futtassa a parancsot a hálózati konfigurációs fájl importálása:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-cli-10"></a>Azure CLI 1.0

1. [Telepítse az Azure parancssori felület 1.0](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Végezze el az Azure CLI 1.0 parancssorból a hátralévő lépéseket.
2. Jelentkezzen be az Azure írja be a `azure login` parancsot.
3. Nyissa meg a címterület-kezelési mód megadásával a `azure config mode asm` parancsot.
4. Módosítsa a könyvtárat és a fájlnév szükség szerint, a következő parancsot a futtassa a parancsot a hálózati konfigurációs fájl importálása:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

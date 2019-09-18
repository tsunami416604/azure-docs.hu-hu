---
title: Azure-Virtual Network (klasszikus) – hálózati konfigurációs fájl konfigurálása | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre és módosíthat virtuális hálózatokat (klasszikus) egy hálózati konfigurációs fájl exportálásával, módosításával és importálásával.
services: virtual-network
documentationcenter: ''
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: ab4faa0f727469e27eb30af54f24036292ec3118
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058703"
---
# <a name="configure-a-virtual-network-classic-using-a-network-configuration-file"></a>Virtuális hálózat (klasszikus) konfigurálása hálózati konfigurációs fájl használatával
> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy a legtöbb új központi telepítés a Resource Manager-alapú üzemi modellt használja.

Létrehozhat és konfigurálhat egy virtuális hálózatot (klasszikus) egy hálózati konfigurációs fájllal a klasszikus Azure parancssori felületen (CLI) vagy Azure PowerShell használatával. Hálózati konfigurációs fájllal nem hozhat létre és nem módosíthat virtuális hálózatot a Azure Resource Manager üzemi modell használatával. Nem használhatja a Azure Portal virtuális hálózat (klasszikus) létrehozására vagy módosítására hálózati konfigurációs fájl használatával, azonban a Azure Portal használatával hálózati konfigurációs fájl nélkül is létrehozhat egy virtuális hálózatot (klasszikus).

Egy hálózati konfigurációs fájllal rendelkező virtuális hálózat (klasszikus) létrehozása és konfigurálása szükséges a fájl exportálásához, módosításához és importálásához.

## <a name="export"></a>Hálózati konfigurációs fájl exportálása

Hálózati konfigurációs fájl exportálásához használhatja a PowerShellt vagy a klasszikus Azure CLI-t. A PowerShell XML-fájlt exportál, míg a klasszikus Azure CLI egy JSON-fájlt exportál.

### <a name="powershell"></a>PowerShell
 
1. [Telepítse Azure PowerShellt, és jelentkezzen be az Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)-ba.
2. Módosítsa a könyvtárat (és győződjön meg róla, hogy létezik) és a fájlnevet a következő parancsban, igény szerint, majd futtassa a parancsot a hálózati konfigurációs fájl exportálásához:

    ```powershell
    Get-AzureVNetConfig -ExportToFile c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

1. [Telepítse a klasszikus Azure CLI](../cli-install-nodejs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-t. Hajtsa végre a többi lépést a klasszikus CLI-parancssorból.
2. Jelentkezzen be az Azure-ba `azure login` a parancs beírásával.
3. A `azure config mode asm` parancs beírásával ellenőrizze, hogy az ASM módban van-e.
4. Módosítsa a könyvtárat (és győződjön meg róla, hogy létezik) és a fájlnevet a következő parancsban, igény szerint, majd futtassa a parancsot a hálózati konfigurációs fájl exportálásához:
    
    ```azurecli
    azure network export c:\azure\networkconfig.json
    ```

## <a name="create-or-modify-a-network-configuration-file"></a>Hálózati konfigurációs fájl létrehozása vagy módosítása

A hálózati konfigurációs fájl egy XML-fájl (a PowerShell használatakor) vagy egy JSON-fájl (a klasszikus CLI használatakor). A fájlt bármilyen szöveges vagy XML/JSON-szerkesztőben szerkesztheti. A [hálózati konfigurációs fájl sémájának beállításai](https://msdn.microsoft.com/library/azure/jj157100.aspx) cikk az összes beállítás részleteit tartalmazza. A beállításokról további magyarázatot a [virtuális hálózatok és beállítások megtekintése](manage-virtual-network.md#view-virtual-networks-and-settings)című témakörben talál. A fájl módosításai:

- Be kell tartania a sémát, vagy a hálózati konfigurációs fájl importálása sikertelen lesz.
- Írja felül az előfizetés meglévő hálózati beállításait, ezért a módosítások végrehajtásakor rendkívül körültekintően járjon el. Hivatkozzon például az alábbi hálózati konfigurációs fájlokra. Tegyük fel, hogy az eredeti fájl két **VirtualNetworkSite** példányt tartalmaz, és módosította azt a példákban látható módon. A fájl importálásakor az Azure törli a fájlból eltávolított **VirtualNetworkSite** -példány virtuális hálózatát. Ez az egyszerűsített forgatókönyv feltételezi, hogy egyetlen erőforrás sincs a virtuális hálózaton, mintha a virtuális hálózat nem törölhető, és az importálás meghiúsul.

> [!IMPORTANT]
> Az Azure olyan alhálózatot tekint, amely **használatban**van. Ha egy alhálózat használatban van, nem módosítható. Mielőtt módosítani szeretné az alhálózati adatokat egy hálózati konfigurációs fájlban, helyezze át az alhálózatra központilag telepített adatokat egy másik, nem módosított alhálózatra. További részletekért lásd: [virtuális gép vagy szerepkör-példány áthelyezése másik alhálózatra](virtual-networks-move-vm-role-to-subnet.md) .

### <a name="example-xml-for-use-with-powershell"></a>Példa az XML használatára a PowerShell-lel

A következő példa hálózati konfigurációs fájl egy *myVirtualNetwork* nevű virtuális hálózatot hoz létre az *USA keleti* régiójában található *10.0.0.0/16* címtartomány mellett. A virtuális hálózat egy *mySubnet* nevű alhálózatot tartalmaz, amelynek a *10.0.0.0/24*címnek kell szerepelnie.

```xml
<?xml version="1.0" encoding="utf-8"?>
<NetworkConfiguration xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

Ha az exportált hálózati konfigurációs fájl nem tartalmaz tartalmat, akkor az előző példában szereplő XML-fájlt másolhatja, majd beillesztheti egy új fájlba.

### <a name="example-json-for-use-with-the-classic-cli"></a>Példa JSON-ra a klasszikus CLI-vel való használatra

A következő példa hálózati konfigurációs fájl egy *myVirtualNetwork* nevű virtuális hálózatot hoz létre az *USA keleti* régiójában található *10.0.0.0/16* címtartomány mellett. A virtuális hálózat egy *mySubnet* nevű alhálózatot tartalmaz, amelynek a *10.0.0.0/24*címnek kell szerepelnie.

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

Ha az exportált hálózati konfigurációs fájl nem tartalmaz tartalmat, a JSON-t átmásolhatja az előző példába, és beillesztheti egy új fájlba.

## <a name="import"></a>Hálózati konfigurációs fájl importálása

Hálózati konfigurációs fájl importálásához használhatja a PowerShellt vagy a klasszikus CLI-t. A PowerShell XML-fájlt importál, míg a klasszikus CLI egy JSON-fájlt importál. Ha az importálás sikertelen, győződjön meg arról, hogy a fájl megfelel a [hálózati konfigurációs sémának](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

### <a name="powershell"></a>PowerShell
 
1. [Telepítse Azure PowerShellt, és jelentkezzen be az Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-install)-ba.
2. Szükség szerint módosítsa a könyvtárat és a fájlnevet a következő parancsban, majd futtassa a parancsot a hálózati konfigurációs fájl importálásához:
 
    ```powershell
    Set-AzureVNetConfig  -ConfigurationPath c:\azure\networkconfig.xml
    ```

### <a name="azure-classic-cli"></a>Azure klasszikus parancssori felület

1. [Telepítse a klasszikus Azure CLI](/cli/azure/install-classic-cli)-t. Hajtsa végre a többi lépést a klasszikus CLI-parancssorból.
2. Jelentkezzen be az Azure-ba `azure login` a parancs beírásával.
3. A `azure config mode asm` parancs beírásával ellenőrizze, hogy az ASM módban van-e.
4. Szükség szerint módosítsa a könyvtárat és a fájlnevet a következő parancsban, majd futtassa a parancsot a hálózati konfigurációs fájl importálásához:

    ```azurecli
    azure network import c:\azure\networkconfig.json
    ```

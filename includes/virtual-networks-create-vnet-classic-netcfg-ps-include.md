---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: bda289e73b9a782cd56c0c94b8f53e8002b1ccf4
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59532632"
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>Hogyan hozhat létre virtuális hálózatot egy hálózati konfigurációs fájlt a PowerShell használatával
Az Azure egy xml-fájlt használ az összes rendelkezésre álló virtuális hálózatok meghatározására előfizetésre. Töltse le ezt a fájlt, szerkesztése vagy törlése a meglévő virtuális hálózatok, és hozzon létre új virtuális hálózatot. Ebből az oktatóanyagból megtudhatja, hogyan letölteni a fájlt, a hálózati konfiguráció (vagy a netcfg) fájl, nevezik, és szerkesztéssel hozzon létre egy új virtuális hálózatot. A hálózati konfigurációs fájlt kapcsolatos további információkért tekintse meg a [az Azure virtual network konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Virtuális hálózat létrehozása a PowerShell használatával a netcfg fájl, hajtsa végre az alábbi lépéseket:

1. Ha még sosem használta az Azure PowerShell-lel, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell](/powershell/azureps-cmdlets-docs) cikkre, majd jelentkezzen be az Azure-ba, és válassza ki az előfizetését.
2. Az Azure PowerShell-konzolt használja a **Get-AzureVnetConfig** parancsmaggal letöltheti a hálózati konfigurációs fájlt a számítógép könyvtárba, a következő parancs futtatásával: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Várt kimenet:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Nyissa meg a minden olyan XML- vagy szöveges szerkesztő alkalmazással 2. lépésben mentett fájlt, és keresse meg a  **\<VirtualNetworkSites >** elemet. Ha már létrehozott hálózatokban, minden egyes hálózati megjelent a saját  **\<VirtualNetworkSite >** elemet.
4. Ebben a forgatókönyvben leírtak a virtuális hálózat létrehozásához adja hozzá a következő XML formátumú majdnem a  **\<VirtualNetworkSites >** elem:

   ```xml
         <?xml version="1.0" encoding="utf-8"?>
         <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
           <VirtualNetworkConfiguration>
             <VirtualNetworkSites>
                 <VirtualNetworkSite name="TestVNet" Location="East US">
                   <AddressSpace>
                     <AddressPrefix>192.168.0.0/16</AddressPrefix>
                   </AddressSpace>
                   <Subnets>
                     <Subnet name="FrontEnd">
                       <AddressPrefix>192.168.1.0/24</AddressPrefix>
                     </Subnet>
                     <Subnet name="BackEnd">
                       <AddressPrefix>192.168.2.0/24</AddressPrefix>
                     </Subnet>
                   </Subnets>
                 </VirtualNetworkSite>
             </VirtualNetworkSites>
           </VirtualNetworkConfiguration>
         </NetworkConfiguration>
   ```
   
5. Mentse a hálózati konfigurációs fájlt.
6. Az Azure PowerShell-konzolt használja a **Set-AzureVnetConfig** parancsmaggal töltse fel a hálózati konfigurációs fájlt a következő parancs futtatásával: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Kimeneti adta vissza:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Ha **OperationStatus** nem *sikeres* a visszaadott kimenetet, ellenőrizze az xml-fájlt a hibák és a 6. lépés kész újra.

7. Az Azure PowerShell-konzolt használja a **Get-AzureVnetSite** parancsmagot, hogy az új hálózati hozzáadásának ellenőrzéséhez a következő parancs futtatásával: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   A visszaadott (melynek rövidítése) kimenete tartalmazza a következő szöveget:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```

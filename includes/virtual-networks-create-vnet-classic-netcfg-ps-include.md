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
ms.openlocfilehash: 4ae4c3100ae13fdb05e17974b433b247128c1a50
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
## <a name="how-to-create-a-virtual-network-using-a-network-config-file-from-powershell"></a>PowerShell a hálózati konfigurációs fájl használatával egy virtuális hálózat létrehozása
Azure előfizetés az összes rendelkezésre álló virtuális hálózatok megadása egy XML-fájlt használja. Letölteni a fájlt, módosítása vagy törlése a meglévő virtuális hálózatok szerkesztheti, majd hozzon létre új virtuális hálózatok. Ebből az oktatóanyagból megtudhatja, hogyan letölteni a fájlt, a hálózati konfiguráció (vagy netcfg) fájl, a továbbiakban, és új virtuális hálózat létrehozása szerkesztésére. A hálózati konfigurációs fájl kapcsolatos további tudnivalókért tekintse meg a [Azure-beli virtuális hálózat konfigurációs séma](https://msdn.microsoft.com/library/azure/jj157100.aspx).

Virtuális hálózat létrehozása a PowerShell használatával netcfg fájllal, végezze el a következő lépéseket:

1. Ha még sosem használta az Azure PowerShell, hajtsa végre a a [telepítése és konfigurálása az Azure PowerShell](/powershell/azureps-cmdlets-docs) cikk, majd jelentkezzen be az Azure-ba, és jelölje ki az előfizetését.
2. Az Azure PowerShell-konzolon, használja a **Get-AzureVnetConfig** parancsmag használatával töltheti le a hálózati konfigurációs fájlt a számítógépen egy könyvtár a következő parancs futtatásával: 
   
   ```powershell
   Get-AzureVNetConfig -ExportToFile c:\azure\NetworkConfig.xml
   ```
   
   Várt kimenet:
  
      ```
      XMLConfiguration                                                                                                     
      ----------------                                                                                                     
      <?xml version="1.0" encoding="utf-8"?>...
      ```

3. Nyissa meg a bármely XML vagy szöveges szerkesztő alkalmazással 2. lépésben mentett fájlt, és keresse meg a **<VirtualNetworkSites>** elemet. Ha olyan hálózatra, már létrehozott, minden egyes hálózati jelenik meg a saját **<VirtualNetworkSite>** elemet.
4. Ebben a forgatókönyvben a virtuális hálózat létrehozásához adja hozzá a következő XML alatt csak a **<VirtualNetworkSites>** elem:

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
6. Az Azure PowerShell-konzolon, használja a **Set-AzureVnetConfig** parancsmaggal töltse fel a hálózati konfigurációs fájlt a következő parancs futtatásával: 
   
   ```powershell
   Set-AzureVNetConfig -ConfigurationPath c:\azure\NetworkConfig.xml
   ```
   
   Kimeneti adott vissza:
   
      ```
      OperationDescription OperationId                          OperationStatus
      -------------------- -----------                          ---------------
      Set-AzureVNetConfig  <Id>                                 Succeeded 
      ```
   
   Ha **OperationStatus** nem *sikeres* visszaadott kimenet, ellenőrizze az XML-fájl, a hibák és a Kész lépés 6 újra.

7. Az Azure PowerShell-konzolon, használja a **Get-AzureVnetSite** parancsmag segítségével győződjön meg arról, hogy az új hálózati lett-e hozzáadva a következő parancs futtatásával: 

   ```powershell
   Get-AzureVNetSite -VNetName TestVNet
   ```
   
   A visszaadott (rövidített) parancs kimenete a következő szöveget:
  
      ```
      AddressSpacePrefixes : {192.168.0.0/16}
      Location             : Central US
      Name                 : TestVNet
      State                : Created
      Subnets              : {FrontEnd, BackEnd}
      OperationDescription : Get-AzureVNetSite
      OperationStatus      : Succeeded
      ```

---
title: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű SQL Server 2017 virtuális gépet az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 02/15/2018
ms.author: jroth
ms.openlocfilehash: 228d2852d9554d378dc663e74460da7ab80d4b24
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Rövid útmutató: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása az Azure PowerShell használatával

Ez a rövid útmutató részletesen bemutatja egy SQL Servert futtató virtuális gép létrehozását az Azure PowerShell használatával.

> [!TIP]
> Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. Az SQL virtuális gépek létrehozásának egyéb Azure PowerShell lehetőségeiről további információért lásd: [Kiépítési útmutató az SQL-kiszolgálón futó virtuális gépekhez az Azure PowerShell használatával](virtual-machines-windows-ps-sql-create.md).

> [!TIP]
> Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

## <a id="subscription"></a> Azure-előfizetés beszerzése

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a id="powershell"></a> Az Azure PowerShell beszerzése

Ehhez a rövid útmutatóhoz az Azure PowerShell-modul 3.6-os vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable AzureRM`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-azurerm-ps) ismertető cikket.

## <a name="configure-powershell"></a>A PowerShell konfigurálása

1. Nyissa meg a PowerShellt, és állítsa be az Azure-fiókja elérését a **Connect-AzureRmAccount** parancs futtatásával.

   ```PowerShell
   Connect-AzureRmAccount
   ```

1. Ekkor meg kell jelennie egy bejelentkezési képernyőnek, amely a hitelesítő adatainak megadását kéri. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Adjon meg egy változót egy egyedi erőforráscsoport-névvel. A gyors útmutató további részének egyszerűbbé tétele érdekében a többi parancs ezt a nevet veszi alapul az egyéb erőforrásnevekhez.

   ```PowerShell
   $ResourceGroupName = "sqlvm1"
   ```

1. Adjon meg egy Azure-célrégiót a virtuális gép összes erőforrásához.

   ```PowerShell
   $Location = "East US"
   ```

1. Hozza létre az erőforráscsoportot.

   ```PowerShell
   New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

1. Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Hozzon létre egy hálózati biztonsági csoportot. Konfiguráljon szabályokat a távoli asztali (RDP) és SQL Server-kapcsolatok lehetővé tételéhez.

   ```PowerShell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzureRmNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Hozza létre a hálózati adaptert.

   ```PowerShell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzureRmNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása

1. Adja meg a hitelesítő adatait a virtuális gépre való bejelentkezéshez. A felhasználónév az „azureadmin”. Gondoskodjon a jelszómódosításról a parancs futtatása előtt.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString 'Change.This!000' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Hozzon létre egy virtuálisgép-konfigurációs objektumot, majd hozza létre a virtuális gépet. A következő parancs létrehoz egy SQL Server 2017 Developer Edition virtuális gépet a Windows Server 2016 rendszeren.

   ```PowerShell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzureRmVMConfig -VMName $VMName -VMSize Standard_DS13 | `
      Set-AzureRmVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate | `
      Set-AzureRmVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" | `
      Add-AzureRmVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > A virtuális gép létrehozása több percig tart.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése

A portál integrációjához és az SQL virtuálisgép-funkciókhoz telepíteni kell az [SQL Server IaaS-ügynök bővítményt](virtual-machines-windows-sql-server-agent-extension.md). Az ügynök az új virtuális gépen való telepítéséhez futtassa a következő parancsot a gép létrehozása után.

   ```PowerShell
   Set-AzureRmVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "1.2" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Távoli asztal a virtuális gépen

1. A következő paranccsal kérje le az új virtuális gép nyilvános IP-címét.

   ```PowerShell
   Get-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. A visszaadott IP-címet a parancssorban az **mstsc** parancs paramétereként megadva elindíthat egy távoli asztali munkamenetet az új virtuális gépen.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Amikor a rendszer a hitelesítő adatokat kéri, válassza egy másik fiók hitelesítő adatainak megadását. Adja meg a felhasználónevet egy fordított perjel előtaggal (például `\azureadmin`, és az ebben a rövid útmutatóban korábban megadott jelszót.

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. Miután bejelentkezett a távoli asztali munkamenetbe, indítsa el a Start menüből az **SQL Server Management Studio 2017** alkalmazást.

1. A **Connect to Server** (Kapcsolódás kiszolgálóhoz) párbeszédpanelen tartsa meg az alapértelmezett értékeket. A kiszolgáló neve a virtuális gép neve. A Hitelesítés értéke **Windows Authentication** (Windows-hitelesítés). Kattintson a **Connect** (Csatlakozás) gombra.

Ezzel helyileg csatlakozott az SQL Serverhez. Ha távolról kíván csatlakozni, [konfigurálja a kapcsolatot](virtual-machines-windows-sql-connect.md) a portálon vagy manuálisan.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem szükséges, hogy a virtuális gép folyamatosan fusson, a szükségtelen költségeket elkerülendő leállíthatja az épp használaton kívüli gépet. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```PowerShell
Stop-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Emellett véglegesen is törölheti a virtuális géppel társított erőforrásokat a **Remove-AzureRmResourceGroup** paranccsal. Ez véglegesen törli magát a virtuális gépet is, ezért ezt a parancsot körültekintően alkalmazza.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy SQL Server 2017-et futtató virtuális gépet hozott létre az Azure PowerShell használatával. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](virtual-machines-windows-migrate-sql.md)

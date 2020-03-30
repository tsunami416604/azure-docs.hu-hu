---
title: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása az Azure PowerShell használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Windows rendszerű SQL Server 2017 virtuális gépet az Azure PowerShell használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8994079cf18a9af5f5e1368761015bbd8b836bd9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74790909"
---
# <a name="quickstart-create-a-sql-server-windows-virtual-machine-with-azure-powershell"></a>Rövid útmutató: SQL Servert futtató, Windows rendszerű virtuális gép létrehozása az Azure PowerShell használatával

Ez a rövid útmutató részletesen bemutatja egy SQL Servert futtató virtuális gép létrehozását az Azure PowerShell használatával.

> [!TIP]
> - Ez a rövid útmutató bemutatja az SQL virtuális gépek gyors kiépítését és az azokhoz való csatlakozást. Az SQL virtuális gépek létrehozásának egyéb Azure PowerShell lehetőségeiről további információért lásd: [Kiépítési útmutató az SQL-kiszolgálón futó virtuális gépekhez az Azure PowerShell használatával](virtual-machines-windows-ps-sql-create.md).
> - Ha kérdése van az SQL Servert futtató virtuális gépek használatával kapcsolatban, tekintse meg a [gyakori kérdéseket](virtual-machines-windows-sql-server-iaas-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a>Azure-előfizetés beszereznie

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Az Azure PowerShell beszerzése

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>A PowerShell konfigurálása

1. Nyissa meg a PowerShellt, és a **Connect-AzAccount** parancs futtatásával létesítsen hozzáférést az Azure-fiókjához.

   ```powershell
   Connect-AzAccount
   ```

1. A hitelesítő adatok megadásához meg kell jelennie egy képernyőnek. Használja ugyanazt az e-mail-címet és jelszót, amelyet az Azure Portalra való bejelentkezéshez használ.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

1. Adjon meg egy változót egy egyedi erőforráscsoport-névvel. A rövid útmutató többi részének egyszerűsítése érdekében a többi parancs ezt a nevet használja más erőforrásnevek alapjául.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Adjon meg egy Azure-célrégiót a virtuális gép összes erőforrásához.

   ```powershell
   $Location = "East US"
   ```

1. Hozza létre az erőforráscsoportot.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>A hálózati beállítások konfigurálása

1. Hozzon létre egy virtuális hálózatot, egy alhálózatot és egy nyilvános IP-címet. Ezek az erőforrások a virtuális gép hálózati csatlakoztatásának biztosítására, illetve az internethez csatlakoztatására használatosak.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Hozzon létre egy hálózati biztonsági csoportot. Konfiguráljon szabályokat a távoli asztali (RDP) és SQL Server-kapcsolatok lehetővé tételéhez.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Hozza létre a hálózati adaptert.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Az SQL virtuális gép létrehozása

1. Adja meg a hitelesítő adatokat, hogy jelentkezzen be a virtuális gép. A felhasználónév "azureadmin". A parancs \<futtatása előtt győződjön meg arról, hogy módosítja a jelszót>.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Hozzon létre egy virtuálisgép-konfigurációs objektumot, majd hozza létre a virtuális gépet. A következő parancs létrehoz egy SQL Server 2017 Developer Edition virtuális gépet a Windows Server 2016 rendszeren.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > A virtuális gép létrehozása több percig tart.

## <a name="install-the-sql-iaas-agent"></a>Az SQL IaaS-ügynök telepítése

A portál integrációjához és az SQL virtuálisgép-funkciókhoz telepíteni kell az [SQL Server IaaS-ügynök bővítményt](virtual-machines-windows-sql-server-agent-extension.md). Az ügynök telepítése az új virtuális gépre, futtassa a következő parancsot a virtuális gép létrehozása után.

   ```powershell
   Set-AzVMSqlServerExtension -ResourceGroupName $ResourceGroupName -VMName $VMName -name "SQLIaasExtension" -version "2.0" -Location $Location
   ```

## <a name="remote-desktop-into-the-vm"></a>Távoli asztal a virtuális gépen

1. A következő paranccsal lekérheti az új virtuális gép nyilvános IP-címét.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Adja át a visszaadott IP-címet parancssori paraméterként **az mstsc-nek,** hogy távoli asztali munkamenetet indítson az új virtuális gépbe.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Amikor a rendszer a hitelesítő adatokat kéri, válassza egy másik fiók hitelesítő adatainak megadását. Adja meg az előző fordított perjelet `\azureadmin`(például ) tartalmazó felhasználónevet és a rövid útmutatóban korábban megadott jelszót.

## <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

1. Miután bejelentkezett a Távoli asztal munkamenetbe, indítsa el az **SQL Server Management Studio 2017-et** a start menüből.

1. A **Csatlakozás kiszolgálóhoz** párbeszédpanelen tartsa meg az alapértelmezett értékeket. A kiszolgáló neve a virtuális gép neve. A Hitelesítés értéke **Windows Authentication** (Windows-hitelesítés). Kattintson a **Csatlakozás** gombra.

Most már helyileg csatlakozik az SQL Server kiszolgálóhoz. Ha távolról szeretne csatlakozni, [konfigurálnia](virtual-machines-windows-sql-connect.md) kell a kapcsolatot a portálról, vagy manuálisan.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nincs szüksége a virtuális gép folyamatos futtatásához, elkerülheti a szükségtelen díjakat, ha leállítja, ha nem használja. A következő parancs leállítja a virtuális gépet, de elérhető állapotban hagyja későbbi használat céljából.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Az **Eltávolítás-AzResourceGroup** paranccsal véglegesen törölheti a virtuális géphez társított összes erőforrást is. Ezzel véglegesen törli a virtuális gépet is, ezért óvatosan használja ezt a parancsot.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban egy SQL Server 2017-et futtató virtuális gépet hozott létre az Azure PowerShell használatával. Az adatok az új SQL Serverre való migrálásával kapcsolatos további információkért lásd a következő cikket.

> [!div class="nextstepaction"]
> [Adatbázisok migrálása SQL virtuális gépekre](virtual-machines-windows-migrate-sql.md)

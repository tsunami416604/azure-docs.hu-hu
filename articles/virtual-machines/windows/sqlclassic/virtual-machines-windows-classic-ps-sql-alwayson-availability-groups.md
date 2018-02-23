---
title: "Az Always On rendelkezésre állási csoport konfigurálása egy Azure virtuális gépen a PowerShell használatával |} Microsoft Docs"
description: "Ez az oktatóanyag használja a klasszikus üzembe helyezési modellel létrehozott erőforrásokat. Always On rendelkezésre állási csoport létrehozása az Azure-ban a PowerShell segítségével."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: 
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: fe7384baa6740d316fb5a8ec7b1894f337d88080
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Az Always On rendelkezésre állási csoport konfigurálása egy Azure virtuális gépen a PowerShell használatával
> [!div class="op_single_selector"]
> * [Klasszikus: felhasználói felület](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasszikus: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Mielőtt elkezdené, vegye figyelembe, hogy most hajthatja végre ezt a feladatot az Azure resource manager modellt. Azt javasoljuk, hogy az Azure resource manager modellt új központi telepítéséhez. Lásd: [SQL Server Always On rendelkezésre állási csoportok az Azure virtuális gépeken](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja. Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az Azure virtuális gépek (VM) segítségével csökkenthető a költség, a magas rendelkezésre állású SQL Server rendszer adatbázis-rendszergazdák. Ez az oktatóanyag bemutatja, hogyan egy rendelkezésre állási csoporthoz az SQL Server Always On-végpontok belül környezetet az Azure segítségével megvalósítható. Az oktatóanyag végén az SQL Server Always On megoldás az Azure-ban a következő elemekből áll:

* Virtuális hálózat, amely tartalmazza a több alhálózattal, beleértve egy előtér- és háttér-alhálózatot.
* Az Active Directory-tartomány a tartományvezérlő.
* Két SQL Server virtuális gépen telepített, a háttér-alhálózathoz és Active Directory-tartományhoz csatlakozik.
* Egy három csomópontos Windows feladatátvevő fürt csomóponttöbbség kvórum modell.
* Egy rendelkezésre állási csoportban két szinkron véglegesítésű másodpéldányt a rendelkezésre állási adatbázis.

Ebben a forgatókönyvben a jó választás az egyszerűség érdekében az Azure-on, nem pedig a költséghatékonyság vagy egyéb tényezők. Például minimalizálhatja a virtuális gépek, a két-replika rendelkezésre állási csoport a tartományvezérlő a kvórum tanúsító fájlmegosztást egy két csomópontos feladatátvevő fürt segítségével menti az Azure számítási órák számát. Ez a módszer a virtuális gépek száma csökkenti a fenti konfigurációban közül.

Ez az oktatóanyag célja mutatjuk be, a lépéseket, amelyek szükségesek a fent ismertetett megoldás beállítása nélkül kidolgozása az egyes lépések részletes adatait. Ezért helyett ad meg a grafikus felhasználói Felülettel konfigurációs lépések, használja a PowerShell parancsfájlok segítségével gyorsan vezeti végig az egyes lépések. Ez az oktatóanyag azt feltételezi, hogy a következő:

* Már van Azure-fiókot a virtuális gép előfizetéssel.
* Telepítette a [Azure PowerShell-parancsmagok](/powershell/azure/overview).
* Már van egy Always On rendelkezésre állási csoportok a helyszíni megoldások alapos ismerete. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Az Azure-előfizetés csatlakoztatása és a virtuális hálózat létrehozása
1. PowerShell-ablakban a helyi számítógépen az Azure-moduljának importálása, töltse le a közzétételi beállítások fájlját a számítógépen, és az Azure-előfizetéshez csatlakozni a PowerShell-munkamenetet a letöltött közzétételi beállítások importálása.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    A **Get-AzurePublishSettingsFile** parancs automatikusan az Azure felügyeleti tanúsítványt hoz létre, és letölti a számítógépen. A böngésző automatikusan megnyílik, és felkéri a Microsoft-fiók hitelesítő adatait adja meg az Azure-előfizetéshez. A letöltött **.publishsettings** fájlt, hogy az Azure-előfizetése kezeléséhez szükséges összes információt tartalmazza. A fájl mentése egy helyi könyvtárba, után importálnia a **Import-AzurePublishSettingsFile** parancsot.

   > [!NOTE]
   > A .publishsettings fájlt tartalmaz a hitelesítő adatok (nem titkosított), amely segítségével felügyelheti az Azure-előfizetések és -szolgáltatásokra. Ezt a fájlt a biztonsági szempontból ajánlott a forrás-könyvtárak (például a Libraries\Documents mappában) kívül ideiglenesen tárolja, és törölje az importálás után is. Egy rosszindulatú felhasználó hozzáférést kap azokhoz a .publishsettings fájlt a Szerkesztés, hozzon létre, és az Azure-szolgáltatások törlése.

2. Adja meg a felhőalapú informatikai infrastruktúra létrehozásához használandó változókat.

        $location = "West US"
        $affinityGroupName = "ContosoAG"
        $affinityGroupDescription = "Contoso SQL HADR Affinity Group"
        $affinityGroupLabel = "IaaS BI Affinity Group"
        $networkConfigPath = "C:\scripts\Network.netcfg"
        $virtualNetworkName = "ContosoNET"
        $storageAccountName = "<uniquestorageaccountname>"
        $storageAccountLabel = "Contoso SQL HADR Storage Account"
        $storageAccountContainer = "https://" + $storageAccountName + ".blob.core.windows.net/vhds/"
        $winImageName = (Get-AzureVMImage | where {$_.Label -like "Windows Server 2008 R2 SP1*"} | sort PublishedDate -Descending)[0].ImageName
        $sqlImageName = (Get-AzureVMImage | where {$_.Label -like "SQL Server 2012 SP1 Enterprise*"} | sort PublishedDate -Descending)[0].ImageName
        $dcServerName = "ContosoDC"
        $dcServiceName = "<uniqueservicename>"
        $availabilitySetName = "SQLHADR"
        $vmAdminUser = "AzureAdmin"
        $vmAdminPassword = "Contoso!000"
        $workingDir = "c:\scripts\"

    Nagy figyelmet fordítani, annak érdekében, hogy a parancsok később lesz sikeres a következőhöz:

   * Változók **$storageAccountName** és **$dcServiceName** egyedinek kell lennie, mert ezek mire szolgál a felhő tárolási fiók és a felhő-kiszolgáló azonosítására szolgál, osztályban, az interneten.
   * A nevek, amennyit megadott a változók **$affinityGroupName** és **$virtualNetworkName** vannak konfigurálva a virtuális hálózati konfiguráció dokumentum később fogjuk.
   * **$sqlImageName** a Virtuálisgép-lemezkép, amely tartalmazza az SQL Server 2012 Service Pack 1 Enterprise Edition frissített nevét adja meg.
   * Az egyszerűség kedvéért **Contoso! 000** ugyanazt a jelszót, amely használja a rendszer a teljes útmutató keresztül van.

3. Affinitáscsoport létrehozásához.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Virtuális hálózat létrehozása a konfigurációs fájl importálásával.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    A konfigurációs fájl tartalmazza a következő XML-dokumentumot. Röviden, adja meg a virtuális hálózat néven **ContosoNET** nevű affinitáscsoportban **ContosoAG**. A címterület rendelkezik **10.10.0.0/16** és két alhálózat, **10.10.1.0/24** és **10.10.2.0/24**, amelyeket az első alhálózat és a hátsó alhálózati, illetve. Az első alhálózat akkor helyezheti el ügyfélalkalmazásokat, például a Microsoft SharePoint. A hátsó alhálózaton, az SQL Server VMs helyétől fog. Ha módosítja a **$affinityGroupName** és **$virtualNetworkName** változók korábban is módosítania kell a megfelelő alábbi nevét.

        <NetworkConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
          <VirtualNetworkConfiguration>
            <Dns />
            <VirtualNetworkSites>
              <VirtualNetworkSite name="ContosoNET" AffinityGroup="ContosoAG">
                <AddressSpace>
                  <AddressPrefix>10.10.0.0/16</AddressPrefix>
                </AddressSpace>
                <Subnets>
                  <Subnet name="Front">
                    <AddressPrefix>10.10.1.0/24</AddressPrefix>
                  </Subnet>
                  <Subnet name="Back">
                    <AddressPrefix>10.10.2.0/24</AddressPrefix>
                  </Subnet>
                </Subnets>
              </VirtualNetworkSite>
            </VirtualNetworkSites>
          </VirtualNetworkConfiguration>
        </NetworkConfiguration>

5. Az affinitáscsoport hozott létre, és állítsa be az aktuális tárfiók az előfizetésben társított storage-fiók létrehozása.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Az új felhőalapú szolgáltatás és a rendelkezésre állási készlet létrehozása a tartományvezérlő kiszolgálón.

        New-AzureVMConfig `
            -Name $dcServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$dcServerName.vhd" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -Windows `
                -DisableAutomaticUpdates `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword |
                New-AzureVM `
                    -ServiceName $dcServiceName `
                    –AffinityGroup $affinityGroupName `
                    -VNetName $virtualNetworkName

    Ezek a parancsok védőeszközön tegye a következőket:

   * **Új AzureVMConfig** hoz létre a Virtuálisgép-konfiguráció.
   * **Adja hozzá AzureProvisioningConfig** a konfigurációs paraméterek, a különálló Windows Server ad.
   * **Adja hozzá AzureDataDisk** hozzáadja a adatlemez, amelyet a gyorsítótárazás nincs beállítva az Active Directory-adatokat, tárolására fog használni.
   * **Új AzureVM** hoz létre egy új felhőalapú szolgáltatás, és hozza létre az új Azure virtuális Gépet az új felhőalapú szolgáltatás.

7. Várjon, amíg az új virtuális gépek teljesen kiépített, és töltse le a távoli asztal a munkakönyvtárat. Mivel az új Azure virtuális gép kiépítését, hosszú ideig tart a `while` hurok továbbra is fennáll, és kérdezze le az új virtuális Gépet, amíg a használatra kész.

        $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName

        While ($VMStatus.InstanceStatus -ne "ReadyRole")
        {
            write-host "Waiting for " $VMStatus.Name "... Current Status = " $VMStatus.InstanceStatus
            Start-Sleep -Seconds 15
            $VMStatus = Get-AzureVM -ServiceName $dcServiceName -Name $dcServerName
        }

        Get-AzureRemoteDesktopFile `
            -ServiceName $dcServiceName `
            -Name $dcServerName `
            -LocalPath "$workingDir$dcServerName.rdp"

A tartományvezérlő kiszolgálót most sikeresen kiépítette. Ezután az Active Directory-tartományhoz kell majd konfigurálnia a tartomány a tartományvezérlő kiszolgálón. Hagyja meg a PowerShell-ablakban nyissa meg a helyi számítógépen. Újra létrehozni a két SQL Server VMs fogja használni.

## <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
1. A tartományvezérlő kiszolgálót kapcsolódni a távoli asztali fájl indításával. A gépek rendszergazdája AzureAdmin felhasználónévvel és jelszóval **Contoso! 000**, amely az új virtuális gép létrehozásakor adott meg.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. Futtassa a következő **DCPROMO. A következő EXE** parancs használatával állítsa be a **corp.contoso.com** tartomány, az adatok könyvtárakat M-meghajtón.

        dcpromo.exe `
            /unattend `
            /ReplicaOrNewDomain:Domain `
            /NewDomain:Forest `
            /NewDomainDNSName:corp.contoso.com `
            /ForestLevel:4 `
            /DomainNetbiosName:CORP `
            /DomainLevel:4 `
            /InstallDNS:Yes `
            /ConfirmGc:Yes `
            /CreateDNSDelegation:No `
            /DatabasePath:"C:\Windows\NTDS" `
            /LogPath:"C:\Windows\NTDS" `
            /SYSVOLPath:"C:\Windows\SYSVOL" `
            /SafeModeAdminPassword:"Contoso!000"

    A parancs befejezése után a virtuális gép automatikusan újraindul.

4. A tartományvezérlő kiszolgálót újra csatlakozott a távoli asztali fájl indításával. Most, jelentkezzen be a **Corp/rendszergazda**.
5. Nyisson meg egy PowerShell-ablakot rendszergazdai módban, és az Active Directory PowerShell-modul importálása a következő paranccsal:

        Import-Module ActiveDirectory

6. A következő parancsokat három felhasználók felvételét a tartományba.

        $pwd = ConvertTo-SecureString "Contoso!000" -AsPlainText -Force
        New-ADUser `
            -Name 'Install' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc1' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true
        New-ADUser `
            -Name 'SQLSvc2' `
            -AccountPassword  $pwd `
            -PasswordNeverExpires $true `
            -ChangePasswordAtLogon $false `
            -Enabled $true

    **CORP\Install** mindent az SQL-kiszolgáló-szolgáltatáspéldány, a feladatátvevő fürt és a rendelkezésre állási csoport konfigurálásához. **CORP\SQLSvc1** és **CORP\SQLSvc2** a két SQL Server virtuális gépen az SQL Server szolgáltatás fiókok használhatók.
7. Ezután futtassa a következő parancsokat, hogy **CORP\Install** a engedéllyel számítógép-objektumok létrehozására a tartományban.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    A fent megadott GUID-azonosítója a GUID azonosítóját. a számítógép-objektum típusa. A **CORP\Install** igényeinek fiók a **összes tulajdonság olvasása** és **számítógép-objektumok létrehozása** hozhat létre a feladatátvevő fürt az aktív közvetlen objektumokat. A **összes tulajdonság olvasása** engedély megadott CORP\Install alapértelmezés szerint így nem kell explicit megadását. A feladatátvevő fürt létrehozásához szükséges engedélyekről további információkért lásd: [feladatátvevő fürt részletes útmutató: fiókok konfigurálása az Active Directory](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Most, hogy befejezte az Active Directory és a felhasználói objektumok, akkor hozhat létre két SQL Server virtuális gépen, és csatlakoztassa azokat a tartományhoz.

## <a name="create-the-sql-server-vms"></a>Az SQL Server virtuális gépek létrehozása
1. Továbbra is használhatja a PowerShell-ablakot, amely meg van nyitva, a helyi számítógépen. Adja meg a következő további változókat:

        $domainName= "corp"
        $FQDN = "corp.contoso.com"
        $subnetName = "Back"
        $sqlServiceName = "<uniqueservicename>"
        $quorumServerName = "ContosoQuorum"
        $sql1ServerName = "ContosoSQL1"
        $sql2ServerName = "ContosoSQL2"
        $availabilitySetName = "SQLHADR"
        $dataDiskSize = 100
        $dnsSettings = New-AzureDns -Name "ContosoBackDNS" -IPAddress "10.10.0.4"

    Az IP-cím **10.10.0.4** általában az első virtuális gép, a létrehozott van rendelve a **10.10.0.0/16** alhálózat az Azure virtuális hálózat. Ellenőrizze, hogy ez az a cím, a tartományvezérlő kiszolgálón való futtatásával **IPCONFIG**.
2. Futtassa a következő parancsok futtatásával hozza létre az első virtuális gép a feladatátvevő fürt nevű adatcsatornán **ContosoQuorum**:

        New-AzureVMConfig `
            -Name $quorumServerName `
            -InstanceSize Medium `
            -ImageName $winImageName `
            -MediaLocation "$storageAccountContainer$quorumServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    New-AzureVM `
                        -ServiceName $sqlServiceName `
                        –AffinityGroup $affinityGroupName `
                        -VNetName $virtualNetworkName `
                        -DnsSettings $dnsSettings

    Vegye figyelembe a következő kapcsolatban a fenti parancs:

   * **Új AzureVMConfig** hoz létre egy Virtuálisgép-konfiguráció a kívánt rendelkezésre állási készlet neve. A következő virtuális gépek létrehozza az azonos rendelkezésre állási készlet neve az, hogy azok az azonos rendelkezésre állási csoport van csatlakoztatva.
   * **Adja hozzá AzureProvisioningConfig** a virtuális gép csatlakozik a létrehozott Active Directory-tartományhoz.
   * **Set-AzureSubnet** helyezi el a virtuális gép vissza az alhálózaton.
   * **Új AzureVM** hoz létre egy új felhőalapú szolgáltatás, és hozza létre az új Azure virtuális Gépet az új felhőalapú szolgáltatás. A **DnsSettings** paraméter határozza meg, hogy az új felhőszolgáltatás kiszolgálóinak a DNS-kiszolgáló rendelkezik-e az IP-cím **10.10.0.4**. Ez az IP-címét a tartományvezérlő kiszolgálón. Ez a paraméter engedélyezéséhez a új virtuális gépek a felhőszolgáltatás sikeresen csatlakoztatása az Active Directory-tartomány van szükség. Ez a paraméter nélkül manuálisan állítsa be az IPv4-beállításokkal a tartományvezérlő kiszolgálót az elsődleges DNS-kiszolgálóként használni, a virtuális gép kiépítése után a virtuális gépen, és majd a virtuális gép csatlakoztatása az Active Directory-tartományhoz.
3. Futtassa a következő parancsok futtatásával hozza létre az SQL Server VMs nevű adatcsatornán **ContosoSQL1** és **ContosoSQL2**.

        # Create ContosoSQL1...
        New-AzureVMConfig `
            -Name $sql1ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql1ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 1 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

        # Create ContosoSQL2...
        New-AzureVMConfig `
            -Name $sql2ServerName `
            -InstanceSize Large `
            -ImageName $sqlImageName `
            -MediaLocation "$storageAccountContainer$sql2ServerName.vhd" `
            -AvailabilitySetName $availabilitySetName `
            -HostCaching "ReadOnly" `
            -DiskLabel "OS" |
            Add-AzureProvisioningConfig `
                -WindowsDomain `
                -AdminUserName $vmAdminUser `
                -Password $vmAdminPassword `
                -DisableAutomaticUpdates `
                -Domain $domainName `
                -JoinDomain $FQDN `
                -DomainUserName $vmAdminUser `
                -DomainPassword $vmAdminPassword |
                Set-AzureSubnet `
                    -SubnetNames $subnetName |
                    Add-AzureEndpoint `
                        -Name "SQL" `
                        -Protocol "tcp" `
                        -PublicPort 2 `
                        -LocalPort 1433 |
                        New-AzureVM `
                            -ServiceName $sqlServiceName

    Vegye figyelembe a következő kapcsolatban a fenti parancsokat:

   * **Új AzureVMConfig** az azonos rendelkezésre állási készlet neve használja, mint a tartományvezérlő kiszolgálót, és az SQL Server 2012 Service Pack 1 Enterprise Edition lemezképet használja a virtuális gép gyűjteményben. Beállítja továbbá az operációsrendszer-lemez olvasási gyorsítótárazás csak (nincs írási gyorsítótárazást). Azt javasoljuk, hogy az adatbázisfájlok külön adatlemezt csatol a virtuális gép áttelepítése, és nincs olvasási vagy írási gyorsítótárazást konfigurálja. A következő legjobb dolog azonban írási gyorsítótárazást az operációsrendszer-lemeze, mert nem távolítható el az operációsrendszer-lemez olvasási gyorsítótárazás eltávolítása.
   * **Adja hozzá AzureProvisioningConfig** a virtuális gép csatlakozik a létrehozott Active Directory-tartományhoz.
   * **Set-AzureSubnet** helyezi el a virtuális gép vissza az alhálózaton.
   * **Adja hozzá AzureEndpoint** hozzáférés végpontok hozzáadása, hogy ügyfélalkalmazások elérjék ezeket az SQL Server services-példányok az interneten. Különböző portok ContosoSQL1 és ContosoSQL2 kapják.
   * **Új AzureVM** hoz létre az új SQL Server virtuális Gépet az azonos ContosoQuorum felhőszolgáltatásra. Ugyanazt a felhőalapú szolgáltatást be kell jelölnie a virtuális gépek, ha azt szeretné, hogy azok az azonos rendelkezésre állási készlet.
4. Várjon, amíg minden egyes virtuális gépek teljesen kiépített és az egyes virtuális gépek, a távoli asztali fájl letöltéséhez a munkakönyvtárat. A `for` hurok Váltás a három új virtuális gépeket, és végrehajtja a legfelső szintű kapcsos zárójelek között lévő parancsok a hozzájuk.

        Foreach ($VM in $VMs = Get-AzureVM -ServiceName $sqlServiceName)
        {
            write-host "Waiting for " $VM.Name "..."

            # Loop until the VM status is "ReadyRole"
            While ($VM.InstanceStatus -ne "ReadyRole")
            {
                write-host "  Current Status = " $VM.InstanceStatus
                Start-Sleep -Seconds 15
                $VM = Get-AzureVM -ServiceName $VM.ServiceName -Name $VM.InstanceName
            }

            write-host "  Current Status = " $VM.InstanceStatus

            # Download remote desktop file
            Get-AzureRemoteDesktopFile -ServiceName $VM.ServiceName -Name $VM.InstanceName -LocalPath "$workingDir$($VM.InstanceName).rdp"
        }

    Az SQL Server virtuális gépek most törlődnek, és fut, de van telepítve az SQL Server alapértelmezett beállításokkal.

## <a name="initialize-the-failover-cluster-vms"></a>A feladatátvevő fürt virtuális gépek inicializálása
Ebben a szakaszban módosítania kell a három kiszolgáló, amelyen le fogja a feladatátvevő fürt és az SQL Server telepítése. Konkrétan:

* Minden kiszolgáló: telepíteni kell a **feladatátvételi fürtszolgáltatás** szolgáltatás.
* Minden kiszolgáló: hozzá kell adnia **CORP\Install** a gépként **rendszergazda**.
* ContosoSQL1 és ContosoSQL2 csak: hozzá kell adnia **CORP\Install** , egy **sysadmin** az alapértelmezett adatbázis szerepköréhez.
* ContosoSQL1 és ContosoSQL2 csak: hozzá kell adnia **NT AUTHORITY\System** , a bejelentkezés a következő engedélyekkel:

  * Az ALTER bármely rendelkezésre állási csoport
  * Connect SQL
  * Kiszolgáló állapotának megtekintése
* ContosoSQL1 és ContosoSQL2 csak: A **TCP** protokoll már engedélyezve van az SQL Server virtuális gépen. Azonban továbbra is szeretné megnyitni a tűzfalon az SQL Server távoli hozzáféréshez.

Most hogy készen. Kezdve **ContosoQuorum**, kövesse az alábbi lépéseket:

1. Csatlakozás **ContosoQuorum** a távoli asztali fájlok indításával. Adjon a számítógép rendszergazdai jogosultságú felhasználónevet **AzureAdmin** és a jelszó **Contoso! 000**, amely a virtuális gépek létrehozásakor megadott.
2. Győződjön meg arról, hogy a számítógépek rendelkeznek sikeresen csatlakoztatva lett **corp.contoso.com**.
3. Várjon, amíg befejeződik a folytatás előtt az automatikus inicializálás feladatok futtatása az SQL Server telepítése.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows Feladatátvételi fürtszolgáltatást.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Jelentkezzen ki ContosoQuorum. Befejezte a kiszolgáló most.

        logoff.exe

A következő inicializálása **ContosoSQL1** és **ContosoSQL2**. Kövesse az alábbi lépéseket, mindkét SQL Server virtuális gépek azonos.

1. A két SQL Server virtuális gépek csatlakozni a távoli asztali fájlok megnyitása. Adjon a számítógép rendszergazdai jogosultságú felhasználónevet **AzureAdmin** és a jelszó **Contoso! 000**, amely a virtuális gépek létrehozásakor megadott.
2. Győződjön meg arról, hogy a számítógépek rendelkeznek sikeresen csatlakoztatva lett **corp.contoso.com**.
3. Várjon, amíg befejeződik a folytatás előtt az automatikus inicializálás feladatok futtatása az SQL Server telepítése.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows Feladatátvételi fürtszolgáltatást.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Importálja az SQL Server PowerShell-szolgáltatóban.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adja hozzá **CORP\Install** , a sysadmin (rendszergazda) szerepkör alapértelmezett SQL Server-példányhoz.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adja hozzá **NT AUTHORITY\System** , a bejelentkezés a fent leírt három engedélyekkel.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Nyissa meg a tűzfalon az SQL Server távoli hozzáféréshez.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Jelentkezzen ki mindkét virtuális gépeket.

         logoff.exe

Végül készen áll a rendelkezésre állási csoport konfigurálásához. Teljes munkát végre fogja használni az SQL Server PowerShell szolgáltató **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>A rendelkezésre állási csoport konfigurálása
1. Csatlakozás **ContosoSQL1** újra a távoli asztali fájlok indításával. A bejelentkezés helyett a gép fiókkal történő bejelentkezés **CORP\Install**.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. Adja meg a következő változókat:

        $server1 = "ContosoSQL1"
        $server2 = "ContosoSQL2"
        $serverQuorum = "ContosoQuorum"
        $acct1 = "CORP\SQLSvc1"
        $acct2 = "CORP\SQLSvc2"
        $password = "Contoso!000"
        $clusterName = "Cluster1"
        $timeout = New-Object System.TimeSpan -ArgumentList 0, 0, 30
        $db = "MyDB1"
        $backupShare = "\\$server1\backup"
        $quorumShare = "\\$server1\quorum"
        $ag = "AG1"
4. Importálja az SQL Server PowerShell-szolgáltatóban.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. SQL Server szolgáltatásfiók módosítása a ContosoSQL1 CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. SQL Server szolgáltatásfiók módosítása a ContosoSQL2 CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Töltse le **CreateAzureFailoverCluster.ps1** a [feladatátvevő fürt létrehozása az Always On rendelkezésre állási csoportok Azure virtuális gép](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) a helyi munkakönyvtárba. Azt ismertetjük, hogy ez a parancsfájl működő feladatátvevő fürt létrehozásához. A Windows feladatátvételi fürtszolgáltatás hogyan működjön együtt az Azure-hálózatot a fontos információk: [az SQL Server Azure virtuális gépek magas rendelkezésre állási és vészhelyreállítási helyreállítási](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Módosítsa a munkakönyvtárat, és a feladatátvevő fürt létrehozása a letöltött parancsfájllal.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Engedélyezze az Always On rendelkezésre állási csoportok az alapértelmezett SQL Server-példányok a **ContosoSQL1** és **ContosoSQL2**.

        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server1\Default `
            -Force
        Enable-SqlAlwaysOn `
            -Path SQLSERVER:\SQL\$server2\Default `
            -NoServiceRestart
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
10. Hozzon létre egy biztonsági mentési könyvtárat, és adja meg az SQL Server szolgáltatásfiókokat engedélyeket. Ebben a könyvtárban fogja használni a másodlagos replikán a rendelkezésre állási adatbázis előkészítése.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Adatbázis létrehozása a **ContosoSQL1** nevű **MyDB1**, teljes biztonsági mentés és a napló biztonsági mentését is igénybe vehet, és visszaállítja ezeket **ContosoSQL2** rendelkező a **WITH NORECOVERY**  lehetőséget.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Végpontokat hoz létre a rendelkezésre állási csoporthoz az SQL Server virtuális gépeken, és állítsa be a megfelelő engedélyekkel a végpontokon.

         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server1\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"
         $endpoint =
             New-SqlHadrEndpoint MyMirroringEndpoint `
             -Port 5022 `
             -Path "SQLSERVER:\SQL\$server2\Default"
         Set-SqlHadrEndpoint `
             -InputObject $endpoint `
             -State "Started"

         Invoke-SqlCmd -Query "CREATE LOGIN [$acct2] FROM WINDOWS" -ServerInstance $server1
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct2]" -ServerInstance $server1
         Invoke-SqlCmd -Query "CREATE LOGIN [$acct1] FROM WINDOWS" -ServerInstance $server2
         Invoke-SqlCmd -Query "GRANT CONNECT ON ENDPOINT::[MyMirroringEndpoint] TO [$acct1]" -ServerInstance $server2
13. A rendelkezésre állási másodpéldányok létrehozása.

         $primaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server1 `
             -EndpointURL "TCP://$server1.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
         $secondaryReplica =
             New-SqlAvailabilityReplica `
             -Name $server2 `
             -EndpointURL "TCP://$server2.corp.contoso.com:5022" `
             -AvailabilityMode "SynchronousCommit" `
             -FailoverMode "Automatic" `
             -Version 11 `
             -AsTemplate
14. Végül hozza létre a rendelkezésre állási csoportot, és a másodlagos másodpéldány csatlakoztatása a rendelkezésre állási csoport.

         New-SqlAvailabilityGroup `
             -Name $ag `
             -Path "SQLSERVER:\SQL\$server1\Default" `
             -AvailabilityReplica @($primaryReplica,$secondaryReplica) `
             -Database $db
         Join-SqlAvailabilityGroup `
             -Path "SQLSERVER:\SQL\$server2\Default" `
             -Name $ag
         Add-SqlAvailabilityDatabase `
             -Path "SQLSERVER:\SQL\$server2\Default\AvailabilityGroups\$ag" `
             -Database $db

## <a name="next-steps"></a>További lépések
Ön már most sikeresen megvalósítva SQL Server Always On rendelkezésre állási csoport létrehozása az Azure-ban. A figyelő a rendelkezésre állási csoport konfigurálásához lásd: [egy ILB figyelőt az Always On rendelkezésre állási csoportok konfigurálása az Azure-](../classic/ps-sql-int-listener.md).

Egyéb Azure-ban az SQL Server használatával kapcsolatos információkért lásd: [Azure virtuális gépeken futó SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

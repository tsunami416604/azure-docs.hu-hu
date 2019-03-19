---
title: Az Always On rendelkezésre állási csoport konfigurálása egy Azure-beli virtuális gépen a PowerShell-lel |} A Microsoft Docs
description: Ebben az oktatóanyagban használja a klasszikus üzemi modellel létrehozott erőforrásokat. Always On rendelkezésre állási csoport létrehozása az Azure-ban a PowerShell használatával.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: a6d8326afa3bcf13234ab072a2cd2909a864738b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002845"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Az Always On rendelkezésre állási csoport konfigurálása a PowerShell-beli virtuális gépen
> [!div class="op_single_selector"]
> * [Klasszikus: FELHASZNÁLÓI FELÜLET](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasszikus: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Mielőtt elkezdené, érdemes lehet, hogy ez a feladat az Azure resource manager-modellben már végrehajthatók. Azt javasoljuk, hogy az Azure resource manager-modellt az új üzembe helyezésekhez. Lásd: [SQL Server Always On rendelkezésre állási csoportok az Azure-beli virtuális gépek](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md).

> [!IMPORTANT]
> Azt javasoljuk, hogy az új telepítések esetén a Resource Manager modellt használja. Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Azure-beli virtuális gépek (VM) segítségével egy magas rendelkezésre állású SQL Server rendszer költségeinek adatbázis-rendszergazdák. Ez az oktatóanyag bemutatja, hogyan egy rendelkezésre állási csoporthoz az SQL Server Always On – teljes körű belül az Azure-környezet segítségével megvalósítható. Az oktatóanyag végén az SQL Server Always On megoldás az Azure-ban a következő elemekből áll:

* Virtuális hálózat, amely több alhálózat, például egy előtér- és a egy háttérbeli alhálózatot tartalmaz.
* Egy tartományvezérlő, egy Active Directory-tartományhoz.
* Két SQL Server virtuális gépek üzembe helyezett a háttérbeli alhálózathoz és Active Directory-tartományhoz csatlakoztatva.
* Egy három csomópontos Windows feladatátvevő fürt csomóponttöbbség kvórum modell.
* Egy rendelkezésre állási csoport két szinkron véglegesítésű másodpéldányt a rendelkezésre állási adatbázis.

Ebben a forgatókönyvben a megfelelő választás az egyszerűség érdekében az Azure-ban, nem pedig a költséghatékonyság vagy egyéb tényezők. Minimalizálhatja például, hogy menteni a számítási óra, az Azure-ban a tartományvezérlő használják, mint a kvórum tanúsító fájlmegosztás kétcsomópontos feladatátvevő fürtben két replika rendelkezésre állási csoport virtuális gépek számát. Ezt a módszert a virtuális gépek száma a fenti konfigurációból eggyel csökkenti.

Ez az oktatóanyag célja bemutatják, hogy kidolgozása során minden egyes lépést a részleteket a fent leírt megoldás beállításához szükséges lépéseket. Ezért ahelyett, hogy megadta a grafikus felhasználói Felülettel konfigurációs lépések, használja a PowerShell-szkriptek, gyorsan vezeti végig az egyes lépések. Ez az oktatóanyag feltételezi, hogy a következő:

* Már rendelkezik a virtuális gép-előfizetéssel rendelkező Azure-fiókra.
* Telepítette a [Azure PowerShell-parancsmagok](/powershell/azure/overview).
* Már van egy Always On rendelkezésre állási csoportok a helyszíni megoldásokhoz szilárd ismeretekkel. További információkért lásd: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Csatlakozás az Azure-előfizetéshez, és a virtuális hálózat létrehozása
1. PowerShell-ablakban a helyi számítógépen az Azure-modul importálása, töltse le a közzétételi beállítások a gép és a PowerShell-munkamenethez csatlakozhat az Azure-előfizetés a letöltött közzétételi beállítások importálása.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    A **Get-AzurePublishSettingsFile** parancs automatikusan létrehoz egy felügyeleti tanúsítványt, az Azure-ral, és letölti azt a gépre. A böngésző automatikusan megnyílik, és kéri, hogy adja meg a Microsoft-fiók hitelesítő adatait az Azure-előfizetésében. A letöltött **.publishsettings** fájlt, hogy az Azure-előfizetése kezeléséhez szükséges összes információt tartalmazza. Ez a fájl mentése egy helyi könyvtárba, után importálnia a **Import-AzurePublishSettingsFile** parancsot.

   > [!NOTE]
   > A .publishsettings fájlt tartalmaz a hitelesítő adatokat. (nem titkosított) az Azure-előfizetések és a szolgáltatások felügyeletéhez használt. A biztonsági szempontból ajánlott a fájlhoz, hogy a forrás-címtárak (például a Libraries\Documents mappában) kívül ideiglenesen tárolja, és törölje azt az importálás befejezése után. Egy rosszindulatú felhasználó hozzáférést kap a .publishsettings fájlt a Szerkesztés, létrehozása és törlése az Azure-szolgáltatások.

2. Adja meg, amely a felhőalapú informatikai infrastruktúra létrehozásához használni kívánt változók egy sorának.

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

    Figyelje meg, győződjön meg arról, hogy a parancsok később lesz sikeres, a következőhöz:

   * Változók **$storageAccountName** és **$dcServiceName** egyedinek kell lennie, mivel a felhőalapú tárolási fiók és a cloud server, osztályban, azonosítsa az interneten használhatók.
   * A nevek megadott változók **$affinityGroupName** és **$virtualNetworkName** vannak konfigurálva a virtuális hálózat konfigurációs dokumentum, amely később fogja használni.
   * **$sqlImageName** a Virtuálisgép-rendszerképet, amely tartalmazza az SQL Server 2012 Service Pack 1 Enterprise Edition frissített nevét adja meg.
   * Az egyszerűség kedvéért **Contoso! 000** ugyanazt a jelszót az egész oktatóanyag során használt van.

3. Affinitáscsoport létrehozásához.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Hozzon létre egy virtuális hálózatot egy konfigurációs fájl importálásával.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    A konfigurációs fájl a következő XML-dokumentum tartalmaz. Röviden, azt adja meg az nevű virtuális hálózat **ContosoNET** nevű affinitáscsoportban **ContosoAG**. A címtér rendelkezik **10.10.0.0/16** , és két alhálózatot **10.10.1.0/24** és **10.10.2.0/24**, amelyek az előtérbeli alhálózat és állnak vissza alhálózati jelölik. Az előtérbeli alhálózat, ahol elhelyezhet ügyfélalkalmazások, például a Microsoft SharePoint. A háttérrendszer alhálózat, az SQL Server virtuális gépek helyétől fog. Ha módosítja a **$affinityGroupName** és **$virtualNetworkName** változók korábban is módosítania kell a megfelelő alábbi nevét.

        <NetworkConfiguration xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
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

5. Hozzon létre egy tárfiókot, amelyet létrehozott, és állítsa be a jelenlegi tárfiókot az előfizetésében affinitáscsoportban van társítva.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Az új felhőalapú szolgáltatás, és a rendelkezésre állási készlet létrehozása a domain controller-kiszolgáló.

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

   * **Új AzureVMConfig** hoz létre egy Virtuálisgép-konfigurációt.
   * **-AzureProvisioningConfig** az egy különálló Windows Server-konfigurációs paramétereket biztosít.
   * **Adjon hozzá AzureDataDisk** ad hozzá az adatlemezt, amely nincs gyorsítótárazás az Active Directory-adatok tárolására szolgáló fogja használni.
   * **Új-AzureVM** hoz létre egy új felhőszolgáltatást, és az új Azure virtuális Gépen hoz létre az új felhőalapú szolgáltatás.

7. Várjon, amíg az új virtuális gép kiépítése, és töltse le a távoli asztali fájl a munkakönyvtár. Mivel az új Azure virtuális gép létrehozását, hosszabb ideig tart a `while` ciklus addig az új virtuális gép lekérdezésére, egészen addig, amíg használatra kész.

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

A domain controller-kiszolgáló már sikeresen kiépítve. Ezután konfigurálja az Active Directory-tartomány a domain controller-kiszolgálóra. Hagyja a PowerShell-ablakban nyissa meg a helyi számítógépen. A két SQL Server virtuális gép létrehozása újra használni.

## <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
1. A domain controller-kiszolgáló kapcsolódni a távoli asztali fájl elindításával. A gép rendszergazdai AzureAdmin felhasználónév és jelszó **Contoso! 000**, amely az új virtuális gép létrehozásakor megadott.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. Futtassa a következő **DCPROMO. EXE** parancs használatával állítsa be a **corp.contoso.com** tartomány, a adatkönyvtárak M-meghajtón.

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

    A parancs befejeződését követően a virtuális gép automatikusan újraindul.

4. Csatlakozhat a domain controller-kiszolgáló ismét elindítani a távoli asztali fájl. Ez alkalommal jelentkezik be, **Corp/rendszergazda**.
5. Nyisson meg egy PowerShell-ablakot rendszergazdai módban, és az Active Directory PowerShell-modul importálása a következő paranccsal:

        Import-Module ActiveDirectory

6. Futtassa az alábbi parancsokat a tartomány három felhasználókat hozzáadni.

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

    **CORP\Install** az SQL kiszolgáló-szolgáltatáspéldány, a feladatátvevő fürt és a rendelkezésre állási csoport kapcsolódó összes elemet konfigurálására szolgál. **CORP\SQLSvc1** és **CORP\SQLSvc2** használják az SQL Server-szolgáltatásfióknak a két SQL Server rendszerű virtuális gépekhez.
7. Ezután futtassa a következő parancsokat, amivel **CORP\Install** az engedélyeket a tartományban lévő számítógép-objektumok létrehozásához.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    A fent megadott GUID a számítógép-objektum típusa GUID azonosítója. A **CORP\Install** fiók igényeinek megfelelően a **összes tulajdonság olvasásához** és **számítógép-objektumok létrehozása** a feladatátvevő fürt az aktív közvetlen objektumok létrehozásához szükséges engedéllyel. A **összes tulajdonság olvasásához** engedély már elbírálása CORP\Install alapértelmezetten, így nem kell explicit módon biztosítania. A feladatátvevő fürt létrehozásához szükséges engedélyekről további információkért lásd: [feladatátvevő fürt – részletes útmutató: Az Active Directory fiókok konfigurálására](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Most, hogy végzett Active Directory és a felhasználói objektumok, akkor két SQL Server virtuális gépek hozhat létre, és csatlakoztassa őket a tartományhoz.

## <a name="create-the-sql-server-vms"></a>Az SQL Server virtuális gépek létrehozása
1. Továbbra is használhatja a PowerShell-ablakot, amely meg van nyitva a helyi számítógépen. A következő további változókat határozhat meg:

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

    Az IP-cím **10.10.0.4** általában az első virtuális gép, így létrehozott van rendelve a **10.10.0.0/16** , az Azure virtuális hálózat alhálózatán. Ellenőrizze, hogy ez az a domain controller-kiszolgáló címét futtatásával **IPCONFIG**.
2. Futtassa a következő parancsokat az első virtuális gép létrehozásához a feladatátvevő fürt, nevű parancsoknak **ContosoQuorum**:

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

    Vegye figyelembe a következő kapcsolatban a fenti parancsot:

   * **Új AzureVMConfig** egy Virtuálisgép-konfigurációt hoz létre a kívánt rendelkezésre állási készlet neve. A következő virtuális gépek jön az azonos rendelkezésre állási készlet neve úgy, hogy csatlakoznak, ugyanazon rendelkezésre állási csoporthoz.
   * **-AzureProvisioningConfig** létrehozott Active Directory-tartományhoz csatlakoztatja a virtuális Gépet.
   * **Set-AzureSubnet** vissza az alhálózaton helyezi el a virtuális Gépet.
   * **Új-AzureVM** hoz létre egy új felhőszolgáltatást, és az új Azure virtuális Gépen hoz létre az új felhőalapú szolgáltatás. A **DnsSettings** paraméter határozza meg, hogy a kiszolgálók az új cloud service-ben a DNS-kiszolgáló rendelkezik-e az IP-cím **10.10.0.4**. Ez az a domain controller-kiszolgáló IP-címét. Ez a paraméter van szükség ahhoz, hogy az új virtuális gépeket a cloud service-ben az Active Directory-tartományhoz csatlakoztatása sikeresen megtörtént. Ezt a paramétert állítsa be kézzel a domain controller-kiszolgáló az elsődleges DNS-kiszolgálóként használni, a virtuális gép kiépítése után a virtuális gépen az IPv4-beállításokkal, és ezután az Active Directory-tartományhoz csatlakoztatni a virtuális Gépet.
3. Futtassa a következő parancsokkal hozhat létre az SQL Server virtuális gépek nevű parancsoknak **ContosoSQL1** és **ContosoSQL2**.

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

    Vegye figyelembe a következő kapcsolatban a fenti parancsok:

   * **Új AzureVMConfig** használja az ugyanazon rendelkezésre állási készlet neve, mint a domain controller-kiszolgáló és az SQL Server 2012 Service Pack 1 Enterprise Edition lemezképet használja a virtuálisgép-katalógusában. Egyúttal az operációsrendszer-lemez olvasási gyorsítótárazás csak a (nincs írási gyorsítótárazás). Azt javasoljuk, hogy az adatbázisfájlok külön adatlemez csatolható a virtuális gép áttelepítése, és konfigurálja azt a nincs olvasási vagy írási gyorsítótárazást. Megoldásunk azonban az írási gyorsítótárazást az operációsrendszer-lemez található, mert nem tudja eltávolítani az operációsrendszer-lemez olvasási gyorsítótárazás eltávolítása.
   * **-AzureProvisioningConfig** létrehozott Active Directory-tartományhoz csatlakoztatja a virtuális Gépet.
   * **Set-AzureSubnet** vissza az alhálózaton helyezi el a virtuális Gépet.
   * **-AzureEndpoint** hozzáadja a hozzáférés végpontjait, hogy ügyfélalkalmazások elérjék ezeket az SQL Server services-példányok az interneten. Különböző portok ContosoSQL1 és ContosoSQL2 vannak megadva.
   * **Új-AzureVM** hoz létre az új SQL Server virtuális gép ugyanazon a felhőszolgáltatáson, ContosoQuorum. A virtuális gépek kell elhelyeznie az ugyanazon a felhőszolgáltatáson, ha azt szeretné, ha meg ugyanabban a rendelkezésre állási csoportban.
4. Várjon, amíg minden virtuális gép kiépítése, és töltse le a távoli asztali fájl a munkakönyvtárban minden virtuális géphez. A `for` hurok Váltás a három új virtuális gépet, és végrehajtja a legfelső szintű kapcsos zárójelek között lévő mindegyikük számára.

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

    Az SQL Server virtuális gépek most már kiépített és fut, de van telepítve az SQL Server alapértelmezett beállításokkal.

## <a name="initialize-the-failover-cluster-vms"></a>A feladatátvevő fürt virtuális gépeihez inicializálása
Ebben a szakaszban módosítania a három kiszolgáló, amely a feladatátvevő fürt és az SQL Server-telepítés fogja használni. Konkrétan:

* Összes kiszolgáló: Telepítenie kell a **feladatátvételi fürtszolgáltatás** funkció.
* Összes kiszolgáló: Hozzá kell adnia **CORP\Install** , a gép **rendszergazda**.
* ContosoSQL1 and ContosoSQL2 only: Hozzá kell adnia **CORP\Install** , egy **SysAdmin (rendszergazda)** az alapértelmezett adatbázis szerepköréhez.
* ContosoSQL1 and ContosoSQL2 only: Hozzá kell adnia **NT AUTHORITY\System** , egy jelentkezzen be a következő engedélyekkel:

  * Az ALTER minden rendelkezésre állási csoport
  * Connect SQL
  * Kiszolgáló állapotának megtekintése
* ContosoSQL1 and ContosoSQL2 only: A **TCP** protokoll már engedélyezve van az SQL Server virtuális gépen. Azonban továbbra is szeretné az SQL Server távoli elérés számára megnyitja a tűzfalat.

Most már készen áll a elindításához. Kezdve **ContosoQuorum**, kövesse az alábbi lépéseket:

1. Csatlakozás **ContosoQuorum** a távoli asztali fájlok elindításával. Használhatja a gépet rendszergazdai jogosultságú felhasználónevet **AzureAdmin** és a jelszó **Contoso! 000**, amely a virtuális gépek létrehozásakor megadott.
2. Győződjön meg arról, hogy a számítógépek rendelkeznek sikeresen csatlakoztatva lett **corp.contoso.com**.
3. Várjon, amíg befejeződik a folytatás előtt az automatikus inicializálás feladatok futtatása az SQL Server telepítése.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvételi fürtszolgáltatás.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adjon hozzá **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Jelentkezzen ki ContosoQuorum. Végzett a kiszolgáló most.

        logoff.exe

Ezután inicializálja **ContosoSQL1** és **ContosoSQL2**. Kövesse az alábbi lépéseket, amelyek a két SQL Server virtuális gépek esetén azonosak.

1. A két SQL Server virtuális gép kapcsolódni a távoli asztali fájlok elindításával. Használhatja a gépet rendszergazdai jogosultságú felhasználónevet **AzureAdmin** és a jelszó **Contoso! 000**, amely a virtuális gépek létrehozásakor megadott.
2. Győződjön meg arról, hogy a számítógépek rendelkeznek sikeresen csatlakoztatva lett **corp.contoso.com**.
3. Várjon, amíg befejeződik a folytatás előtt az automatikus inicializálás feladatok futtatása az SQL Server telepítése.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvételi fürtszolgáltatás.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adjon hozzá **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Importálja az SQL Server PowerShell-szolgáltatóban.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adjon hozzá **CORP\Install** , a sysadmin (rendszergazda) szerepkör alapértelmezett SQL Server-példányon.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adjon hozzá **NT AUTHORITY\System** , a bejelentkezés a fent leírt három engedélyekkel.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Nyissa meg a tűzfalon az SQL Server távoli hozzáféréshez.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Jelentkezzen ki mindkét virtuális gép.

         logoff.exe

Végül készen áll a rendelkezésre állási csoport konfigurálása. Az SQL Server PowerShell-szolgáltató segítségével végrehajthatja a munkát a **ContosoSQL1**.

## <a name="configure-the-availability-group"></a>A rendelkezésre állási csoport konfigurálása
1. Csatlakozás **ContosoSQL1** újra a távoli asztali fájlok elindításával. A bejelentkezés helyett a gép fiókkal jelentkezik be, **CORP\Install**.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. A következő változókat határozhat meg:

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
5. Módosítsa az SQL Server-szolgáltatásfiók számára ContosoSQL1 CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Módosítsa az SQL Server-szolgáltatásfiók számára ContosoSQL2 CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Töltse le **CreateAzureFailoverCluster.ps1** a [feladatátvevő fürt létrehozása az Always On rendelkezésre állási csoportok az Azure virtuális Gépen](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) a helyi munkakönyvtárba. Ez a szkript működő feladatátvevő fürt létrehozásához használni. Fontos információ a Windows feladatátvételi fürtszolgáltatás hogyan működjön együtt az Azure-hálózat: [az SQL Server Azure virtuális gépek magas rendelkezésre állás és vészhelyreállítás helyreállítási](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Módosítsa a munkakönyvtár és a feladatátvevő fürt létrehozása a letöltött szkript.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Engedélyezze az Always On rendelkezésre állási csoportok számára az alapértelmezett SQL Server-példányokat a **ContosoSQL1** és **ContosoSQL2**.

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
10. Hozzon létre egy biztonsági mentési könyvtárat, és adja meg az SQL Server szolgáltatás fiókokhoz tartozó engedélyeket. Ebben a könyvtárban fogja használni a másodlagos replikát a rendelkezésre állási adatbázis előkészítése.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Hozzon létre egy adatbázist a **ContosoSQL1** nevű **MyDB1**, egy teljes biztonsági mentés és a napló biztonsági mentését is igénybe vehet, és visszaállítja ezeket **ContosoSQL2** együtt a **WITH NORECOVERY PARAMÉTERREL**  lehetőséget.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. A rendelkezésre állási csoport végpontok létrehozása az SQL Server virtuális gépeken, és állítsa be a megfelelő engedélyekkel a végpontokon.

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
13. A rendelkezésre állási replikák létrehozásához.

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
14. Végül hozza létre a rendelkezésre állási csoportot, és a másodlagos másodpéldány csatlakoztatása a rendelkezésre állási csoporthoz.

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
Akkor most már sikeresen megvalósított SQL Server Always On rendelkezésre állási csoport létrehozásával az Azure-ban. Egy figyelőt a rendelkezésre állási csoport beállítása: [Always On rendelkezésre állási csoportokra vonatkozó ILB figyelő konfigurálása az Azure-ban](../classic/ps-sql-int-listener.md).

Más Azure-beli SQL Server használatával kapcsolatos információkért lásd: [Azure virtuális gépeken futó SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

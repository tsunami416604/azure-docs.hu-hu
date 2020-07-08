---
title: Az Always On rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépen a PowerShell használatával | Microsoft Docs
description: Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrásokat használja. A PowerShell használatával hozzon létre egy always on rendelkezésre állási csoportot az Azure-ban.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a4e2f175-fe56-4218-86c7-a43fb916cc64
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/17/2017
ms.author: mikeray
ms.openlocfilehash: 7f20d79ea353830b41290c7b91d8d1de2b1b3abe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84014859"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>Az Always On rendelkezésre állási csoport konfigurálása Azure-beli virtuális gépen PowerShell-lel
> [!div class="op_single_selector"]
> * [Klasszikus: felhasználói felület](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasszikus: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Mielőtt elkezdené, vegye figyelembe, hogy most már elvégezheti ezt a feladatot az Azure Resource Manager-modellben. Az Azure Resource Manager-modellt ajánlott új környezetekhez. Tekintse [meg SQL Server always on rendelkezésre állási csoportokat az Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)szolgáltatásban.

> [!IMPORTANT]
> Javasoljuk, hogy az új központi telepítések a Resource Manager-modellt használják. Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../../azure-resource-manager/management/deployment-models.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az Azure Virtual Machines (VM) segítségével az adatbázis-rendszergazdák csökkenthetik a magas rendelkezésre állású SQL Server rendszer költségeit. Ebből az oktatóanyagból megtudhatja, hogyan valósítható meg a rendelkezésre állási csoport a SQL Server always on Azure-környezeten belüli végpontok közötti használatával. Az oktatóanyag végén a SQL Server mindig az Azure-ban megoldás az alábbi elemekből áll:

* Olyan virtuális hálózat, amely több alhálózatot tartalmaz, beleértve az előtér-és a háttérbeli alhálózatot is.
* Active Directory tartománnyal rendelkező tartományvezérlő.
* Két SQL Server a háttérbeli alhálózatra telepített és a Active Directory tartományhoz csatlakoztatott virtuális gépek.
* Három csomópontos Windows feladatátvevő fürt a csomópontok többségi Kvórumának modelljével.
* Egy rendelkezésre állási adatbázis két szinkron véglegesíthető replikáját tartalmazó rendelkezésre állási csoport.

Ez a forgatókönyv ideális választás az Azure-beli egyszerűségre, nem pedig a költséghatékonyságra és egyéb tényezőkre. Például lekicsinyítheti a virtuális gépek számát egy két replika rendelkezésre állási csoport számára, hogy a számítási órákat az Azure-ban mentse, ha a tartományvezérlőt kvórum fájlmegosztásként használja egy két csomópontos feladatátvevő fürtben. Ez a módszer csökkenti a virtuális gépek darabszámát a fenti konfiguráció alapján.

Ebből az oktatóanyagból megtudhatja, hogy milyen lépéseket kell végrehajtania a fentebb ismertetett megoldás beállításához, az egyes lépések részleteinek kidolgozása nélkül. Ezért ahelyett, hogy a grafikus felhasználói felület konfigurációs lépéseit megadta, a PowerShell-parancsfájlok használatával gyorsan elvégezheti az egyes lépéseket. Ez az oktatóanyag a következőket feltételezi:

* Már rendelkezik egy Azure-fiókkal a virtuális gép előfizetésével.
* Telepítette a [Azure PowerShell-parancsmagokat](/powershell/azure/overview).
* Már rendelkezik az Always On rendelkezésre állási csoportok helyszíni megoldásokhoz való alapos ismeretével. További információ: [Always On rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Kapcsolódjon az Azure-előfizetéséhez, és hozza létre a virtuális hálózatot
1. A helyi számítógépen lévő PowerShell-ablakban importálja az Azure-modult, töltse le a közzétételi beállítások fájlt a gépre, és a letöltött közzétételi beállítások importálásával kapcsolja össze a PowerShell-munkamenetet az Azure-előfizetésével.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    A **Get-AzurePublishSettingsFile** parancs automatikusan létrehoz egy felügyeleti tanúsítványt az Azure-ban, és letölti azt a gépre. A rendszer automatikusan megnyit egy böngészőt, és megkéri, hogy adja meg az Azure-előfizetéséhez tartozó Microsoft-fiók hitelesítő adatokat. A letöltött **. publishsettings** fájl az Azure-előfizetés kezeléséhez szükséges összes információt tartalmazza. Miután mentette a fájlt egy helyi könyvtárba, importálja azt az **import-AzurePublishSettingsFile** parancs használatával.

   > [!NOTE]
   > A. publishsettings fájl tartalmazza az Azure-előfizetések és-szolgáltatások felügyeletéhez használt hitelesítő adatokat (kódolatlan). Ennek a fájlnak az ajánlott biztonsági gyakorlata, hogy ideiglenesen tárolja a forrás-címtárakon kívül (például a Libraries\Documents mappában), majd törölje azt az importálás befejeződése után. A. publishsettings fájl elérését elérő rosszindulatú felhasználó szerkesztheti, létrehozhatja és törölheti az Azure-szolgáltatásokat.

2. Definiáljon egy olyan változót, amelyet a felhőalapú informatikai infrastruktúra létrehozásához fog használni.

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

    Ügyeljen arra, hogy a parancsok később is sikeresek legyenek:

   * A **$storageAccountName** és **$dcServiceName** változóknak egyedieknek kell lenniük, mivel a felhőalapú Storage-fiók és a felhőalapú kiszolgáló azonosítására szolgálnak az interneten.
   * A **$affinityGroupName** és **$virtualNetworkName** változóhoz megadott nevek a virtuális hálózat konfigurációs dokumentumában vannak konfigurálva, amelyet később fog használni.
   * **$sqlImageName** a SQL Server 2012 Service Pack 1 Enterprise Edition verziót tartalmazó virtuálisgép-rendszerkép frissített nevét adja meg.
   * Az egyszerűség kedvéért a **contoso! 000** a teljes oktatóanyag során használt jelszó.

3. Hozzon létre egy affinitási csoportot.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Hozzon létre egy virtuális hálózatot egy konfigurációs fájl importálásával.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    A konfigurációs fájl a következő XML-dokumentumot tartalmazza. Röviden, a **ContosoAG**nevű affinitási csoportban egy **ContosoNET** nevű virtuális hálózatot határoz meg. Ez a címtartomány **10.10.0.0/16** , és két alhálózattal rendelkezik ( **10.10.1.0/24** és **10.10.2.0/24**), amelyek az első alhálózatot és a hátsó alhálózatot használják. Az első alhálózatban olyan ügyfélalkalmazások helyezhetők el, mint például a Microsoft SharePoint. A hátsó alhálózat a SQL Server virtuális gépek elhelyezésére szolgál. Ha korábban módosítja a **$affinityGroupName** és a **$virtualNetworkName** változót, akkor a megfelelő neveket is módosítania kell.

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

5. Hozzon létre egy olyan Storage-fiókot, amely társítva van a létrehozott affinitási csoporttal, és állítsa be az aktuális Storage-fiókként az előfizetésében.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Hozza létre a tartományvezérlő kiszolgálót az új Cloud Service és a rendelkezésre állási csoportba.

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

    Ezek a vezetékes parancsok a következő műveleteket végzik el:

   * A **New-AzureVMConfig** létrehoz egy virtuális gép konfigurációját.
   * Az **Add-AzureProvisioningConfig** egy önálló Windows Server konfigurációs paramétereit adja meg.
   * A **Add-AzureDataDisk** hozzáadja az adatlemezt, amelyet Active Directory-adattároláshoz fog használni, a gyorsítótárazás beállítás értékeként pedig a none értékre van állítva.
   * A **New-AzureVM** létrehoz egy új felhőalapú szolgáltatást, és létrehozza az új Azure-beli virtuális gépet az új felhőalapú szolgáltatásban.

7. Várjon, amíg az új virtuális gép kiépíthető, majd töltse le a távoli asztali fájlt a munkakönyvtárra. Mivel az új Azure-beli virtuális gép hosszú időt vesz igénybe, a `while` hurok továbbra is lekérdezi az új virtuális gépet, amíg készen nem áll a használatra.

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

A tartományvezérlő-kiszolgáló sikeresen kiépítve. Ezután konfigurálja a Active Directory tartományt ezen a tartományvezérlő-kiszolgálón. Hagyja nyitva a PowerShell ablakot a helyi számítógépen. Később ismét használni fogja a két SQL Server virtuális gép létrehozását.

## <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
1. Kapcsolódjon a tartományvezérlő kiszolgálóhoz a távoli asztal fájljának elindításával. Használja a gép rendszergazdája Felhasználónév AzureAdmin és a **contoso! 000**jelszót, amelyet az új virtuális gép létrehozásakor adott meg.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. Futtassa a következő **DCPROMO.EXE** parancsot a **Corp.contoso.com** tartomány beállításához az M meghajtón található adatkönyvtárakkal.

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

    A parancs befejeződése után a virtuális gép automatikusan újraindul.

4. Kapcsolódjon újra a tartományvezérlő-kiszolgálóhoz a távoli asztal fájljának elindításával. Ezúttal jelentkezzen be **corp\rendszergazda**-ként.
5. Nyisson meg egy PowerShell-ablakot rendszergazdai módban, és importálja a Active Directory PowerShell-modult a következő parancs használatával:

        Import-Module ActiveDirectory

6. Futtassa a következő parancsokat, hogy három felhasználót vegyen fel a tartományba.

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

    A **CORP\Install** a SQL Server szolgáltatási példányokkal, a feladatátvevő fürttel és a rendelkezésre állási csoporttal kapcsolatos összes adat konfigurálására szolgál. A **CORP\SQLSvc1** és a **CORP\SQLSvc2** a két SQL Server virtuális gép SQL Server-szolgáltatásfiókok használják.
7. Ezután futtassa a következő parancsokat, hogy **CORP\Install** a számítógép-objektumok tartományon belüli létrehozására vonatkozó engedélyeket.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    A fent megadott GUID a számítógép-objektum típusának GUID azonosítója. A **CORP\Install** fióknak az **összes tulajdonság olvasása** és **számítógép-objektumok létrehozása** engedéllyel kell rendelkeznie a feladatátvevő fürthöz tartozó aktív közvetlen objektumok létrehozásához. Az **olvasás az összes tulajdonságra** engedélyt a CORP\Install alapértelmezés szerint már megadta, így nem kell explicit módon megadnia. A feladatátvevő fürt létrehozásához szükséges engedélyekkel kapcsolatos további információkért tekintse meg a [feladatátvevő fürt részletes útmutatóját: fiókok konfigurálása Active Directoryban](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx).

    Most, hogy befejezte a Active Directory és a felhasználói objektumok konfigurálását, hozzon létre két SQL Server virtuális gépet, és csatlakoztassa őket ehhez a tartományhoz.

## <a name="create-the-sql-server-vms"></a>A SQL Server virtuális gépek létrehozása
1. Folytassa a helyi számítógépen megnyitott PowerShell-ablak használatának folytatásával. Adja meg a következő további változókat:

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

    Az IP- **10.10.0.4** általában az Azure-beli virtuális hálózat **10.10.0.0/16** alhálózatában létrehozott első virtuális géphez van hozzárendelve. Győződjön meg arról, hogy ez a tartományvezérlő-kiszolgáló címe az **ipconfig**futtatásával.
2. Futtassa a következő vezetékes parancsokat az első virtuális gép létrehozásához a feladatátvevő fürtben, **ContosoQuorum**néven:

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

    Vegye figyelembe a következőket a fenti parancsra vonatkozóan:

   * A **New-AzureVMConfig** létrehoz egy virtuálisgép-konfigurációt a kívánt rendelkezésre állási készlet nevével. A következő virtuális gépek ugyanazzal a rendelkezésre állási csoport nevével jönnek létre, így ugyanahhoz a rendelkezésre állási csoporthoz csatlakozva lesznek.
   * A **Add-AzureProvisioningConfig** csatlakoztatja a virtuális gépet a létrehozott Active Directory tartományhoz.
   * A **set-AzureSubnet** a virtuális gépet a hátsó alhálózaton helyezi el.
   * A **New-AzureVM** létrehoz egy új felhőalapú szolgáltatást, és létrehozza az új Azure-beli virtuális gépet az új felhőalapú szolgáltatásban. A **DnsSettings** paraméter azt adja meg, hogy az új felhőalapú szolgáltatás kiszolgálóihoz tartozó DNS-kiszolgáló IP- **10.10.0.4**rendelkezik. Ez a tartományvezérlő kiszolgálójának IP-címe. Ez a paraméter szükséges ahhoz, hogy az új virtuális gépek a felhőalapú szolgáltatásban sikeresen csatlakozzanak a Active Directory tartományhoz. A paraméter nélkül manuálisan kell beállítania az IPv4-beállításokat a virtuális gépen, hogy a tartományvezérlő kiszolgálót elsődleges DNS-kiszolgálóként használja a virtuális gép üzembe helyezése után, majd csatlakoztassa a virtuális gépet a Active Directory tartományhoz.
3. Futtassa a következő vezetékes parancsokat a **ContosoSQL1** és a **ContosoSQL2**nevű SQL Server virtuális gépek létrehozásához.

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

    A fenti parancsokkal kapcsolatban vegye figyelembe a következőket:

   * A **New-AzureVMConfig** ugyanazt a rendelkezésre állási csoport nevét használja, mint a tartományvezérlő-kiszolgáló, és a SQL Server 2012 Service Pack 1 Enterprise Edition rendszerképet használja a virtuális gép galériájában. Emellett az operációs rendszer lemezét csak olvasható gyorsítótárazásra állítja be (nincs írási gyorsítótárazás). Javasoljuk, hogy telepítse át az adatbázisfájlok egy különálló adatlemezre, amelyet a virtuális géphez csatlakoztat, és konfigurálja azt olvasási vagy írási gyorsítótárazás nélkül. A következő legjobb megoldás azonban az írási gyorsítótárazás eltávolítása az operációsrendszer-lemezen, mert az operációs rendszer lemezén nem távolítható el az olvasási gyorsítótárazás.
   * A **Add-AzureProvisioningConfig** csatlakoztatja a virtuális gépet a létrehozott Active Directory tartományhoz.
   * A **set-AzureSubnet** a virtuális gépet a hátsó alhálózaton helyezi el.
   * Az **Add-AzureEndpoint** hozzáférési végpontokat ad hozzá, hogy az ügyfélalkalmazások hozzáférhessenek ezekhez a SQL Server Services-példányokhoz az interneten. A ContosoSQL1 és a ContosoSQL2 különböző portokat kap.
   * A **New-AzureVM** létrehozza az új SQL Server VM ugyanabban a felhőalapú szolgáltatásban, mint a ContosoQuorum. A virtuális gépeket ugyanabban a felhőalapú szolgáltatásban kell elhelyezni, ha azt szeretné, hogy az azonos rendelkezésre állási csoportba kerüljön.
4. Várjon, amíg minden virtuális gép kiépíthető, és mindegyik virtuális gép letölti a távoli asztali fájlt a munkakönyvtárra. A `for` hurok a három új virtuális gépen halad át, és a parancsokat a legfelső szintű kapcsos zárójelben hajtja végre.

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

    A SQL Server virtuális gépek üzembe helyezése és futtatása már megtörtént, de az alapértelmezett beállításokkal SQL Server telepíti őket.

## <a name="initialize-the-failover-cluster-vms"></a>A feladatátvevő fürt virtuális gépei inicializálásának inicializálása
Ebben a szakaszban módosítania kell a feladatátvevő fürtben használni kívánt három kiszolgálót és a SQL Server telepítését. Ezek a következők:

* Minden kiszolgáló: telepítenie kell a **feladatátvételi fürtszolgáltatást** .
* Minden kiszolgáló: a **CORP\Install** -t a számítógép **rendszergazdájaként**kell hozzáadnia.
* Csak ContosoSQL1 és ContosoSQL2: a **CORP\Install** -et **rendszergazda** szerepkörként kell felvennie az alapértelmezett adatbázisba.
* Csak ContosoSQL1 és ContosoSQL2: a következő engedélyekkel kell hozzáadnia a bejelentkezéshez az **NT AUTHORITY\System** :

  * A rendelkezésre állási csoport módosítása
  * SQL-kapcsolat
  * Kiszolgáló állapotának megtekintése
* Csak ContosoSQL1 és ContosoSQL2: a **TCP** protokoll már engedélyezve van a SQL Server VM. Azonban továbbra is meg kell nyitnia a tűzfalat SQL Server távoli eléréséhez.

Most már készen áll a kezdésre. A **ContosoQuorum**-től kezdve kövesse az alábbi lépéseket:

1. A távoli asztal fájljainak elindításával csatlakozhat a **ContosoQuorum** . Használja a gép rendszergazdája Felhasználónév **AzureAdmin** és a **contoso! 000**jelszót, amelyet a virtuális gépek létrehozásakor adott meg.
2. Ellenőrizze, hogy a számítógépek sikeresen csatlakoznak-e a **Corp.contoso.com**.
3. Várjon, amíg a SQL Server telepítés befejezi az automatikus inicializálási feladatok futtatását a továbblépés előtt.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvételi fürtszolgáltatást.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá a **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Jelentkezzen ki a ContosoQuorum. Most már ezzel a kiszolgálóval végzett.

        logoff.exe

Ezután inicializálja a **ContosoSQL1** és a **ContosoSQL2**. Kövesse az alábbi lépéseket, amelyek azonosak mindkét SQL Server virtuális gépek esetében.

1. Kapcsolódjon a két SQL Server virtuális géphez a távoli asztal fájljainak elindításával. Használja a gép rendszergazdája Felhasználónév **AzureAdmin** és a **contoso! 000**jelszót, amelyet a virtuális gépek létrehozásakor adott meg.
2. Ellenőrizze, hogy a számítógépek sikeresen csatlakoznak-e a **Corp.contoso.com**.
3. Várjon, amíg a SQL Server telepítés befejezi az automatikus inicializálási feladatok futtatását a továbblépés előtt.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvételi fürtszolgáltatást.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá a **CORP\Install** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Importálja a SQL Server PowerShell-szolgáltatót.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adja hozzá a **CORP\Install** -t az alapértelmezett SQL Server-példány sysadmin szerepköre.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adja hozzá az **NT AUTHORITY\System** bejelentkezését a fent ismertetett három engedélyhez.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. SQL Server távoli eléréséhez nyissa meg a tűzfalat.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Jelentkezzen ki mindkét virtuális gépről.

         logoff.exe

Végül már készen áll a rendelkezésre állási csoport konfigurálására. A SQL Server PowerShell-szolgáltató használatával hajtsa végre az összes munkát a **ContosoSQL1**-on.

## <a name="configure-the-availability-group"></a>A rendelkezésre állási csoport konfigurálása
1. A távoli asztal fájljainak elindításával ismét csatlakozhat a **ContosoSQL1** . A számítógépfiók használatával történő bejelentkezés helyett jelentkezzen be a **CORP\Install**használatával.
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
4. Importálja a SQL Server PowerShell-szolgáltatót.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. A ContosoSQL1 SQL Server-szolgáltatásfiók módosítása a CORP\SQLSvc1.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. A ContosoSQL2 SQL Server-szolgáltatásfiók módosítása a CORP\SQLSvc2.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Töltse le **CreateAzureFailoverCluster.ps1t** a [feladatátvevő fürt létrehozása az Azure-beli virtuális gépen található always on rendelkezésre állási csoportokhoz](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) a helyi munkakönyvtárba. Ezt a szkriptet fogja használni a funkcionális feladatátvevő fürt létrehozásához. Fontos információk arról, hogy a Windows feladatátvételi fürtszolgáltatás hogyan kommunikál az Azure-hálózattal: a [magas rendelkezésre állás és a vész-helyreállítás az azure Virtual Machines SQL Server](../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).
8. Váltson a munkakönyvtárra, és hozza létre a feladatátvevő fürtöt a letöltött parancsfájllal.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Az Always On rendelkezésre állási csoportok engedélyezése az alapértelmezett SQL Server példányok számára a **ContosoSQL1** és a **ContosoSQL2**.

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
10. Hozzon létre egy biztonsági mentési könyvtárat, és adjon meg engedélyeket a SQL Server-szolgáltatásfiókok számára. Ezt a könyvtárat fogja használni a rendelkezésre állási adatbázis előkészítéséhez a másodlagos replikán.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Hozzon létre egy adatbázist a **MyDB1**nevű **ContosoSQL1** , használja a teljes biztonsági mentést és a napló biztonsági mentését, majd állítsa vissza őket a **ContosoSQL2** a **with derecovery** kapcsolóval.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Hozza létre a rendelkezésre állási csoport végpontjait a SQL Server virtuális gépeken, és állítsa be a megfelelő engedélyeket a végpontokon.

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
13. Hozza létre a rendelkezésre állási replikákat.

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
14. Végül hozza létre a rendelkezésre állási csoportot, és csatlakoztassa a másodlagos replikát a rendelkezésre állási csoporthoz.

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
Sikeresen implementálta SQL Server always on Azure-beli rendelkezésre állási csoport létrehozásával. Ehhez a rendelkezésre állási csoporthoz tartozó figyelő konfigurálását lásd: [ILB-figyelő konfigurálása always on rendelkezésre állási csoportok számára az Azure-ban](../classic/ps-sql-int-listener.md).

További információ az Azure-beli SQL Server használatáról: [SQL Server az Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md)szolgáltatásban.

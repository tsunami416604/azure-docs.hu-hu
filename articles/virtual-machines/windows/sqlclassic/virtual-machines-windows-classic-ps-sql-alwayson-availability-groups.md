---
title: Konfigurálja a Mindig elérhető helyen szolgáltatást csoport egy Azure virtuális gép en PowerShell használatával | Microsoft dokumentumok
description: Ez az oktatóanyag a klasszikus üzembe helyezési modellel létrehozott erőforrásokat használja. A PowerShell segítségével hozzon létre egy mindig elérhető állási csoportot az Azure-ban.
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
ms.openlocfilehash: ba6f1300353247ef2de99b2bd903bc82665d9a52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978143"
---
# <a name="configure-the-always-on-availability-group-on-an-azure-vm-with-powershell"></a>A Mindig elérhető álláscsoport konfigurálása egy Azure-beli virtuális gépen a PowerShell használatával
> [!div class="op_single_selector"]
> * [Klasszikus: UI](../classic/portal-sql-alwayson-availability-groups.md)
> * [Klasszikus: PowerShell](../classic/ps-sql-alwayson-availability-groups.md)
<br/>

Mielőtt elkezdené, fontolja meg, hogy most már befejezheti ezt a feladatot az Azure erőforrás-kezelő modellben. Az Azure erőforrás-kezelői modelljét az új üzembe helyezésekhez javasoljuk. Lásd: [SQL Server Always On availability csoportok Azure virtuális gépeken.](../sql/virtual-machines-windows-portal-sql-availability-group-overview.md)

> [!IMPORTANT]
> Azt javasoljuk, hogy a legtöbb új központi telepítések használja az Erőforrás-kezelő modell. Az Azure két különböző üzembe helyezési modellt rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Erőforrás-kezelőés a klasszikus.](../../../azure-resource-manager/management/deployment-models.md) Ez a cikk a klasszikus üzembehelyezési modellt ismerteti.

Az Azure virtuális gépek (VM-ek) segíthetnek az adatbázis-rendszergazdáknak a magas rendelkezésre állású SQL Server-rendszer költségeinek csökkentésében. Ez az oktatóanyag bemutatja, hogyan valósíthatja meg a rendelkezésre állási csoport használatával SQL Server Mindig a teljes körű egy Azure-környezetben. Az oktatóanyag végén az SQL Server Always On megoldása az Azure-ban a következő elemekből fog állni:

* Több alhálózatot tartalmazó virtuális hálózat, beleértve az előtér- és a háttér-alhálózatot.
* Active Directory-tartománnyal rendelkező tartományvezérlő.
* Két SQL Server virtuális gép, amelyek a háttér-alhálózatra vannak telepítve, és csatlakoznak az Active Directory tartományhoz.
* Háromcsomópontos Windows feladatátvevő fürt a Csomópont többségi kvórummodellel.
* Egy rendelkezésre állási csoport két szinkron véglegesítési replikák egy rendelkezésre állási adatbázis.

Ez a forgatókönyv az Azure egyszerűsége, nem pedig a költséghatékonyság vagy más tényezők miatt érhető el. Például minimalizálhatja a virtuális gépek számát egy két replika rendelkezésre állási csoport menteni a számítási órák az Azure-ban a tartományvezérlő, mint a kvórumfájl-megosztás tanúsító egy kétcsomópontos feladatátvételi fürtben. Ez a módszer csökkenti a virtuális gépek számát egy a fenti konfigurációból.

Ez az oktatóanyag a leírt megoldás fenti beállításához szükséges lépéseket ismerteti, az egyes lépések részleteinek részletezése nélkül. Ezért ahelyett, hogy a GUI konfigurációs lépéseket, a PowerShell-parancsfájlok segítségével gyorsan végigminden egyes lépés. Ez az oktatóanyag a következőket feltételezi:

* Már rendelkezik egy Azure-fiókkal a virtuális gép-előfizetéssel.
* Telepítette az [Azure PowerShell-parancsmagokat.](/powershell/azure/overview)
* Már rendelkezik az Always On rendelkezésre állási csoportok megbízható megértésével a helyszíni megoldásokhoz. További információ: [Mindig a rendelkezésre állási csoportok (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

## <a name="connect-to-your-azure-subscription-and-create-the-virtual-network"></a>Csatlakozás Azure-előfizetéséhez és a virtuális hálózat létrehozása
1. A helyi számítógép PowerShell-ablakában importálja az Azure-modult, töltse le a közzétételi beállításfájlt a gépre, és csatlakoztassa a PowerShell-munkamenetet az Azure-előfizetéshez a letöltött közzétételi beállítások importálásával.

        Import-Module "C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\Azure\Azure.psd1"
        Get-AzurePublishSettingsFile
        Import-AzurePublishSettingsFile <publishsettingsfilepath>

    A **Get-AzurePublishSettingsFile** parancs automatikusan létrehoz egy felügyeleti tanúsítványt az Azure-ral, és letölti azt a gépre. A böngésző automatikusan megnyílik, és a rendszer kéri, hogy adja meg a Microsoft-fiók hitelesítő adatait az Azure-előfizetéshez. A letöltött **.publishsettings** fájl tartalmazza az Azure-előfizetés kezeléséhez szükséges összes információt. Miután mentette ezt a fájlt egy helyi könyvtárba, importálja az **Importálás-AzurePublishSettingsFile** paranccsal.

   > [!NOTE]
   > A .publishsettings fájl tartalmazza az Azure-előfizetések és -szolgáltatások felügyeletéhez használt hitelesítő adatokat (kódolatlan). A fájl biztonsági ajánlott eljárása, ha ideiglenesen a forráskönyvtárakon kívül tárolja (például a Könyvtárak\Dokumentumok mappában), majd az importálás befejezése után törli. A .publishsettings fájlhoz hozzáférést megkapó rosszindulatú felhasználók szerkeszthetik, létrehozhatják és törölhetik az Azure-szolgáltatásokat.

2. Határozza meg a felhőinformatikai infrastruktúra létrehozásához használt változók sorozatát.

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

    Ügyeljen az alábbiakra annak érdekében, hogy a parancsok később sikeresek legyenek:

   * A **változóknak $storageAccountName** és **$dcServiceName** egyedinek kell lenniük, mivel a felhőalapú tárfiók, illetve a felhőkiszolgáló azonosítására szolgálnak az interneten.
   * A változókhoz **$affinityGroupName** és **$virtualNetworkName** megadott nevek a később használni kívánt virtuális hálózati konfigurációs dokumentumban vannak konfigurálva.
   * **$sqlImageName** az SQL Server 2012 Service Pack 1 Enterprise Edition programot tartalmazó virtuális géplemez frissített nevét adja meg.
   * Az egyszerűség kedvéért a **Contoso!000** ugyanaz a jelszó, amelyet a teljes oktatóanyag során használ.

3. Hozzon létre egy affinitáscsoportot.

        New-AzureAffinityGroup `
            -Name $affinityGroupName `
            -Location $location `
            -Description $affinityGroupDescription `
            -Label $affinityGroupLabel

4. Hozzon létre egy virtuális hálózatot egy konfigurációs fájl importálásával.

        Set-AzureVNetConfig `
            -ConfigurationPath $networkConfigPath

    A konfigurációs fájl a következő XML-dokumentumot tartalmazza. Röviden, megadja a **ContosoNET** nevű virtuális hálózatot a **ContosoAG**nevű affinitáscsoportban. A **10.10.0.0/16** címtérrel rendelkezik, és két alhálózatot, **a 10.10.1.0/24** és **a 10.10.2.0/24-et,** amelyek az első alhálózat, illetve a hátsó alhálózat. Az elülső alhálózat ban helyezheti el az ügyfélalkalmazásokat, például a Microsoft SharePointot. A hátsó alhálózat, ahol el fogja helyezni az SQL Server virtuális gépek. Ha korábban módosítja a **$affinityGroupName** és **$virtualNetworkName** változókat, akkor az alábbi neveket is meg kell változtatnia.

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

5. Hozzon létre egy tárfiókot, amely a létrehozott affinitáscsoporthoz van társítva, és állítsa be az előfizetés aktuális tárfiókaként.

        New-AzureStorageAccount `
            -StorageAccountName $storageAccountName `
            -Label $storageAccountLabel `
            -AffinityGroup $affinityGroupName
        Set-AzureSubscription `
            -SubscriptionName (Get-AzureSubscription).SubscriptionName `
            -CurrentStorageAccount $storageAccountName

6. Hozza létre a tartományvezérlő-kiszolgálót az új felhőszolgáltatás és rendelkezésre állási csoportban.

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

    Ezek a vezetékes parancsok a következő ke, hogy:

   * **Az új AzureVMConfig** virtuális gépkonfigurációt hoz létre.
   * **Add-AzureProvisioningConfig** adja meg egy önálló Windows-kiszolgáló konfigurációs paramétereit.
   * **Az Add-AzureDataDisk** hozzáadja az Active Directory-adatok tárolására használt adatlemezt, és a gyorsítótárazási beállítás nincs.
   * **Az Új-AzureVM** új felhőszolgáltatást hoz létre, és létrehozza az új Azure virtuális gépaz új felhőszolgáltatásban.

7. Várja meg, amíg az új virtuális gép teljes mértékben kivan építve, és töltse le a távoli asztali fájlt a munkakönyvtárba. Mivel az új Azure-virtuális gép kiépítése `while` hosszú időt vesz igénybe, a hurok továbbra is az új virtuális gép lekérdezése, amíg készen áll a használatra.

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

A tartományvezérlő-kiszolgáló kiépítése sikeresen megtörtént. Ezután konfigurálja az Active Directory tartományt ezen a tartományvezérlő-kiszolgálón. Hagyja nyitva a PowerShell ablakot a helyi számítógépen. Később újra használni fogja a két SQL Server virtuális gép létrehozásához.

## <a name="configure-the-domain-controller"></a>A tartományvezérlő konfigurálása
1. Csatlakozzon a tartományvezérlő-kiszolgálóhoz a távoli asztali fájl elindításával. Használja a számítógép-rendszergazda felhasználónevét AzureAdmin és a **contoso!000**jelszót, amelyet az új virtuális gép létrehozásakor adott meg.
2. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
3. Futtassa a következő DCPROMO.Run the following **DCPROMO. EXE** parancsot a **corp.contoso.com** tartomány beállításához az M meghajtóadat-könyvtáraival.

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

4. Csatlakozzon újra a tartományvezérlő-kiszolgálóhoz a távoli asztali fájl elindításával. Ezúttal jelentkezzen be **corp\administrator néven.**
5. Nyisson meg egy PowerShell-ablakot rendszergazdai módban, és importálja az Active Directory PowerShell-modult a következő paranccsal:

        Import-Module ActiveDirectory

6. Futtassa a következő parancsokat, és vegyen fel három felhasználót a tartományba.

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

    **A CORP\Install** az SQL Server szolgáltatáspéldányokkal, a feladatátvevő fürttel és a rendelkezésre állási csoporttal kapcsolatos összes szolgáltatás konfigurálására szolgál. **A CORP\SQLSvc1** és **a CORP\SQLSvc2** a két SQL Server virtuális gép SQL Server szolgáltatásfiókjaként szolgál.
7. Ezután futtassa a következő parancsokat, hogy **a CORP\Telepítse** az engedélyeket a tartományban lévő számítógép-objektumok létrehozásához.

        Cd ad:
        $sid = new-object System.Security.Principal.SecurityIdentifier (Get-ADUser "Install").SID
        $guid = new-object Guid bf967a86-0de6-11d0-a285-00aa003049e2
        $ace1 = new-object System.DirectoryServices.ActiveDirectoryAccessRule $sid,"CreateChild","Allow",$guid,"All"
        $corp = Get-ADObject -Identity "DC=corp,DC=contoso,DC=com"
        $acl = Get-Acl $corp
        $acl.AddAccessRule($ace1)
        Set-Acl -Path "DC=corp,DC=contoso,DC=com" -AclObject $acl

    A fent megadott GUID a számítógépobjektum-típus GUID azonosítója. A **CORP\Install** fióknak szüksége van az **Összes olvasása tulajdonságra** és **a Számítógép-objektumok létrehozására** vonatkozó engedélyre a feladatátvevő fürt Active Direct-objektumainak létrehozásához. Alapértelmezés szerint az **Összes tulajdonság olvasása** engedély alapértelmezés szerint már meg van adva a CORP\Install szolgáltatásnak, így nem kell explicit módon megadnia. A feladatátvevő fürt létrehozásához szükséges engedélyekről a [Feladatátvevő fürt részletes útmutatója: Fiókok konfigurálása az Active Directoryban](https://technet.microsoft.com/library/cc731002%28v=WS.10%29.aspx)című témakörben talál további információt.

    Most, hogy befejezte az Active Directory és a felhasználói objektumok konfigurálását, két SQL Server virtuális gépet hoz létre, és csatlakozik hozzájuk ehhez a tartományhoz.

## <a name="create-the-sql-server-vms"></a>Sql Server virtuális gépek létrehozása
1. Továbbra is használja a PowerShell-ablakot, amely meg van nyitva a helyi számítógépen. Adja meg a következő további változókat:

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

    A **10.10.0.4** IP-cím általában az Azure virtuális hálózat **10.10.0.0/16** alhálózatában létrehozott első virtuális géphez van rendelve. **Az IPCONFIG**futtatásával ellenőrizze, hogy ez-e a tartományvezérlő-kiszolgáló címe.
2. A **contosoQuorum**nevű feladatátvevő fürt első virtuális gépének létrehozásához futtassa a következő vezetékes parancsokat:

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

    Vegye figyelembe a fenti paranccsal kapcsolatban a következőket:

   * **Az új AzureVMConfig virtuálisgép-konfigurációt** hoz létre a kívánt rendelkezésre állási készlet nevével. A következő virtuális gépek ugyanazzal az elérhetőségi készlet névvel jönnek létre, hogy ugyanahhoz az elérhetőségi csoporthoz csatlakozzanak.
   * **Add-AzureProvisioningConfig** csatlakozik a virtuális gép a létrehozott Active Directory-tartományhoz.
   * **Set-AzureSubnet** helyezi a virtuális gép a hátsó alhálózatba.
   * **Az Új-AzureVM** új felhőszolgáltatást hoz létre, és létrehozza az új Azure virtuális gépaz új felhőszolgáltatásban. A **DnsSettings** paraméter azt adja meg, hogy az új felhőszolgáltatás kiszolgálóinak DNS-kiszolgálója **10.10.0.4**IP-címmel rendelkezik. Ez a tartományvezérlő-kiszolgáló IP-címe. Ez a paraméter szükséges ahhoz, hogy a felhőszolgáltatásban lévő új virtuális gépek sikeresen csatlakozhassanak az Active Directory-tartományhoz. E paraméter nélkül manuálisan kell beállítania az IPv4-beállításokat a virtuális gépben, hogy a tartományvezérlő-kiszolgálót használja elsődleges DNS-kiszolgálóként a virtuális gép kiépítése után, majd csatlakoztassa a virtuális gépaz Active Directory tartományhoz.
3. A **ContosoSQL1** és a **ContosoSQL2**nevű SQL Server virtuális gépek létrehozásához futtassa a következő vezetékes parancsokat.

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

    Vegye figyelembe a fenti parancsokra vonatkozóan a következőket:

   * **Az Új-AzureVMConfig** ugyanazt a rendelkezésre állási készletnevet használja, mint a tartományvezérlő-kiszolgáló, és az SQL Server 2012 Service Pack 1 Enterprise Edition lemezképet használja a virtuálisgép-gyűjteményben. Azt is beállítja az operációs rendszer lemezét, hogy csak olvasási gyorsítótárazás (nincs írási gyorsítótárazás). Azt javasoljuk, hogy telepítse át az adatbázisfájlokat egy külön adatlemezre, amelyet a virtuális géphez csatol, és állítsa be olvasási vagy írási gyorsítótárazás nélkül. A következő legjobb dolog azonban az írási gyorsítótárazás eltávolítása az operációs rendszer lemezén, mert az operációs rendszer lemezén nem lehet olvasni az olvasási gyorsítótárazást eltávolítani.
   * **Add-AzureProvisioningConfig** csatlakozik a virtuális gép a létrehozott Active Directory-tartományhoz.
   * **Set-AzureSubnet** helyezi a virtuális gép a hátsó alhálózatba.
   * **Az Add-AzureEndpoint** hozzáférési végpontokat ad hozzá, hogy az ügyfélalkalmazások elérhessék ezeket az SQL Server-szolgáltatási példányokat az interneten. A ContosoSQL1 és a ContosoSQL2 különböző portokat kap.
   * **Az új AzureVM** az új SQL Server virtuális gép ugyanabban a felhőszolgáltatásban hozza létre, mint a ContosoQuorum. A virtuális gépeket ugyanabban a felhőszolgáltatásban kell elhelyeznie, ha azt szeretné, hogy ugyanabban a rendelkezésre állási csoportban legyenek.
4. Várja meg, amíg az egyes virtuális gépek teljes mértékben ki vannak építve, és az egyes virtuális gépek letöltik a távoli asztali fájlt a munkakönyvtárba. A `for` ciklus végighalad a három új virtuális gépen, és végrehajtja a parancsokat a legfelső szintű göndör zárójelben mindegyikhez.

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

    Az SQL Server virtuális gépek most már ki vannak építve és futnak, de az SQL Server alapértelmezett beállításokkal van telepítve.

## <a name="initialize-the-failover-cluster-vms"></a>A feladatátvevő fürt virtuális gépeiink inicializálása
Ebben a szakaszban módosítania kell a feladatátvevő fürtben és az SQL Server telepítésében használt három kiszolgálót. Ezek a következők:

* Minden kiszolgáló: Telepítenie kell a **feladatátvevő fürtözési** szolgáltatást.
* Minden kiszolgáló: Hozzá kell adnia **a CORP\Install** **programadminisztrátort**.
* ContosoSQL1 és ContosoSQL2 csak: Hozzá kell adnia **corp\Install** **rendszergazdai** szerepkörként az alapértelmezett adatbázisban.
* ContosoSQL1 és ContosoSQL2 csak: Az **NT AUTHORITY\System** bejelentkezésként a következő engedélyekkel kell hozzáadnia:

  * Bármely rendelkezésre állási csoport módosítása
  * Az SQL csatlakoztatása
  * Kiszolgáló állapotának megtekintése
* ContosoSQL1 és ContosoSQL2 csak: A **TCP** protokoll már engedélyezve van az SQL Server virtuális gép. Az SQL Server távoli eléréséhez azonban továbbra is meg kell nyitnia a tűzfalat.

Készen áll a kezdésre. A **ContosoQuorum**kezdetben kövesse az alábbi lépéseket:

1. Csatlakozzon a **ContosoQuorum-hoz** a távoli asztali fájlok elindításával. Használja a számítógép-rendszergazda felhasználónevét **AzureAdmin** és a jelszót **Contoso!000**, amely a virtuális gépek létrehozásakor megadott.
2. Ellenőrizze, hogy a számítógépek sikeresen csatlakoztak-e **a corp.contoso.com.**
3. A folytatás előtt várja meg, amíg az SQL Server telepítése befejezi az automatikus inicializálási feladatok futtatását.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvevő fürtözési szolgáltatását.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá **a CORP\Install mappát** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Jelentkezzen ki a ContosoQuorum-ból. Most már végzett ezzel a szerverrel.

        logoff.exe

Ezután inicializálja **a ContosoSQL1** és **a ContosoSQL2 programot.** Kövesse az alábbi lépéseket, amelyek mindkét SQL Server virtuális gép esetében azonosak.

1. Csatlakozzon a két SQL Server virtuális géphez a távoli asztali fájlok elindításával. Használja a számítógép-rendszergazda felhasználónevét **AzureAdmin** és a jelszót **Contoso!000**, amely a virtuális gépek létrehozásakor megadott.
2. Ellenőrizze, hogy a számítógépek sikeresen csatlakoztak-e **a corp.contoso.com.**
3. A folytatás előtt várja meg, amíg az SQL Server telepítése befejezi az automatikus inicializálási feladatok futtatását.
4. Nyisson meg egy PowerShell-ablakot rendszergazdai módban.
5. Telepítse a Windows feladatátvevő fürtözési szolgáltatását.

        Import-Module ServerManager
        Add-WindowsFeature Failover-Clustering
6. Adja hozzá **a CORP\Install mappát** helyi rendszergazdaként.

        net localgroup administrators "CORP\Install" /Add
7. Importálja az SQL Server PowerShell-szolgáltatót.

        Set-ExecutionPolicy -Execution RemoteSigned -Force
        Import-Module -Name "sqlps" -DisableNameChecking
8. Adja hozzá a **CORP\Install** kiszolgálót az alapértelmezett SQL Server-példány rendszergazdai szerepköreként.

        net localgroup administrators "CORP\Install" /Add
        Invoke-SqlCmd -Query "EXEC sp_addsrvrolemember 'CORP\Install', 'sysadmin'" -ServerInstance "."
9. Adja hozzá **az NT AUTHORITY\System** szolgáltatást bejelentkezésként a fent leírt három engedéllyel.

        Invoke-SqlCmd -Query "CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
        Invoke-SqlCmd -Query "GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM] AS SA" -ServerInstance "."
10. Nyissa meg a tűzfalat az SQL Server táveléréséhez.

         netsh advfirewall firewall add rule name='SQL Server (TCP-In)' program='C:\Program Files\Microsoft SQL Server\MSSQL11.MSSQLSERVER\MSSQL\Binn\sqlservr.exe' dir=in action=allow protocol=TCP
11. Jelentkezzen ki mindkét virtuális gépből.

         logoff.exe

Végül készen áll a rendelkezésre állási csoport konfigurálására. Az SQL Server PowerShell-szolgáltató t fogja használni a **ContosoSQL1**összes munkájának elvégzéséhez.

## <a name="configure-the-availability-group"></a>A rendelkezésre állási csoport konfigurálása
1. Csatlakozzon ismét a **ContosoSQL1-hez** a távoli asztali fájlok elindításával. Ahelyett, hogy a számítógépfiókkal jelentkezne be, jelentkezzen be a **CORP\Install**segítségével.
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
4. Importálja az SQL Server PowerShell-szolgáltatót.

        Set-ExecutionPolicy RemoteSigned -Force
        Import-Module "sqlps" -DisableNameChecking
5. Módosítsa a ContosoSQL1 SQL Server szolgáltatásfiókját CORP\SQLSvc1 értékre.

        $wmi1 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server1
        $wmi1.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct1,$password)}
        $svc1 = Get-Service -ComputerName $server1 -Name 'MSSQLSERVER'
        $svc1.Stop()
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc1.Start();
        $svc1.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
6. Módosítsa a ContosoSQL2 SQL Server szolgáltatásfiókját CORP\SQLSvc2 értékre.

        $wmi2 = new-object ("Microsoft.SqlServer.Management.Smo.Wmi.ManagedComputer") $server2
        $wmi2.services | where {$_.Type -eq 'SqlServer'} | foreach{$_.SetServiceAccount($acct2,$password)}
        $svc2 = Get-Service -ComputerName $server2 -Name 'MSSQLSERVER'
        $svc2.Stop()
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Stopped,$timeout)
        $svc2.Start();
        $svc2.WaitForStatus([System.ServiceProcess.ServiceControllerStatus]::Running,$timeout)
7. Töltse **le a CreateAzureFailoverCluster.ps1** fájlból a [Feladatátvételi fürt létrehozása az Azure virtuális gép mindig rendelkezésre állási csoportjaihoz](https://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a) a helyi munkakönyvtárba. Ezt a parancsfájlt fogja használni egy funkcionális feladatátvevő fürt létrehozásához. A Windows feladatátvevő fürtözésének azure-hálózattal való együttműködéséről a [Magas rendelkezésre állású és vészhelyreállítás az Azure virtuális gépeken az SQL Server szolgáltatáshoz](../sql/virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)című témakörben talál fontos információt.
8. Váltson a munkakönyvtárra, és hozza létre a feladatátvevő fürtöt a letöltött parancsfájllal.

        Set-ExecutionPolicy Unrestricted -Force
        .\CreateAzureFailoverCluster.ps1 -ClusterName "$clusterName" -ClusterNode "$server1","$server2","$serverQuorum"
9. Engedélyezze a Mindig rendelkezésre állási csoportokat a **ContosoSQL1** és a **ContosoSQL2**alapértelmezett SQL Server-példányaihoz.

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
10. Hozzon létre egy biztonsági címtárat, és adjon engedélyeket az SQL Server szolgáltatásfiókokhoz. Ezzel a könyvtárral előkészítheti a másodlagos replika rendelkezésre állási adatbázisát.

         $backup = "C:\backup"
         New-Item $backup -ItemType directory
         net share backup=$backup "/grant:$acct1,FULL" "/grant:$acct2,FULL"
         icacls.exe "$backup" /grant:r ("$acct1" + ":(OI)(CI)F") ("$acct2" + ":(OI)(CI)F")
11. Hozzon létre egy adatbázist a **ContosoSQL1** nevű **MyDB1,** hogy mind a teljes biztonsági mentést és a napló biztonsági mentést, és állítsa vissza őket a **ContosoSQL2** a **WITH NORECOVERY** beállítással.

         Invoke-SqlCmd -Query "CREATE database $db"
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server1
         Backup-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server1 -BackupAction Log
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.bak" -ServerInstance $server2 -NoRecovery
         Restore-SqlDatabase -Database $db -BackupFile "$backupShare\db.log" -ServerInstance $server2 -RestoreAction Log -NoRecovery
12. Hozza létre a rendelkezésre állási csoport végpontjait az SQL Server virtuális gépeken, és állítsa be a megfelelő engedélyeket a végpontokon.

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
13. Hozza létre a rendelkezésre állási replikák.

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
14. Végül hozza létre az elérhetőségi csoportot, és csatlakozzon a másodlagos replika a rendelkezésre állási csoporthoz.

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
Most már sikeresen megvalósította az SQL Server Always On alkalmazást egy rendelkezésre állási csoport létrehozásával az Azure-ban. Ha egy figyelőt szeretne beállítani ehhez az elérhetőségi csoporthoz, olvassa el az [ILB-figyelő konfigurálása az Azure-ban az Always On rendelkezésre állási csoportokhoz című témakört.](../classic/ps-sql-int-listener.md)

Az SQL Server Azure-beli használatáról az [SQL Server azure-beli virtuális gépeken](../sql/virtual-machines-windows-sql-server-iaas-overview.md)című témakörben talál további információt.

---
title: "Hozzon létre egy virtuális gép Windows PowerShell használatával |} Microsoft Docs"
description: "Hozzon létre Windows virtuális gépek Azure PowerShell és a klasszikus telepítési modell használatával."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 42c0d4be-573c-45d1-b9b0-9327537702f7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: af672a873b33cbd0b6151fd564e84c96f0b6e1e3
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="create-a-windows-virtual-machine-with-powershell-and-the-classic-deployment-model"></a>Windows virtuális gép létrehozása a PowerShell és a klasszikus telepítési modell
> [!div class="op_single_selector"]
> * [Azure portál – Windows](tutorial.md)
> * [PowerShell - Windows](create-powershell.md)
> 
> 

<br>

> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. [ Ismerje meg, hogyan ](../../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Ezeket a lépéseket mutatja be Azure PowerShell-parancsokat, amelyek létrehozása, és előre konfigurálása a Windows-alapú Azure virtuális gép egy építőelem módszer segítségével testre szabhatja. Ez a folyamat használhat gyorsan létrehozhat egy új Windows-alapú virtuális gép egy parancsot, és bontsa ki a meglévő telepítés hozzá vagy hozzon létre egy egyéni fejlesztési és tesztelési célú vagy az informatikai szakemberek környezetben gyorsan elkészítse több parancs készleteket.

Ezeket a lépéseket hajtsa végre az Azure PowerShell-parancs készletek létrehozásához a fill-a-a-üres cellákat megközelítést. Ezt a módszert akkor lehet hasznos, ha még nem ismeri a PowerShell vagy csak szeretné tudni, hogy milyen értékeket adhatja meg a sikeres konfigurációhoz. Speciális PowerShell felhasználók igénybe vehet a parancsokat és helyettesítse a saját a változók értékeit (a "$" kezdődő sorok).

Ha még nem tette meg, kövesse az utasításokat, a [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview) Azure PowerShell telepítése a helyi számítógépen. Ezután nyissa meg a Windows PowerShell-parancssort.

## <a name="step-1-add-your-account"></a>1. lépés: A fiók hozzáadása
1. A PowerShell-parancssorba írja be a **Add-AzureAccount** kattintson **Enter**. 
2. Írja be az Azure-előfizetéshez társított e-mail címét, és kattintson a **Folytatás**. 
3. Adja meg a fiókhoz tartozó jelszót. 
4. Kattintson a **bejelentkezés**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>2. lépés: Az előfizetés és a storage-fiók beállítása
Az Azure-előfizetés és a storage-fiók beállítása a Windows PowerShell parancssorába a parancsok futtatásával. Cserélje le a mindent, ami az ajánlatokat, beleértve a < és > karakter, helyes nevét.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

A megfelelő előfizetés neve lekérheti a kibocsátás SubscriptionName tulajdonság a **Get-AzureSubscription** parancsot. A megfelelő tárfióknév lekérheti a kimenetét Label tulajdonságának a **Get-AzureStorageAccount** parancs futtatása után a **válasszon-AzureSubscription** parancsot.

## <a name="step-3-determine-the-imagefamily"></a>3. lépés: A ImageFamily meghatározása
Ezután meg kell határoznia az Azure virtuális gép létrehozásához megfelelő adott kép ImageFamily vagy címke értéke. A rendelkezésre álló ImageFamily értékek listáját ezzel a paranccsal kaphat.

    Get-AzureVMImage | select ImageFamily -Unique

Íme néhány példa a Windows-alapú számítógépek ImageFamily értékeket:

* Windows Server 2012 R2 Datacenter
* Windows Server 2008 R2 SP1
* A Windows Server 2016 Technical Preview 4
* SQL Server 2012 SP1 Enterprise, Windows Server 2012

Ha megtalálta a keresett lemezképet, nyissa meg a friss példánya a választott vagy a PowerShell integrált parancsfájlkezelési környezet (ISE) a szövegszerkesztőben. A következő átmásolja az új fájl vagy a PowerShell ISE, és a ImageFamily érték.

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Bizonyos esetekben a lemezkép neve helyett a ImageFamily érték Label tulajdonságának van. Ha a keresett ImageFamily tulajdonsággal kép nem található, listában a képek ezzel a paranccsal a címke tulajdonság által.

    Get-AzureVMImage | select Label -Unique

Ha ezzel a paranccsal a megfelelő lemezképet, nyissa meg a friss példánya a választott vagy a PowerShell ISE a szövegszerkesztőben. A következő átmásolja az új fájl vagy a PowerShell ISE, és a címke értéke.

    $label="<Label value>"
    $image = Get-AzureVMImage | where { $_.Label -eq $label } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## <a name="step-4-build-your-command-set"></a>4. lépés: A parancs-készlet létrehozása
A parancs szerint az új szöveges fájlt vagy a ISE másolja a megfelelő állít elő alatt és a változó kitöltése és eltávolítása a többi build a < és > karakter. Lásd: a két [példák](#examples) a végső eredményének képet a jelen cikk végén.

Indítsa el a parancs, válassza ki az egyik alábbi két parancs blokkok (kötelező).

1. lehetőség: Adja meg a virtuális gép nevét és méretét.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

2. lehetőség: Adja meg a nevét, méretét, és rendelkezésre állási készlet neve.

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D-, DS- vagy G sorozatú virtuális gépek InstanceSize értékeit, lásd: [virtuális gépek és Felhőszolgáltatások mérete az Azure-](https://msdn.microsoft.com/library/azure/dn197896.aspx).

> [!NOTE]
> Ha nagyvállalati szerződéssel rendelkező frissítési garancia, és szeretné kihasználni a Windows Server [hibrid használja juttatás](https://azure.microsoft.com/pricing/hybrid-use-benefit/), vegye fel a **- LicenseType** paramétert a **New-AzureVMConfig** parancsmag, az értéket átadja **Windows_Server** szokásos felhasználási eseténél.  Lehet, hogy a képfájl feltöltött; a hibrid használjon szabványos lemezképe nem használható a gyűjteményből.
> 
> 

Szükség esetén egy önálló Windows-számítógép, adja meg a helyi rendszergazda fiókot és jelszót.

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

Írjon be egy erős jelszót. Ellenőrizze annak erőssége, lásd: [jelszóellenőrző: az erős jelszavak használata](https://www.microsoft.com/security/pc-security/password-checker.aspx).

Szükség esetén a Windows-számítógép hozzáadása a meglévő Active Directory-tartományban, adja meg a helyi rendszergazdai fiókot és jelszót, a tartomány nevét és egy tartományi fiók.

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="<FQDN of the domain that the machine is joining>"
    $domacctdomain="<domain of the account that has permission to add the machine to the domain>"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

További előtti konfigurációs beállítások a Windows-alapú virtuális gépek, a szintaxisának megtekintéséhez a **Windows** és **WindowsDomain és** paraméterkészletek [Add-AzureProvisioningConfig](/powershell/module/azure/add-azureprovisioningconfig).

Ha szükséges rendelje hozzá a virtuális gép egy adott IP-cím, egy statikus DIP néven ismert.

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

Ellenőrizheti, hogy az elérhető legyen-e egy adott IP-cím:

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

Ha szükséges rendelje hozzá a virtuális gép egy bizonyos alhálózat az Azure virtuális hálózat.

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

Egyetlen adatlemezt opcionálisan adja hozzá a virtuális gép.

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

Az Active Directory-tartományvezérlő állítsa be a "None" $hcaching.

Opcionálisan adja hozzá a virtuális gép a külső forgalom terhelésű készlet.

    $protocol="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $protocol -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

Végül válasszon egyet az ezen a virtuális gép létrehozásához szükséges parancs blokkok.

1. lehetőség: A létező felhőalapú szolgáltatást a virtuális gép létrehozásához.

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

A felhőalapú szolgáltatás rövid neve nem a felhőalapú szolgáltatások listája az Azure portálon vagy a lista az erőforráscsoportok az Azure portálon megjelenő nevét.

2. lehetőség: A virtuális gép létrehozása egy létező felhőalapú szolgáltatást és a virtuális hálózat.

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## <a name="step-5-run-your-command-set"></a>5. lépés: Futtassa a parancsot
Nézze át az Azure PowerShell parancs beépített a szöveg- vagy a PowerShell ISE álló a több blokkra parancsok a 4. lépéssel. Győződjön meg arról, hogy megadta-e a szükséges változók, és hogy a megfelelő értékeket. Győződjön meg arról, hogy rendelkezik-e el az összes is a < és > karakter.

Egy szövegszerkesztőben használ, ha a parancskészlethez másolása a vágólapra, és kattintson a jobb gombbal a megnyitott Windows PowerShell-parancssort. Ez a parancskészlethez ki, a PowerShell-parancsokat, és az Azure virtuális gép létrehozása. Alternatív megoldásként futtassa a parancsot a PowerShell ISE beállítása.

Ha létrehozni újra a virtuális gép vagy egy hasonló, akkor a következőket teheti:

* Ez a parancs egy olyan PowerShell-parancsfájlt (*.ps1) állítja mentéséhez.
* Ez a parancs az Azure Automation-runbook állítja mentéséhez a **Automation-fiók** szakasza az Azure-portálon.

## <a id="examples"></a>Példák
Az alábbiakban a két példa látható a fenti lépések használatával olyan Windows-alapú Azure virtuális gépek létrehozása az Azure PowerShell-parancs létrehozására.

### <a name="example-1"></a>1. példa
A PowerShell kell parancs beállított hozzon létre egy Active Directory-tartományvezérlő a kezdeti virtuális gépen, amely:

* A Windows Server 2012 R2 Datacenter rendszerképet használja.
* A AZDC1 névvel rendelkezik.
* Egy olyan önálló számítógép.
* Rendelkezik egy további adatlemezt 20 GB.
* A statikus IP-címmel 192.168.244.4 rendelkezik.
* A BackEnd alhálózathoz AZDatacenter virtuális hálózat szerepel.
* Az Azure-KELJFELJANCSI felhőszolgáltatás van.

Ez a megfelelő Azure PowerShell-parancsot, állítsa be a virtuális gép létrehozása üres sorok közötti valamennyi blokkja olvashatóság érdekében.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="AZDC1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the local administrator account."
    $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=20
    $disklabel="DCData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### <a name="example-2"></a>2. példa
A PowerShell kell parancs értékűre üzleti kiszolgáló virtuális gép létrehozása:

* A Windows Server 2012 R2 Datacenter rendszerképet használja.
* A LOB1 névvel rendelkezik.
* A corp.contoso.com tartomány tagja.
* 200 GB-os egy további adatok lemezzel rendelkezik.
* A FrontEnd alhálózathoz AZDatacenter virtuális hálózat szerepel.
* Az Azure-KELJFELJANCSI felhőszolgáltatás van.

Ez a megfelelő Azure PowerShell-parancsot a virtuális gép létrehozásához.

    $family="Windows Server 2012 R2 Datacenter"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
    $vmname="LOB1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred1=Get-Credential –Message "Type the name and password of the local administrator account."
    $cred2=Get-Credential –Message "Now type the name (not including the domain) and password of an account that has permission to add the machine to the domain."
    $domaindns="corp.contoso.com"
    $domacctdomain="CORP"
    $vm1 | Add-AzureProvisioningConfig -AdminUsername $cred1.Username -Password $cred1.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $cred2.Username -DomainPassword $cred2.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=200
    $disklabel="LOBData"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname


## <a name="next-steps"></a>Következő lépések
Ha egy 127 GB-nál nagyobb méretű operációsrendszer-lemez van szüksége, akkor [bontsa ki az operációs rendszer meghajtó](../../virtual-machines-windows-expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


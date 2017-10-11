---
title: "Az Azure Active Directory tartományi szolgáltatások: Felügyeleti útmutató |} Microsoft Docs"
description: "A Windows rendszerű virtuális gép csatlakoztatása felügyelt tartományhoz Azure PowerShell és a klasszikus telepítési modell használatával."
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 9143b843-7327-43c3-baab-6e24a18db25e
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
ms.openlocfilehash: 1bb1546fb616131a1e1868a0d0610c4cad5d73e2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>A Windows Server virtuális gép csatlakoztatása felügyelt tartományhoz PowerShell használatával
> [!div class="op_single_selector"]
> * [Klasszikus Azure portál – Windows](active-directory-ds-admin-guide-join-windows-vm.md)
> * [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. Azure AD tartományi szolgáltatások jelenleg nem támogatja a Resource Manager modellt.
>
>

Ezeket a lépéseket mutatja be Azure PowerShell-parancsokat, amelyek létrehozása, és előre konfigurálása a Windows-alapú Azure virtuális gép egy építőelem módszer segítségével testre szabhatja. Az alábbi lépések segítségével a Windows-alapú Azure virtuális gép létrehozása, és csatlakoztassa azt az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

Ezeket a lépéseket hajtsa végre az Azure PowerShell-parancs készletek létrehozásához a fill-a-a-üres cellákat megközelítést. Ezt a módszert akkor lehet hasznos, ha még nem ismeri a PowerShell vagy meg szeretné ismerni, hogy milyen értékeket adhatja meg a sikeres konfigurációhoz. Speciális PowerShell felhasználók igénybe vehet a parancsokat és helyettesítse a saját a változók értékeit (a "$" kezdődő sorok).

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

## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>3. lépés: Részletes útmutató - a virtuális gép kiépítéséhez, és csatlakoztassa azt a felügyelt tartományhoz
Ez a megfelelő Azure PowerShell-parancsot, állítsa be a virtuális gép létrehozása üres sorok közötti valamennyi blokkja olvashatóság érdekében.

Adjon meg információt a Windows rendszerű virtuális gép úgy kell létrehozni.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

D-, DS- vagy G sorozatú virtuális gépek InstanceSize értékeit, lásd: [virtuális gépek és Felhőszolgáltatások mérete az Azure-](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Adja meg a felügyelt tartományra.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Adja meg a felhőalapú szolgáltatás nevét.

    $svcname="Contoso100-test"

Adja meg, amelyhez a virtuális Géphez kell csatlakoztatni a virtuális hálózat nevét. Győződjön meg arról, hogy a felügyelt AAD-DS-tartomány érhető el a virtuális hálózaton található.

    $vnetname="MyPreviewVnet"

Válassza ki a virtuális gép a virtuális gép kiépítéséhez használni kívánt kép.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Konfigurálja a VM - virtuális gép neve, a példány mérete és a használni kívánt kép.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Szerezze be a helyi rendszergazdai hitelesítő adatokat a virtuális gép számára. Írjon be egy erős helyi rendszergazdai jelszót.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Szerezze be a virtuális gép csatlakoztatása a felügyelt tartományra "AAD DC rendszergazdák" csoportba tartozó felhasználói fiók hitelesítő adatait. Ne adjon meg a tartománynév - példányhoz, a fenti példában, "bob" adtuk meg a felhasználónevet.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

A virtuális gép konfigurálása, mert a tartományhoz csatlakoztatás követelmény & szükséges hitelesítő adatok megadása.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Állítsa be egy alhálózatot a virtuális gép számára.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Választható lehetőség: Az IP-cím a tartomány mutasson. Ha az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz kell lennie a virtuális hálózat DNS-kiszolgálók IP-címét, a lépésre nincs szükség.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

Most kiépítése a tartományhoz csatlakoztatott Windows virtuális Gépet.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Parancsfájl a Windows virtuális gépek ellátásához, majd automatikusan csatlakoztatása az AAD tartományi szolgáltatásokra által felügyelt tartományokhoz
A PowerShell parancskészlethez létrehoz egy virtuális gépet egy üzleti kiszolgáló, amely:

* A Windows Server 2012 R2 Datacenter rendszerképet használja.
* Ez egy nagyon kicsi virtuális gép.
* A Contoso100-teszt névvel rendelkezik.
* Automatikusan tartomány a contoso100 felügyelt tartományhoz csatlakozik.
* Bekerül a felügyelt tartományra azonos virtuális hálózaton.

Ez a teljes minta parancsfájl a Windows rendszerű virtuális gép létrehozásához, és automatikusan csatlakoztatása az Azure AD tartományi szolgáltatások által felügyelt tartományokhoz.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Kapcsolódó tartalom
* [Azure AD tartományi szolgáltatások – első lépések útmutató](active-directory-ds-getting-started.md)
* [Azure AD tartományi szolgáltatások által kezelt tartomány felügyelete](active-directory-ds-admin-guide-administer-domain.md)

---
title: Red Hat Enterprise Linux saját előfizetéssel rendelkező Azure-lemezképek | Microsoft Docs
description: Ismerkedjen meg a saját előfizetésekkel kapcsolatos rendszerképekkel az Azure-beli Red Hat Enterprise Linux
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: alsin
ms.openlocfilehash: e9cbd98afd5f9ed5561cba2236d85da331db7895
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75944702"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux saját előfizetéssel rendelkező Gold-lemezképek az Azure-ban
A Red Hat Enterprise Linux-(RHEL-) lemezképek az Azure-ban az utólagos elszámolású (TB) vagy a saját előfizetéses (Red Hat Gold image) modellen keresztül érhetők el az Azure-ban. Ez a dokumentum áttekintést nyújt az Azure Red Hat Gold images-képeiről.

## <a name="important-points-to-consider"></a>Megfontolandó fontos szempontok

- Az ebben a programban megadott Red Hat Gold-lemezképek az Azure Gallery/Marketplace RHEL TB-rendszerképeihez hasonló, éles használatra kész RHEL-lemezképek.

- A rendszerképek az Azure-ban [Red Hat Enterprise Linux rendszerképekben](./redhat-images.md) ismertetett aktuális szabályzatokat követik

- A rendszerképekből létrehozott virtuális gépekre a szabványos támogatási szabályzatok vonatkoznak

- A Red Hat Gold images-ről kiépített virtuális gépek nem teljesítik a RHEL TB-lemezképekhez kapcsolódó RHEL-díjakat

- A lemezképek nem jogosultak, ezért az előfizetés-kezelővel regisztrálnia kell a virtuális gépeket, és előfizethet a Red Hat közvetlen frissítéseinek letöltésére

- A Linux-lemezképek esetében jelenleg nem lehet dinamikusan váltani a BYOS és a TB számlázási modelljei között. A számlázási modell átváltásához a virtuális gépet a megfelelő rendszerképből kell újból üzembe helyezni

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>A Red Hat Gold images-képek elérésére vonatkozó követelmények és feltételek

1. Ismerkedjen meg a [Red Hat Cloud Access program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) feltételeivel, és engedélyezze a Red Hat-előfizetéseket a Cloud Accesshez a [Red Hat előfizetés-kezelőben](https://access.redhat.com/management/cloud). Meg kell adnia a Felhőbeli hozzáféréshez regisztrálni kívánt Azure-előfizetést (ka) t.

1. Ha engedélyezte a Red Hat-előfizetéseket a Felhőbeli hozzáféréshez, amelyek megfelelnek a megfelelő jogosultsági követelményeknek, az Azure-előfizetések automatikusan engedélyezve lesznek a Gold-képek eléréséhez.

### <a name="expected-time-for-image-access"></a>A rendszerkép-hozzáférés várt ideje

A felhőalapú hozzáférés-engedélyezés lépéseinek elvégzése után a Red Hat ellenőrzi, hogy jogosult-e a Red Hat Gold images-re. Ha az érvényesítés sikeres, három órán belül megkapja az arany rendszerképek elérését.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>A Red Hat Gold images-képek használata a Azure Portal

1. Miután az Azure-előfizetése hozzáfér a Red Hat Gold images-hez, megkeresheti őket a [Azure Portalban](https://portal.azure.com) **egy erőforrás létrehozásához** , majd az **összes megjelenítéséhez**.

1. A lap tetején láthatja, hogy saját ajánlatokat tartalmaz.

    ![Piactér – privát ajánlatok](./media/rhel-byos-privateoffers.png)

1. A saját ajánlatok megjelenítéséhez kattintson a lila hivatkozásra, vagy görgessen le a lap aljára.

1. A felhasználói felületen a többi kiépítés nem lesz más, mint a többi meglévő Red Hat-rendszerkép. Válassza ki a RHEL verzióját, és kövesse az utasításokat a virtuális gép üzembe helyezéséhez. Ez a folyamat azt is lehetővé teszi, hogy elfogadja a rendszerkép feltételeit az utolsó lépésben.

>[!NOTE]
>Ezek a lépések nem teszik lehetővé a Red Hat Gold képképpel való üzembe helyezést a programozott telepítéshez – további lépésre lesz szükség az alábbi "További információ" című szakaszban leírtak szerint.

A dokumentum többi része a CLI metódusra összpontosít a feltételek kiépítéséhez és elfogadásához a képen. A felhasználói felület és a CLI teljes mértékben felcserélhető, ha a végeredmény (egy kiépített RHEL Gold-rendszerkép virtuális gép).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>A Red Hat Gold images használata az Azure CLI-vel
Az alábbi utasítások végigvezetik a RHEL virtuális gép kezdeti üzembe helyezési folyamatán az Azure CLI használatával. Ezek az utasítások feltételezik, hogy az [Azure CLI telepítve](https://docs.microsoft.com/cli/azure/install-azure-cli)van.

>[!IMPORTANT]
>Győződjön meg arról, hogy a közzétevő, az ajánlat, a terv és a Képhivatkozások összes kisbetűjét használja a következő parancsokhoz

1. Győződjön meg arról, hogy a kívánt előfizetése van:
    ```azurecli
    az account show -o=json
    ```

1. Hozzon létre egy erőforráscsoportot a Red Hat Gold rendszerképekhez használt virtuális géphez:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. A rendszerkép elfogadásának feltételei:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Ezeket a feltételeket csak egyszer kell elfogadni az *Azure-előfizetések, a RENDSZERKÉP SKU-száma*alapján.

1. Választható Ellenőrizze a virtuális gép telepítését a következő paranccsal:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Helyezze üzembe a virtuális gépet úgy, hogy ugyanazt a parancsot futtatja, mint a `--validate` argumentum nélkül:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH-t a virtuális gépre, és ellenőrizze, hogy van-e egy nem jogosult rendszerképe. Ehhez futtassa a `sudo yum repolist` (RHEL 8 használata `sudo dnf repolist`). A kimenet azt kéri, hogy az előfizetés-kezelővel regisztrálja a virtuális gépet a Red Hat használatával.

>[!NOTE]
>A RHEL 8 `dnf` és `yum` felcserélhetők, erről bővebben a [RHEL 8 felügyeleti útmutatójában](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)olvashat.


## <a name="use-the-red-hat-gold-images-from-powershell"></a>A Red Hat Gold images használata a PowerShellből
A következő példa egy parancsfájlt mutat be. Az erőforráscsoport, a hely, a virtuális gép neve, a bejelentkezési adatok és más változók helyére a választott konfigurációt kell cserélnie. A közzétevőnek és a terv információjának kisbetűnek kell lennie.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="additional-information"></a>További információk
- Ha olyan előfizetésre próbál virtuális gépet kiépíteni, amely nincs engedélyezve ehhez az ajánlathoz, a következő hibaüzenet jelenik meg, és a Microsoft vagy a Red Hat használatával kapcsolatba kell lépnie az előfizetés engedélyezésével.
    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

- Ha a RHEL BYOS rendszerképből hoz létre pillanatképet, és közzéteszi a rendszerképet a [megosztott rendszerkép](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)-katalógusban, meg kell adnia a pillanatkép eredeti forrásának megfelelő díjcsomag-információt. A parancs például a következőhöz hasonló lehet: (jegyezze fel a terv paramétereit az utolsó sorban):
    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

- Ha automatizálást használ a virtuális gépek RHEL BYOS-lemezképből való kiépítéséhez, meg kell adnia a fentebb láthatóhoz hasonló csomag-paramétereket. Ha például a Terraform-t használja, megadhatja a terv információit egy [csomag blokkban](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Következő lépések
* A Felhőbeli hozzáférés részletes útmutatói és program részletei a [Red Hat Cloud Access dokumentációjában találhatók.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
* További információ az [Azure Red Hat frissítési infrastruktúráról](./redhat-rhui.md).
* Ha többet szeretne megtudni az Azure-beli Red Hat-lemezképekről, lépjen a [dokumentáció lapra](./redhat-images.md).
* A Red Hat-támogatási házirendekkel kapcsolatos információk a RHEL összes verziójára vonatkozóan a [Red Hat Enterprise Linux életciklus](https://access.redhat.com/support/policy/updates/errata) oldalon találhatók.

---
title: Red Hat Enterprise Linux saját előfizetéssel rendelkező Azure-lemezképek | Microsoft Docs
description: Ismerkedjen meg a saját előfizetésekkel kapcsolatos rendszerképekkel Red Hat Enterprise Linux Azure-ban.
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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970477"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux saját előfizetéssel rendelkező Gold-lemezképek az Azure-ban

A Red Hat Enterprise Linux-(RHEL-) lemezképek az Azure-ban az utólagos elszámolású vagy a saját előfizetéssel (BYOS) (Red Hat Gold image) modellen keresztül érhetők el. Ez a cikk áttekintést nyújt az Azure Red Hat Gold images-képeiről.

>[!NOTE]
> A RHEL BYOS Gold-lemezképek az Azure nyilvános (kereskedelmi) és Azure Government felhőkben érhetők el. Nem érhetők el az Azure China-ban vagy az Azure Blackforest-felhőkben.

## <a name="important-points-to-consider"></a>Megfontolandó fontos szempontok

- Az ebben a programban megadott Red Hat Gold images az Azure Marketplace-en elérhető RHEL-RHEL hasonló, éles használatra kész képeket tartalmaz.
- A rendszerképek az Azure-ban [Red Hat Enterprise Linux lemezképekben](./redhat-images.md)ismertetett aktuális szabályzatokat követik.
- A rendszerképekből létrehozott virtuális gépekre a szabványos támogatási szabályzatok vonatkoznak.
- A Red Hat Gold images-ről kiépített virtuális gépek nem teljesítik a RHEL utólagos elszámolású lemezképekhez kapcsolódó RHEL díjakat.
- A lemezképek nem jogosultak. A Red Hat előfizetés-kezelővel regisztrálnia és elő kell fizetnie a virtuális gépeket, hogy közvetlenül a Red hat-ból kapjon frissítéseket.
- A Linux-lemezképek esetében jelenleg nem lehet dinamikusan váltani a BYOS és az utólagos elszámolású számlázási modellek között. A számlázási modell átváltásához újra kell telepítenie a virtuális gépet a megfelelő lemezképből.

>[!NOTE]
> A 2. generációs RHEL BYOS-lemezképek jelenleg nem érhetők el a Piactéri ajánlaton keresztül. Ha a 2. generációs RHEL BYOS-rendszerképre van szüksége, látogasson el a Cloud Access Irányítópultra a Red Hat előfizetés-kezelésben. További információt a [Red Hat dokumentációjában](https://access.redhat.com/articles/4847681)talál.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>A Red Hat Gold images-képek elérésére vonatkozó követelmények és feltételek

1. Ismerje meg a [Red Hat Cloud Access program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) feltételeit. Red Hat-előfizetések engedélyezése a Cloud Accesshez a [Red Hat előfizetés-kezelőben](https://access.redhat.com/management/cloud). Meg kell adnia az Azure-előfizetéseket, amelyek regisztrálva lesznek a Felhőbeli hozzáféréshez.

1. Ha a Felhőbeli hozzáféréshez engedélyezett Red Hat-előfizetések megfelelnek a jogosultsági követelményeknek, az Azure-előfizetések automatikusan engedélyezve vannak a Gold-képek eléréséhez.

### <a name="expected-time-for-image-access"></a>A rendszerkép-hozzáférés várt ideje

Miután befejezte a Felhőbeli hozzáférés engedélyezésének lépéseit, a Red Hat ellenőrzi, hogy jogosult-e a Red Hat Gold Imagere. Ha az érvényesítés sikeres, három órán belül megkapja az arany rendszerképek elérését.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>A Red Hat Gold images-képek használata a Azure Portal

1. Miután az Azure-előfizetés hozzáfér a Red Hat Gold images-hez, megkeresheti őket a [Azure Portalban](https://portal.azure.com). Válassza az **erőforrás létrehozása** > az **összes**megjelenítése lehetőséget.

1. A lap tetején láthatja, hogy saját ajánlatokat tartalmaz.

    ![Piactér – privát ajánlatok](./media/rhel-byos-privateoffers.png)

1. Válassza ki a lila hivatkozást, vagy görgessen le a lap aljára, és tekintse meg saját ajánlatait.

1. A felhasználói felületen a többi kiépítés nem különbözik a többi meglévő Red Hat-rendszerképtől. Válassza ki a RHEL verzióját, és kövesse az utasításokat a virtuális gép kiépítéséhez. Ez a folyamat azt is lehetővé teszi, hogy elfogadja a rendszerkép feltételeit az utolsó lépésben.

>[!NOTE]
>Ezek a lépések nem teszik lehetővé a Red Hat Gold-rendszerkép alkalmazását a programozott üzembe helyezéshez. További lépésre van szükség a "További információ" szakaszban leírtak szerint.

A dokumentum többi része a CLI metódusra összpontosít a feltételek kiépítéséhez és elfogadásához a képen. A felhasználói felület és a CLI teljes mértékben felcserélhető, ha a végeredmény (egy kiépített RHEL Gold-rendszerkép virtuális gép).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>A Red Hat Gold images használata az Azure CLI-vel

Az alábbi utasítások végigvezetik a RHEL virtuális gép kezdeti üzembe helyezési folyamatán az Azure CLI használatával. Ezek az utasítások feltételezik, hogy az [Azure CLI telepítve](https://docs.microsoft.com/cli/azure/install-azure-cli)van.

>[!IMPORTANT]
>Győződjön meg arról, hogy a közzétevő, az ajánlat, a terv és a Képhivatkozások összes kisbetűjét használja a következő parancsokhoz.

1. Győződjön meg arról, hogy a kívánt előfizetést használja.

    ```azurecli
    az account show -o=json
    ```

1. Hozzon létre egy erőforráscsoportot a Red Hat Gold rendszerképekhez használt virtuális géphez.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Fogadja el a rendszerkép feltételeit.

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

1. Hozzon létre egy virtuális gépet úgy, hogy ugyanazt a parancsot futtatja, mint az előző példában látható `--validate` argumentum nélkül.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH-t a virtuális géphez, és ellenőrizze, hogy van-e egy nem jogosult rendszerkép. Ennek a lépésnek a végrehajtásához futtassa `sudo yum repolist`. A RHEL 8 esetében használja a `sudo dnf repolist`. A kimenet azt kéri, hogy az előfizetés-kezelővel regisztrálja a virtuális gépet a Red Hat használatával.

>[!NOTE]
>A RHEL 8, `dnf` és `yum` felcserélhető. További információ: [RHEL 8 felügyeleti útmutató](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>A Red Hat Gold images használata a PowerShellből

A következő szkript egy példa. Cserélje le az erőforráscsoportot, a helyet, a virtuális gép nevét, a bejelentkezési adatokat és az egyéb változókat az Ön által választott konfigurációra. A közzétevőnek és a terv információjának kisbetűnek kell lennie.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Titkosítás Red Hat Enterprise Linux saját előfizetés – Gold images

Red Hat Enterprise Linux a BYOS [Azure Disk Encryption](../../linux/disk-encryption-overview.md)használatával is biztonságossá teheti. A titkosítás engedélyezéséhez *regisztrálni kell az előfizetést* . A RHEL BYOS Gold-képek regisztrálásával kapcsolatos további információkért lásd: a [rendszer regisztrálása és előfizetése a Red Hat Customer Portalon a Red Hat Subscription-Manager használatával](https://access.redhat.com/solutions/253273). Ha aktív Red Hat-előfizetéssel rendelkezik, akkor olvassa el a [Red Hat Customer Portal aktiválási kulcsainak létrehozását](https://access.redhat.com/articles/1378093)is.

A Azure Disk Encryption [Red Hat egyéni rendszerképeken](../../linux/redhat-create-upload-vhd.md)nem támogatott. A [Linux rendszerű virtuális gépekre](../../linux/disk-encryption-overview.md#additional-vm-requirements)vonatkozó további Azure Disk Encryption követelmények és előfeltételek dokumentálása Azure Disk Encryption.

A Azure Disk Encryption alkalmazására vonatkozó lépésekért lásd: [Azure Disk Encryption forgatókönyvek Linux rendszerű virtuális gépeken](../../linux/disk-encryption-linux.md) és kapcsolódó cikkekben.

## <a name="additional-information"></a>További információ

- Ha olyan előfizetésre próbál létrehozni egy virtuális gépet, amely nincs engedélyezve ehhez az ajánlathoz, a következő üzenet jelenik meg:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Ebben az esetben forduljon a Microsoft vagy a Red Hat szolgáltatáshoz, és engedélyezze az előfizetését.

- Ha módosít egy pillanatképet egy RHEL BYOS-rendszerképből, és megkísérli közzétenni ezt az egyéni rendszerképet a [megosztott rendszerkép](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)-katalógusban, meg kell adnia a pillanatkép eredeti forrásának megfelelő tervezési információkat. A parancs például a következőképpen nézhet ki:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Jegyezze fel a terv paramétereit az utolsó sorban.

    A [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) egyéni lemezképeken nem támogatott.

- Ha az Automation használatával helyez üzembe virtuális gépeket a RHEL BYOS-lemezképből, meg kell adnia a minta parancsaiban láthatóhoz hasonló csomag-paramétereket. Ha például a Terraform-t használja, megadhatja a terv információit egy [csomag blokkban](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Következő lépések

- A Felhőbeli hozzáférés részletes útmutatóját és a program részleteit a [Red Hat Cloud Access dokumentációjában](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)találja.
- Ha többet szeretne megtudni a Red Hat frissítési infrastruktúráról, tekintse meg az [Azure Red Hat frissítési infrastruktúráját](./redhat-rhui.md)ismertető témakört.
- Ha többet szeretne megtudni az Azure-beli Red Hat-lemezképekről, tekintse meg a [dokumentációs oldalt](./redhat-images.md).
- A RHEL összes verziójának Red Hat-támogatási házirendjeivel kapcsolatos információkért tekintse meg a [Red Hat Enterprise Linux életciklusát](https://access.redhat.com/support/policy/updates/errata) ismertető oldalt.
- A RHEL Gold images-ről további dokumentációt a [Red Hat dokumentációjában](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)talál.

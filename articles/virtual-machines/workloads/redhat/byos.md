---
title: Red Hat Enterprise Linux hozza-a-saját-előfizetéses Azure-képek | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat saját előfizetéses lemezképeket a Red Hat Enterprise Linux azure-beli rendszerhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264595"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>A Red Hat Enterprise Linux saját előfizetéssel rendelkezik Gold Images-t hoz létre az Azure-ban

A Red Hat Enterprise Linux (RHEL) lemezképek az Azure-ban csak akkor érhető el, ha csak felosztó-ki(ke) vagy hozza el saját előfizetését (BYOS) (Red Hat Gold Image) modell. Ez a cikk áttekintést nyújt a Red Hat Gold Images az Azure-ban.

>[!NOTE]
> Az RHEL BYOS Gold Images az Azure Public (kereskedelmi) és az Azure Government-felhőkben érhető el. Nem érhetők el az Azure China vagy az Azure Blackforest felhők.

## <a name="important-points-to-consider"></a>Fontos szempontok, amelyeket figyelembe kell venni

- A programban található Red Hat Gold Images az Azure Marketplace-en az RHEL használatalapú használatalapú lemezképeihez hasonló éles használatra kész RHEL-lemezképek.
- A képek az [Azure-beli Red Hat Enterprise Linux-lemezképekben](./redhat-images.md)leírt aktuális szabályzatokat követik.
- A szabványos támogatási szabályzatok az ezekből a lemezképekből létrehozott virtuális gépekre vonatkoznak.
- A Red Hat Gold Imagesből kiépített virtuális gépek nem viselnek RHEL-felosztó-kiosztó-felosztó-szolgáltatás-képekhez kapcsolódó RHEL-díjakat.
- A képek nem jogosultak. A Red Hat Subscription-Manager használatával regisztrálhat és elő kell fizetnie a virtuális gépekre, hogy közvetlenül frissítéseket kapjon a Red Hatról.
- Jelenleg nem lehet dinamikusan váltani a BYOS és a linuxos lemezképek felosztó-kiosztó számlázási modelljei között. A számlázási modell váltásához újra kell telepítenie a virtuális gép a megfelelő lemezkép.

>[!NOTE]
> Generációs 2 RHEL BYOS-lemezképek jelenleg nem érhetők el a piactéri ajánlaton keresztül. Ha 2. generációs RHEL BYOS-lemezképre van szüksége, látogasson el a Cloud Access irányítópultjára a Red Hat előfizetés-kezelés ben. További információt a [Red Hat dokumentációjában](https://access.redhat.com/articles/4847681)talál.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>A Red Hat Gold Képek elérésének követelményei és feltételei

1. Ismerkedjen meg a [Red Hat Cloud Access program](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) feltételeivel. Engedélyezze Red Hat-előfizetéseit a Cloud Access szolgáltatáshoz a [Red Hat Subscription-Manager en.](https://access.redhat.com/management/cloud) A Cloud Access szolgáltatásra regisztrálandó Azure-előfizetésekkel kell rendelkeznie.

1. Ha a Cloud Accesshez engedélyezett Red Hat-előfizetések megfelelnek a jogosultsági követelményeknek, az Azure-előfizetések automatikusan engedélyezve lesznek a Gold Image-hozzáféréshez.

### <a name="expected-time-for-image-access"></a>A lemezkép-hozzáférés várható ideje

Miután befejezte a Cloud Access engedélyezése lépéseket, a Red Hat ellenőrzi a Red Hat Gold Images-re való jogosultságát. Ha az ellenőrzés sikeres, három órán belül hozzáférhet az Arany képekhez.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>A Red Hat Gold Images használata az Azure Portalról

1. Miután az Azure-előfizetés hozzáférést kapott a Red Hat Gold Images-hez, megtalálhatja őket az [Azure Portalon.](https://portal.azure.com) Nyissa meg **az Erőforrás** > létrehozása**Az összes megtekintése című témakört.**

1. Az oldal tetején láthatja, hogy privát ajánlatai vannak.

    ![Marketplace privát ajánlatok](./media/rhel-byos-privateoffers.png)

1. Jelölje ki a lila hivatkozást, vagy görgessen le az oldal aljára a privát ajánlatok megtekintéséhez.

1. A felhasználói felület en való kiépítés többi része nem különbözik a többi meglévő Red Hat-lemezképéből. Válassza ki az RHEL-verziót, és kövesse az utasításokat a virtuális gép kiépítéséhez. Ez a folyamat azt is lehetővé teszi, hogy elfogadja a feltételeket a kép az utolsó lépésben.

>[!NOTE]
>Ezek a lépések eddig nem teszik lehetővé a Red Hat Gold Image programozott telepítés. A "További információk" című részben leírtak szerint további lépésre van szükség.

A dokumentum többi része a CLI-módszerre összpontosít, amely leépíti és elfogadja a lemezkép feltételeit. A felhasználói felület és a CLI teljes mértékben felcserélhetők, ami a végeredményt (egy kiépített RHEL Gold Image VM) illeti.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Az Azure CLI Red Hat Gold images használata

A következő utasítások végigvezeti a kezdeti üzembe helyezési folyamat egy RHEL virtuális gép az Azure CLI használatával. Ezek az utasítások feltételezik, hogy az [Azure CLI telepítve](https://docs.microsoft.com/cli/azure/install-azure-cli)van.

>[!IMPORTANT]
>Győződjön meg arról, hogy a közzétevői, ajánlati, terv- és képhivatkozásait a következő parancsokhoz használja.

1. Ellenőrizze, hogy a kívánt előfizetésben van-e.

    ```azurecli
    az account show -o=json
    ```

1. Hozzon létre egy erőforráscsoportot a Red Hat Gold Image virtuális géphez.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Fogadja el a képfeltételeket.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Ezeket a feltételeket *az Azure-előfizetésenként egyszer kell elfogadni, lemezképtermékváltozatonként.*

1. (Nem kötelező) Ellenőrizze a virtuális gép központi telepítését a következő paranccsal:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. A virtuális gép kiépítése ugyanazt a parancsot, `--validate` mint az előző példában az argumentum nélkül látható futtatásával.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH-t a virtuális gépbe, és ellenőrizze, hogy rendelkezik-e egy jogosulatlan rendszerképpel. Ehhez a lépéshez `sudo yum repolist`futtassa a futtassa a futtassa a futtassa a futtassa Az RHEL 8 `sudo dnf repolist`esetén használja a használatát. A kimenet arra kéri, hogy az Előfizetés-Manager használatával regisztrálja a virtuális gépet a Red Hat.

>[!NOTE]
>Az RHEL `dnf` 8-on, és `yum` felcserélhetők. További információt az [RHEL 8 felügyeleti útmutatóban talál.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings)

## <a name="use-the-red-hat-gold-images-from-powershell"></a>A PowerShell Red Hat Gold Images-képeinek használata

A következő parancsfájl egy példa. Cserélje le az erőforráscsoportot, a helyet, a virtuális gép nevét, a bejelentkezési adatokat és az egyéb változókat a választott konfigurációra. A közzétevőés a csomag adatainak kisbetűsnek kell lenniük.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Titkosítsa a Red Hat Enterprise Linux-ot, hogy saját előfizetése Gold Images

A Red Hat Enterprise Linux BYOS Gold Images az [Azure Disk Encryption](../../linux/disk-encryption-overview.md)segítségével biztosítható. Az előfizetést regisztrálni *kell* a titkosítás engedélyezése előtt. A RHEL BYOS Gold Image regisztrálásáról a [Rendszer regisztrálása és előfizetése a Red Hat Subscription-Manager használatával](https://access.redhat.com/solutions/253273)című témakörben talál további információt. Ha aktív Red Hat-előfizetéssel rendelkezik, olvassa el [a Red Hat ügyfélportál aktiválási kulcsainak létrehozása című](https://access.redhat.com/articles/1378093)részt is.

Az Azure Disk Encryption nem támogatott a [Red Hat egyéni lemezképeken.](../../linux/redhat-create-upload-vhd.md) További Azure lemeztitkosítási követelmények és előfeltételek dokumentálva vannak az [Azure Disk Encryption for Linux virtuális gépek.](../../linux/disk-encryption-overview.md#additional-vm-requirements)

Az Azure disk encryption alkalmazásának lépései: [Azure Disk Encryption forgatókönyvek Linux virtuális gépeken](../../linux/disk-encryption-linux.md) és a kapcsolódó cikkek.

## <a name="additional-information"></a>További információ

- Ha olyan előfizetésen próbál meg virtuális gépet létesíteni, amely nincs engedélyezve ehhez az ajánlathoz, a következő üzenet jelenik meg:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    Ebben az esetben az előfizetés engedélyezéséhez lépjen kapcsolatba a Microsofttal vagy a Red Hat tal.

- Ha rhel byos-lemezképből módosít egy pillanatképet, és megpróbálja közzétenni az egyéni lemezképet a [megosztott képtárban,](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries)meg kell adnia a pillanatkép eredeti forrásának megfelelő tervadatokat. A parancs például a következőkre néz ki:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Jegyezze fel a terv paramétereit az utolsó sorban.

    [Az Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) nem támogatott egyéni lemezképeken.

- Ha az automatizálás használatával a virtuális gépek kiépítése a RHEL BYOS-lemezképek, meg kell adnia a terv paraméterei hasonló, mint a mintaparancsok. Ha például a Terraform ot használja, a terv adatait egy [tervblokkban](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan)adja meg.

## <a name="next-steps"></a>További lépések

- A Cloud Access részletes útmutatóit és programrészleteit a [Red Hat Cloud Access dokumentációjában](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)találja.
- Ha többet szeretne megtudni a Red Hat frissítési infrastruktúráról, olvassa el az [Azure Red Hat frissítési infrastruktúráját.](./redhat-rhui.md)
- Ha többet szeretne megtudni az Azure-beli Red Hat-képekről, tekintse meg a [dokumentációs oldalt.](./redhat-images.md)
- A Red Hat támogatási szabályzatai az RHEL összes verziójához a [Red Hat Enterprise Linux életciklus-oldalán](https://access.redhat.com/support/policy/updates/errata) találhatók.
- Az RHEL Gold Images további dokumentációját a [Red Hat dokumentációjában](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)találja.

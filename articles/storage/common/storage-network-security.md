---
title: Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása |} A Microsoft Docs
description: A tárfiók rétegezett hálózati biztonságának konfigurálása.
services: storage
author: cbrooksmsft
ms.service: storage
ms.topic: article
ms.date: 10/30/2018
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: c6b4ed787fa9e8881990801bdee77550a1a0bf2f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473629"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása

Az Azure Storage egy többrétegű biztonsági modellt biztosít. Ez a modell lehetővé teszi, hogy a tárfiókok egy adott csoportjának támogatott hálózatok biztonságos. Ha a hálózati szabályok vannak beállítva, csak a megadott készlet hálózatok összefoglaló adatokat kéri alkalmazások férhetnek hozzá egy tárfiókot.

Egy alkalmazás, amely hozzáfér a storage-fiók, a hálózati szabályok akkor vannak érvényben, amikor a kérést a megfelelő engedély szükséges. Az Azure Active Directory (AD) hitelesítő adatait (blobok és üzenetsorok) támogatott engedélyezési (előzetes verzió) egy érvényes tárelérési kulccsal vagy SAS-jogkivonatát.

> [!IMPORTANT]
> Alapértelmezés szerint ne tudják bekapcsolni a tűzfalszabályok a tárfiók beérkező adatok blokkolja, kivéve, ha a kérések származhatnak olyan szolgáltatás, amely egy Azure virtuális hálózaton (VNet) belül. Blokkolt közé tartoznak az egyéb Azure-szolgáltatások, a naplózás és mérőszámok szolgáltatások, az Azure Portalról, és így tovább.
>
> Az Azure-szolgáltatások, amelyek azáltal, hogy az alhálózat, a szolgáltatáspéldány virtuális hálózaton belüli működnie hozzáférést biztosíthat. Korlátozott számú keresztül forgatókönyvek engedélyezése a [kivételek](#exceptions) az alábbi szakaszban ismertetett mechanizmus. Az Azure portal eléréséhez kell egy gépen belül a megbízható (IP vagy virtuális hálózatok közötti) beállításával lehet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

Alapértelmezés szerint konfigurálja a storage-fiókok (beleértve az internetes forgalmat) minden hálózati forgalmat hozzáférését. Majd hozzáférést biztosítani a forgalom az adott virtuális hálózatok. Ez a konfiguráció lehetővé teszi az alkalmazások biztonságos hálózati határt hozhat létre. Is is hozzáférést biztosít a nyilvános internetes IP-címtartományok, adott interneten vagy a helyi ügyfelektől érkező kapcsolatokat engedélyezi.

Az összes hálózati protokollok, az Azure storage, beleértve a REST és az SMB hálózati szabályok életbe lépnek. Elérni az adatokat, például az Azure portal, a Storage Explorer és az AZCopy explicit hálózati szabályok szükségesek.

Hálózati szabályokat alkalmazhat a meglévő tárfiókok, vagy ha új storage-fiókokat hozhat létre.

Hálózati szabályok érvényesek, ha azok irányuló kérések van kényszerítve. SAS-tokeneket, amelyek egy adott IP-cím hozzáférést korlátozza a hozzáférést a token tulajdonosának szolgálja, de új hozzáférést nem konfigurált hálózati szabályok mellett.

Virtuális gép mely (ideértve a csatlakoztatást és válassza le a műveletek és a lemez IO) a hálózati szabályok nem érinti. A lapblobok REST hozzáférést a hálózati szabályok védik.

Klasszikus tárfiókok nem támogatják a tűzfalak és virtuális hálózatok.

A storage-fiókok nem felügyelt lemez is használható a biztonsági mentési és helyreállítási virtuális gépeken alkalmazott kivételt hoz létre a hálózati szabályok. Ez a folyamat leírása itt található a [kivételek](#exceptions) című szakaszát. Tűzfalkivételek nem alkalmazhatók a felügyelt lemezekkel rendelkező, mivel már felügyeli őket az Azure.

## <a name="change-the-default-network-access-rule"></a>Módosítsa az alapértelmezett hálózati hozzáférési szabályt

Alapértelmezés szerint a tárfiókok bármely hálózati ügyfelek kapcsolatokat fogadjon. A kiválasztott hálózatok való hozzáférés korlátozásához, először módosítania kell az alapértelmezett művelet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet a Kapcsolódás az Azure Storage lehetővé teszi az alkalmazások. Beállítás az alapértelmezett hálózati szabály **megtagadása** blokkolja az összes az adatokhoz való hozzáférés, kivéve, ha az adott hálózati szabályok **biztosítson** hozzáférést is alkalmazza a rendszer. Győződjön meg arról, hozzáférést minden olyan engedélyezett hálózatok, hálózati szabályok segítségével, hogy megtagadja a hozzáférést az alapértelmezett szabály módosítása előtt.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

Alapértelmezett hálózati hozzáférési szabályok storage-fiókok az Azure portal, PowerShell vagy CLIv2 segítségével kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.

1. Alapértelmezés szerint engedélyezi a hozzáférést, válassza a hozzáférés engedélyezéséhez **kiválasztott hálózatok**. Ahhoz, hogy minden hálózati forgalmat, dönt, hogy engedélyezze a hozzáférést **minden hálózatból elérhető**.

1. Kattintson a **mentése** alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShell-lel](/powershell/azure/install-Az-ps) és [jelentkezzen be a](/powershell/azure/authenticate-azureps).

1. Az alapértelmezett szabályt, a tárfiók állapota megjelenítéséhez.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint nem engedélyezi a hálózati hozzáférést.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint hálózati hozzáférés engedélyezéséhez.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) és [jelentkezzen be a](/cli/azure/authenticate-azure-cli).

1. Az alapértelmezett szabályt, a tárfiók állapota megjelenítéséhez.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.defaultAction
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint nem engedélyezi a hálózati hozzáférést.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Deny
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint hálózati hozzáférés engedélyezéséhez.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --default-action Allow
    ```

## <a name="grant-access-from-a-virtual-network"></a>Egy virtuális hálózathoz való hozzáférés engedélyezése

Beállíthatja, hogy engedélyezi a hozzáférést csak adott virtuális hálózatok a storage-fiókokat.

Engedélyezze a [szolgáltatásvégpont](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage a virtuális hálózaton belül. Ezt a végpontot egy vezető optimális útvonalat biztosít a forgalom az Azure Storage szolgáltatást. Minden egyes kérelemmel is továbbítja az identitások, a virtuális hálózatot és alhálózatot. A rendszergazdák konfigurálhatják majd hálózati szabályokat a tárfiók, amelyek lehetővé teszik a kérelmek fogadását az adott alhálózatok a virtuális hálózaton. Az ügyfelek kapnak a szabályokban a hálózaton keresztüli hozzáférés továbbra is az adatok a storage-fiók a engedélyezési követelmények teljesítéséhez.

Minden tárfióknak támogatja a legfeljebb 100 virtuális hálózati szabályok, amelyek kombinálható [IP-hálózati szabályok](#grant-access-from-an-internet-ip-range).

### <a name="available-virtual-network-regions"></a>Rendelkezésre álló virtuális hálózati régióban

Általánosságban elmondható végpontok közötti virtuális hálózatok és a szolgáltatáspéldány ugyanazon Azure-régióban működik. Az Azure Storage a Szolgáltatásvégpontok használatakor ebben a hatókörben tartalmazza növekedésével a [párosított régióra](/azure/best-practices-availability-paired-regions). A Szolgáltatásvégpontok folytonosságát egy regionális feladatátvétel és az írásvédett georedundáns tárolás (RA-GRS) példányok való hozzáférés során. Hálózati szabályok, amelyek hozzáférést biztosítanak a virtuális hálózatról egy storage-fiókhoz is hozzáférést tetszőleges RA-GRS-példányra.

Vészhelyreállítás tervezése egy regionális kimaradás során, amikor kell létrehoznia a virtuális hálózatok a párosított régióban előre. Engedélyezze a szolgáltatásvégpontokat az Azure Storage-, hálózati szabályokkal hozzáférést biztosít az ezeket a másodlagos virtuális hálózatokat. Ezután alkalmazza ezeket a szabályokat a georedundáns tárolás fiókokhoz.

> [!NOTE]
> A Szolgáltatásvégpontok forgalmat a virtuális hálózat és a kijelölt régiópárra a régión kívül nem vonatkoznak. Storage-fiókok storage-fiók az elsődleges régióban, vagy a kijelölt párosított régióban való hozzáférés biztosítása a virtuális hálózatok hálózati szabályok csak alkalmazhat.

### <a name="required-permissions"></a>Szükséges engedélyek

Egy virtuális hálózati szabályt alkalmazni egy tárfiókot, a felhasználó a hozzáadott alhálózatokra vonatkozó megfelelő engedélyekkel kell rendelkeznie. Az engedély szükséges *alhálózathoz csatlakozzon szolgáltatás* és szerepel a *Tárfiók-közreműködő* beépített szerepkör. Azt is hozzáadhat egyéni szerepkör-definíciók.

Storage-fiók és a virtuális hálózatok biztosított hozzáférés lehet különböző előfizetésben található, de ezen előfizetések kell lennie az Azure AD-bérlőhöz tartozik.

### <a name="managing-virtual-network-rules"></a>A virtuális hálózati szabályok kezelése

Virtuális hálózati szabályok tárfiókok az Azure portal, PowerShell vagy CLIv2 keresztül kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.

1. Ellenőrizze, hogy engedélyezze a hozzáférést a kiválasztott **kiválasztott hálózatok**.

1. Hozzáférést egy új hálózati szabályt, a virtuális hálózat alatt **virtuális hálózatok**, kattintson a **meglévő virtuális hálózat hozzáadása**válassza **virtuális hálózatok** és **Alhálózatok** lehetőségeket, és kattintson **Hozzáadás**. Hozzon létre egy új virtuális hálózatot, és hozzáférés biztosítása, kattintson a **hozzáadása új virtuális hálózat**. Adja meg az új virtuális hálózat létrehozása, és kattintson a szükséges információk **létrehozás**.

    > [!NOTE]
    > Ha az Azure Storage-szolgáltatásvégpontot korábban nem volt beállítva a kiválasztott virtuális hálózat és alhálózatok, konfigurálhatja a művelet részeként.

1. Egy virtuális hálózat vagy alhálózat szabály eltávolításához kattintson **...**  nyissa meg a virtuális hálózat vagy alhálózat helyi menüjére, majd kattintson a **eltávolítása**.

1. Kattintson a **mentése** alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShell-lel](/powershell/azure/install-Az-ps) és [jelentkezzen be a](/powershell/azure/authenticate-azureps).

1. A virtuális hálózati szabályok listája.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Szolgáltatásvégpont engedélyezése az Azure Storage egy meglévő virtuális hálózatot és alhálózatot az.

    ```PowerShell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

1. Távolítsa el a virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```PowerShell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy a hálózati szabályok nem befolyásolják.

#### <a name="cliv2"></a>CLIv2

1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) és [jelentkezzen be a](/cli/azure/authenticate-azure-cli).

1. A virtuális hálózati szabályok listája.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query virtualNetworkRules
    ```

1. Szolgáltatásvégpont engedélyezése az Azure Storage egy meglévő virtuális hálózatot és alhálózatot az.

    ```azurecli
    az network vnet subnet update --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --service-endpoints "Microsoft.Storage"
    ```

1. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

1. Távolítsa el a virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy a hálózati szabályok nem befolyásolják.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférést biztosít egy internetes IP-címtartomány

Konfigurálhatja a storage-fiókok hozzáférést adott nyilvános internetről IP-címtartományok. Ez a konfiguráció bizonyos internetes szolgáltatásokhoz való hozzáférést és a helyszíni hálózatokkal és általános internetes forgalmat blokkol.

Adja meg az engedélyezett internetes használatával címtartományok [CIDR-jelölés](https://tools.ietf.org/html/rfc4632) formájában *16.17.18.0/24* vagy az egyes IP címek például *16.17.18.19*.

   > [!NOTE]
   > Kis címtartományok használatával "/ 31" vagy "/ 32" előtag méretei nem támogatottak. Ezek a tartományok egyedi IP-cím szabályok használatával kell konfigurálni.

Csak engedélyezett IP-hálózati szabályok **nyilvános interneten** IP-címeket. IP-címtartományok, magánhálózatok számára fenntartott (ahogyan az az [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) az IP-szabályok nem engedélyezett. Magánhálózatokat közé tartozik a címek kezdődő _10.*_, _172.16. *_ - _172.31. *_, és _192.168. *_.

   > [!NOTE]
   > IP-hálózati szabályok nem befolyásolják a az azonos Azure-régióban a storage-fiók érkező kérelmek esetén. Használat [virtuális hálózati szabályok](#grant-access-from-a-virtual-network) ugyanabban a régióban kérelem engedélyezéséhez.

Jelenleg csak az IPV4-cím támogatott.

Minden tárfióknak támogatja a legfeljebb 100 IP hálózati szabályok, amelyek kombinálható [virtuális hálózati szabályok](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Hozzáférés a helyszíni hálózatok konfigurálása

Való hozzáférés biztosítása a helyszíni hálózat egy IP-hálózati szabállyal a tárfiókhoz, meg kell adnia az internetkapcsolattal rendelkező, a hálózat által használt IP-címek. Segítségért forduljon a rendszergazdához.

Használhat [ExpressRoute](/azure/expressroute/expressroute-introduction) , a hálózat csatlakoztatása az Azure-hálózatot. Itt minden kapcsolatnak két nyilvános IP-cím van beállítva. A Microsoft Edge tekinthet meg, és a használata [Azure nyilvános társviszony-létesítés](/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) hasonlóan az Azure Storage a Microsoft Serviceshez való csatlakozáshoz. Engedélyezi a kommunikációt az Azure Storage, a nyilvános IP-címét a Kapcsolatcsoportok IP hálózati szabályokat létrehozni. Az ExpressRoute-kapcsolatcsoport nyilvános IP-címek keresése [nyisson egy támogatási jegyet az expressroute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon keresztül.

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

Storage-fiókok az Azure Portalon, a PowerShell vagy a CLIv2 IP-hálózati szabályok is kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.

1. Ellenőrizze, hogy engedélyezze a hozzáférést a kiválasztott **kiválasztott hálózatok**.

1. Adja meg az IP-címtartomány egy internetes hozzáférést, adja meg az IP-cím vagy a címtartomány (CIDR formátumban) alatt **tűzfal** > **címtartomány**.

1. Az IP-hálózati szabály eltávolításához kattintson a Kuka ikonra a címtartomány mellett.

1. Kattintson a **mentése** alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShell-lel](/powershell/azure/install-Az-ps) és [jelentkezzen be a](/powershell/azure/authenticate-azureps).

1. IP-hálózati szabályok listája.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adja hozzá az egyes IP-cím hálózati szabályt.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adjon hozzá egy IP-címtartomány hálózati szabályt.

    ```PowerShell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Távolítsa el az egyes IP-cím hálózati szabályt.

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Távolítsa el az IP-címtartomány hálózati szabályt.

    ```PowerShell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy a hálózati szabályok nem befolyásolják.

#### <a name="cliv2"></a>CLIv2

1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) és [jelentkezzen be a](/cli/azure/authenticate-azure-cli).

1. IP-hálózati szabályok listája.

    ```azurecli
    az storage account network-rule list --resource-group "myresourcegroup" --account-name "mystorageaccount" --query ipRules
    ```

1. Adja hozzá az egyes IP-cím hálózati szabályt.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Adjon hozzá egy IP-címtartomány hálózati szabályt.

    ```azurecli
    az storage account network-rule add --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

1. Távolítsa el az egyes IP-cím hálózati szabályt.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.19"
    ```

1. Távolítsa el az IP-címtartomány hálózati szabályt.

    ```azurecli
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --ip-address "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy a hálózati szabályok nem befolyásolják.

## <a name="exceptions"></a>Kivételek

A hálózati szabályok engedélyezheti a biztonságos hálózati konfigurációja a legtöbb forgatókönyvhöz. Vannak azonban bizonyos esetekben, ahol kivételek rendelkeznie kell a összes funkciójának engedélyezése. Kivételek, a megbízható Microsoft-szolgáltatások és a storage analytics adatokhoz való hozzáférést a storage-fiókok segítségével konfigurálhat.

### <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások

Bizonyos Microsoft-szolgáltatások, amelyek a storage-fiókok kezelése, amelyek nem adható hozzáférés hálózati jogcímszabályai hálózatokról működnek.

Az ilyen típusú szolgáltatás munkahelyi helyesen érdekében lehetővé teszi a megbízható Microsoft-szolgáltatások megkerülése a hálózati szabályok készletét. Ezek a szolgáltatások fogja használni a tárfiók eléréséhez erős hitelesítés.

Ha engedélyezi a **engedélyezése a Microsoft-szolgáltatások megbízható...**  kivétel, a következő szolgáltatásokat (ha regisztrált az előfizetésben), a storage-fiók hozzáféréssel rendelkeznek:

|Szolgáltatás|Erőforrás-szolgáltató neve|Cél|
|:------|:---------------------|:------|
|Azure Backup|Microsoft.Backup|Futtassa a biztonsági mentéshez és helyreállításhoz, a nem felügyelt lemezek az iaas-beli virtuális gépeken. (nem felügyelt lemezek esetén szükséges). [További információk](/azure/backup/backup-introduction-to-azure-backup).|
|Azure Site Recovery|Microsoft.SiteRecovery |Vészhelyreállítás konfigurálása az Azure IaaS virtuális gépek replikációjának engedélyezése. Ez azért szükséges, ha a tűzfal engedélyezve van a gyorsítótár tárfiókja vagy a forrás tárfiókban vagy a célként megadott tárfiók használata.  [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication).|
|Azure DevTest Labs|Microsoft.DevTestLab|Egyéni rendszerkép létrehozása és az összetevő telepítését. [További információk](/azure/devtest-lab/devtest-lab-overview).|
|Azure Event Grid|Microsoft.EventGrid|A Blob Storage esemény-közzététel engedélyezése, és lehetővé teszi az Event Grid tárolási üzenetsorok közzétételére. Ismerje meg [blob storage-események](/azure/event-grid/event-sources) és [várólisták közzétételét](/azure/event-grid/event-handlers).|
|Azure Event Hubs|Microsoft.EventHub|Az Event Hubs Capture adatok archiválása. [További](/azure/event-hubs/event-hubs-capture-overview).|
|Azure-hálózatok|Microsoft.Networking|Store és a hálózati forgalom naplóinak elemzése. [További információk](/azure/network-watcher/network-watcher-packet-capture-overview).|
|Azure Monitor|Microsoft.Insights|Lehetővé teszi, hogy a figyelési adatok biztonságos storage-fiókba írásos [további](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security#monitoring-and-secured-Azure-storage-and-networks).|
|Azure SQL Data Warehouse|Microsoft.Sql|Lehetővé teszi, hogy importálása és exportálása a PolyBase forgatókönyveket. [További információk](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview).|

### <a name="storage-analytics-data-access"></a>Storage analytics-adatok elérése

Bizonyos esetekben a diagnosztikai naplók és mérőszámok olvasási hozzáférést szükség a a hálózathatáron kívül. Meg lehet adni a kivételeket úgy, hogy a hálózati szabályokat, hogy a storage-fiók naplófájlok vagy tabulky metrik írásvédett. [További tudnivalók a storage analytics használata.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése

Hálózati kivételeit, annak az Azure portal, PowerShell vagy az Azure CLI segítségével is kezelheti v2.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a beállítások menü nevű **tűzfalak és virtuális hálózatok**.

1. Ellenőrizze, hogy engedélyezze a hozzáférést a kiválasztott **kiválasztott hálózatok**.

1. A **kivételek**, válassza ki a kivételek kíván biztosítani.

1. Kattintson a **mentése** alkalmazza a módosításokat.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShell-lel](/powershell/azure/install-Az-ps) és [jelentkezzen be a](/powershell/azure/authenticate-azureps).

1. A storage-fiók hálózati szabályok esetében a kivételek jelennek meg.

    ```PowerShell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. A storage-fiók hálózati szabályok alóli kivételek konfigurálása.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Távolítsa el a storage-fiók hálózati szabályok alóli kivételek.

    ```PowerShell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy távolítsa el a kivételek nem befolyásolják.

#### <a name="cliv2"></a>CLIv2

1. Telepítse a [Azure CLI-vel](/cli/azure/install-azure-cli) és [jelentkezzen be a](/cli/azure/authenticate-azure-cli).

1. A storage-fiók hálózati szabályok esetében a kivételek jelennek meg.

    ```azurecli
    az storage account show --resource-group "myresourcegroup" --name "mystorageaccount" --query networkRuleSet.bypass
    ```

1. A storage-fiók hálózati szabályok alóli kivételek konfigurálása.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass Logging Metrics AzureServices
    ```

1. Távolítsa el a storage-fiók hálózati szabályok alóli kivételek.

    ```azurecli
    az storage account update --resource-group "myresourcegroup" --name "mystorageaccount" --bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [adja meg az alapértelmezett szabályt](#change-the-default-network-access-rule) való **megtagadása**, vagy távolítsa el a kivételek nem befolyásolják.

## <a name="next-steps"></a>További lépések

További információ az Azure hálózati Szolgáltatásvégpontok [Szolgáltatásvégpontokat](/azure/virtual-network/virtual-network-service-endpoints-overview).

Tájékozódjon részletesebben az Azure Storage biztonsági [Azure Storage biztonsági útmutatóját](storage-security-guide.md).

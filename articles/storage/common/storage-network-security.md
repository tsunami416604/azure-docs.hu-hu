---
title: Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása |} A Microsoft Docs
description: A tárfiók rétegezett hálózati biztonságának konfigurálása.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: tamram
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 03f383bd6e44a55388e0bef6a38c4a1880d6f044
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268261"
---
# <a name="configure-azure-storage-firewalls-and-virtual-networks"></a>Az Azure Storage-tűzfalak és virtuális hálózatok konfigurálása

Az Azure Storage egy többrétegű biztonsági modellt biztosít. Ez a modell lehetővé teszi az alkalmazások és a vállalati környezetek által igénybe vett Storage-fiókok elérési szintjének védelmét és szabályozását a felhasznált hálózatok típusa és részhalmaza alapján. A hálózati szabályok konfigurálásakor csak a megadott hálózatokon adatokat kérő alkalmazások férhetnek hozzá egy Storage-fiókhoz. A Storage-fiókhoz való hozzáférést korlátozhatja a megadott IP-címekről, IP-tartományokról vagy egy Azure-Virtual Network (VNet) lévő alhálózatok listájáról.

A Storage-fiókokhoz nyilvános végpont tartozik, amely az interneten keresztül érhető el. Létrehozhat [privát végpontokat is a Storage-fiókjához](storage-private-endpoints.md), amely egy magánhálózati IP-címet rendel a VNet a Storage-fiókhoz, és a VNet és a Storage-fiók közötti összes forgalmat privát kapcsolaton keresztül biztosítja. Az Azure Storage tűzfal hozzáférés-vezérlési hozzáférést biztosít a Storage-fiók nyilvános végpontjának. A tűzfalat használhatja a nyilvános végponton keresztüli összes hozzáférés blokkolására is, ha privát végpontokat használ. A tárolási tűzfal konfigurációja azt is lehetővé teszi, hogy a megbízható Azure platform szolgáltatásai biztonságosan férhessenek hozzá a Storage-fiókhoz.

Egy olyan alkalmazás, amely hozzáfér egy Storage-fiókhoz, ha a hálózati szabályok érvényben maradnak, a kérelemhez megfelelő engedély szükséges. Az engedélyezést Azure Active Directory (Azure AD) hitelesítő adatok támogatják a blobok és várólisták esetében, érvényes fiók-hozzáférési kulccsal vagy SAS-jogkivonattal.

> [!IMPORTANT]
> Ha bekapcsolja a tűzfalszabályok bekapcsolását a Storage-fiókhoz, az alapértelmezés szerint letiltja a bejövő adatkéréseket, kivéve, ha a kérelmek Azure-Virtual Network (VNet) vagy engedélyezett nyilvános IP-címeken belüli szolgáltatásból származnak. Blokkolt közé tartoznak az egyéb Azure-szolgáltatások, a naplózás és mérőszámok szolgáltatások, az Azure Portalról, és így tovább.
>
> Hozzáférést biztosíthat az olyan Azure-szolgáltatásokhoz, amelyek egy VNet belül működnek, és lehetővé teszik a szolgáltatás-példányt üzemeltető alhálózatról érkező forgalom elérését. Az alábbiakban ismertetett [kivételek](#exceptions) használatával korlátozott számú forgatókönyvet is engedélyezhet. Ha a Azure Portal keresztül szeretne hozzáférni a Storage-fiókból, a beállított megbízható határon (IP-vagy VNet) belüli gépen kell lennie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Forgatókönyvek

A Storage-fiók biztonságossá tételéhez először konfigurálnia kell egy olyan szabályt, amely megtagadja a hozzáférést az összes hálózatról (beleértve az internetes forgalmat) a nyilvános végponton, alapértelmezés szerint. Ezután olyan szabályokat kell konfigurálnia, amelyek hozzáférést biztosítanak az adott virtuális hálózatok érkező forgalomhoz. Olyan szabályokat is beállíthat, amelyek hozzáférést biztosítanak a forgalomhoz a nyilvános internetes IP-címtartományok kiválasztásával, valamint az adott internetes vagy helyszíni ügyfelek kapcsolatainak engedélyezésével. Ez a konfiguráció lehetővé teszi az alkalmazások biztonságos hálózati határt hozhat létre.

Az adott virtuális hálózatokról és a nyilvános IP-címtartományok elérését engedélyező tűzfalszabályok ugyanazon a Storage-fiókon kombinálhatók. A tárolási tűzfalszabályok alkalmazhatók a meglévő Storage-fiókokra, illetve új Storage-fiókok létrehozásakor is.

A tárolási tűzfalszabályok a Storage-fiók nyilvános végpontján érvényesek. Nincs szükség tűzfal-hozzáférési szabályokra a Storage-fiókok privát végpontjai forgalmának engedélyezéséhez. A privát végpontok létrehozásának jóváhagyásának folyamata implicit hozzáférést biztosít a privát végpontot üzemeltető alhálózatról érkező forgalomhoz.

Az összes hálózati protokollok, az Azure storage, beleértve a REST és az SMB hálózati szabályok életbe lépnek. Ha olyan eszközökkel szeretné elérni az adatelérést, mint a Azure Portal, a Storage Explorer és a AZCopy, akkor explicit hálózati szabályokat kell konfigurálni.

Hálózati szabályok érvényesek, ha azok irányuló kérések van kényszerítve. SAS-tokeneket, amelyek egy adott IP-cím hozzáférést korlátozza a hozzáférést a token tulajdonosának szolgálja, de új hozzáférést nem konfigurált hálózati szabályok mellett.

Virtuális gép mely (ideértve a csatlakoztatást és válassza le a műveletek és a lemez IO) a hálózati szabályok nem érinti. A lapblobok REST hozzáférést a hálózati szabályok védik.

Klasszikus tárfiókok nem támogatják a tűzfalak és virtuális hálózatok.

A storage-fiókok nem felügyelt lemez is használható a biztonsági mentési és helyreállítási virtuális gépeken alkalmazott kivételt hoz létre a hálózati szabályok. Ezt a folyamatot a jelen cikk [kivételek](#exceptions) című szakaszában dokumentáljuk. Tűzfalkivételek nem alkalmazhatók a felügyelt lemezekkel rendelkező, mivel már felügyeli őket az Azure.

## <a name="change-the-default-network-access-rule"></a>Módosítsa az alapértelmezett hálózati hozzáférési szabályt

Alapértelmezés szerint a tárfiókok bármely hálózati ügyfelek kapcsolatokat fogadjon. A kiválasztott hálózatok való hozzáférés korlátozásához, először módosítania kell az alapértelmezett művelet.

> [!WARNING]
> A hálózati szabályok módosítása hatással lehet a Kapcsolódás az Azure Storage lehetővé teszi az alkalmazások. Ha az alapértelmezett hálózati szabályt állítja **be, az letiltja** az összes hozzáférését az összes adathoz **, kivéve** , ha a hozzáférést biztosító meghatározott hálózati szabályok is érvényesek. Győződjön meg arról, hozzáférést minden olyan engedélyezett hálózatok, hálózati szabályok segítségével, hogy megtagadja a hozzáférést az alapértelmezett szabály módosítása előtt.

### <a name="managing-default-network-access-rules"></a>Alapértelmezett hálózati hozzáférési szabályok kezelése

Alapértelmezett hálózati hozzáférési szabályok storage-fiókok az Azure portal, PowerShell vagy CLIv2 segítségével kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Ha alapértelmezés szerint szeretné megtagadni a hozzáférést, válassza a **kijelölt hálózatokból**való hozzáférés engedélyezése lehetőséget. Az összes hálózatról érkező forgalom engedélyezéséhez válassza az **összes hálózatról**való hozzáférés engedélyezése lehetőséget.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. Az alapértelmezett szabályt, a tárfiók állapota megjelenítéséhez.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").DefaultAction
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint nem engedélyezi a hálózati hozzáférést.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Deny
    ```

1. Állítsa be az alapértelmezett szabályt, alapértelmezés szerint hálózati hozzáférés engedélyezéséhez.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -DefaultAction Allow
    ```

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

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

A Storage-fiókokat úgy is konfigurálhatja, hogy csak bizonyos alhálózatokról engedélyezze a hozzáférést. Az engedélyezett alhálózatok ugyanahhoz az előfizetéshez vagy egy másik előfizetéshez tartozó VNet tartozhatnak, beleértve a más Azure Active Directory bérlőhöz tartozó előfizetéseket is.

Engedélyezzen egy [szolgáltatási végpontot](/azure/virtual-network/virtual-network-service-endpoints-overview) az Azure Storage-hoz a VNet belül. A szolgáltatási végpont az Azure Storage szolgáltatás optimális elérési útján irányítja át a forgalmat a VNet. Az alhálózat és a virtuális hálózat identitásait is továbbítjuk az egyes kérésekhez. A rendszergazdák ezután konfigurálhatják a Storage-fiók hálózati szabályait, amelyek lehetővé teszik a kérelmek fogadását egy adott alhálózatról egy VNet. Az ügyfelek kapnak a szabályokban a hálózaton keresztüli hozzáférés továbbra is az adatok a storage-fiók a engedélyezési követelmények teljesítéséhez.

Az egyes Storage-fiókok akár 100 virtuális hálózati szabályt is támogatnak, amelyek az [IP-hálózati szabályokkal](#grant-access-from-an-internet-ip-range)kombinálhatók.

### <a name="available-virtual-network-regions"></a>Rendelkezésre álló virtuális hálózati régióban

Általánosságban elmondható végpontok közötti virtuális hálózatok és a szolgáltatáspéldány ugyanazon Azure-régióban működik. A szolgáltatási végpontok Azure Storage-ban való használatakor ez a hatókör nő a [párosított régió](/azure/best-practices-availability-paired-regions)belefoglalásával. A Szolgáltatásvégpontok folytonosságát egy regionális feladatátvétel és az írásvédett georedundáns tárolás (RA-GRS) példányok való hozzáférés során. Hálózati szabályok, amelyek hozzáférést biztosítanak a virtuális hálózatról egy storage-fiókhoz is hozzáférést tetszőleges RA-GRS-példányra.

Vészhelyreállítás tervezése egy regionális kimaradás során, amikor kell létrehoznia a virtuális hálózatok a párosított régióban előre. Engedélyezze a szolgáltatásvégpontokat az Azure Storage-, hálózati szabályokkal hozzáférést biztosít az ezeket a másodlagos virtuális hálózatokat. Ezután alkalmazza ezeket a szabályokat a georedundáns tárolás fiókokhoz.

> [!NOTE]
> A Szolgáltatásvégpontok forgalmat a virtuális hálózat és a kijelölt régiópárra a régión kívül nem vonatkoznak. Storage-fiókok storage-fiók az elsődleges régióban, vagy a kijelölt párosított régióban való hozzáférés biztosítása a virtuális hálózatok hálózati szabályok csak alkalmazhat.

### <a name="required-permissions"></a>Szükséges engedélyek

Egy virtuális hálózati szabályt alkalmazni egy tárfiókot, a felhasználó a hozzáadott alhálózatokra vonatkozó megfelelő engedélyekkel kell rendelkeznie. A szükséges engedély *egy alhálózathoz való csatlakoztatási szolgáltatás* , amely a *Storage-fiók közreműködői* beépített szerepkör részét képezi. Azt is hozzáadhat egyéni szerepkör-definíciók.

A Storage-fiók és a hozzáférést biztosító virtuális hálózatok különböző előfizetésekben lehetnek, beleértve az olyan előfizetéseket, amelyek egy másik Azure AD-bérlő részét képezik.

> [!NOTE]
> A virtuális hálózatok olyan alhálózatokhoz való hozzáférését biztosító szabályok konfigurálása, amelyek egy másik Azure Active Directory bérlő részét képezik, jelenleg csak a PowerShell, a CLI és a REST API-k támogatják. Ezek a szabályok nem konfigurálhatók a Azure Portalon keresztül, de a portálon is megtekinthetők.

### <a name="managing-virtual-network-rules"></a>A virtuális hálózati szabályok kezelése

Virtuális hálózati szabályok tárfiókok az Azure portal, PowerShell vagy CLIv2 keresztül kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. Ha új hálózati szabállyal szeretne hozzáférést biztosítani egy virtuális hálózathoz, a **virtuális hálózatok**területen kattintson a **meglévő virtuális hálózat hozzáadása**lehetőségre, válassza a **virtuális hálózatok** és **alhálózatok** lehetőséget, majd kattintson a **Hozzáadás**gombra. Új virtuális hálózat létrehozásához és az IT-hozzáférés biztosításához kattintson az **új virtuális hálózat hozzáadása**lehetőségre. Adja meg az új virtuális hálózat létrehozásához szükséges adatokat, majd kattintson a **Létrehozás**gombra.

    > [!NOTE]
    > Ha az Azure Storage-szolgáltatásvégpontot korábban nem volt beállítva a kiválasztott virtuális hálózat és alhálózatok, konfigurálhatja a művelet részeként.
    >
    > Jelenleg csak az ugyanahhoz a Azure Active Directory bérlőhöz tartozó virtuális hálózatok jelennek meg a szabályok létrehozásakor. Egy másik bérlőhöz tartozó virtuális hálózatban lévő alhálózathoz való hozzáférés biztosításához használja a PowerShell, a CLI vagy a REST API-kat.

1. Virtuális hálózat vagy alhálózat szabályának eltávolításához kattintson a **...** elemre a virtuális hálózat vagy alhálózat helyi menüjének megnyitásához, majd kattintson az **Eltávolítás**gombra.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. A virtuális hálózati szabályok listája.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").VirtualNetworkRules
    ```

1. Szolgáltatásvégpont engedélyezése az Azure Storage egy meglévő virtuális hálózatot és alhálózatot az.

    ```powershell
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" -AddressPrefix "10.0.0.0/24" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    ```

1. Adjon hozzá egy virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat hálózati szabályának hozzáadásához használjon egy teljesen minősített **VirtualNetworkResourceId** paramétert "/Subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name" formátumban.

1. Távolítsa el a virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```powershell
    $subnet = Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" -Name "myvnet" | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -VirtualNetworkResourceId $subnet.Id
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

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

    > [!TIP]
    > Egy másik Azure AD-bérlőhöz tartozó VNet lévő alhálózat szabályának hozzáadásához használjon egy teljesen minősített alhálózati azonosítót a következő formában: "/Subscriptions/\<előfizetés-azonosító\>/resourceGroups/\<resourceGroup-Name\>/providers/Microsoft.Network/virtualNetworks/\<vNet-Name\>/Subnets/\<alhálózat-név\>".
    >
    > Az **előfizetés** paraméter használatával lekérheti az alhálózati azonosítót egy másik Azure ad-bérlőhöz tartozó VNet.

1. Távolítsa el a virtuális hálózatot és alhálózatot a hálózati szabályt.

    ```azurecli
    $subnetid=(az network vnet subnet show --resource-group "myresourcegroup" --vnet-name "myvnet" --name "mysubnet" --query id --output tsv)
    az storage account network-rule remove --resource-group "myresourcegroup" --account-name "mystorageaccount" --subnet $subnetid
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="grant-access-from-an-internet-ip-range"></a>Hozzáférést biztosít egy internetes IP-címtartomány

Konfigurálhatja a storage-fiókok hozzáférést adott nyilvános internetről IP-címtartományok. Ez a konfiguráció bizonyos internetes szolgáltatásokhoz való hozzáférést és a helyszíni hálózatokkal és általános internetes forgalmat blokkol.

Adja meg az engedélyezett IP-címtartományok [CIDR jelöléssel](https://tools.ietf.org/html/rfc4632) a *16.17.18.0/24* formátumban, illetve olyan egyedi IP-címeket, mint a *16.17.18.19*.

   > [!NOTE]
   > Kis címtartományok használatával "/ 31" vagy "/ 32" előtag méretei nem támogatottak. Ezek a tartományok egyedi IP-cím szabályok használatával kell konfigurálni.

Az IP-hálózati szabályok csak a **nyilvános internetes** IP-címek esetében engedélyezettek. A magánhálózati hálózatok számára fenntartott IP-címtartományok (az [RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)-ben meghatározottak szerint) nem engedélyezettek az IP-szabályokban. A magánhálózati hálózatok közé tartoznak a következők: _10. *_ , _172,16. *_  - _172,31. *_ és _192,168. *_ .

   > [!NOTE]
   > IP-hálózati szabályok nem befolyásolják a az azonos Azure-régióban a storage-fiók érkező kérelmek esetén. A [virtuális hálózati szabályok](#grant-access-from-a-virtual-network) használatával engedélyezze az azonos régiókra vonatkozó kérelmeket.

  > [!NOTE]
  > A Storage-fiókkal azonos régióban üzembe helyezett szolgáltatások saját Azure IP-címeket használnak a kommunikációhoz. Így az adott Azure-szolgáltatásokhoz való hozzáférés nem korlátozható a nyilvános bejövő IP-címtartomány alapján.

A tárolási tűzfalszabályok konfigurálásához csak IPV4-címek támogatottak.

Az egyes Storage-fiókok legfeljebb 100 IP-hálózati szabályt támogatnak.

### <a name="configuring-access-from-on-premises-networks"></a>Hozzáférés a helyszíni hálózatok konfigurálása

Való hozzáférés biztosítása a helyszíni hálózat egy IP-hálózati szabállyal a tárfiókhoz, meg kell adnia az internetkapcsolattal rendelkező, a hálózat által használt IP-címek. Segítségért forduljon a rendszergazdához.

Ha a [ExpressRoute](/azure/expressroute/expressroute-introduction) -t használja a telephelyéről, a nyilvános és a Microsoft-partnerek számára, meg kell határoznia a használt NAT IP-címeket. Nyilvános társviszony-létesítés esetén alapértelmezés szerint minden ExpressRoute-kapcsolatcsoport két NAT IP-címet használ, amelyeket akkor alkalmaz az Azure-szolgáltatások forgalmára, amikor a forgalom belép a Microsoft Azure gerinchálózatába. A Microsoft-társak esetében a használt NAT IP-címek vagy a szolgáltató által biztosított vagy biztosított ügyfelek. A szolgáltatási erőforrások hozzáférésének engedélyezéséhez engedélyeznie kell ezeket a nyilvános IP-címeket az erőforrás IP-tűzfalának beállításai között. A nyilvános társviszony-létesítési ExpressRoute-kapcsolatcsoport IP-címeinek megkereséséhez [hozzon létre egy támogatási jegyet az ExpressRoute-tal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) az Azure Portalon. További információk az [ExpressRoute NAT nyilvános és Microsoft-társviszony-létesítéséről](/azure/expressroute/expressroute-nat#nat-requirements-for-azure-public-peering).

### <a name="managing-ip-network-rules"></a>IP-hálózati szabályok kezelése

Storage-fiókok az Azure Portalon, a PowerShell vagy a CLIv2 IP-hálózati szabályok is kezelheti.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. Ha hozzáférést szeretne biztosítani egy internetes IP-tartományhoz, adja meg az IP-címet vagy címtartományt (CIDR formátumban) a **tűzfal** > **címtartomány**területen.

1. Az IP-hálózati szabály eltávolításához kattintson a Kuka ikonra a címtartomány mellett.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. IP-hálózati szabályok listája.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount").IPRules
    ```

1. Adja hozzá az egyes IP-cím hálózati szabályt.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Adjon hozzá egy IP-címtartomány hálózati szabályt.

    ```powershell
    Add-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

1. Távolítsa el az egyes IP-cím hálózati szabályt.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.19"
    ```

1. Távolítsa el az IP-címtartomány hálózati szabályt.

    ```powershell
    Remove-AzStorageAccountNetworkRule -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount" -IPAddressOrRange "16.17.18.0/24"
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

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
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a hálózati szabályok nem lépnek érvénybe.

## <a name="exceptions"></a>Kivételek

A hálózati szabályok segítenek a biztonságos környezet létrehozásában az alkalmazások és az adatok közötti kapcsolatokhoz a legtöbb esetben. Néhány alkalmazás azonban olyan Azure-szolgáltatástól függ, amely nem különíthető el egyedien a virtuális hálózat vagy az IP-cím szabályai alapján. Az alkalmazások teljes funkcionalitásának lehetővé tételéhez azonban az ilyen szolgáltatásokat biztosítani kell a tároláshoz. Ilyen helyzetekben használhatja a ***megbízható Microsoft-szolgáltatások engedélyezése...*** beállítást annak engedélyezéséhez, hogy az ilyen szolgáltatások hozzáférjenek az adataihoz, a naplókhoz vagy az elemzésekhez.

### <a name="trusted-microsoft-services"></a>Megbízható Microsoft-szolgáltatások

Bizonyos Microsoft-szolgáltatások olyan hálózatokból működnek, amelyek nem vehetők fel a hálózati szabályokba. Az ilyen megbízható Microsoft-szolgáltatások egy részhalmazát megadhatja a Storage-fiókhoz, miközben más alkalmazások hálózati szabályait is megtarthatja. Ezek a megbízható szolgáltatások a biztonságos hitelesítés használatával biztonságosan csatlakozhatnak a Storage-fiókhoz. Engedélyezte a Microsoft-szolgáltatásokhoz való megbízható hozzáférés két üzemmódját.

- Egyes szolgáltatások erőforrásai, **Amikor regisztrálva vannak az előfizetésben**, a kiválasztható műveletekhez, például a naplók vagy a biztonsági mentés írásához is hozzáférhetnek a Storage **-** fiókhoz.
- Egyes szolgáltatások erőforrásai explicit módon férhetnek hozzá a Storage-fiókhoz, ha **hozzárendel egy RBAC-szerepkört** a rendszerhez rendelt felügyelt identitáshoz.


Ha engedélyezi a **megbízható Microsoft-szolgáltatások engedélyezése...** beállítást, az alábbi, a Storage-fiókkal megegyező előfizetésben regisztrált szolgáltatások erőforrásai korlátozott számú művelethez kapnak hozzáférést a leírtak szerint:

| Szolgáltatás                  | Erőforrás-szolgáltató neve     | Engedélyezett műveletek                 |
|:------------------------ |:-------------------------- |:---------------------------------- |
| Azure Backup             | Microsoft.RecoveryServices | Futtassa a biztonsági mentéshez és helyreállításhoz, a nem felügyelt lemezek az iaas-beli virtuális gépeken. (nem felügyelt lemezek esetén szükséges). [További információk](/azure/backup/backup-introduction-to-azure-backup). |
| Azure Data Box           | Microsoft.DataBox          | Lehetővé teszi az Azure-ba történő adatimportálást Data Box használatával. [További információk](/azure/databox/data-box-overview). |
| Azure DevTest Labs       | Microsoft.DevTestLab       | Egyéni rendszerkép létrehozása és az összetevő telepítését. [További információk](/azure/devtest-lab/devtest-lab-overview). |
| Azure Event Grid         | Microsoft.EventGrid        | A Blob Storage esemény-közzététel engedélyezése, és lehetővé teszi az Event Grid tárolási üzenetsorok közzétételére. Tudnivalók a [blob Storage-eseményekről](/azure/event-grid/event-sources) és [a várólistákon való közzétételről](/azure/event-grid/event-handlers). |
| Azure Event Hubs         | Microsoft.EventHub         | Az Event Hubs Capture adatok archiválása. [További információ](/azure/event-hubs/event-hubs-capture-overview). |
| Azure File Sync          | Microsoft.StorageSync      | Lehetővé teszi a helyszíni fájlkiszolgáló átalakítását az Azure-fájlmegosztás gyorsítótárába. Lehetővé teszi a többhelyes szinkronizálást, a gyors katasztrófa-helyreállítást és a Felhőbeli biztonsági mentést. [További információ](../files/storage-sync-files-planning.md) |
| Azure HDInsight          | Microsoft.HDInsight        | Az alapértelmezett fájlrendszer kezdeti tartalmának kiépítése egy új HDInsight-fürthöz. [További információk](/azure/hdinsight/hdinsight-hadoop-use-blob-storage). |
| Azure importálási exportálás      | Microsoft.ImportExport     | Lehetővé teszi az adatok importálását az Azure-ba és az Azure-ból származó adatok exportálását az importálási/exportálási szolgáltatás segítségével. [További információk](/azure/storage/common/storage-import-export-service).  |
| Azure Monitor            | Microsoft.Insights         | Lehetővé teszi, hogy a figyelési adatai biztonságos Storage-fiókba legyenek írva, beleértve az erőforrás-diagnosztikai naplókat, Azure Active Directory a bejelentkezést és a naplókat, valamint Microsoft Intune naplókat. [További információk](/azure/monitoring-and-diagnostics/monitoring-roles-permissions-security). |
| Azure-hálózatok         | Microsoft.Network          | Store és a hálózati forgalom naplóinak elemzése. [További információk](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview). |
| Azure Site Recovery      | Microsoft.SiteRecovery     | Engedélyezze a replikációt az Azure IaaS-alapú virtuális gépek vész-helyreállításához, ha tűzfalon alapuló gyorsítótár-, forrás-vagy tároló-fiókot használ.  [További információk](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication). |

A **megbízható Microsoft-szolgáltatások engedélyezése...** beállítás azt is lehetővé teszi, hogy az alábbi szolgáltatások egy adott példánya hozzáférhessen a Storage-fiókhoz, ha explicit módon HOZZÁRENDEL [egy RBAC-szerepkört](storage-auth-aad.md#assign-rbac-roles-for-access-rights) az adott erőforrás-példányhoz tartozó [rendszerhez rendelt felügyelt identitáshoz](../../active-directory/managed-identities-azure-resources/overview.md) . Ebben az esetben a példányhoz való hozzáférés hatóköre megfelel a felügyelt identitáshoz rendelt RBAC szerepkörnek.

| Szolgáltatás                        | Erőforrás-szolgáltató neve                 | Cél            |
| :----------------------------- | :------------------------------------- | :----------------- |
| Azure Cognitive Search         | Microsoft.Search/searchServices        | Lehetővé teszi Cognitive Search szolgáltatások számára a Storage-fiókok elérését indexelésre, feldolgozásra és lekérdezésre. |
| Azure Container Registry Tasks | Microsoft.ContainerRegistry/registries | Az ACR-feladatok tároló-lemezképek létrehozásakor férhetnek hozzá a Storage-fiókokhoz. |
| Azure Data Factory             | Microsoft.DataFactory/factories        | Lehetővé teszi a Storage-fiókok elérését az ADF futtatókörnyezeten keresztül. |
| Azure Data Share               | Microsoft. DataShare/fiókok           | Lehetővé teszi a Storage-fiókok elérését az adatmegosztáson keresztül. |
| Azure Logic Apps               | Microsoft.Logic/workflows              | Lehetővé teszi a Logic apps számára a Storage-fiókok elérését. [További információk](/azure/logic-apps/create-managed-service-identity#authenticate-access-with-managed-identity.md). |
| Azure Machine Learning szolgáltatás | Microsoft.MachineLearningServices      | Engedélyezett Azure Machine Learning munkaterületek a kísérlet kimenetét, modelljeit és naplóit írják a blob Storage-ba, és beolvasják az adatokat. [További információk](/azure/machine-learning/service/how-to-enable-virtual-network#use-a-storage-account-for-your-workspace). | 
| Azure SQL Data Warehouse       | Microsoft.Sql                          | Lehetővé teszi az adatok importálását és exportálását egy adott SQL Database példányból a Base használatával. [További információk](/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview). |
| Azure Stream Analytics         | Microsoft.StreamAnalytics             | Lehetővé teszi a folyamatos átviteli feladatok adatainak blob Storage-ba való írását. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. [További információk](/azure/stream-analytics/blob-output-managed-identity). |
| Azure Synapse Analytics        | Microsoft. szinapszis/munkaterületek          | Lehetővé teszi az Azure Storage-beli adatokhoz való hozzáférést a szinapszis Analyticsből. |


### <a name="storage-analytics-data-access"></a>Storage analytics-adatok elérése

Bizonyos esetekben a diagnosztikai naplók és mérőszámok olvasási hozzáférést szükség a a hálózathatáron kívül. A megbízható szolgáltatások a Storage-fiókhoz való hozzáférésének konfigurálásakor engedélyezheti az olvasási hozzáférést a naplófájlokhoz, a metrikák tábláihoz vagy mindkettőhöz. [További információ a Storage Analytics használatáról.](/azure/storage/storage-analytics)

### <a name="managing-exceptions"></a>Kivételek kezelése

Hálózati kivételeit, annak az Azure portal, PowerShell vagy az Azure CLI segítségével is kezelheti v2.

#### <a name="azure-portal"></a>Azure Portal

1. Nyissa meg a védeni kívánt tárfiókot.

1. Kattintson a **tűzfalak és virtuális hálózatok**beállítások menüjére.

1. Győződjön meg arról, hogy a **kijelölt hálózatokból**való hozzáférés engedélyezését választotta.

1. A **kivételek**területen válassza ki a megadni kívánt kivételeket.

1. A módosítások alkalmazásához kattintson a **Mentés** gombra.

#### <a name="powershell"></a>PowerShell

1. Telepítse a [Azure PowerShellt](/powershell/azure/install-Az-ps) , és [Jelentkezzen be](/powershell/azure/authenticate-azureps).

1. A storage-fiók hálózati szabályok esetében a kivételek jelennek meg.

    ```powershell
    (Get-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount").Bypass
    ```

1. A storage-fiók hálózati szabályok alóli kivételek konfigurálása.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass AzureServices,Metrics,Logging
    ```

1. Távolítsa el a storage-fiók hálózati szabályok alóli kivételek.

    ```powershell
    Update-AzStorageAccountNetworkRuleSet -ResourceGroupName "myresourcegroup" -Name "mystorageaccount" -Bypass None
    ```

> [!IMPORTANT]
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a kivételek eltávolítása ne legyen hatással.

#### <a name="cliv2"></a>CLIv2

1. Telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t, és [Jelentkezzen be](/cli/azure/authenticate-azure-cli).

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
> Ügyeljen arra, hogy [az alapértelmezett szabályt](#change-the-default-network-access-rule) a **Megtagadás**értékre állítsa, vagy a kivételek eltávolítása ne legyen hatással.

## <a name="next-steps"></a>Következő lépések

További információ az Azure Network Service-végpontokról a [szolgáltatási végpontokon](/azure/virtual-network/virtual-network-service-endpoints-overview).

Mélyebb betekintést nyújt az Azure Storage biztonságára az [Azure Storage biztonsági útmutatójában](../blobs/security-recommendations.md).
